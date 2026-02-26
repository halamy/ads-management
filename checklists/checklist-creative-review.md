# Checklist de Review de Criativos

Squad: ads-management | Executor: ads-creative-analyst
Frequencia: A cada analise de criativos ou antes de subir novos | Tempo: 10-15 min
Fonte: [SOURCE: Dara Denney — Hook-Hold-CTA] + [SOURCE: Barry Hott — Winner Patterns] + [SOURCE: Nick Shackelford — Creative Testing]

## 1. Dados Minimos (Gate de Entrada)

- [ ] Criativo tem 1000+ impressoes? (SE NAO → rotular como TESTE, nao julgar)
- [ ] Criativo tem 3+ dias rodando? (SE NAO → aguardar antes de classificar)
- [ ] Metricas disponiveis: CTR, CPA ou ROAS, frequencia, impressoes
- [ ] Ficha do cliente carregada (restricoes, produtos, comunicacao)

**VETO: Se gate de entrada nao passa → NAO prosseguir com review**

## 2. Analise de Hook (3 Segundos)

- [ ] Video tem hook visual nos primeiros 3 segundos?
- [ ] Hook textual presente (overlay ou legenda)?
- [ ] Hook e do tipo identificavel? (pattern interrupt / curiosity gap / pain call-out / social proof / before-after / direct benefit)
- [ ] Thumb-stop: CTR esta acima do benchmark do segmento?
- [ ] Hook esta alinhado com o angulo do criativo?

## 3. Analise de Corpo (Hold)

- [ ] Apos o hook, o criativo mantem atencao? (watch time ou engagement)
- [ ] Entrega valor ou informacao relevante?
- [ ] Conecta com dor/desejo do publico-alvo?
- [ ] Mecanismo do produto e explicado (como resolve)?
- [ ] Transicao hook → corpo e fluida (nao perde o espectador)?

## 4. Analise de CTA

- [ ] CTA presente e claro?
- [ ] CTA diz O QUE fazer (clicar, comprar, falar no WhatsApp)?
- [ ] CTA cria urgencia ou motivo para agir AGORA?
- [ ] CTA esta alinhado com o objetivo da campanha?
- [ ] Link/destino correto e funcionando?

## 5. Classificacao por Ciclo de Vida

- [ ] WINNER: Top 20% por CPA/ROAS + 1000+ imp + 3+ dias estavel → escalar + iterar
- [ ] FADIGADO: Freq > 3.0 E CTR caindo > 20% vs pico → preparar substituicao
- [ ] MORTO: CPA > 1.5x meta OU CTR caiu > 40% OU freq > 4.0 → pausar
- [ ] TESTE: < 1000 imp → aguardar dados
- [ ] OTIMIZAR: CPA entre meta e 1.5x meta → testar variacao

## 6. Deconstrucao de Winner (se aplicavel)

- [ ] Angulo identificado (qual dor/desejo ataca?)
- [ ] Formato documentado (video/estatico/carrossel/UGC)
- [ ] Tipo de hook documentado
- [ ] Tipo de prova social documentado
- [ ] Padrao replicavel descrito ("funciona porque...")
- [ ] Scoring aplicado (Hook/Hold/CTA/Relevancia/Compliance)

## 7. Compliance com Ficha do Cliente

- [ ] Produtos mencionados estao em `client.produtos_ativos`?
- [ ] Nenhum produto descontinuado referenciado?
- [ ] Nenhuma palavra de `client.regras.comunicacao.palavras_proibidas`?
- [ ] Restricoes de produto respeitadas (`client.restricoes_produto`)?
- [ ] Restricoes legais respeitadas (`client.restricoes_legais`)?
- [ ] Tom de comunicacao alinhado com `client.regras.comunicacao.tom`?

## 8. Pipeline e Proximo Passo

- [ ] Winners documentados para futuras iteracoes?
- [ ] Fadigados tem substitutos planejados?
- [ ] Mortos pausados e angulo arquivado?
- [ ] Pipeline ratio definido? (70% iteracao + 30% novo)
- [ ] Proximo passo claro? (brief → producao → teste → validacao)

## Veto Conditions

- NAO classificar criativo com < 1000 impressoes como winner ou morto
- NAO avaliar criativo por estetica — sempre por dados e framework
- NAO copiar criativo de concorrente — extrair padrao
- NAO aprovar brief que viola restricoes do cliente
- NAO testar mudando angulo E formato ao mesmo tempo
- NAO julgar criativo com < 3 dias como fadigado
