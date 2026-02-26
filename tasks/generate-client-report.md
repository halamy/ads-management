# generate-client-report

Task atomica para geracao de relatorio estruturado para cliente.

```yaml
task:
  name: generate-client-report
  executor: ads-optimizer
  elicit: true
  estimated_time: "10-15 min"

  description: |
    Gera relatorio mensal ou semanal estruturado para enviar ao cliente.
    Substitui o print de tela no WhatsApp por um report profissional.

  inputs:
    required:
      - "Nome do cliente"
      - "Periodo (semanal ou mensal)"
      - "Dados de performance (investimento, conversoes, CPA/ROAS, CTR)"
    optional:
      - "Dados do periodo anterior (para comparativo)"
      - "Objetivos acordados com o cliente"
      - "Observacoes ou contexto relevante"

  elicitation:
    - question: "Nome do cliente e periodo do relatorio?"
      type: "freeform"
      required: true
    - question: "Cole os dados de performance: investimento, conversoes, CPA/ROAS, CTR por campanha"
      type: "freeform"
      required: true
    - question: "Tem dados do periodo anterior para comparativo?"
      type: "freeform"
      required: false

  steps:
    - step: 1
      action: "Carregar ficha do cliente de data/clients/{slug}.yaml"
      checkpoint:
        veto: "SE ficha nao existe → alertar e pedir dados minimos do cliente"

    - step: 2
      action: "Organizar dados em tabela por campanha"
      checkpoint:
        veto: "SE dados insuficientes para preencher tabela → pedir dados faltantes"

    - step: 3
      action: "Calcular variacao vs periodo anterior (se disponivel)"

    - step: 4
      action: "Identificar destaques (melhor campanha, melhor criativo)"

    - step: 5
      action: "Identificar pontos de atencao"

    - step: 6
      action: "Gerar 3 recomendacoes para proximo periodo"

    - step: 7
      action: "Compliance check — recomendacoes NAO violam restricoes do cliente"
      checkpoint:
        veto: "SE recomendacao menciona produto descontinuado ou viola restricoes → REFORMULAR"

  action_items:
    - "[ ] Ficha do cliente carregada"
    - "[ ] Dados organizados em tabela por campanha"
    - "[ ] Variacao vs periodo anterior calculada"
    - "[ ] Destaques identificados com justificativa"
    - "[ ] Pontos de atencao listados"
    - "[ ] 3 recomendacoes concretas geradas"
    - "[ ] Compliance check contra ficha do cliente"

  output_format: |
    ## Relatorio [Semanal/Mensal]: [Cliente] — [Periodo]

    ### Resumo
    Investimento: R$X | Resultados: X [leads/vendas] | CPA/ROAS: R$X / Xx

    ### Performance por Campanha
    | Campanha | Invest. | Resultado | CPA/ROAS | CTR | vs Anterior |

    ### Destaques
    - (o que funcionou bem e por que)

    ### Pontos de Atencao
    - (o que precisa de ajuste)

    ### Recomendacoes
    1. (acao baseada em dados)
    2. (acao baseada em dados)
    3. (acao baseada em dados)

  completion_criteria:
    - "Tabela de performance por campanha"
    - "Comparativo vs periodo anterior (se dados disponiveis)"
    - "3 recomendacoes concretas"
    - "Linguagem acessivel para cliente (sem jargao tecnico excessivo)"

  veto_conditions:
    - "SE sem dados de performance → NAO gerar relatorio, pedir dados"
    - "SE dados de apenas 1 dia → NAO gerar relatorio mensal/semanal"
```
