# 📸 Hospedagem de capas de ebook (guia de setup)

Os 243 arquivos `.webp` de capas de ebook (**472 MB**) **não vão para este repo**.
Eles devem ficar num bucket público com URL consistente, e o `MarketplaceProductCard.tsx`
faz referência por URL.

## Opções recomendadas (em ordem de preferência)

### 1. Cloudflare R2 (recomendado)
- Custo: **$0.015/GB/mês + egress grátis**
- Compatível com S3 (pode usar AWS CLI ou SDK)
- Vincula domínio custom (ex.: `cdn.marketplace-nexus.com`)

```bash
# 1. criar bucket
wrangler r2 bucket create marketplace-covers

# 2. upload das 243 capas (preservando hierarquia)
aws s3 sync assets/ebook_covers/ \
  s3://marketplace-covers/ebook_covers/ \
  --endpoint-url https://<ACCOUNT_ID>.r2.cloudflarestorage.com

# 3. configurar domínio público
# Via dashboard: https://dash.cloudflare.com → R2 → Public access
# Ou via wrangler: wrangler r2 bucket update marketplace-covers --public
```

### 2. AWS S3 + CloudFront
- Custo similar
- Mais maduro em tooling

```bash
aws s3 sync ./assets/ebook_covers s3://marketplace-nexus-covers/ \
  --endpoint=https://s3.<region>.amazonaws.com \
  --acl public-read
```

### 3. Bunny CDN (armazenamento + CDN)
- Custo menor ainda para volumes médios
- Upload via painel deles

## Mapeamento no frontend

O frontend busca capa do produto assim (já implementado):

```tsx
// frontend/src/components/Marketplace/MarketplaceProductCard.tsx
const coverUrl = product.coverCdnUrl ?? `/assets/ebook_covers/${product.slug}.webp`;
```

Quando o bucket estiver configurado, basta popular a coluna `coverCdnUrl`
no DB via migration nova:

```sql
-- backend/migrations/2026_07_cover_cdn_urls.sql
UPDATE products SET cover_cdn_url = 'https://cdn.marketplace-nexus.com/ebook_covers/' || slug || '.webp';
```

## O que ficar no repo (até o bucket existir)

Enquanto o bucket não está configurado, mantenha **menos de 10 capas pequenas**
em `assets/ebook_covers/` como fallback. As outras 233 ficam fora do repo e
serão instaladas ao rodar:

```bash
# Após clonar (em dev local)
bash scripts/pull-covers-from-cdn.sh
```

(Esse script será criado quando o bucket público for ativado.)
