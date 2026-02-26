# create-campaign-plan

Task atomica para criacao de plano de campanha para novo cliente.

```yaml
task:
  name: create-campaign-plan
  executor: ads-strategist
  elicit: true
  estimated_time: "10-15 min"

  description: |
    Gera plano estrategico completo para novo cliente incluindo funil,
    split de budget, publicos, e metas por campanha.

  inputs:
    required:
      - "Tipo de negocio (e-commerce, local, infoprodutor)"
      - "Segmento/nicho"
      - "Budget mensal em trafego"
      - "Objetivo principal (leads, vendas, awareness)"
    optional:
      - "Plataformas preferidas"
      - "Historico de campanhas anteriores"
      - "Publico-alvo definido"
      - "Assets disponiveis (criativos, LP, catalogo)"

  elicitation:
    - question: "Qual o tipo de negocio e segmento?"
      type: "freeform"
      required: true
    - question: "Qual o budget mensal para trafego?"
      type: "freeform"
      required: true
    - question: "Qual o objetivo principal?"
      type: "select"
      options: ["Leads (WhatsApp/formulario)", "Vendas diretas (e-commerce)", "Awareness/Branding", "Mix"]
      required: true
    - question: "Ja rodou campanhas antes? Se sim, quais resultados?"
      type: "freeform"
      required: false

  steps:
    - step: 1
      action: "Carregar ficha do cliente de data/clients/{slug}.yaml"
      checkpoint:
        veto: "SE ficha nao existe → alertar e pedir dados minimos"

    - step: 2
      action: "Classificar tipo de cliente (local vs e-comm) e aplicar heuristica correspondente"
      checkpoint:
        required: "Tipo classificado antes de prosseguir"

    - step: 3
      action: "Definir funil CVJ (Aware → Engage → Convert → Retarget)"
      checkpoint:
        veto: "SE funil nao tem pelo menos 3 etapas → REVISAR"

    - step: 4
      action: "Calcular split de budget por etapa do funil e plataforma"
      checkpoint:
        veto: "SE split nao soma 100% → CORRIGIR antes de prosseguir"

    - step: 5
      action: "Definir metas (CPA/CPL/ROAS) baseado em benchmarks do segmento"

    - step: 6
      action: "Listar publicos recomendados por plataforma"

    - step: 7
      action: "Compliance check — validar plano contra restricoes da ficha do cliente"
      checkpoint:
        veto: "SE plano viola client.restricoes_produto ou client.regras.comunicacao → BLOQUEAR"

    - step: 8
      action: "Definir proximos passos (handoff para meta-ads-expert e/ou google-ads-expert)"

  action_items:
    - "[ ] Ficha do cliente carregada"
    - "[ ] Tipo de cliente classificado (local/e-comm/info)"
    - "[ ] Funil definido com etapas e % budget"
    - "[ ] Split de budget soma 100%"
    - "[ ] Metas definidas com base em benchmarks"
    - "[ ] Publicos recomendados por plataforma"
    - "[ ] Compliance check contra ficha do cliente"
    - "[ ] Proximos passos com handoff definido"

  output_example: |
    ## Plano Estrategico: [Cliente] — [Segmento]

    **Tipo:** [Local/E-commerce] | **Budget:** R$X/mes | **Objetivo:** [Leads/Vendas]

    ### Funil
    - TOFU (X%): R$X — [tipo de campanha]
    - BOFU (X%): R$X — [tipo de campanha]
    - Retargeting (X%): R$X — [tipo de campanha]

    ### Split de Plataforma
    - Meta: X% (R$X) — [justificativa]
    - Google: X% (R$X) — [justificativa]

    ### Metas
    - CPA/CPL meta: R$X
    - Volume esperado: X conversoes/mes
    - ROAS meta: Xx (se e-commerce)

    ### Proximos Passos
    1. Validar metas com cliente
    2. meta-ads-expert → setup Meta
    3. google-ads-expert → setup Google

  completion_criteria:
    - "Funil definido com % de budget por etapa"
    - "Split de plataforma justificado"
    - "Metas definidas com benchmarks"
    - "Proximos passos claros com handoff"

  veto_conditions:
    - "SE sem budget definido → NAO criar plano, pedir valor"
    - "SE sem objetivo definido → NAO criar plano, pedir objetivo"
    - "SE budget < R$500/mes → ALERTAR que volume sera insuficiente para otimizar"
```
