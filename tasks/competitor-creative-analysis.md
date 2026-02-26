# competitor-creative-analysis

Task atomica para analise competitiva de criativos via Ad Library.

```yaml
task:
  name: competitor-creative-analysis
  executor: ads-creative-analyst
  elicit: true
  estimated_time: "10-15 min"

  description: |
    Analisa criativos de concorrentes via Meta Ad Library e/ou Google Ads Transparency
    para identificar padroes de angulos, formatos e hooks do segmento.
    Extrai angulos exploraveis (onde concorrentes NAO estao) e gera insights acionaveis.
    NUNCA copia criativos — extrai PADROES.

  inputs:
    required:
      - "Cliente (nome ou slug para carregar ficha de contexto)"
      - "Dados dos anuncios concorrentes (screenshots, descricoes ou links da Ad Library)"
    optional:
      - "Nomes de concorrentes especificos para pesquisar"
      - "Segmento para pesquisa ampla"
      - "Plataforma (Meta Ad Library, Google Ads Transparency)"

  elicitation:
    - question: "Qual cliente? (carrego a ficha de contexto para comparar posicionamento)"
      type: "freeform"
      required: true
    - question: "Cole os dados dos anuncios concorrentes: screenshots, descricoes ou links da Ad Library. Pode colar varios."
      type: "freeform"
      required: true
    - question: "Quer focar em concorrentes especificos ou analise ampla do segmento?"
      type: "select"
      options: ["Concorrentes especificos (me diga quais)", "Analise ampla do segmento", "Ambos"]
      required: false

  steps:
    - step: 1
      name: "Carregar Contexto"
      action: "Carregar ficha do cliente de data/clients/{slug}.yaml"
      extract:
        - "client.segmento (para benchmark do setor)"
        - "client.publico_alvo (para avaliar relevancia)"
        - "client.aprendizados (o que ja sabemos que funciona)"
        - "client.restricoes (o que NAO podemos fazer)"

    - step: 2
      name: "Mapear Anuncios Concorrentes"
      action: "Catalogar cada anuncio com framework padronizado"
      per_ad_analysis:
        - "Anunciante: nome"
        - "Formato: video / estatico / carrossel / UGC"
        - "Angulo: dor / desejo / prova / educacao / urgencia / comparacao"
        - "Hook: tipo + descricao"
        - "CTA: direto / indireto + texto"
        - "Prova social: tipo usado"
        - "Tempo ativo: quanto tempo o anuncio esta rodando (longevidade = signal de performance)"

    - step: 3
      name: "Identificar Padroes do Segmento"
      action: "Agrupar analises por dimensao para identificar padroes"
      analysis:
        angulos: "Quais angulos SAO mais usados no segmento?"
        formatos: "Quais formatos predominam?"
        hooks: "Quais tipos de hook aparecem mais?"
        provas: "Que tipo de prova social e comum?"
        ctas: "Quais CTAs predominam?"
        longevidade: "Quais anuncios estao ativos ha mais tempo? (signal de winner)"

    - step: 4
      name: "Encontrar Gaps e Oportunidades"
      action: "Identificar angulos AUSENTES — onde ninguem esta"
      rules: |
        Angulos que TODOS usam = saturado (difícil se diferenciar)
        Angulos que NINGUEM usa = oportunidade (testar como conceito novo)
        Anuncios com alta longevidade = signal de winner (analisar por que)
        Formato dominante = baseline (ter, mas nao depender so dele)
        Formato ausente = oportunidade de formato

    - step: 5
      name: "Gerar Insights Acionaveis"
      action: "Transformar analise em recomendacoes para o cliente"
      format: |
        3-5 angulos recomendados com justificativa:
        - Angulos de oportunidade (gap do mercado)
        - Angulos validados (longevidade alta em concorrente)
        - Angulos a evitar (saturados, todo mundo faz igual)

    - step: 6
      name: "Compliance Filter"
      action: "Filtrar recomendacoes que violam restricoes do cliente"
      checkpoint:
        veto: "SE angulo recomendado viola restricoes do cliente → REMOVER da lista"

  output_example: |
    ## Analise Competitiva: [Cliente] — Segmento [X]

    **Ficha:** [segmento] | Concorrentes analisados: X
    **Fonte:** Meta Ad Library / Google Ads Transparency

    ### Mapa de Anuncios Analisados

    | # | Anunciante | Formato | Angulo | Hook | Longevidade |
    |---|-----------|---------|--------|------|-------------|
    | 1 | [nome] | Video UGC | Dor | Pain call-out | 45 dias (signal) |
    | 2 | [nome] | Estatico | Prova | Numeros | 12 dias |
    | 3 | [nome] | Carrossel | Educacao | Curiosidade | 30 dias |
    | ... | | | | | |

    ### Padroes do Segmento

    | Dimensao | Dominante | Frequencia |
    |----------|-----------|------------|
    | Angulo mais usado | [X] | X de Y anuncios |
    | Formato dominante | [X] | X% |
    | Hook mais comum | [X] | X% |
    | Prova social | [X] | X% |

    ### Gaps e Oportunidades

    | Tipo | Angulo/Formato | Por que e oportunidade |
    |------|---------------|----------------------|
    | GAP | [angulo ausente] | Nenhum concorrente explora — diferenciacao |
    | GAP | [formato ausente] | Segmento todo em [X], ninguem testa [Y] |
    | VALIDADO | [angulo com longevidade] | Concorrente roda ha X dias — signal de winner |

    ### Recomendacoes para [Cliente]

    **Angulos a TESTAR (oportunidade):**
    1. [angulo] — [justificativa baseada em gap]
    2. [angulo] — [justificativa]

    **Angulos VALIDADOS (concorrencia prova que funciona):**
    3. [angulo] — [concorrente X roda ha Y dias, adaptar execucao]

    **Angulos a EVITAR (saturados):**
    - [angulo] — todo mundo usa, dificil diferenciar

    ### Compliance Check
    - [x] Nenhuma recomendacao viola restricoes do cliente
    - [x] Angulos respeitam restricoes legais
    - [x] Nenhuma copia direta de criativo concorrente

    ### Proximo Passo
    → Usar angulos recomendados como input da task create-creative-brief

  action_items:
    - "[ ] Ficha do cliente carregada"
    - "[ ] Anuncios concorrentes catalogados com framework padronizado"
    - "[ ] Padroes do segmento identificados por dimensao"
    - "[ ] Gaps e oportunidades mapeados"
    - "[ ] 3-5 angulos recomendados com justificativa"
    - "[ ] Compliance check contra ficha do cliente"

  completion_criteria:
    - "Anuncios concorrentes catalogados com framework padronizado"
    - "Padroes do segmento identificados"
    - "Gaps e oportunidades mapeados"
    - "3-5 angulos recomendados com justificativa"
    - "Compliance check com ficha do cliente"

  veto_conditions:
    - "SE analise recomenda copiar criativo concorrente → VETO — extrair PADRAO, nao criativo"
    - "SE angulo recomendado viola restricoes do cliente → REMOVER da lista"
    - "SE sem dados de concorrentes (nenhum anuncio fornecido) → NAO inventar, pedir dados"
    - "SE analise tem < 5 anuncios → ALERTAR que amostra e pequena para identificar padroes"
```
