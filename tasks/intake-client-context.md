# intake-client-context

Task para gerar ficha de cliente (client.yaml) semi-automaticamente.

```yaml
task:
  name: intake-client-context
  executor: ads-chief
  elicit: true
  estimated_time: "10-15 min por cliente"

  description: |
    Gera a ficha de contexto do cliente em 2 etapas:
    1. AUTOMATICO: Extrair dados do site do cliente (produtos, precos, publico aparente)
    2. GESTOR COMPLETA: Perguntas sobre budget, metas, restricoes, aprendizados

    Output: data/clients/{slug}.yaml preenchido e pronto para uso pelo squad.

  trigger: |
    - Novo cliente (durante wf-client-onboarding fase 1)
    - Cliente existente sem ficha (gestor menciona conta sem .yaml)
    - Comando: *ficha {nome-do-cliente}
    - Comando: *intake {url-do-site}

  # ═══════════════════════════════════════════════════════════════════════════
  # ETAPA 1: EXTRACAO AUTOMATICA (do site)
  # ═══════════════════════════════════════════════════════════════════════════

  etapa_1_automatica:
    name: "Raspar dados do site"
    input: "URL do site do cliente"
    action: |
      Acessar o site do cliente e extrair:
      1. PRODUTOS: nomes, categorias, precos, formatos (capsula, gota, etc.)
      2. PUBLICO APARENTE: tom do site, linguagem, personas visadas
      3. COMUNICACAO: tom (formal/informal), cores, estilo visual
      4. ASSETS: tem blog? Tem depoimentos? Tem LP especifica?
      5. RESTRICOES LEGAIS APARENTES: disclaimers, selos (ANVISA, CRM)

    extraction_rules:
      produtos:
        - "Listar TODOS os produtos visiveis no site/loja"
        - "Extrair: nome, preco, categoria, formato (se aplicavel)"
        - "Identificar produto carro-chefe (destaque, primeiro listado)"
        - "Identificar se tem produto por assinatura/recorrencia"
      publico:
        - "Analisar linguagem: fala com maes? jovens? idosos?"
        - "Analisar visual: clean? colorido? premium? popular?"
        - "Analisar sessao 'sobre nos' se existir"
      comunicacao:
        - "Tom: formal, informal, tecnico, divertido, maternal"
        - "Palavras recorrentes no site"
        - "Disclaimers legais presentes"

    output: "Rascunho da ficha com campos automaticos preenchidos"

  # ═══════════════════════════════════════════════════════════════════════════
  # ETAPA 2: GESTOR COMPLETA (perguntas)
  # ═══════════════════════════════════════════════════════════════════════════

  etapa_2_gestor:
    name: "Gestor completa dados que so ele sabe"
    elicit: true
    presentation: |
      Apresentar ao gestor:
      "Consegui extrair isso do site: [resumo]. Agora preciso que voce complete:"

    questions:
      - question: "Budget mensal total pra essa conta? E split entre plataformas?"
        field: "client.budget"
        type: "freeform"
        required: true

      - question: "CPA meta e/ou ROAS meta? Quantas conversoes/mes espera?"
        field: "client.metas"
        type: "freeform"
        required: true

      - question: "Tem algo que o cliente NAO vende ou NAO pode prometer? (restricoes de produto)"
        field: "client.restricoes_produto"
        type: "freeform"
        required: true
        hint: "Ex: nao tem goma, nao faz frete gratis, nao vende pra fora do BR"

      - question: "Restricoes legais do segmento? (ANVISA, CRM, CRECI, etc.)"
        field: "client.regras.restricoes_legais"
        type: "freeform"
        required: false
        hint: "Se nao souber, eu deduzo pelo segmento"

      - question: "O que voce ja sabe que FUNCIONA pra esse cliente? E o que NAO funciona?"
        field: "client.aprendizados"
        type: "freeform"
        required: false
        hint: "Ex: criativo sazonal performa bem, catalogo dinamico fadiga rapido"

      - question: "Tem algo mais que o squad precisa saber sobre esse cliente?"
        field: "client.notas"
        type: "freeform"
        required: false

  # ═══════════════════════════════════════════════════════════════════════════
  # ETAPA 3: GERAR E VALIDAR
  # ═══════════════════════════════════════════════════════════════════════════

  etapa_3_gerar:
    name: "Gerar ficha e validar"
    action: |
      1. Combinar dados extraidos (etapa 1) + respostas do gestor (etapa 2)
      2. Gerar {slug}.yaml usando _template-client.yaml como base
      3. Preencher metadata (criado_em, preenchido_por)
      4. Apresentar resumo ao gestor para confirmacao

    validation:
      required_fields:
        - "client.slug"
        - "client.nome"
        - "client.segmento"
        - "client.produtos_ativos (minimo 1)"
        - "client.plataformas (minimo 1)"
        - "client.budget.mensal_total"
        - "client.metas.objetivo_principal"
        - "client.metas.cpa_meta OU client.metas.roas_meta"
      optional_but_recommended:
        - "client.restricoes_produto"
        - "client.publico.principal"
        - "client.aprendizados"
        - "client.regras.restricoes_legais"

    output_format: |
      ## Ficha do Cliente: {nome}

      **Extraido automaticamente:**
      - Produtos: {N} encontrados
      - Publico aparente: {descricao}
      - Tom: {tom}

      **Preenchido pelo gestor:**
      - Budget: {budget}
      - Metas: CPA {cpa_meta} / ROAS {roas_meta}
      - Restricoes: {N} registradas

      **Arquivo salvo em:** data/clients/{slug}.yaml

      Confirma? (S/N)

  # ═══════════════════════════════════════════════════════════════════════════
  # COMPLETION & VETO
  # ═══════════════════════════════════════════════════════════════════════════

  completion_criteria:
    - "Ficha YAML gerada em data/clients/{slug}.yaml"
    - "Campos obrigatorios preenchidos"
    - "Gestor confirmou dados"
    - "Arquivo acessivel pelos agents do squad"

  veto_conditions:
    - "SE site do cliente inacessivel → pular etapa 1, fazer tudo via perguntas"
    - "SE gestor nao sabe budget → NAO gerar ficha, pedir que confirme com cliente"
    - "SE nenhum produto identificado → NAO gerar ficha, algo esta errado"
    - "SE gestor nao confirma dados → NAO salvar, ajustar primeiro"
```
