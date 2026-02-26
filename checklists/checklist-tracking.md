# Checklist de Verificacao de Tracking

Squad: ads-management | Executor: meta-ads-expert / google-ads-expert
Frequencia: Onboarding + semanal (verificacao) | Tempo: 15-30 min
Fonte: [SOURCE: Mike Rhodes — AdAID Check 0] + [SOURCE: Ed Leake — God Tier Tracking SOP]

## Meta Ads Tracking

### Pixel
- [ ] Pixel instalado no site (verificar com Meta Pixel Helper)
- [ ] PageView disparando em todas as paginas
- [ ] Evento de conversao principal disparando (Purchase/Lead/etc.)
- [ ] Parametros de evento corretos (value, currency, content_id)
- [ ] Dominio verificado no Business Manager

### CAPI (Conversions API)
- [ ] CAPI configurada (server-side)
- [ ] Event Match Quality > 6.0 (ideal > 7.0)
- [ ] Deduplicacao ativa (event_id configurado)
- [ ] Eventos CAPI + Pixel batendo (sem duplicacao)

### Atribuicao
- [ ] Janela de atribuicao configurada (7d click, 1d view padrao)
- [ ] Conversoes na plataforma vs analytics: discrepancia < 20%?

## Google Ads Tracking

### Google Tag
- [ ] Google Tag instalado (verificar com Tag Assistant)
- [ ] Conversao principal configurada
- [ ] Enhanced Conversions ativo
- [ ] Valor de conversao passando corretamente (se ecommerce)

### Google Analytics 4
- [ ] GA4 conectado com Google Ads
- [ ] Eventos de conversao marcados em GA4
- [ ] Conversoes importadas para Google Ads

### Search Console
- [ ] Site verificado
- [ ] Dados de performance disponiveis

## Verificacao Cruzada
- [ ] Conversoes Meta vs Analytics: diferenca < 20%
- [ ] Conversoes Google vs Analytics: diferenca < 20%
- [ ] SE diferenca > 20% → investigar (tracking, atribuicao, ou real)
- [ ] Teste de conversao real realizado (preencher form / comprar)

## Troubleshooting Comum
- [ ] Pixel nao dispara → verificar instalacao, bloqueadores, consent mode
- [ ] CAPI com EMQ baixo → verificar parametros (email, phone, fbp, fbc)
- [ ] Conversoes duplicadas → verificar deduplicacao (event_id)
- [ ] Zero conversoes → verificar evento, URL, dominio

## Veto Conditions
- NAO lancar campanha com tracking nao verificado
- NAO ignorar discrepancia > 20% — pode ser dinheiro perdido
- NAO confiar apenas no pixel (CAPI obrigatoria para Meta)
- SE EMQ < 5.0 → CORRIGIR antes de escalar budget
