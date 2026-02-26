# analyze-ad-creatives

Task atomica para analise de criativos de performance.

```yaml
task:
  name: analyze-ad-creatives
  executor: ads-creative-analyst
  elicit: true
  estimated_time: "10-15 min"

  description: |
    Recebe metricas de criativos ativos, classifica por ciclo de vida
    (WINNER/FADIGADO/MORTO/TESTE), deconstrui winners e recomenda
    acoes de iteracao, pausa e substituicao.

  inputs:
    required:
      - "Cliente (nome ou slug para carregar ficha de contexto)"
      - "Metricas por criativo: CTR, CPA ou ROAS, frequencia, impressoes"
      - "Descricao do criativo (formato, angulo, hook)"
    optional:
      - "CPA/ROAS meta do cliente"
      - "Tempo que cada criativo esta rodando"
      - "Dados de watch time (se video)"
      - "Thumb-stop rate (se disponivel)"

  elicitation:
    - question: "Qual cliente? (carrego a ficha de contexto)"
      type: "freeform"
      required: true
    - question: "Cole os dados dos criativos: nome/descricao, formato, CTR, CPA/ROAS, frequencia, impressoes"
      type: "freeform"
      required: true
    - question: "Qual a meta de CPA ou ROAS desse cliente?"
      type: "freeform"
      required: true
    - question: "Tem dados de watch time ou thumb-stop rate? (opcional)"
      type: "freeform"
      required: false

  steps:
    - step: 1
      name: "Carregar Contexto"
      action: "Carregar ficha do cliente de data/clients/{slug}.yaml"
      checkpoint:
        veto: "SE ficha nao existe → alertar e pedir dados minimos (segmento, publico, restricoes)"

    - step: 2
      name: "Classificar Criativos"
      action: "Aplicar framework de ciclo de vida a cada criativo"
      rules: |
        TESTE: < 1000 impressoes → NAO julgar, aguardar dados
        WINNER: Top 20% por CPA/ROAS + 1000+ imp + 3+ dias estavel
        FADIGADO: Freq > 3.0 E CTR caindo > 20% vs pico
        MORTO: CPA > 1.5x meta OU CTR caiu > 40% vs pico OU freq > 4.0
        OTIMIZAR: CPA entre meta e 1.5x meta (nao e winner nem morto)

    - step: 3
      name: "Deconstruir Winners"
      action: "Aplicar framework de deconstrucao ao(s) criativo(s) winner"
      analysis:
        - "ANGULO: Qual dor/desejo o criativo ataca?"
        - "FORMATO: Estatico, video, carrossel, UGC?"
        - "HOOK: Tipo de hook (pattern interrupt, curiosity gap, pain call-out, etc.)"
        - "PROVA: Tipo de prova social (depoimento, numeros, visual)"
        - "CTA: Direto ou indireto?"
        - "POR QUE FUNCIONA: Padrao replicavel identificado"

    - step: 4
      name: "Creative Scoring"
      action: "Pontuar criativo(s) winner com framework de scoring"
      scoring: |
        Hook Power (30%): 1-5
        Hold/Corpo (25%): 1-5
        CTA Clarity (20%): 1-5
        Relevancia (15%): 1-5
        Compliance (10%): 1-5
        TOTAL: Weighted average
        >= 4.0 = FORTE | 3.0-3.9 = MEDIO | < 3.0 = FRACO

    - step: 5
      name: "Recomendar Acoes"
      action: "Gerar acoes priorizadas por criativo"
      format: |
        WINNERS: Escalar + criar iteracoes (mesmo angulo, nova execucao)
        FADIGADOS: Preparar substituicao, nao pausar se CPA ainda OK
        MORTOS: Pausar imediatamente, arquivar angulo e aprendizados
        TESTES: Aguardar dados minimos (1000 imp)
        OTIMIZAR: Testar variacao de hook ou CTA

    - step: 6
      name: "Compliance Check"
      action: "Verificar se recomendacoes respeitam restricoes do cliente"
      checkpoint:
        veto: "SE recomendacao viola client.restricoes_produto ou client.regras.comunicacao → BLOQUEAR"

  output_example: |
    ## Analise de Criativos: [Cliente]

    **Ficha:** [segmento] | Publico: [descricao] | CPA meta: R$X

    ### Classificacao

    | # | Criativo | CTR | CPA | Freq | Status | Acao |
    |---|----------|-----|-----|------|--------|------|
    | 1 | [descricao] | X% | R$X | X.X | WINNER | Escalar + 3 iteracoes |
    | 2 | [descricao] | X% | R$X | X.X | FADIGADO | Preparar substituicao |
    | 3 | [descricao] | X% | R$X | X.X | MORTO | Pausar |
    | 4 | [descricao] | X% | R$X | X.X | TESTE | Aguardar 1000 imp |

    ### Deconstrucao do Winner (#1)

    | Dimensao | Analise |
    |----------|---------|
    | Angulo | [dor/desejo atacado] |
    | Hook | [tipo de hook + descricao] |
    | Formato | [video/estatico/carrossel/UGC] |
    | Prova | [tipo de prova social] |
    | Por que funciona | [padrao replicavel] |

    ### Scoring (#1)

    | Dimensao | Score |
    |----------|-------|
    | Hook Power | X/5 |
    | Hold/Corpo | X/5 |
    | CTA Clarity | X/5 |
    | Relevancia | X/5 |
    | Compliance | X/5 |
    | **TOTAL** | **X.X/5** |

    ### Acoes Priorizadas

    1. **URGENTE:** [acao com criativo morto/fadigado]
    2. **IMPORTANTE:** [iteracoes sobre winner]
    3. **PROXIMO:** [pipeline de novos criativos]

    ### Compliance Check
    - [x] Restricoes do cliente respeitadas
    - [x] Sem claims proibidos
    - [x] Produtos mencionados sao ativos

  action_items:
    - "[ ] Ficha do cliente carregada"
    - "[ ] Todos os criativos classificados (WINNER/FADIGADO/MORTO/TESTE/OTIMIZAR)"
    - "[ ] Winners deconstruidos com padrao replicavel"
    - "[ ] Creative scoring aplicado aos winners"
    - "[ ] Acoes priorizadas por criativo"
    - "[ ] Compliance check contra ficha do cliente"

  completion_criteria:
    - "Todos os criativos classificados (WINNER/FADIGADO/MORTO/TESTE/OTIMIZAR)"
    - "Winners deconstruidos com padrao replicavel documentado"
    - "Acoes priorizadas por criativo"
    - "Compliance check com ficha do cliente"

  veto_conditions:
    - "SE criativo tem < 1000 impressoes → NAO classificar como winner ou morto, rotular como TESTE"
    - "SE sem metricas por criativo → NAO analisar, pedir dados"
    - "SE recomendacao viola restricoes do cliente → BLOQUEAR e reformular"
    - "SE criativo tem < 3 dias → NAO classificar como fadigado, pode ser aprendizado"
    - "SE avaliacao baseada em estetica sem dados → VETO — dados > opiniao"
```
