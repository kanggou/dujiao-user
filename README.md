# Dujiao-Next User Web

Dujiao-Next User Web is the customer-facing frontend for browsing products, placing orders, and completing payments.

## Tech Stack

- Vue 3
- TypeScript
- Vite
- Tailwind CSS
- Pinia

## What This App Does

- Product listing and product detail pages
- Checkout flow for member and guest users
- Payment result and order query pages
- User account center and order history

## Quick Start

```bash
npm install
npm run dev
```

Build for production:

```bash
npm run build
```

## Discord 登录

登录页支持「使用 Discord 登录」（OAuth2 授权码模式）：

- 入口：`src/views/auth/Login.vue` 的 Discord 按钮 → 跳转 Discord 授权
- 回调：`src/views/auth/DiscordCallback.vue`（路由 `/auth/discord/callback`），带 `state` 防 CSRF
- store：`src/stores/userAuth.ts` 的 `discordLogin(code)`
- 后端配合：`GET /api/v1/auth/discord/config`（按钮是否显示由 `enabled` 决定）、`POST /api/v1/auth/discord/login`

行为：Discord 身份已绑定则直接登录；未绑定则自动建号并绑定。需在后端 `config.yml` 的 `discord_auth` 配置 client_id / client_secret / redirect_uri 并启用。

## Online Documentation

- https://dujiao-next.com
