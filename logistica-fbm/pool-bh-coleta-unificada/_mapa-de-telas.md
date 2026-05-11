---
tipo: mapa-de-telas
feature: logistica-fbm
hipotese: pool-bh-coleta-unificada
release: v1.5
gerado-em: 2026-05-08
atualizado-em: 2026-05-11
fonte: 9 journeys do PRD logistica-fbm-unificada
---

# Mapa de telas — BIG logistics FBM v1.5

Documento de referência para wireframing. Produzido no **passo 0** do skill `bighub-wireframes` (mapa de telas). Lista todas as telas únicas que aparecem nas 9 journeys do v1.5, com prioridade e ordem de desenho recomendada.

> Este é um meta-documento, não um wireframe. Atualizar conforme telas viram desenho ou conforme journeys são revisadas.

## Refatoração estrutural (2026-05-11) — `importar-contrato-preview`

Análise de 3 contratos reais (GLS, FedEx, OnTime) revelou que a estrutura "Zonas + Tabela peso×preço + Surcharges" era simples demais. Refatoração da abstração de **Contrato** em 5 camadas, com foco POC em camadas 2+3:

```
Contrato
├─ 1. Identidade (partes, vigência, pagamento, equipamento)
├─ 2. PRICING (núcleo) ← foco POC
│   ├─ Zonas globais (estatico-pais | matriz-orig-dest | direto | cp-faixa | regional-por-origem)
│   ├─ Produtos (cada um com sub-tabelas filtradas, tasa volumétrica, restrições)
│   └─ Adicionais globais (DUAs, ilhas, CCAA)
├─ 3. MODIFICADORES MULTIPLICATIVOS ← foco POC
│   ├─ Combustível (escala condicional externa €/L → %)
│   ├─ Descarbonização (% fixo)
│   ├─ Seguro adicional opcional (% + cobertura + 9-10 exclusões)
│   └─ IVA (regra fiscal externa)
├─ 4. Operativas (reembolsos, perecíveis ATP, perigosas ADR, excluídas) → texto livre
└─ 5. Legais (LCTTM, subcontratação, jurisdição) → texto livre
```

**Validação cruzada** (atualizada 2026-05-11 com 4 contratos reais; documentação técnica completa em `[[seller-importar-contrato-poc-happy]]`):

| Dimensão | GLS | FedEx | OnTime | Correos Express |
|---|:---:|:---:|:---:|:---:|
| Múltiplos produtos | 4 | 5 | 11 serviços | **13 produtos** |
| Zonas globais vs por produto | por produto | globais | globais por categoria | destinos por produto |
| Mapeamento `estatico-pais` | ✓ | — | — | ✓ |
| Mapeamento `matriz-orig-dest` | — | ✓ 50×50 | ✓ XL 50×50 | — |
| Mapeamento `direto` (ilhas) | ✓ | — | ✓ | ✓ |
| Mapeamento `cp-faixa` | — | — | ✓ Portugal | — |
| Mapeamento `regional-por-origem` | — | — | ✓ XS 9 regiões | — |
| Sub-tabelas por embalagem/condição | — | ✓ Env/Pak | ✓ XP tipologia | — |
| Padrão "1 produto = 1 tabela peso×destino" | — | — | — | ✓ **(dominante)** |
| Tarifa multiplicador kg adicional | ✓ | ✓ | ✓ | ✓ |
| **Combustível escala explícita** | — | — | ✓ 33 faixas | — |
| **Combustível por referência à lei** | implícito | — | — | ✓ explícito |
| **Descarbonização %** | — | — | ✓ 1% | — |
| Seguro com exclusões | ⚠ simples | — | ✓ 9-10 categorias | ⚠ simples |
| Tasa volumétrica por produto | uniforme | — | 7 coefs | 2 perfis |
| **Volume mínimo contratual** | — | — | — | ✓ |
| Cláusulas legais detalhadas | médio | só tabela | extensa | extensa |

**Wireframes impactados** (refatorados 2026-05-11):
- `importar-contrato-preview.html` (Etapa 1) — R4 reorganizada de 3 categorias planas para 4 (Zonas · Produtos · Adicionais · Modificadores) com hierarquia 2 níveis no acordeão
- `importar-contrato-preview-prototipo.html` (Etapa 2) — 5 cenários novos baseados em FedEx multi-produto e OnTime com modificadores externos. Inclui simulador de cotação pós-ativação como guard-rail

**Implicações para o PRD** (a registrar quando voltarmos):
- Schema canônico da entidade `Contrato` precisa expor 5 camadas com flexibilidade nos tipos de mapeamento de zona e formato de sub-tabela
- Prompt do LLM extrator: detectar produtos primeiro, depois sub-tabelas com filtros condicionais, separadamente os modificadores multiplicativos
- Modificadores como combustível e descarbonização podem precisar de fonte de dados externa (MITMA, etc.) — não vivem só na tabela ativa

## Decisões aplicadas (2026-05-08)

1. **Seções 3 (Saúde) e 5 (Tendências) do dashboard**: tratadas como **cenários** da tela `dashboard-uso-cobranca` (toggle on/off conforme telemetria v1.5), **não telas separadas**.
2. **`painel-coletas` é tela separada**: desenhar dedicada para visão multi-origem completa, não apenas Seção 2 do dashboard.

## Status atual de wireframes (2026-05-11)

**✅ Onda 1 do v1.5 completa.** 14 wireframes base (Etapa 1) · 5 protótipos de tela (Etapa 2) · 8 jornadas navegáveis (Etapa 3) entregues.

### Cobertura por journey

| Journey | Etapa 1 (telas) | Etapa 2 (cenários) | Etapa 3 (jornada) |
|---|---|---|---|
| `setup-inicial-happy` | 1 (wizard-shell) | 1 (8 conteúdos) | ✅ 8 fases |
| `seller-pedido-happy` | 5 (fila · detalhe · coletas · csv · manual) | 1 (detalhe, 8 cenários) | ✅ 6 fases |
| `seller-rma-canal-bh-happy` | 2 (fila · detalhe) | 1 (detalhe, 5 cenários) | ✅ 6 fases |
| `seller-fatura-etiquetas-mensal-happy` | 2 (planos-faturas · etiquetas-ciclo) | 1 (planos-faturas, 4 cenários) | ✅ 5 fases |
| `seller-config-regra-happy` | 2 (hub · builder) | — (embutido na jornada) | ✅ 5 fases (should-have) |
| `seller-importar-contrato-poc-happy` | 1 (preview, refatorada) | 1 (5 cenários multi-contrato) | ✅ 5 fases |
| `seller-dashboard-uso-cobranca-happy` | 1 (mono-tela) | 1 (5 cenários) | ✅ 3 rotinas |
| `comprador-cancela-antes-coleta` | reusa detalhe-pedido | reusa detalhe-pedido | ✅ 5 fases |
| `comprador-cotacao-frete-checkout-happy` | ⏸ fora do v1.5 | ⏸ | ⏸ |

### Cortes registrados no protótipo de jornada (alinhados com escopo MVP)

- **`dashboard-uso-cobranca`**: Seções 3 (Saúde) e 5 (Tendências) cortadas — `should-have` dependentes de telemetria v1.5. Permanecem no journey doc como referência conceitual.

### Aprendizados que viraram skill
A geração da Etapa 3 do dashboard revelou que **mono-tela com rotinas ≠ wizard sequencial**. O skill `bighub-wireframes` foi atualizado com (a) tabela distinguindo 3 modos de Etapa 3 (wizard / ramificada / mono-tela-rotinas), (b) snippet JS de `rotinas[]` com metadata, (c) princípio "fases vêm da journey, não de invenção", (d) reuso de classes/tokens do shell de jornada de referência, (e) `should-have` cortável com decisão registrada.

## Mapa por journey

### setup-inicial-happy
**Persona**: Seller PF/PJ pós-ativação · **Fases**: 8 · **Tempo**: 10–25 min

| # | Fase | Tela | Status |
|---|------|------|--------|
| 1 | Welcome + mapa do setup | `wizard-welcome` | nova |
| 2 | Endereços de origem | `wizard-origens` | nova |
| 3 | Embalagens cadastradas | `wizard-embalagens` | nova |
| 4 | Pool BH (decisão) | `wizard-pool-bh` | nova |
| 5 | Transportadoras | `wizard-carriers` | nova |
| 5b | Tabela contrato (fallback) | `form-manual-tabela-contrato` ou `importar-contrato-*` | reuso/nova |
| 6 | Janelas de coleta | `wizard-coletas` | nova |
| 7 | Conexão de canais | `wizard-canais` | nova |
| 8 | Resumo e ativação | `wizard-resumo-ativacao` | nova |

### seller-pedido-happy
**Persona**: Seller FBM multi-MKP UE · **Fases**: 7 · **Tempo**: 1–2 min digital

| # | Fase | Tela | Status |
|---|------|------|--------|
| 1 | Pedido chega (canal BH) | — | sistema-only |
| 1 | Pedido chega (CSV externo) | `importacoes-csv` | nova |
| 1 | Pedido chega (manual) | `novo-pedido-manual` | nova |
| 1 | Triagem na fila | `fila-pedidos` | nova |
| 2 | Avaliar e confirmar roteamento | `detalhe-pedido` | nova |
| — | Picking + embalagem | — | sistema-only (físico) |
| 3 | Gerar e imprimir docs | `detalhe-pedido` (cenário: docs-gerados) | cenário |
| 4 | Aguarda coleta | `painel-coletas` | nova |
| 5 | Coleta (físico + webhook) | — | sistema-only |
| 6 | Em trânsito | `detalhe-pedido` (cenário: em-transito) | cenário |
| 7 | Entregue | `detalhe-pedido` (cenário: entregue) | cenário |

### seller-importar-contrato-poc-happy
**Persona**: Seller com PDF de contrato · **Fases**: 5 · **Tempo**: ~5–10 min

| # | Fase | Tela | Status |
|---|------|------|--------|
| 1 | Upload do PDF | `importar-contrato-upload` | nova |
| 2 | Processamento OCR+LLM | `importar-contrato-processando` | nova |
| 3 | Preview da extração | `importar-contrato-preview` | nova |
| 4 | Revisão e ajustes | `importar-contrato-preview` (cenário: edição) | cenário |
| 5 | Validação + ativação | `importar-contrato-preview` (cenário: salva) | cenário |

### seller-fatura-etiquetas-mensal-happy
**Persona**: Seller com ≥1 ciclo · **Fases**: 7 · **Tempo**: 30s–3 min/mês

| # | Fase | Tela | Status |
|---|------|------|--------|
| 1 | Fechamento ciclo (snapshot) | — | sistema-only |
| 2 | Geração linha-item | — | sistema-only |
| 3 | Notificação ao seller | — | sistema-only (email/push) |
| 4 | Seller revisa fatura | `planos-faturas-detalhe` (+ modal `etiquetas-do-ciclo-tabela`) | nova |
| 5 | Aviso pré-cobrança D+3 | — | sistema-only |
| 6 | Cobrança automática D+5 | — | sistema-only |
| 7 | Confirmação pagamento | `planos-faturas-detalhe` (cenário: paga) | cenário |

### comprador-cancela-antes-coleta
**Persona**: Seller recebendo cancel via webhook · **Fases**: 6 · **Tempo**: ~1 min digital

| # | Fase | Tela | Status |
|---|------|------|--------|
| 1 | Comprador cancela no MKP | — | sistema-only (fora BH) |
| 2 | BH detecta + notifica | `fila-pedidos` (cenário: banner-cancel) + `dashboard-uso-cobranca` (Seção 1) | cenário/reuso |
| 3 | Seller abre pedido | `detalhe-pedido` (cenário: cancel-pendente) | cenário |
| 4 | Confirma → ações auto | `detalhe-pedido` (cenário: cancel-checklist) | cenário |
| 5 | Retira pacote | `detalhe-pedido` (cenário: aguarda-retirada) | cenário |
| 6 | Reembolso (passivo) | `detalhe-pedido` (cenário: cancelado-final) | cenário |

### seller-rma-canal-bh-happy
**Persona**: Seller recebendo RMA canal BH · **Fases**: 7 · **Tempo**: ~3–4 min digital

| # | Fase | Tela | Status |
|---|------|------|--------|
| 1 | Comprador solicita RMA | — | sistema-only (portal BH) |
| 2 | BH notifica + classifica | `fila-devolucoes` + `dashboard-uso-cobranca` (Seção 1) | nova/reuso |
| 3 | Seller aceita → ações auto | `detalhe-devolucao` (cenário: aceita) | nova/cenário |
| 4 | Comprador despacha | `detalhe-devolucao` (cenário: aguardando-envio) | cenário |
| 5 | Trânsito reverso | `detalhe-devolucao` (cenário: em-transito-rev) | cenário |
| 6 | Pacote chega + inspeção | `detalhe-devolucao` (cenário: inspecao) | cenário |
| 7 | Reembolso + estoque | `detalhe-devolucao` (cenário: rma-final) | cenário |

### seller-config-regra-happy
**Persona**: Seller já operando · **Fases**: 4 · **Tempo**: variável

| # | Fase | Tela | Status |
|---|------|------|--------|
| 1 | Entrar em Configurações | (navegação sidebar) | sistema-only |
| 2 | Hub | `config-hub` | nova |
| 3 | Editar config estática | wizard-* (modo edição) | reuso |
| 4 | Criar regra condicional | `regra-builder` | nova |

### seller-dashboard-uso-cobranca-happy
**Persona**: Seller ativo, rotina diária · **Seções**: 5 verticais · **Tempo**: 30s–12 min

Mono-tela. Toda a journey orbita `dashboard-uso-cobranca`. Cenários:
- **happy** (default)
- **vazio** (primeiro acesso)
- **pico** (Black Friday)
- **falha** (carregamento parcial)
- **ciclo-anterior** (filtro de data)
- **filtro-origem-ativo** (multi-origem)
- **saude-degradada** (toggle Seção 3)
- **tendencias-on** (toggle Seção 5)

CTA secundário: drill-down `etiquetas-do-ciclo-tabela` (modal/painel).

### comprador-cotacao-frete-checkout-happy *(FORA do v1.5)*
Telas vivem em PRDs de Vitrine / big.pt MKP / portais externos. **Não** entram no escopo de wireframes do BIG logistics v1.5.

## Consolidado de telas únicas (v1.5)

| # | Tela | Journeys | Total fases | Cenários distintos | Prio |
|--:|---|---|--:|---|---|
| 1 | `dashboard-uso-cobranca` | dashboard, fatura (link), pedido, cancel, rma | 8+ | 8 (happy + 7 variantes incl. saude-degradada e tendencias) | **alta** |
| 2 | `detalhe-pedido` | pedido (2,3,6,7), cancel (3,4,5,6) | 8 | 8 (happy, docs, em-trânsito, entregue, 4× cancel) | **alta** |
| 3 | `fila-pedidos` | pedido (1), cancel (2 banner) | 2 | 5 (happy, vazio, pico, banner-cancel, sla-risco) | **alta** |
| 4 | `detalhe-devolucao` | rma (3–7) | 5 | 5 (aceita, aguardando, trânsito-rev, inspeção, rma-final) | **alta** |
| 5 | `fila-devolucoes` | rma (2) | 1 | 3 (happy, vazio, urgência-14d) | **média** |
| 6 | `wizard-welcome` | setup (1) | 1 | happy | **média** |
| 7 | `wizard-origens` | setup (2), config (3) | 2 | 1 origem, multi-origem, edição | **média** |
| 8 | `wizard-embalagens` | setup (3), config (3) | 2 | defaults P/M/G, vazio, edição | **média** |
| 9 | `wizard-pool-bh` | setup (4) | 1 | comparativo 3 cards | **média** |
| 10 | `wizard-carriers` | setup (5), config (3) | 2 | só pool, contrato próprio, erro, edição | **alta** (gateway p/ import-contrato) |
| 11 | `wizard-coletas` | setup (6), config (3) | 2 | 1 origem, multi-origem×carrier, edição | **média** |
| 12 | `wizard-canais` | setup (7), config (3) | 2 | aba ecossistema, aba externos, edição | **média** |
| 13 | `wizard-resumo-ativacao` | setup (8) | 1 | health-check verde, com pendências | **média** |
| 14 | `importar-contrato-upload` | poc-contrato (1) | 1 | drag-drop, erro PDF | **alta** |
| 15 | `importar-contrato-processando` | poc-contrato (2) | 1 | progress, timeout | **alta** |
| 16 | `importar-contrato-preview` | poc-contrato (3,4,5) | 3 | preview-confidence, edição, salva | **alta** |
| 17 | `form-manual-tabela-contrato` | setup (5b fallback), poc-contrato (Plano B) | 1 | 4 passos | **média** (fallback) |
| 18 | `painel-coletas` | pedido (4) | 1 | calendário 1 origem, multi-origem, urgência | **média** (decisão #2) |
| 19 | `importacoes-csv` | pedido (1) | 1 | drag-drop, agendado, mapeamento, erro | **média** |
| 20 | `novo-pedido-manual` | pedido (1) | 1 | form livre | **baixa** |
| 21 | `planos-faturas-detalhe` | fatura (4,7) | 2 | breakdown, modal etiquetas, paga, IVA reverse | **alta** |
| 22 | `etiquetas-do-ciclo-tabela` | dashboard (Seção 4 CTA), fatura (modal Fase 4) | 2 | happy, filtro RMA, filtro excluídas | **média** |
| 23 | `config-hub` | config (2) | 1 | hub, regras-vazio | **média** (should-have) |
| 24 | `regra-builder` | config (4) | 1 | roteamento, embalagem, sugestão nome, conflito | **média** (should-have) |

**Excluídas** (sistema-only, off-product, fora-escopo): webhooks de canal/carrier; snapshot/cobrança SEPA; picking físico; notificações push/email; telas do checkout do comprador.

## Insight estrutural — wizard shell

As 8 telas do setup (`wizard-welcome` → `wizard-resumo-ativacao`) compartilham **shell linear**: progress header + content + footer com `[voltar][pular][continuar]`. Faz sentido desenhar **1 shell × 8 conteúdos**, não 8 wireframes independentes — economiza ~70% do trabalho. Tratar `wizard-shell` como tela-template e cada wizard-* como variação de conteúdo.

## Ordem de desenho recomendada

| Ordem | Tela(s) | Razão |
|------:|---|---|
| ✅ 1 | `dashboard-uso-cobranca` | **Já validada** (Etapas 1+2). Pendente: refazer Etapa 3 sob flow novo (`-jornada.html`) |
| 2 | `detalhe-pedido` | Segunda âncora — 8 cenários, 2 journeys, fronteira com fatura/cancel |
| 3 | `fila-pedidos` | Entry point diário, reusa estilos do dashboard |
| 4 | `detalhe-devolucao` + `fila-devolucoes` | Espelha pedido — herança de padrão |
| 5 | `importar-contrato-preview` | POC com layout único (PDF lado-a-lado) — destrava upload e processando |
| 6 | `wizard-shell` (template) + 8 conteúdos | Setup inteiro num shell comum |
| 7 | `planos-faturas-detalhe` + `etiquetas-do-ciclo-tabela` | Par link-bidirecional com dashboard |
| 8 | `painel-coletas` | Tela dedicada (decisão #2) |
| 9 | `config-hub` + `regra-builder` | Should-have, por último |
| 10 | `importacoes-csv` + `novo-pedido-manual` | Periféricas |

## Próximos passos

- [x] Mapa de telas (este doc)
- [ ] Etapa 1 — `detalhe-pedido` (regiões × variações)
- [ ] Etapa 1 — `fila-pedidos`
- [ ] Etapa 1 — `detalhe-devolucao` + `fila-devolucoes`
- [ ] Etapa 1 — `importar-contrato-preview`
- [ ] Etapa 1 — `wizard-shell` (template) + conteúdos
- [ ] Etapa 1 — `planos-faturas-detalhe` + `etiquetas-do-ciclo-tabela`
- [ ] Etapa 1 — `painel-coletas`
- [ ] Etapa 1 — `config-hub` + `regra-builder`
- [ ] Etapa 1 — `importacoes-csv` + `novo-pedido-manual`
- [ ] Etapa 2 — telas com cenários relevantes (priorizar `detalhe-pedido` com 8 cenários)
- [ ] Etapa 3 — `seller-pedido-happy-jornada.html` (junta fila + detalhe + painel-coletas)
- [ ] Etapa 3 — `seller-rma-canal-bh-happy-jornada.html`
- [ ] Etapa 3 — `comprador-cancela-antes-coleta-jornada.html`
- [ ] Etapa 3 — `seller-importar-contrato-poc-happy-jornada.html`
- [ ] Etapa 3 — `seller-dashboard-uso-cobranca-happy-jornada.html` (refazer)
- [ ] Etapa 3 — `setup-inicial-happy-jornada.html`
- [ ] Etapa 3 — `seller-fatura-etiquetas-mensal-happy-jornada.html`
- [ ] Etapa 3 — `seller-config-regra-happy-jornada.html`
