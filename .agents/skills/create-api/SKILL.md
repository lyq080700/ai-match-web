---
name: create-api
description: 指导在前端项目中按团队规范创建和维护 HTTP 接口，包括类型定义、请求封装、命名约定与错误处理。当前端需要新增或调整 API 时使用本技能。
---

# 创建与维护 API

## 使用场景

当前端需要：

- 为某个业务模块 **新增接口**
- 为已有接口 **补充类型定义**
- **重构接口文件** 或拆分模块

请使用本技能，并同时遵守 `.agents/rules/05-API规范.md` 中的强制规则。

---

## 步骤 1：确定接口归属模块

1. 先确定业务模块名称（如 `user`、`match`、`profile`）。
2. 对应的文件归属：
   - 请求封装文件：`services/<module>.ts`
   - 类型定义文件：`interfaces/<module>/api.ts`
   - 业务模型类型：`interfaces/<module>/model.ts`

**约定：**

- 所有请求函数都放在 `services` 下按模块拆分，禁止在组件、page、store 中直接写 axios 请求。

---

## 步骤 2：在 interfaces 中定义类型

在 `interfaces/<module>/model.ts` 中定义基础业务模型：

```ts
// interfaces/user/model.ts
export interface User {
  id: number;
  name: string;
  avatar: string;
  status: UserStatus;
}
```

在 `interfaces/<module>/api.ts` 中定义请求/响应类型：

```ts
// interfaces/user/api.ts
import type {User} from './model';

export interface GetUserListParams {
  page: number;
  pageSize: number;
}

export interface GetUserListResponse {
  list: User[];
  total: number;
}
```

**注意：**

- 请求参数类型和返回值类型应严格依据接口文档，**禁止凭空创造字段**。
- 返回值一般是：
  ```
  {
    code: number;
    msg: string;
    data: {...}
  }
  ```
  axios 拦截器内部已处理 `code` 判断并返回 `data`，因此这里只需定义 `data` 的结构。

---

## 步骤 3：在 services 中创建请求函数

在 `services/<module>.ts` 中：

```ts
// services/user.ts
import axios from 'axios';
import type {
  GetUserListParams,
  GetUserListResponse,
  GetUserDetailResponse,
} from '@/interfaces/user/api';

const httpClient = axios.create({
  baseURL: '/api',
});

export const getUserList = async (params: GetUserListParams) => {
  const response = await httpClient.get<GetUserListResponse>('/users', {params});
  return response.data;
};

export const getUserDetail = async (id: number) => {
  const response = await httpClient.get<GetUserDetailResponse>(`/users/${id}`);
  return response.data;
};

export const updateUser = async (data: {id: number; name: string}) => {
  return httpClient.put(`/users/${data.id}`, data);
};

export const deleteUser = async (id: number) => {
  return httpClient.delete(`/users/${id}`);
};
```

---

## 步骤 4：命名与错误处理约定

**命名（必须遵守 `05-API规范` 中的 NON-NEGOTIABLE）：**

- 获取列表：`getXxxList`
- 获取详情：`getXxxDetail`
- 创建：`createXxx`
- 更新：`updateXxx`
- 删除：`deleteXxx`
- **禁止** 使用 `fetchXxx` 等前缀。

**错误处理：**

- 接口错误由 axios 拦截器统一处理，业务代码中**禁止重复**加错误提示。
- 业务侧只处理成功逻辑，以及**前端自身校验错误**（如表单校验失败）。

---

## 步骤 5：在业务代码中使用

在组件或 store 中使用时：

```ts
import {getUserList} from '@/services/user';

const onLoad = async () => {
  const res = await getUserList({page: 1, pageSize: 10});
  // 这里的 res 类型已经是 GetUserListResponse
};
```

---

## 快速检查清单

- [ ] 是否在 `interfaces/<module>/api.ts` 中定义了清晰的请求/响应类型？
- [ ] 是否在 `services/<module>.ts` 中集中管理该模块的所有接口？
- [ ] 函数命名是否符合 `get/create/update/deleteXxx` 规范？
- [ ] 是否避免在业务代码中重复处理接口错误提示？
