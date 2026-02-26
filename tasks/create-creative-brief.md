# create-creative-brief

Task atomica para geracao de briefs criativos de performance.

```yaml
task:
  name: create-creative-brief
  executor: ads-creative-analyst
  elicit: true
  estimated_time: "10-15 min"

  description: |
    Gera briefs criativos estruturados com angulos, hooks, CTAs e referencias.
    Baseado em padroes de winners existentes e pipeline ratio (70% iteracao + 30% novo).
    Entrega brief acionavel para producao (design/video), NAO o criativo em si.

  inputs:
    required:
      - "Cliente (nome ou slug para carregar ficha de contexto)"
      - "Quantidade de criativos desejada"
      - "Objetivo (top-of-funnel, retargeting, lancamento, substituicao de fadigado)"
    optional:
      - "Winners anteriores (dados de criativos que funcionaram)"
      - "Angulos ja testados (para nao repetir)"
      - "Assets disponiveis (fotos, videos, UGC, depoimentos)"
      - "Resultado da task analyze-ad-creatives (classificacao + deconstrucao)"

  elicitation:
    - question: "Qual cliente? (carrego a ficha de contexto)"
      type: "freeform"
      required: true
    - question: "Quantos criativos precisa? E qual o objetivo? (substituir fadigado, novo lancamento, escalar)"
      type: "freeform"
      required: true
    - question: "Tem dados de criativos winners anteriores? (angulo, formato, metricas)"
      type: "freeform"
      required: false
    - question: "Quais assets tem disponiveis? (fotos, videos, UGC creators, depoimentos de clientes)"
      type: "freeform"
      required: false

  steps:
    - step: 1
      name: "Carregar Contexto"
      action: "Carregar ficha do cliente de data/clients/{slug}.yaml"
      extract:
        - "client.produtos_ativos (destacar featured)"
        - "client.publico_alvo (principal e secundario)"
        - "client.regras.comunicacao (tom, palavras proibidas)"
        - "client.restricoes_legais (ANVISA, CRM, etc.)"
        - "client.aprendizados (o que funciona/nao funciona)"
        - "client.restricoes_produto (o que NAO pode)"
      checkpoint:
        veto: "SE ficha nao existe → alertar e pedir dados minimos"

    - step: 2
      name: "Definir Pipeline Mix"
      action: "Calcular ratio de iteracao vs conceito novo"
      rules: |
        SE tem winners documentados:
          70% iteracoes de winners (mesmo angulo, nova execucao)
          30% conceitos novos (angulo inedito)
        SE nao tem winners (conta nova ou sem historico):
          100% conceitos novos com 3+ angulos diferentes
          Formatos variados (video, estatico, carrossel)
        SEMPRE: pelo menos 2 angulos diferentes no lote

    - step: 3
      name: "Selecionar Angulos"
      action: "Definir angulos para cada criativo do lote"
      angle_types:
        - "DOR: Problema que o produto resolve"
        - "DESEJO: Resultado que o produto entrega"
        - "PROVA: Evidencia de que funciona (social proof)"
        - "EDUCACAO: Ensinar algo util relacionado ao produto"
        - "CURIOSIDADE: Gerar interesse sem revelar tudo"
        - "COMPARACAO: Antes/depois ou alternativa inferior"
        - "URGENCIA: Motivo para agir agora"
      rules: |
        NAO repetir angulo ja testado sem resultado (se informado)
        PRIORIZAR angulos do aprendizado do cliente (client.aprendizados.funciona)
        EVITAR angulos listados em client.aprendizados.nao_funciona

    - step: 4
      name: "Montar Brief por Criativo"
      action: "Gerar brief estruturado para cada criativo"
      brief_structure:
        - "Formato: Video UGC / Video produzido / Estatico / Carrossel"
        - "Angulo: Qual dor/desejo/prova"
        - "Hook (3s): Descricao do gancho visual + textual"
        - "Corpo: O que acontece depois do hook"
        - "CTA: Chamada para acao + link"
        - "Prova social: Que tipo usar (depoimento, numeros, visual)"
        - "Referencia: Winner que inspira (se iteracao) ou referencia externa"
        - "Compliance: Restricoes aplicaveis deste cliente"

    - step: 5
      name: "Compliance Check"
      action: "Validar cada brief contra restricoes do cliente"
      checks:
        - "Produtos mencionados estao em client.produtos_ativos?"
        - "Nenhuma palavra de client.regras.comunicacao.palavras_proibidas?"
        - "Restricoes legais respeitadas (ANVISA, CRM, etc.)?"
        - "Restricoes de produto respeitadas?"
        - "Tom de comunicacao alinhado com client.regras.comunicacao.tom?"
      checkpoint:
        veto: "SE qualquer check falha → reformular brief antes de entregar"

    - step: 6
      name: "Resumo do Pipeline"
      action: "Apresentar visao geral do lote com ratio e diversidade"
      summary: |
        Total de criativos: X
        Iteracoes de winners: X (Y%)
        Conceitos novos: X (Y%)
        Angulos usados: [lista]
        Formatos usados: [lista]
        Compliance: OK / PENDENTE

  output_example: |
    ## Brief Criativo: [Cliente] — [X] Novos Criativos

    **Ficha:** [segmento] | Publico: [descricao]
    **Pipeline mix:** X iteracoes (70%) + X novos (30%)
    **Objetivo:** [substituir fadigado / escalar / lancamento]

    ---

    ### Criativo 1: [Formato] — "[Nome do conceito]"

    | Campo | Detalhe |
    |-------|---------|
    | Formato | [Video UGC / Estatico / Carrossel] |
    | Angulo | [Dor / Desejo / Prova / Educacao] — [descricao] |
    | Hook (3s) | [descricao visual + textual do gancho] |
    | Corpo | [o que acontece apos o hook] |
    | CTA | [chamada para acao + tipo de link] |
    | Prova social | [depoimento / numeros / visual] |
    | Tipo | [Iteracao de winner #X / Conceito novo] |
    | Referencia | [Winner que inspira ou referencia externa] |
    | Compliance | [restricoes aplicaveis listadas] |

    ### Criativo 2: ...
    ### Criativo 3: ...

    ---

    ### Checklist de Compliance

    - [ ] Nenhum produto fora do catalogo ativo
    - [ ] Nenhuma palavra proibida
    - [ ] Restricoes legais respeitadas ([listar])
    - [ ] Tom de comunicacao alinhado
    - [ ] Restricoes de produto respeitadas ([listar])

    ### Resumo do Pipeline

    | Metrica | Valor |
    |---------|-------|
    | Total criativos | X |
    | Iteracoes | X (Y%) |
    | Conceitos novos | X (Y%) |
    | Angulos distintos | X |
    | Formatos distintos | X |

    ### Proximo Passo
    → Produzir criativos e subir via meta-ads-expert / google-ads-expert

  completion_criteria:
    - "Brief estruturado por criativo (formato, angulo, hook, corpo, CTA, prova, compliance)"
    - "Pipeline mix definido (iteracao vs novo)"
    - "Compliance check PASS em todos os briefs"
    - "Pelo menos 2 angulos diferentes no lote"
    - "Proximo passo com handoff claro"

  veto_conditions:
    - "SE sem dados do cliente (nem ficha nem dados manuais) → NAO gerar brief generico, pedir contexto"
    - "SE brief menciona produto descontinuado ou fora do catalogo → BLOQUEAR"
    - "SE brief usa palavra proibida pelo cliente → REFORMULAR antes de entregar"
    - "SE brief viola restricao legal (ANVISA, CRM) → BLOQUEAR imediatamente"
    - "SE todos os criativos do lote usam mesmo angulo → VETO — exigir diversidade (min 2 angulos)"
    - "SE brief nao tem hook de 3 segundos definido (video) → VETO — hook e obrigatorio"
```
