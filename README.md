# 🍔 Mr Burguer — Sistema IA de Pedidos

> Bot de WhatsApp con IA para tomar pedidos automáticamente · Irapuato, Guanajuato

## Stack

- **Backend:** Node.js 22 + Fastify
- **Base de datos:** PostgreSQL (Fly.io Postgres)
- **ORM:** Drizzle ORM
- **Hosting:** Fly.io (Dallas `dfw`)
- **Canal:** WhatsApp Business Cloud API (Meta)
- **Panel admin:** HTML/CSS/JS (sin framework, servido desde la misma app)

## Estructura

```
mr-burguer/
├── fly.toml              ← Config Fly.io
├── Dockerfile            ← Node 22 Alpine
├── package.json
├── drizzle.config.js
├── .env.example
├── public/
│   └── admin.html        ← Panel admin completo
└── src/
    ├── index.js           ← Servidor principal Fastify
    ├── db/
    │   ├── schema.js      ← 10 tablas PostgreSQL
    │   ├── index.js       ← Conexión Drizzle
    │   └── seed.js        ← Menú, zonas y admin inicial
    ├── ai/
    │   └── engine.js      ← Motor de conversación IA
    └── routes/
        ├── webhook.js     ← WhatsApp Cloud API
        ├── web.js         ← Chat desde web
        ├── orders.js      ← API pedidos
        ├── menu.js        ← API menú
        ├── auth.js        ← JWT login
        ├── admin.js       ← Panel + stats
        └── extra.js       ← Zonas, horarios, conversaciones
```

## Inicio rápido

```bash
cp .env.example .env
# Edita .env con tus credenciales

npm install
npm run db:push
node src/db/seed.js

npm run dev
# http://localhost:3000/admin  →  admin / MrBurguer2024!
```

## Despliegue en Fly.io

```bash
fly apps create mr-burguer-api
fly postgres create --name mr-burguer-db --region dfw
fly postgres attach mr-burguer-db --app mr-burguer-api
fly secrets set JWT_SECRET="..." WHATSAPP_TOKEN="..." WHATSAPP_PHONE_NUMBER_ID="..." WHATSAPP_VERIFY_TOKEN="..."
fly deploy
fly ssh console --app mr-burguer-api -C "node src/db/seed.js"
```

## Endpoints principales

| Ruta | Descripción |
|------|-------------|
| `GET /health` | Estado del servidor |
| `GET /admin` | Panel admin web |
| `GET /webhook/whatsapp` | Verificación Meta |
| `POST /webhook/whatsapp` | Mensajes WhatsApp entrantes |
| `POST /api/chat` | Chat IA desde web |
| `POST /api/auth/login` | Login admin |
| `GET /api/orders` | Lista de pedidos *(auth)* |
| `PATCH /api/orders/:id/status` | Cambiar estado *(auth)* |
| `GET /api/menu` | Menú público |
| `GET /api/stats` | Stats dashboard *(auth)* |

## Costo estimado en Fly.io

~$5–8 USD/mes (VM shared-cpu-1x + Postgres 1GB, con auto-stop).

---

**Mr Burguer · Residencial Floresta, Irapuato GTO**
