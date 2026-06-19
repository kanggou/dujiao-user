# User Web

Vue 3 + TypeScript + Vite 用户端商店前台。

## 启动与构建

```bash
# 开发(端口 5173,代理 /api、/uploads、/sitemap.xml、/robots.txt 到 :8080)
npm run dev

# 类型检查 + 生产构建
npm run build

# 预览 dist
npm run preview
```

依赖 API 后端运行在 `http://localhost:8080`(见 `vite.config.ts` 的 proxy)。

生产构建会自动 `drop: ['console', 'debugger']`(见 `vite.config.ts` 的 esbuild 配置)。

## 技术栈

- Vue 3.5 + `<script setup>` + TypeScript
- Vite 7
- Pinia(状态)
- Vue Router 4
- Vue I18n 9
- Tailwind CSS v4(`@tailwindcss/postcss` + `@tailwindcss/typography`)
- @unhead/vue(SEO meta)
- @heroicons/vue(图标)
- DOMPurify(用户内容净化)
- qrcode(支付二维码)

## 目录结构

```
src/
├── api/          后端接口封装
├── components/   通用组件
├── composables/  组合式函数
├── constants/    常量
├── i18n/         多语言资源
├── stores/       Pinia stores
├── router/       路由定义
├── types/        类型定义
├── utils/
├── views/        页面级组件
├── App.vue / main.ts / style.css
```

## 构建优化

`vite.config.ts` 把 `qrcode` 与 `vue-i18n` 拆成独立 chunk(`vendor-qrcode`、`vendor-vue-i18n`),避免主包体积膨胀。

`cfasync-module-script` 插件给 module 类型的 `<script>` 加 `data-cfasync="false"`,绕过 Cloudflare Rocket Loader。

## 约定

- TypeScript 严格模式
- API 调用统一走 `src/api/`,不直接 `fetch`
- 渲染外部 HTML 必须经 DOMPurify
- 多语言键通过 `useI18n()` 调用,文案先加 i18n 资源
- SEO 相关 meta 用 `@unhead/vue` 的 `useHead`

## 常见任务

- 新增商品/订单页 → `views/` 加文件 + `router/` 注册
- 调用新 API → `src/api/<module>.ts` 定义请求函数
- 接入新支付渠道 UI → 在结账流程里根据后端返回的 `redirect_url` / 二维码渲染,不要硬编码渠道分支
