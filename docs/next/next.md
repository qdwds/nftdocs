# 前端项目架构
前端框架中有很多比如react、vue、Angular等非常多的框架。为了兼容主流我们选择[nextjs](https://nextjs.org/)框架来实现我们的业务。

> Next JS 是一个 React 框架,它提供了使用 React JS JavaScript 库创建快速 Web 应用程序的构建块。Next.js 提供了生产环境所需的所有功能以及最佳的开发体验：包括静态及服务器端融合渲染、 支持 TypeScript、智能化打包、 路由预取等功能 无需任何配置。

## 项目创建
```
npx create-next-app nft_app
```

## 安装web3库
```typescript
pnpm install @rainbow-me/rainbowkit wagmi ethers@^5
```

## 安装ui框架
```typescript
pnpm install  @mui/material @emotion/react @emotion/styled
```

## 项目架构
```
.
├── README.md
├── components  //  组件
│   ├── Button
│   │   └── index.tsx
│   ├── Header
│   │   └── index.tsx
│   └── NFTCard
│       └── index.tsx
├── next-env.d.ts
├── next.config.js
├── package.json
├── pages   //  业务逻辑页面
│   ├── _app.tsx
│   ├── _document.tsx
│   ├── api
│   ├── index.tsx
│   └── market
│       └── index.tsx
├── pnpm-lock.yaml
├── public
│   ├── favicon.ico
│   ├── next.svg
│   ├── thirteen.svg
│   └── vercel.svg
├── src //  配置相关
│   ├── createEmotionCache.ts
│   ├── rainbowkit.ts
│   ├── theme.ts
│   └── wagmi.ts
├── styles  //  样式
│   ├── global.css
│   └── home.module.css
└── tsconfig.json
```
