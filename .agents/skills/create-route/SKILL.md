---
name: create-route
description: 指导在前端项目中按团队规范创建和维护路由，包括目录结构、Next.js App Router 用法、懒加载模式。当前端需要新增或重构页面路由时使用本技能。
---

# 创建与维护路由

## 重要提示

在开始创建之前，请务必阅读以下关键规范：

**必读规范**：
- `.agents/rules/03-项目结构.md` - 目录结构要求
- `.agents/rules/06-路由规范.md` - 路由配置约束

**常见错误警告**：
- 样式文件必须使用 `.module.scss` 后缀，禁止使用 `.scss`
- 路由目录名使用 `kebab-case`，例如 `login`、`profile`
- 使用 Next.js App Router 模式

---

## 标准路由目录结构

项目使用 Next.js 16 App Router 模式：

```text
app/
├── (auth)/                 # 路由组
│   ├── login/
│   │   ├─ page.tsx
│   │   ├─ layout.tsx
│   │   └─ index.module.scss
```

**关键要求**：
- 样式文件后缀：`index.module.scss`（非 `.scss`）
- 组件导出：默认导出组件
- 目录名：`kebab-case`

---

## 步骤 1：创建页面组件

```tsx
// app/(auth)/login/page.tsx
import React from 'react';
import styles from './index.module.scss';

const LoginPage: React.FC = () => {
  return <div className={styles.loginPage}>LoginPage</div>;
};

export default LoginPage;
```

**验证点**：
- [ ] 文件名为 `page.tsx`
- [ ] 样式导入为 `./index.module.scss`
- [ ] 默认导出组件

---

## 步骤 2：创建布局（如需要）

```tsx
//login/layout.tsx
import React from app/(auth)/ 'react';

export default function LoginLayout({children}: {children: React.ReactNode}) {
  return <div className="login-layout">{children}</div>;
}
```

---

## 步骤 3：创建 Loading（如需要）

```tsx
// app/(auth)/login/loading.tsx
export default function Loading() {
  return <div>Loading...</div>;
}
```

---

## 步骤 4：验证文件结构

创建完成后，检查目录结构是否符合规范：

```text
app/(auth)/login/
  ├─ page.tsx              ✓
  ├─ layout.tsx            ✓（可选）
  ├─ loading.tsx           ✓（可选）
  └─ index.module.scss     ✓（必须是 .module.scss）
```

---

## 页面级组件放置

如果页面需要专用组件，创建 `components/` 目录：

```text
app/(main)/profile/
  ├─ page.tsx
  ├─ layout.tsx
  ├─ index.module.scss
  └─ components/           # 页面专用组件
      └─ avatar/
          ├─ index.tsx
          └─ index.module.scss
```

**组件放置规则**（详见 `.agents/rules/04-组件规范.md`）：
- 页面级组件（仅当前页面使用）→ `app/<route>/components/`
- 通用组件（多处复用）→ `components/`

---

## 快速检查清单

创建完成后，逐项核对：

- [ ] 路由目录名为 `kebab-case`
- [ ] 存在 `page.tsx`
- [ ] 存在 `index.module.scss`（非 `.scss`）
- [ ] Page 中样式导入为 `./index.module.scss`
- [ ] 组件放置位置正确（通用 vs 页面级）

**样式还原检查**：涉及 UI 还原的样式开发，请参考 `.agents/skills/create-proposal/SKILL.md` 中的「样式还原验证检查清单」及对应页面的 `docs/样式还原/<名称>-UI分析清单.md`。
