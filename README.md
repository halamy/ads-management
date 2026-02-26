# Squad: Gestao de Trafego Pago (ads-management)

Squad especializado em gestao de trafego pago para Meta Ads e Google Ads,
baseado em elite minds com frameworks documentados.

## Estrutura

```
ads-management/
├── agents/
│   ├── ads-chief.md          # Orchestrator — roteia demandas
│   ├── ads-strategist.md     # Tier 0 — Estrategia de funil (Molly Pittman + Ralph Burns)
│   ├── ads-diagnostician.md  # Tier 0 — Diagnostico de performance (Mike Rhodes)
│   ├── meta-ads-expert.md    # Tier 1 — Especialista Meta Ads (Depesh Mandalia + Pedro Sobral)
│   ├── google-ads-expert.md  # Tier 1 — Especialista Google Ads (Kasim Aslam + Ed Leake)
│   ├── ads-creative-analyst.md # Tier 1 — Analise criativa (Dara Denney + Barry Hott + Nick Shackelford)
│   └── ads-optimizer.md      # Tier 2 — Otimizacao rotineira (Ed Leake + Andrew Foxwell)
├── tasks/
│   ├── diagnose-campaign.md
│   ├── create-campaign-plan.md
│   ├── daily-health-check.md
│   ├── generate-client-report.md
│   ├── intake-client-context.md
│   ├── analyze-ad-creatives.md
│   ├── create-creative-brief.md
│   └── competitor-creative-analysis.md
├── workflows/
│   ├── wf-client-onboarding.yaml
│   ├── wf-daily-optimization.yaml
│   ├── wf-performance-diagnosis.yaml
│   └── wf-creative-refresh.yaml
├── templates/
│   ├── relatorio-mensal.yaml
│   ├── relatorio-semanal.yaml
│   ├── plano-estrategico.yaml
│   ├── log-diario.yaml
│   ├── analise-criativos.yaml
│   └── brief-criativo.yaml
├── checklists/
│   ├── checklist-diario.md
│   ├── checklist-semanal.md
│   ├── checklist-mensal.md
│   ├── checklist-lancamento.md
│   ├── checklist-tracking.md
│   └── checklist-creative-review.md
├── data/
│   ├── benchmarks.yaml
│   ├── heuristics-index.yaml
│   └── clients/
│       ├── _template-client.yaml
│       └── uvits-vitaminas.yaml
├── config.yaml
└── README.md
```

## Como Usar

### Ponto de Entrada
Ative `@ads-management:ads-chief` para ser roteado ao agent correto.

### Uso Direto por Agent
- `@ads-management:ads-strategist` — Planejamento de novo cliente
- `@ads-management:ads-diagnostician` — Algo esta errado com a campanha
- `@ads-management:meta-ads-expert` — Perguntas de Meta Ads
- `@ads-management:google-ads-expert` — Perguntas de Google Ads
- `@ads-management:ads-creative-analyst` — Analise e estrategia criativa
- `@ads-management:ads-optimizer` — Rotina diaria de otimizacao

### Cenarios Comuns

| Situacao | Agent | Exemplo |
|----------|-------|---------|
| Cliente novo | ads-strategist | "Tenho um cliente dentista, R$2k/mes" |
| CPA subiu | ads-diagnostician | "CPA subiu 40%, CTR caiu, freq 3.2" |
| Montar campanha Meta | meta-ads-expert | "Como estruturo CBO pra e-commerce?" |
| Montar conta Google | google-ads-expert | "Setup Google Ads pra negocio local" |
| Criativos cansados | ads-creative-analyst | "Hook rate caiu, frequencia alta" |
| Rotina do dia | ads-optimizer | "Dados de hoje das 9 contas" |
| Relatorio pro cliente | ads-optimizer | "Gera relatorio do mes" |

## Elite Minds

| Mind | Framework | Score | Agent |
|------|-----------|-------|-------|
| Depesh Mandalia | BPM Method + CBO Cookbook | 9/9 | meta-ads-expert |
| Pedro Sobral | Metodo Sobral | 8/9 | meta-ads-expert |
| Kasim Aslam / John Moran | You vs Google + 6 Campaigns | 9/9 | google-ads-expert |
| Ed Leake | God Tier Ads 400+ Steps | 9/9 | google-ads-expert + ads-optimizer |
| Mike Rhodes | AdAID Framework | 8/9 | ads-diagnostician |
| Molly Pittman | CVJ + Profitable Traffic System | 9/9 | ads-strategist |
| Ralph Burns | CaAMP + Kaizen Kreative | 7/9 | ads-strategist |
| Andrew Foxwell | AAA Method + SOPs | 8/9 | ads-optimizer |
| Dara Denney | Performance Creative — Hook-Hold-CTA | 9/9 | ads-creative-analyst |
| Barry Hott | Ad Creative Iteration System | 8/9 | ads-creative-analyst |
| Nick Shackelford | Structured Creative Testing | 8/9 | ads-creative-analyst |

## Workflows

| Workflow | Trigger | Fases |
|----------|---------|-------|
| wf-client-onboarding | Novo cliente | Briefing → Estrategia → Setup → Lancamento → Otimizacao |
| wf-daily-optimization | Rotina diaria | Check → Diagnostico → Decisao → Acao → Log |
| wf-performance-diagnosis | Queda de performance | Alerta → Investigacao → Causa-raiz → Plano |
| wf-creative-refresh | Fadiga criativa | Deteccao → Analise Winners → Brief → Teste → Validacao |

## Stats

- **7 agents** | **8 tasks** | **4 workflows** | **6 templates** | **6 checklists**
- **11 elite minds** com frameworks documentados
- **v1.3.0** — Creative Analyst System adicionado (2026-02-25)
