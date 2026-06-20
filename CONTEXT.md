# Context: Signalia

## Domain Vocabulary

| Term | Definition |
|------|------------|
| Canal | Espaço criado por um analista para publicar sinais; tem preço e periodicidade de assinatura |
| Analista | Usuário que cria e gerencia um canal pago; recebe 85% do valor da assinatura |
| Investidor | Usuário que assina canais e recebe sinais de trading |
| Sinal | Publicação de trading enviada por um analista dentro do seu canal (ex: compra BTC a R$X, stop em Y) |
| Assinatura | Vínculo pago entre um investidor e um canal; gerenciada via gateway de pagamento |
| PLATFORM_FEE_PCT | Taxa da plataforma retida em cada pagamento de assinatura (15% por padrão) |
| Gateway | Provedor de pagamento integrado: Stripe (internacional), MercadoPago ou Pagar.me (Brasil) |
| Webhook | Notificação assíncrona recebida do gateway para atualizar status de pagamento/assinatura |
| MFA | Autenticação de dois fatores habilitada opcionalmente por cada usuário |
| Análise | Publicação mais elaborada do analista (diferente de sinal: tem contexto, gráfico, texto longo) |
| Portfólio | Visão consolidada das posições do investidor com base nos sinais recebidos |
| Wallet | Endereço de carteira cripto cadastrado pelo usuário para rastreamento de posições on-chain (não é custodial) |

## Key Invariants

- A plataforma nunca custodia fundos — pagamentos passam pelo gateway e são repassados ao analista menos a taxa.
- Um usuário pode ser simultaneamente analista e investidor.
- Sinais e análises são vinculados a um canal; sem assinatura ativa, o investidor não acessa o conteúdo.
- O back é a única fonte de verdade: front e mobile apenas consomem a API REST, nunca escrevem no banco diretamente.
- Toda comunicação front/mobile → back passa pelo nginx em produção (`/api` → back:3001, `/` → front:3000).
- Refresh de token JWT é automático (front e mobile): acesso expira em 1h, refresh em 7d.

## What this project is NOT

- Não é uma exchange ou corretora — não executa ordens de trading automaticamente.
- Não é um bot de sinais — os sinais são publicados manualmente pelos analistas.
- Não é custodial — não armazena chaves privadas nem move cripto dos usuários.
- O mobile não é um clone do front: tem escopo menor (leitura de sinais, notificações, auth) sem gestão de canais/pagamentos.
