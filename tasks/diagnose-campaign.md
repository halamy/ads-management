# diagnose-campaign

Task atomica para diagnostico de campanha com problema.

```yaml
task:
  name: diagnose-campaign
  executor: ads-diagnostician
  elicit: true
  estimated_time: "5-10 min"

  description: |
    Executa a arvore de diagnostico AdAID (Ask-Information-Do) para identificar
    causa-raiz de queda de performance e recomendar acoes priorizadas.

  inputs:
    required:
      - "Sintoma (o que esta errado: CPA subiu, conversoes cairam, etc.)"
      - "Plataforma (Meta ou Google)"
      - "Metricas atuais (CPA/ROAS, CTR, frequencia se Meta)"
      - "Metricas meta (CPA/ROAS esperado)"
    optional:
      - "Quando comecou o problema"
      - "Algo mudou recentemente (criativo, publico, budget, site)"
      - "Tempo de campanha (dias rodando)"

  elicitation:
    - question: "Qual o sintoma? (CPA subiu, ROAS caiu, conversoes pararam, etc.)"
      type: "freeform"
      required: true
    - question: "Plataforma?"
      type: "select"
      options: ["Meta Ads", "Google Ads", "Ambas"]
      required: true
    - question: "Cole as metricas: CPA/ROAS atual, meta, CTR, frequencia (Meta), conversoes/dia"
      type: "freeform"
      required: true
    - question: "Mudou algo nos ultimos 7 dias? (criativo, publico, budget, site, tracking)"
      type: "freeform"
      required: false

  steps:
    - step: 1
      name: "CONTEXT"
      action: "Carregar ficha do cliente de data/clients/{slug}.yaml"
      checkpoint:
        veto: "SE ficha nao existe → alertar e prosseguir com dados fornecidos"

    - step: 2
      name: "ASK"
      action: "Coletar sintoma e dados via elicitacao"
      checkpoint:
        veto: "SE sem dados de metricas apos elicitacao → NAO prosseguir, pedir novamente"

    - step: 3
      name: "INFORMATION"
      action: "Executar arvore de diagnostico com os checks NA ORDEM (nao pular)"
      checks:
        - "CHECK 0: Tracking funcionando? (OBRIGATORIO — sempre primeiro)"
        - "CHECK 1: Criativo fadigado? (freq > 3, CTR caindo)"
        - "CHECK 2: Publico saturado? (< 100k, sobreposicao)"
        - "CHECK 3: Fase de aprendizado? (< 7 dias, < 50 conv)"
        - "CHECK 4: Budget/lance insuficiente?"
        - "CHECK 5: Landing page/oferta?"
        - "CHECK 6: Fator externo?"
      checkpoint:
        rule: "Executar checks sequencialmente. Parar no primeiro match com confianca > 70%."

    - step: 4
      name: "DO"
      action: "Apresentar diagnostico com causa provavel e 3 acoes priorizadas"
      format: |
        Diagnostico: [CAUSA] (confianca X%)
        1. URGENTE: [acao imediata]
        2. IMPORTANTE: [acao em 24-48h]
        3. MONITORAR: [acao de acompanhamento]
        Por que: [explicacao didatica]

    - step: 5
      name: "COMPLIANCE"
      action: "Validar acoes contra restricoes da ficha do cliente"
      checkpoint:
        veto: "SE acao viola client.restricoes_produto ou client.regras.comunicacao → REFORMULAR"

  action_items:
    - "[ ] Ficha do cliente carregada (ou dados minimos coletados)"
    - "[ ] Sintoma e metricas coletados"
    - "[ ] Checks executados sequencialmente (0 → 6)"
    - "[ ] Causa-raiz identificada com nivel de confianca"
    - "[ ] 3 acoes priorizadas geradas"
    - "[ ] Compliance check contra ficha do cliente"

  output_example: |
    ## Diagnostico: [Cliente] — [Sintoma]

    **Dados:** CPA +40% | CTR caindo | Frequencia 3.2

    ### Arvore de Diagnostico
    - CHECK 0 (Tracking): Confirmar
    - CHECK 1 (Fadiga): PROVAVEL — freq 3.2, CTR caindo
    - CHECK 2 (Publico): Possivel — verificar tamanho

    ### Diagnostico: FADIGA DE CRIATIVO (confianca 85%)

    ### Acoes
    1. URGENTE: Criar 3-5 novos criativos com angulos diferentes
    2. IMPORTANTE: Expandir publico se < 100k
    3. MONITORAR: Acompanhar CPA por 72h apos troca

    **Por que:** Frequencia 3.2 indica que o publico ja viu seus anuncios
    muitas vezes. Cada exposicao adicional diminui o impacto.

  completion_criteria:
    - "Causa-raiz identificada com nivel de confianca"
    - "3 acoes priorizadas por urgencia"
    - "Explicacao do por que (didatica)"

  veto_conditions:
    - "SE sem dados de metricas → NAO diagnosticar, pedir dados"
    - "SE queda abrupta (0 conversoes) → CHECK 0 (tracking) OBRIGATORIO antes de qualquer outro"
    - "SE campanha < 7 dias → NAO diagnosticar como problema, e fase de aprendizado"
```
