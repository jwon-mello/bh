# BIG Hub — Wireframes

Wireframes low-fidelity (estética sketch/whiteboard) das iniciativas do Big Hub.

Hospedados via GitHub Pages: **https://jwon-mello.github.io/bh/**

## Estrutura

```
bighub-wireframes/
├── logistica-fbm/
│   └── pool-bh-coleta-unificada/   ← 29 HTMLs · onda 1 completa (v1.5)
├── cadastro-lojistas/
│   ├── eligibility-por-perfil/
│   ├── eligibility-preview/
│   └── eligibility-universal/
└── loja-templates/
    └── checkout-antes-briefing/
```

## Convenção de nomes (workflow de wireframing em 4 etapas)

```
Pré: Mapa de telas        — enumera telas únicas a partir da journey
Etapa 1: Wireframe base   — grid regiões × variações A/B/C por tela
Etapa 2: Protótipo de tela — combinação validada + cenários (vazio/pico/falha)
Etapa 3: Jornada navegável — shell BH + N telas da journey encadeadas
```

Arquivos:
- `{caminho}--{tela}.html` — Etapa 1
- `{caminho}--{tela}-prototipo.html` — Etapa 2
- `{caminho}-jornada.html` — Etapa 3

## BIG logistics FBM v1.5 — onda 1 completa

**14 wireframes base · 5 protótipos de tela · 8 jornadas navegáveis** cobrindo as 8 user stories must/should-have do v1.5.

Documentação completa no ClickUp (doc `Product Documentation` no space "Novo Ecosistema 2").

## Como usar

Os HTMLs são autônomos (CSS inline, Google Fonts via CDN, vanilla JS). Abrem direto no browser, no Obsidian Web Viewer, ou via GitHub Pages.

Estética sketch é intencional: sinaliza "ainda é esboço" e abre espaço para crítica do FLUXO, não da forma.
