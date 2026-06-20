# Signalia

Plataforma de canais de sinais cripto por assinatura. Um analista cria um canal pago, investidores assinam e recebem sinais de trading em tempo real. A plataforma retém 15% de cada assinatura (`PLATFORM_FEE_PCT`).

## Status

`active` — back, front e mobile com código funcional e integração confirmada.

## Sub-projetos

| Sub-projeto | Stack | Caminho | Docs |
|---|---|---|---|
| `signalia-back` | Node.js / Express / TypeScript / Prisma | `./signalia-back/` | `./signalia-back/docs/` |
| `signalia-front` | Next.js 16 / React 19 | `./signalia-front/` | `./signalia-front/docs/` |
| `signalia-mobile` | Expo / React Native | `./signalia-mobile/` | `./signalia-mobile/docs/` |

## Infra compartilhada (Docker)

O `docker-compose.yml` na raiz sobe toda a infra de suporte + back + front:

| Serviço | Imagem | Porta | Papel |
|---|---|---|---|
| `postgres` | postgres:16-alpine | 5432 | Banco principal (Prisma ORM) |
| `redis` | redis:7-alpine | 6379 | Cache e sessões |
| `rabbitmq` | rabbitmq:3-management | 5672 / 15672 | Fila de mensagens (webhooks, notificações) |
| `back` | build `./signalia-back` | — | API REST (porta interna 3001) |
| `front` | build `./signalia-front` | — | Next.js (porta interna 3000) |
| `nginx` | nginx:alpine | 80 | Reverse proxy: `/api` → back, `/` → front |

## Como executar localmente (full stack)

```bash
cd signalia

# sobe toda a infra + back + front
docker-compose up -d

# acompanha logs
docker-compose logs -f back front
```

O mobile consome o back diretamente via REST — rode separado:

```bash
cd signalia-mobile
npx expo start
```

## Variáveis de ambiente do back (docker-compose injetadas)

`DATABASE_URL`, `REDIS_URL`, `RABBITMQ_URL`, `JWT_SECRET`, `JWT_REFRESH_SECRET`, `JWT_EXPIRES_IN`, `JWT_REFRESH_EXPIRES_IN`, `PORT`, `NODE_ENV`, `FRONTEND_URL`, `API_URL`, `PLATFORM_FEE_PCT`

Para variáveis adicionais (OAuth, Stripe, MercadoPago, Pagar.me, MFA), veja `signalia-back/docs/README.md`.
