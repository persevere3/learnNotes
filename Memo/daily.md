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
## i18n
```
npm install vue-i18n@9
```

```
// src/locales/en.ts
export default {
  greeting: 'Hello, {name}!',
  logout: 'Logout',
}
```

```
// src/locales/i18n.ts
import { createI18n } from 'vue-i18n'

const messages: Record<string, any> = {}

// 自動載入所有語言檔案
const modules = import.meta.glob('@/locales/*.ts', { eager: true })

for (const path in modules) {
  const matched = path.match(/\/([a-zA-Z-_]+)\.ts$/)
  if (matched) {
    const locale = matched[1]
    if(locale !== 'i18n') messages[locale] = (modules[path] as any).default
  }
}

export const i18n = createI18n({
  legacy: false, // 使用 Composition API 模式
  globalInjection: true, // 可在 template 中直接使用 $t
  locale: 'zh-TW',
  fallbackLocale: 'en',
  messages
})
```

```
// main.ts
import { i18n } from './locales/i18n'

app.use(i18n)
```

## 代码统一


### 安裝 Prettier 與 ESLint 所需的套件
```
npm install -D \
prettier \
eslint \
eslint-plugin-vue \
@typescript-eslint/eslint-plugin \
@typescript-eslint/parser \
eslint-plugin-prettier \
eslint-config-prettier
```

### VSCode 插件
| 插件名稱                      | 用途                       |
| ------------------------- | ------------------------ |
| ESLint                    | 即時檢查代碼風格與錯誤              |
| Prettier - Code formatter | 自動排版格式化                  |
| Volar                     | Vue 3 支援 + TypeScript 體驗 |
| TypeScript Vue Plugin     | 更完整的型別支援                 |

### .prettierrc
```
{
  "semi": false,
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "trailingComma": "none",
  "bracketSpacing": true,
  "arrowParens": "avoid"
}
```

| 設定項目            | 說明             |
| --------------- | -------------- |
| `semi`          | 關閉行尾分號         |
| `singleQuote`   | 使用單引號          |
| `printWidth`    | 每行最大長度為 100    |
| `tabWidth`      | tab 代表兩個空白     |
| `trailingComma` | 最後一個元素後不加逗號    |
| `arrowParens`   | 箭頭函式參數為單個時不加括號 |
### .prettierignore
```
# node_modules - 所有套件依賴
node_modules

# build outputs - 編譯或打包後的輸出
dist
build
coverage

# lock files - 鎖定檔案不格式化
package-lock.json
pnpm-lock.yaml
yarn.lock

# .env files - 環境變數檔案
.env
.env.* 

# 自動生成的型別與壓縮檔案
*.d.ts
*.min.js
*.min.css
*.log

# public 資料夾中靜態資源（如 JS、第三方）
public/**/*.js
public/**/*.css

# IDE 或作業系統檔案
.DS_Store
.vscode/*
!.vscode/settings.json  # 保留專案設定可見

# 單元測試快照或暫存檔案（如 Vitest、Jest）
**/__snapshots__/
**/*.snap
**/.cache/
```
### .eslintrc.js
```
module.exports = {
  root: true,
  env: {
    browser: true,
    node: true
  },
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: '@typescript-eslint/parser',
    ecmaVersion: 2020,
    sourceType: 'module'
  },
  extends: [
    'eslint:recommended',                    // ESLint 推薦規則
    'plugin:vue/vue3-recommended',           // Vue 3 推薦規則
    'plugin:@typescript-eslint/recommended', // TS 推薦規則
    'plugin:prettier/recommended'            // 整合 Prettier（避免衝突）
  ],
  plugins: ['vue', '@typescript-eslint'],
  rules: {
    'vue/multi-word-component-names': 'off',
    '@typescript-eslint/no-explicit-any': 'off'
  }
}
```

| 欄位        | 說明                               |
| --------- | -------------------------------- |
| `parser`  | 使用 Vue + TypeScript 語法解析器        |
| `extends` | 結合 Vue、TS、ESLint 與 Prettier 推薦規則 |
| `rules`   | 自訂規則（例如允許使用 `any`、單字元元件名）        |
### .eslintignore
```
node_modules
dist
```

### .vscode/settings.json
```
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.validate": ["javascript", "typescript", "vue"],
  "prettier.enableDebugLogs": false
}
```

|設定項目|說明|
|---|---|
|`formatOnSave`|儲存時自動格式化|
|`codeActionsOnSave`|儲存時自動修復 ESLint 檢查錯誤|
|`eslint.validate`|讓 ESLint 也能檢查 Vue/TS 等檔案格式|

[[prettier]]


# 2025-07-28 Flutter

### 1. 变量的定义
可以使用var来定义变量，变量的类型可以使用变量值推断出来，也可以使用特定类型来定义变量
```dart
// var 变量名称 = 赋值;
var name = "hi"; //String类型
var age = 18; //int类型
var high = 1.70; //double类型

// 变量类型 变量名称 = 赋值;
String name = "hi"; // String类型
int age = 18; // int类型
double high = 1.70; // double类型
```

print 用法 
runtimeType用法
``` dart
print('name=${name}');
print('name=${name.runtimeType}');
```

如果变量不限于单个类型，则可以使用dynamic或Object来定义变量
```dart
dynamic value = 18;
print("value = $value");
value = "bruce";
print("value = $value");
value = 3.5;
print("value = $value");
  
Object val = 18;
print("val = $val");
val = "bruce";
print("val = $val");
val = 3.5;
print("val = $val");
```

final和const都是用作定义常量的， 也就是说定义后不能修改
- const在赋值时，赋值内容必须是编译期间就能确定下来的
- final是可以动态获取的，比如赋值一个函数
```dart
void main() {
  int a = 1;
  int b = 2;
  // a = 3; // a可以重新赋值
  final c1 = a * b;
  const c2 = a * b; // 提示错误，因为a和b并不是使用const定义的
  final t1 = test();
  const t2 = test(); // 提示错误
}

// 定义test函数
void test() {
  print('test 方法');
}
```

 如果一个变量没有初始化值，那么它的默认值为null

### 2. 内置类型
> **Numbers（数值）:**  包含**int**和**double**两种类型，没有像Java中的float类型，int和double都是num的子类型。
>
> **Strings（字符串）:** 可以使用单引号或者双引号来创建字符串。
>
> **Booleans（布尔值）**：Dart有一个名为bool的类型，只有两个对象具有bool类型：true和false，他们都是编译时常量。
>
> **List（列表）: **在 Dart 中**数组**就是 [List](https://api.dartlang.org/stable/dart-core/List-class.html) 对象。
>
> **Map（键值对象）：**Map 是一个键值对相关的对象。
>
> **Runes：**runes 代表字符串的 UTF-32 code points。
>
> **Symbols：**一个 [Symbol](https://api.dartlang.org/stable/dart-core/Symbol-class.html) object 代表 Dart 程序中声明的操作符或者标识符。

### 3. 数字类型
转换类型
```dart
// 1. 字符串转换数字 .parse()
var str = int.parse('123');
var str1 = double.parse('123.22');
print('$str, ${str.runtimeType}; $str1, ${str1.runtimeType}');


// 2. 数字转字符串 .toString()
var str2 = 123.toString();
var str3 = 123.44.toString();
print('$str2, ${str2.runtimeType}; $str3, ${str3.runtimeType}');
```

### 4. 布尔类型
** 注意: Dart中不能判断非0即真, 或者非空即真，string可以使用 isEmpty **

### 5. 字符串
可以使用单引号或双引号
```dart
// 1.定义字符串的方式
var s1 = 'Hello World';
var s2 = "Hello Dart";

var s3 = 'Hello\'Fullter';
var s4 = "Hello'Fullter";

// 2.多行字符串
var message1 = '''
	哈哈哈
	呵呵呵
	嘿嘿嘿
''';
```

### 赋值操作
#### ??=
- 如果变量的值为null，则使用右侧的值进行赋值；
- 如果变量的值不为null，则保持原有的值，不会进行赋值操作；

```dart
void main() {
  var name1 = '张三';
  name1 ??= '李四';
  print(name1); // 张三
  var name2 = '';
  name2 ??= '李四';
  print(name2); // 李四
}
```

### 条件运算符
#### ??
`expr1 ?? expr2`
- 如果`expr1`是`null`,则返回`expr2`的值；
- 如果`expr1`不是`null`, 则使用`expr1`的值；

```
void main() {
  var name1 = '张三';
  var name2 = null;
  name2 = '李四' ?? name1;
  print(name2);
}
```

### List
定义
```dart
// List定义
// 1.使用类型推导定义
var lists = ['a', 'b', 'c', 'd'];
print('$lists ${lists.runtimeType}  ${lists.length}');

// 2.明确指定类型
List<int> nums = [1, 2, 3, 4];
print('$nums ${nums.runtimeType}  ${nums.length}');
```

操作
```dart
  // 数组的添加、删除、包含操作
  var lists = ['a', 'b', 'c', 'd', 'a'];

  // 添加元素 
  lists.add('e');

  // 添加元素
  lists.addAll(['ddd', 'eee', 'aaa']);

  // 插入元素
  lists.insert(0, 'aa');

  // join split 

  // 遍历
  // 1 for(var i = 0; i < lists.length; i++)
  // 2 forEach

  // 删除元素
  lists.remove('a');

  // 根据下标删除元素
  lists.removeAt(3);
  print('$lists ${lists.runtimeType} ${lists.length} ${lists.contains('aaa')}');
```

### Set - 去除重复的值
定义
```dart
// Set的定义
// 1.使用类型推导定义
var listsSet = {'a', 'b', 'c', 'c'};
print('$listsSet ${listsSet.runtimeType}  ${listsSet.length}');

// 2.明确指定类型
Set<int> numsSet = {1, 2, 3, 4};
print('$numsSet ${numsSet.runtimeType}  ${numsSet.length}');
```

操作
``` dart
  // Set的添加、删除、包含操作
  var listsSet = {'a', 'b', 'c', 'ddd'};

  // 添加
  listsSet.add('e');

  // 添加
  listsSet.addAll({'ddd', 'eee', 'aaa'});

  // 转成List
  listsSet.toList()

  // 删除元素
  listsSet.remove('ddd');
  print('$listsSet ${listsSet.runtimeType} ${listsSet.length} ${listsSet.contains('aaa')}');
```

### Map
定义
 ```dart
// Map的定义
// 1.使用类型推导定义
var map1 = {'name': '张三', 'age': 18};
print('$map1 ${map1.runtimeType}  ${map1.length}');

// 2.明确指定类型
Map<String, Object> map2 = {'height': 1.78, 'address': '广州市'};
print('$map2 ${map2.runtimeType}  ${map2.length}');
```

操作
``` dart
  // Map的操作
  var map1 = {'name': '张三', 'age': 18};
  print('$map1 ${map1.runtimeType} ${map1.length}');
  // 根据key获取value 
  // 没有 map1.name 写法
  print(map1['name']); // 张三

  // 获取所有的keys
  print('${map1.keys} ${map1.keys.runtimeType}');

  // 获取所有的values
  print('${map1.values} ${map1.values.runtimeType}');

  // 获取所有的values
  print('${map1.containsKey('age')} ${map1.containsValue(18)}');

  // map1.keys.toList()

  // 根据key删除元素
  map1.remove('age');
  print('${map1}');
```

### 函数

```dart
/*
返回值 函数的名称(参数列表) {
  函数体
  return 返回值
}
*/
// 普通的函数定义
String getStr(String str) {
  return str;
}
// 箭头函数定义
String getStr(String str) => str;

// 没有 return 默认返回 null 
```

可选参数

```dart
// 命名可选参数
void user(String name, {int age, String sex}){
  print('$name, $age, $sex');
}

void main() {
  user('张三'); // 张三, null, null
  user('张三', age: 12); // 张三, 12, null
  user('张三', sex: '男', age: 20); // 张三, 20, 男 
}

```

### 类别

构造函数
```dart
class Person {
  String name;
  int age;
  
  // 默认构造函数
  // Person(String name, int age){
  //   this.name = name;
  //   this.age = age;
  // }
  // 上面可以简写成下面这种形式
  Person(this.name, this.age);

  // 命名构造函数	
  Person.now(){
    print(this.name, this.age);
  }

  void getInfo() {
    print("${this.name}-----${this.age}");
  }

  void setInfo(int age) {
    this.age = age;
  }
}

void main() {
  // 实例化
  Person p1 = new Person('张三', 10);
  p1.getInfo();

  Person p2 = new Person.now('李四', 19);
  p2.getInfo();

  print(Person.name) // X ，只能透过实例化取得的对象去访问name	
}

```

抽离类
/lib/Person.dart
```dart
class Person {
  String name;
  int age;
  Person(this.name, this.age);

  Person.now(){
    print('我是命名构造函数');
  }

  void getInfo() {
    print("${this.name}-----${this.age}");
  }

  void setInfo(int age) {
    this.age = age;
  }
}
```

主文件：
```dart
import './lib/Person.dart';

void main() {
  // 实例化Person
  Person p1 = new Person('张三', 10);
  p1.getInfo();
}
``` 

### 项目结构
material.dart ui库
main 入口函数
widget 组件

Container
宽度 = 子元素宽度

Row Column
MainAxisAlignment.spaceAround

Expanded
flex

Padding
padding: EdgeInsets.all(10)，padding: EdgeInsets.only(left: 10)

Icon
Icons.ac_unit, color: Colors.blue，color: Color(0xffffff)