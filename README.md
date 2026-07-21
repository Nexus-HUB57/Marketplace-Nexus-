# 🛒 Marketplace Nexus

> Plataforma de marketplace digital da **Nexus AI** — gestão unificada de produtos (ebooks, skills, packs), checkout, biblioteca do usuário e integrações com Academia EAD.

[![Status](https://img.shields.io/badge/status-migrated-blue)]()
[![Repo](https://img.shields.io/badge/source-marketplace--nexus-green)]()

---

## 📋 O que é este repositório

Este repo é **exclusivo** do **Marketplace Nexus**:
- Backend tRPC (`marketplace*` + `academia*`)
- Frontend React + Vite (Marketplace + Academia EAD)
- Schemas DB e migrations
- Catalog de ebooks (manifest + PDFs do produto)
- Conteúdo educacional da Academia (md/html)

Materiais **fora deste repo** (mantidos no monorepo [`MMN_AI-to-AI`](https://github.com/Nexus-HUB57/MMN_AI-to-AI)):
- Core da orquestração Nexus / Judge / CEO-AI
- Workflows de deploy
- Capa visual dos ebooks (472 MB → R2/S3)

---

## 🗂 Estrutura

```
Marketplace-Nexus-/
├── backend/
│   ├── migrations/                    SQL de marketplace
│   └── src/
│       ├── routers/                   tRPC routers (8)
│       ├── services/                  business logic (5)
│       ├── workers/                   jobs (marketplace sync)
│       └── domains/                   marketplace + skillMarketplace
├── database/
│   ├── migrations/                    user_library SQL
│   └── schemas/                       marketplace-schema.ts
├── frontend/
│   └── src/
│       ├── components/Marketplace/    UI components (6)
│       └── pages/                     Rotas (11)
├── scripts/                           migradores + gerar ebooks
├── data/                              catalog JSON
├── assets/
│   └── ebooks_pdf/curso_universo_ia/  PDFs reais do produto
├── academia/                          Conteúdo md/html
│   ├── apostilas/, cursos/, webinars/
│   ├── governanca/, playbooks/, tutoriais/
│   └── producao/, scripts/
└── docs/                              SYNC + ARCHITECTURE
```

---

## 🚀 Setup local

```bash
# 1. dependências do backend (trpc, drizzle, db)
cd backend && npm install

# 2. dependências do frontend
cd ../frontend && npm install

# 3. aplicar migrations
cd ../backend && psql $DATABASE_URL -f migrations/0015_expand_marketplace_orders_id.sql
psql $DATABASE_URL -f migrations/2026_06_21_marketplace_pack_grants.sql
psql $DATABASE_URL -f ../database/migrations/0012_marketplace_user_library.sql

# 4. rodar scripts de seed/catalog
python3 ../scripts/seed_marketplace_ebooks.py
python3 ../scripts/generate_marketplace_ebooks.py

# 5. subir backend e frontend
npm run dev  # backend
npm run dev  # frontend, em outro terminal
```

---

## 🧪 Como rodar os testes do marketplace

```bash
# Backend tRPC routers (marketplace + academia)
cd backend
npm run test -- --run marketplaceRouter
npm run test -- --run marketplaceNexusRouter
npm run test -- --run academiaEadRouter

# Smoke-test da academia
bash ../academia/scripts/smoke-test-academia.sh
```

---

## 📚 Documentação interna

- [`docs/MARKETPLACE_EBOOKS_SYNC_REPORT.md`](docs/MARKETPLACE_EBOOKS_SYNC_REPORT.md) — relatório de sincronização de ebooks
- [`docs/MARKETPLACE_NEXUS_ARCHITECTURE.md`](docs/MARKETPLACE_NEXUS_ARCHITECTURE.md) — arquitetura detalhada
- [`scripts/MARKETPLACE_EBOOKS_SYNC_REPORT.md`](scripts/MARKETPLACE_EBOOKS_SYNC_REPORT.md) — guidelines de sync
- [`academia/governanca/`](academia/governanca/) — regras de governança do conteúdo
- [`academia/playbooks/`](academia/playbooks/) — playbooks de produto

---

## 🤝 Contribuição

1. Trabalhe em branch: `git checkout -b feat/<sua-feature>`
2. Commit no padrão Conventional Commits
3. PR aberto contra `main` (revisão obrigatória)
4. CI valida typecheck, lint, build antes do merge

---
