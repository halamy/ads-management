# daily-health-check

Task atomica para checagem diaria de saude de todas as contas.

```yaml
task:
  name: daily-health-check
  executor: ads-optimizer
  elicit: true
  estimated_time: "10-15 min para 9 contas"

  description: |
    Executa o checklist diario de otimizacao para todas as contas ativas.
    O usuario cola os dados e recebe acoes priorizadas por conta.

  inputs:
    required:
      - "Dados por conta: nome, CPA/ROAS atual, CPA/ROAS meta, conversoes ontem, frequencia (Meta)"
    optional:
      - "Plataforma (Meta/Google) por conta"
      - "Observacoes (algo mudou ontem?)"

  elicitation:
    - question: "Cole os dados de hoje das suas contas. Formato sugerido:\n\nConta | Plataforma | CPA/ROAS atual | Meta | Conversoes ontem | Frequencia"
      type: "freeform"
      required: true

  steps:
    - step: 1
      action: "Para cada conta, carregar ficha do cliente de data/clients/{slug}.yaml"
      checkpoint:
        veto: "SE ficha nao existe para conta → marcar [SEM FICHA] e alertar"

    - step: 2
      action: "Classificar cada conta: ESCALAR / MANTER / OTIMIZAR / PAUSAR / FADIGA"
      criteria: |
        ESCALAR: CPA <= meta E conversoes >= 5/dia E estavel 5+ dias
        MANTER: CPA <= meta E conversoes < 5/dia
        OTIMIZAR: CPA entre meta e 1.5x meta
        PAUSAR: CPA > 1.5x meta OU ROAS < 50% da meta
        FADIGA: Frequencia > 3.0 E CTR caindo
      checkpoint:
        veto: "SE conta sem dados suficientes → marcar REVISAR, nao classificar"

    - step: 3
      action: "Ordenar por urgencia: PAUSAR > FADIGA > OTIMIZAR > ESCALAR > MANTER"

    - step: 4
      action: "Gerar tabela com status e acao por conta"

    - step: 5
      action: "Compliance check — validar acoes contra restricoes da ficha de cada cliente"
      checkpoint:
        veto: "SE acao viola client.restricoes_produto ou client.budget.limite_maximo → BLOQUEAR"

    - step: 6
      action: "Listar acoes priorizadas (urgente > importante > rotina)"

  action_items:
    - "[ ] Fichas de clientes carregadas para todas as contas"
    - "[ ] Todas as contas classificadas (ESCALAR/MANTER/OTIMIZAR/PAUSAR/FADIGA)"
    - "[ ] Contas ordenadas por urgencia"
    - "[ ] Tabela gerada com status e acao"
    - "[ ] Compliance check contra ficha de cada cliente"
    - "[ ] Acoes priorizadas listadas"

  output_example: |
    ## Checklist Diario — [DATA]

    | Conta | Status | Metrica | Acao |
    |-------|--------|---------|------|
    | Academia | PAUSAR | CPA R$22 (meta R$15) | Pausar criativos, trocar |
    | Restaurante | OTIMIZAR | CPA R$12 (meta R$10) | Testar novo criativo |
    | Loja | ESCALAR | ROAS 4.2x (meta 3x) | Aumentar budget 20% |
    | Dentista | MANTER | CPA R$18 (meta R$20) | Nao mexer |

    Acoes urgentes: 1 | Oportunidades: 1 | Estaveis: 2

  completion_criteria:
    - "Todas as contas classificadas"
    - "Acoes priorizadas por urgencia"
    - "Nenhuma conta sem status definido"

  veto_conditions:
    - "SE usuario nao forneceu dados → NAO classificar, pedir dados"
    - "SE dados incompletos (sem meta) → NAO decidir, pedir meta"
```
