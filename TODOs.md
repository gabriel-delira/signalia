- [ ] PID (Detran)
- [ ] Aliexpress
	- [ ] Mochila igual a da Gabi
	- [ ] Liquido Hidrofobico
- [ ] Radiografia



Singalia:
- [ ]  Comunicar com Telegram ou wpp para enviar alerta
- [ ] Testar xp no mobile
- [ ] Adicionar aba awedash para analistas
	- [ ] Instagram
	- [x] Twitter
	- [x] Reddit
	- [x] Youtube
	- [x] TradingView
- [x] 2FA
- [x] proxy
- [ ] Adicionar metodos de pagamento (Stripe, Mercado Pago, PayPal)
	- [ ] ei, vamos trabalhar nos projetos dentro de "signalia". Dito isso, precisamos fazer a integração com MercadoPago e Stripe ao menos. Sendo q com o Mercado Pago vamos usar a forma de pagamento por "Split". Além disso, precisamos deixar esses meios de pagamentos configuraveis de acordo com o pais do usuario, então por exemplo pro Brasil posso querer comecar com a opção do Mercado Pago somente e para um pais como Estados Unidos quero q usemos o Stripe. Q q vc acha disso?
	- [x] Pagar.me
	- [x] Mercado Pago
	- [x] Stripe
	- [ ] T0-service
	- [ ] Pockyt-service
	- [ ] Monerium
	- [ ] Wirex
	- [ ] Bloquo
- [ ] Atualizar a criação de canais:
	- [x] Remover opção pagamento unico
	- [ ] Deixar explicito os valores:
		- [ ] Definido pelo analista
		- [ ] Real a ser pago pelo investidor
		- [ ] Quanto o analista recebe no final
- [ ] Possibilidade de trocar de carteiras e ou bolsa no card do portifolio
- [x] Connectar com as carteiras
- [x] Acertar a publicação de mensagens no chat
- [x] Acertar o tamanho da fonte do titulo nas abas do canal
- [x] on channels marketplace it seems a good idea to put the analysts list and on click one of them it redirects to analysts page with some description
- [ ] Conexão q precisa ser feita
	- [ ] Crypto
		- [ ] **Wallets** (MetaMask, Phantom, Coinbase Wallet, Trust Wallet, Rainbow) → abre Reown AppKit ou campo de endereço manual
		- [ ] **Exchanges** (Binance, Kraken, Coinbase Pro, KuCoin, OKX) → API Key + Secret Key (comportamento original)
	- [ ] Stocks
		- [ ] **Brokers** (Alpaca, Interactive Brokers, XP, Clear, Rico) → Account ID + API Key com uma nota explicando que conecta via API da corretora



Show agora q fizemos essas integrações precisamos revisitar o esquema de assinaturas q temos. Hoje existem 3 tipos de assinatura: - Mensal - Anual - "Uso unico" Primeiramente quero remover o "Uso único", acho q não é muito usual para assinaturas ter um caso desses. Em seguida estive pensando aqui em relação a minha margem de lucro e como faria pra a plataforma receber o seu percentual. Dai cheguei a seguinte conclusão: "Quero cobrar de analista + investidor de 12 a 15% em relação ao valor da assinatura" de forma que o analista no momento que está definindo o valor da sua assinatura ele vai: 
1. Preencher o valor da assinatura. 
	1. 1.1 Abaixo disso podemos ter um pequeno texto como um lembrete algo como "Lembrete: Se você for um analista novo e quer focar em arrecadar assinantes preços competitivos podem ser melhor." 
2. Abaixo disso terão 2 sliders "vinculados" e um checkbox "Net of fees". Os sliders somados devem dar, inicialmente, os 15%. Nesses sliders o analista tem o direito de mudar o percentual dele q começaria em 7.5% para até 5% onde quanto menos ele reduz o dele mais aumenta o do investidor de maneira a sempre mantermos minha receita de 15% (ou seja, aqui como o valor pago pelo investidor pode aumentar isso significa q meu custo aumenta tbm, logo a conta seria algo do tipo ).
    Uma outra opção aqui seria algo do tipo: um "slider" na tela de precificação do analista onde **Lado Esquerdo:** "Eu absorvo as taxas" (Ele - analista -  recebe menos, mas o preço para o cliente é mais atrativo, o que pode gerar mais volume de vendas). **Lado Direito:** "Repassar taxas ao cliente" (Ele recebe o valor integral, mas o preço final fica mais alto, o que pode diminuir a conversão).
3. Quanto ao checkbox, a ideia dele é q quando o analista marcar, isso significa q ele quer definir o valor a ser ganho por ele o resto a gente faz a conta. Ou seja, quando ele marcar isso a ideia é q o valor q ele definir para a "assinatura" é na real o quanto ele quer receber dai em cima disso aplicamos a nossa conta para chegar nos nosso 15% de margem onde parte o analista vai pagar e parte o investidor de acordo com o split anterior
4. Abaixo disso precisamos de uma "região descritiva" onde o analista veja:
	1. Quanto o investidor paga
	2. Quanto o analista de fato recebe
	3. Qual a taxa da plataforma
	4. Em resumo seria algo como: "Se você cobrar X, seu cliente paga Y e você recebe Z"
5. Ah uma observação para o preço, caso n seja gratuito ele deveria ter um valor minimo de 19,90, para fazer sentido cobrir a operação financeira







## Backend — o que foi criado

**Prisma:** `Wallet` model + `ChainType` enum adicionados ao schema.

**Camada de providers (pluggável):**

```
src/providers/portfolio/
  types.ts                    ← TokenBalance, WalletBalances (tipos compartilhados)
  evm.provider.ts             ← interface EvmPortfolioProvider
  solana.provider.ts          ← interface SolanaPortfolioProvider
  alchemy/alchemy.provider.ts ← implementação EVM (multi-chain, ERC-20)
  helius/helius.provider.ts   ← implementação Solana (SOL + SPL tokens)
  index.ts                    ← ← troque o provider aqui, sem tocar no service
```

**Endpoints novos:**

```
GET    /wallets           → lista wallets do usuário
POST   /wallets           → salva endereço + chainType (EVM | SOLANA) + label
DELETE /wallets/:id       → remove wallet
GET    /wallets/balances  → agrega saldos de todas as wallets (Alchemy + Helius em paralelo)
```

---

## Frontend — o que foi criado

- `WalletProvider` (Reown AppKit) com EVM + Solana num modal só
- `WalletService` — chamadas para os 4 endpoints
- `wallet.controller.tsx` + `wallet.presenter.tsx` seguindo o padrão do projeto
- Página em `/wallet`
- i18n adicionado nos 4 locales (en-US, pt-BR, es-AR, fr-FR)

---

## Próximos passos para você

1. Rodar `prisma migrate dev --name add-wallet` no backend
2. Adicionar as keys no `.env`:
    - `ALCHEMY_API_KEY` → [dashboard.alchemy.com](https://dashboard.alchemy.com/)
    - `HELIUS_API_KEY` → [dev.helius.xyz](https://dev.helius.xyz/)
    - `NEXT_PUBLIC_REOWN_PROJECT_ID` → [cloud.reown.com](https://cloud.reown.com/)
