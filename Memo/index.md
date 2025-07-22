# 2025-07
```
// api/modules/table.ts
export default {
  get: () => http.get('/table'),
  create: (data) => http.post('/table', data),
  update: (data) => http.put('/table', data),
  remove: (id) => http.delete(`/table/${id}`),
};
```

```
// api/index.ts
import table from './modules/table';
export default {
  table,
};
```

```
import api from '@/api';

api.table.get(); 
```

# 2025-07-14
flutter 打包图片 [[专案工具]]

# 2025-07-18
封装 表单 
element-plus prop (有验证才需要)
5-3  lodash/clonedeep
5-4  CssProperties
	checkbox 需要在 v-if 确定有的情况下才渲染
	option 需要 watch ，option 可能是从后端来的

活动后台需要的表单功能:
- label 字体是红色
- 提示字串
- 一個 formitems 有两個输入匡
- 条件显示输入匡
- 插入有值表格(可新增项目，操作)

# 2025-07-21
api
```
src/api/
├── modules/              
│   ├── login.ts
│   ├── register.ts
│   ├── profile.ts
│   ├── settings.ts
│   ├── product.ts
│   ├── order.ts
│   ├── payment.ts
│   ├── config.ts
│   └── logs.ts
└── shared/           # 共享配置
    ├── client.ts     # HTTP 客戶端
    ├── types.ts      # 通用類型
    └── interceptors.ts
```

```
import axios from 'axios'

import { useRouter } from 'vue-router'

import { ElMessage } from 'element-plus'

import type { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios'

  

const router = useRouter()

  

interface RequestInstance {

  (config: AxiosRequestConfig): Promise<unknown>

  get<T = unknown>(url: string, config?: Omit<AxiosRequestConfig, 'url' | 'method'>): Promise<T>

  post<T = unknown>(url: string, data?: unknown, config?: Omit<AxiosRequestConfig, 'url' | 'method' | 'data'>): Promise<T>

  put<T = unknown>(url: string, data?: unknown, config?: Omit<AxiosRequestConfig, 'url' | 'method' | 'data'>): Promise<T>

  delete<T = unknown>(url: string, config?: Omit<AxiosRequestConfig, 'url' | 'method'>): Promise<T>

}

  

const service: AxiosInstance = axios.create({

  baseURL: '/api',

  timeout: 15000,

  // headers: {

  //   'Content-Type': 'application/json',

  // },

  withCredentials: true,

})

  

// 請求攔截器

service.interceptors.request.use(

  (config) => config,

  (error) => {

    console.error('Request error:', error)

    return Promise.reject(error)

  },

)

  

// 響應攔截器

service.interceptors.response.use(

  (response: AxiosResponse) => {

    const res = response.data
 
  

    if (response.config.responseType === 'blob') return res

  

    if (typeof res === 'object' && res !== null && 'code' in res) {

      const { code, message, data, error } = res

  

      if (code === 200) {

        return data !== undefined ? data : res

      }

  

      const errMsg = error?.message ? `${message}[${error.message}]` : message || '請求失敗'

  

      switch (code) {

        case 400:

          ElMessage.error(error.message)

          break

        case 401:

          localStorage.clear()

          router.push('/auth/login')

          ElMessage.error('請先登入')

          break

        case 403:

          ElMessage.error('權限不足')

          break

        case 404:

          ElMessage.error('請求的資源不存在')

          break

        case 405:

          ElMessage.error('請求方法不允許')

          break

        case 500:

          ElMessage.error('伺服器錯誤')

          break

        default:

          ElMessage.error(errMsg)

      }

  

      return Promise.reject(new Error(errMsg))

    }

  

    return res

  },

  

  (error) => {

    console.error('Response error:', error)

  

    if (!error.response) {

      ElMessage.error('網絡錯誤，請檢查您的網絡連線')

      return Promise.reject(error)

    }

  

    const errorData = error.response.data || {}

    const errMsg = errorData?.error?.message ? `${errorData.message}[${errorData.error.message}]` : errorData?.message || error.message || '請求失敗'

  

    switch (error.response.status) {

      case 400:

        ElMessage.error(errorData.error.message)

        break

      case 401:

        localStorage.clear()

        router.push('/auth/login')

        ElMessage.error('請先登入')

        break

      case 403:

        ElMessage.error('權限不足')

        break

      case 404:

        ElMessage.error('請求的資源不存在')

        break

      case 405:

        ElMessage.error('請求方法不允許')

        break

      case 408:

        ElMessage.error('請求逾時，請稍後再試')

        break

      case 429:

        ElMessage.error('請求過於頻繁，請稍後再試')

        break

      case 500:

        ElMessage.error('伺服器錯誤')

        break

      case 502:

      case 503:

      case 504:

        ElMessage.error('伺服器暫時無法回應，請稍後再試')

        break

      default:

        ElMessage.error(errMsg)

    }

  

    return Promise.reject(error)

  },

)

  

// 輸出封裝後的請求物件

const request = service as unknown as RequestInstance

export default request
```

```
import request from '@/utils/request.ts'

import { API_ENDPOINTS } from '@/config/api.config.ts'

import type { User, UpdateUserRequest, PaginationParams, PaginatedResponse, AdminUser, CreateAdminUserRequest, UpdateAdminUserRequest } from '@/types/api.types.ts'

  

const userApi = {

  // 一般用戶管理

  getUser(params: { plat?: string; site?: string; uid?: string; state?: string; pageIndex:   
  number; pageSize: number }): Promise<PaginatedResponse<User>> {
    return request.get(API_ENDPOINTS.USERS.LIST, { params })
  },

  updateUser(data: UpdateUserRequest): Promise<User> {
    return request.put(API_ENDPOINTS.USERS.UPDATE(data.siteid, data.uid, data.state))
  },

  // 管理員用戶管理
  getAdminUsers(params: PaginationParams): Promise<PaginatedResponse<AdminUser>> {
    return request.get(API_ENDPOINTS.ADMIN_USERS.LIST, { params })
  },

  getAdminUserById(username: string): Promise<AdminUser> {
    return request.get(API_ENDPOINTS.ADMIN_USERS.DETAIL(username))
  },

  createAdminUser(data: CreateAdminUserRequest): Promise<AdminUser> {
    return request.post(API_ENDPOINTS.ADMIN_USERS.LIST, data)
  },

  updateAdminUser(id: string, data: UpdateAdminUserRequest): Promise<AdminUser> {
    return request.put(API_ENDPOINTS.ADMIN_USERS.UPDATE(id), data)
  },

  deleteAdminUser(id: string): Promise<void> {
    return request.delete(API_ENDPOINTS.ADMIN_USERS.DELETE(id))
  },

  getAdminUserRole(userId: string): Promise<string> {
    return request.get(API_ENDPOINTS.ADMIN_USERS.ROLE(userId))
  },

}

export { userApi }
export default userApi
```

composables，utils
```
src/
├── composables/
│   ├── useUserList.ts         // API 查詢與 loading 控制
│   ├── useForm.ts             // 表單狀態與驗證
│   └── form/
│       └── useFormRules.ts    // 拆分成子模組也 OK
│
├── utils/
│   ├── format.ts              // 日期/金額等格式處理
│   ├── validator.ts           // 自定驗證函式
│   └── debounce.ts            // 防抖函式
```

router
```
src/
├── router/
│   ├── constantRoutes.ts     // 靜態基礎路由（如登入頁、404）
│   ├── asyncRoutes.ts        // 預定義動態路由（角色權限相關）
│   ├── backendRoutes.ts      // 依後端返回資料產生的動態路由
│   ├── index.ts              // 初始化與匯出 Router 實體
│   └── guards.ts             // 路由導航守衛（如登入驗證、角色權限）
```

stores
```
// stores/user.ts
import { defineStore } from 'pinia'
import { ref } from 'vue'
import { fetchUserRoutes } from '@/api/routes'
import { transformBackendRoutes } from '@/router/backendRoutes'
import router from '@/router'

export const useUserStore = defineStore('user', () => {
  // 👉 State
  const token = ref('')
  const userInfo = ref<any>(null)
  const routesLoaded = ref(false)

  // 👉 Actions
  function setToken(newToken: string) {
    token.value = newToken
    localStorage.setItem('token', newToken)
  }

  function logout() {
    token.value = ''
    userInfo.value = null
    routesLoaded.value = false
    localStorage.removeItem('token')
  }

  async function loadRoutes() {
    if (routesLoaded.value) return

    const backendRoutes = await fetchUserRoutes()
    const vueRoutes = transformBackendRoutes(backendRoutes)

    vueRoutes.forEach(route => {
      router.addRoute(route)
    })

    routesLoaded.value = true
  }

  return {
    // state
    token,
    userInfo,
    routesLoaded,
    // actions
    setToken,
    logout,
    loadRoutes,
  }
})
```

types

components
layouts
views

config ?

# 2025-07-22
table 

@
```
// vite.config.ts
import { fileURLToPath, URL } from 'node:url'
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))  // 把 @ 指向 src
    }
  }
})
```

mock
```
npm install mockjs

import Mock from 'mockjs'

const data = Mock.mock({
  'list|5-10': [{
    'id|+1': 1,
    'name': '@cname',
    'age|18-30': 1,
    'email': '@email'
  }]
})

console.log(data)
```

持久储存 
```
npm install @vueuse/core

//
import { useStorage } from '@vueuse/core'

const name = useStorage('name', 'default value')
```

defineProps
```
const props = defineProps<{
  title: string
  count?: number
}>()
```

```
const props = withDefaults(defineProps<{
  title?: string
  count?: number
}>, {
  title: 'Default Title',
  count: 0
})
```

defineEmits
```
const emits = defineEmits<{
  (event: 'update', value: number): void
  (event: 'delete', id: string): void
}>()

emit('update', 123)
emit('delete', 'abc')
```

definemodel
```
// 父组件
<MyComponent v-model:title="someTitle" v-model:count="someCount" />

// 相當于 `props: { title }` + `emits: ['update:title']`

const title = defineModel<string>('title')
const count = defineModel<number>('count')
```

defineExpose
```
// 子组件
function doSomething() {
  console.log('child method called')
}

defineExpose({
  doSomething
})
```

<template>
  <ChildComponent ref="childRef" />
  <button @click="childRef?.doSomething()">呼叫子元件方法</button>
</template>
<template>
  <ChildComponent ref="childRef" />
  <button @click="childRef?.doSomething()">呼叫子元件方法</button>
</template>

```
// 父组件
<template>
  <ChildComponent ref="childRef" />
  <button @click="childRef?.doSomething()">呼叫子元件方法</button>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import ChildComponent from './ChildComponent.vue'

const childRef = ref<InstanceType<typeof ChildComponent> | null>(null)
</script>
```


# 2025-07-23
i18


格式化 editor


样式



