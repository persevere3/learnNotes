# .prettierrc 配置文件
{
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "semi": false,
  "singleQuote": true,
  "quoteProps": "as-needed",
  "jsxSingleQuote": true,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "bracketSameLine": false,
  "arrowParens": "avoid",
  "rangeStart": 0,
  "rangeEnd": null,
  "requirePragma": false,
  "insertPragma": false,
  "proseWrap": "preserve",
  "htmlWhitespaceSensitivity": "css",
  "vueIndentScriptAndStyle": false,
  "endOfLine": "lf",
  "embeddedLanguageFormatting": "auto",
  "singleAttributePerLine": false
}

# package.json 相關依賴和腳本配置
{
  "devDependencies": {
    "prettier": "^3.0.0",
    "@trivago/prettier-plugin-sort-imports": "^4.2.0",
    "prettier-plugin-organize-attributes": "^1.0.0",
    "prettier-plugin-tailwindcss": "^0.5.0"
  },
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "lint:fix": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts --fix --ignore-path .gitignore"
  }
}

# .prettierignore 配置文件
# 構建輸出目錄
/dist
/build
/out

# 依賴目錄
node_modules/
.pnpm-store/

# 環境配置文件
.env
.env.local
.env.*.local

# 日誌文件
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*

# 編輯器和IDE
.vscode/
.idea/
*.swp
*.swo

# 系統文件
.DS_Store
Thumbs.db

# 緩存文件
.cache/
.temp/
.tmp/

# 文檔和靜態文件（可選）
/docs
/public
*.md

# 特定文件類型
*.min.js
*.min.css

# 配置文件（根據需要調整）
# package-lock.json
# yarn.lock
# pnpm-lock.yaml

# VSCode settings.json 配置
{
  // Prettier 相關設置
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.formatOnPaste": true,
  "editor.formatOnType": false,
  
  // 特定文件類型的格式化器
  "[vue]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[css]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[scss]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  
  // 保存時的自動操作
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },
  
  // Vue 相關設置
  "vetur.format.defaultFormatter.html": "prettier",
  "vetur.format.defaultFormatter.js": "prettier",
  "vetur.format.defaultFormatter.ts": "prettier",
  
  // Volar 設置（Vue 3 推薦）
  "vue.format.template.initialIndent": false,
  "vue.format.script.initialIndent": false,
  "vue.format.style.initialIndent": false,
  
  // 其他編輯器設置
  "editor.tabSize": 2,
  "editor.insertSpaces": true,
  "files.eol": "\n",
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  
  // 文件關聯
  "files.associations": {
    "*.vue": "vue"
  }
}

# VSCode extensions.json 推薦擴展
{
  "recommendations": [
    "esbenp.prettier-vscode",
    "Vue.volar",
    "Vue.vscode-typescript-vue-plugin",
    "dbaeumer.vscode-eslint",
    "bradlc.vscode-tailwindcss"
  ]
}

# .editorconfig 配置文件（與 Prettier 配合使用）
root = true

[*]
charset = utf-8
end_of_line = lf
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false

# npm/yarn 腳本使用示例
# 格式化所有文件
npm run format

# 檢查格式化狀態
npm run format:check

# 配置屬性詳細解釋說明

## .prettierrc 所有配置項詳細說明：

### 📏 行寬和縮進相關
**printWidth: 100**
- 作用：設置每行代碼的最大字符數
- 默認值：80
- 選擇原因：後台項目通常有複雜的業務邏輯，100字符提供更好的可讀性
- 示例：
  ```javascript
  // printWidth: 80 會換行
  const result = someVeryLongFunctionName(param1, param2, param3, param4)
  
  // printWidth: 100 不會換行
  const result = someVeryLongFunctionName(param1, param2, param3, param4, param5)
  ```

**tabWidth: 2**
- 作用：設置縮進的空格數量
- 默認值：2
- 選擇原因：Vue 生態系統和前端項目的標準，節省水平空間
- 示例：
  ```javascript
  if (condition) {
  ··console.log('2 個空格縮進')
  }
  ```

**useTabs: false**
- 作用：是否使用制表符 (tab) 進行縮進
- 默認值：false
- 選擇原因：空格縮進在不同編輯器中顯示一致，避免混合使用問題

### 📝 引號和分號相關
**semi: false**
- 作用：是否在語句結尾添加分號
- 默認值：true
- 選擇原因：現代 JavaScript/TypeScript 風格，ASI 自動插入分號
- 示例：
  ```javascript
  // semi: true
  const name = 'John';
  const age = 25;
  
  // semi: false
  const name = 'John'
  const age = 25
  ```

**singleQuote: true**
- 作用：是否使用單引號替代雙引號
- 默認值：false
- 選擇原因：Vue 官方風格指南推薦，減少按鍵次數
- 示例：
  ```javascript
  // singleQuote: false
  const message = "Hello World"
  
  // singleQuote: true
  const message = 'Hello World'
  ```

**quoteProps: "as-needed"**
- 作用：對象屬性的引號使用策略
- 可選值：'as-needed' | 'consistent' | 'preserve'
- 選擇原因：只在必要時添加引號，保持代碼簡潔
- 示例：
  ```javascript
  // as-needed
  const obj = {
    name: 'John',
    'user-id': 123
  }
  
  // consistent
  const obj = {
    'name': 'John',
    'user-id': 123
  }
  ```

**jsxSingleQuote: true**
- 作用：JSX 中是否使用單引號
- 默認值：false
- 選擇原因：與 JavaScript 保持一致

### 🔗 尾隨逗號和括號相關
**trailingComma: "es5"**
- 作用：尾隨逗號的使用策略
- 可選值：'none' | 'es5' | 'all'
- 選擇原因：在 ES5 支持的地方添加尾隨逗號，方便版本控制
- 示例：
  ```javascript
  // es5
  const obj = {
    name: 'John',
    age: 25, // ← 尾隨逗號
  }
  
  // 函數參數不會添加（ES5 不支持）
  function test(a, b) {}
  ```

**bracketSpacing: true**
- 作用：對象字面量的括號內是否添加空格
- 默認值：true
- 示例：
  ```javascript
  // bracketSpacing: true
  const obj = { name: 'John' }
  
  // bracketSpacing: false
  const obj = {name: 'John'}
  ```

**bracketSameLine: false**
- 作用：多行 JSX 元素的 > 是否與最後一行屬性在同一行
- 默認值：false
- 示例：
  ```jsx
  // bracketSameLine: false
  <MyComponent
    prop1="value1"
    prop2="value2"
  >
  
  // bracketSameLine: true
  <MyComponent
    prop1="value1"
    prop2="value2">
  ```

**arrowParens: "avoid"**
- 作用：箭頭函數參數的括號使用策略
- 可選值：'always' | 'avoid'
- 選擇原因：單參數時省略括號，代碼更簡潔
- 示例：
  ```javascript
  // avoid
  const func = x => x * 2
  const func2 = (x, y) => x + y
  
  // always
  const func = (x) => x * 2
  const func2 = (x, y) => x + y
  ```

### 🎯 格式化範圍和特殊處理
**rangeStart: 0** 和 **rangeEnd: null**
- 作用：指定格式化的字符範圍
- 默認值：0 和 Infinity
- 說明：格式化整個文件

**requirePragma: false**
- 作用：是否要求文件頂部有特殊註釋才進行格式化
- 默認值：false
- 說明：對所有文件進行格式化

**insertPragma: false**
- 作用：是否在文件頂部插入 @format 標記
- 默認值：false
- 說明：不插入特殊標記

### 📄 HTML 和 Vue 相關
**htmlWhitespaceSensitivity: "css"**
- 作用：HTML 空白符的處理敏感度
- 可選值：'css' | 'strict' | 'ignore'
- 選擇原因：遵循 CSS display 屬性的空白符處理規則
- 示例：
  ```html
  <!-- css: 根據 CSS 規則處理空白 -->
  <span>Hello</span> <span>World</span>
  ```

**vueIndentScriptAndStyle: false**
- 作用：Vue 文件中的 <script> 和 <style> 標籤內容是否縮進
- 默認值：false
- 選擇原因：避免額外縮進，保持代碼對齊
- 示例：
  ```vue
  <template>
    <div>content</div>
  </template>
  
  <script>
  // vueIndentScriptAndStyle: false
  export default {
    name: 'Component'
  }
  </script>
  ```

**singleAttributePerLine: false**
- 作用：HTML/Vue 標籤的屬性是否每行一個
- 默認值：false
- 選擇原因：允許多個短屬性在同一行，提高可讀性

### 🔄 換行和編碼相關
**endOfLine: "lf"**
- 作用：設置換行符類型
- 可選值：'lf' | 'crlf' | 'cr' | 'auto'
- 選擇原因：統一使用 Unix 風格換行符，避免跨平台問題

**proseWrap: "preserve"**
- 作用：Markdown 文件的文本換行處理
- 可選值：'always' | 'never' | 'preserve'
- 選擇原因：保持原始換行，不強制重新包裝

**embeddedLanguageFormatting: "auto"**
- 作用：是否格式化嵌入的代碼（如 markdown 中的代碼塊）
- 可選值：'auto' | 'off'
- 選擇原因：自動檢測並格式化嵌入的代碼

## 🔧 插件配置說明：

**@trivago/prettier-plugin-sort-imports**
- 功能：自動排序和分組 import 語句
- 配置：
  ```json
  {
    "importOrder": [
      "^vue(.*)$",
      "^@vue/(.*)$", 
      "^@/(.*)$",
      "^[./]"
    ],
    "importOrderSeparation": true,
    "importOrderSortSpecifiers": true
  }
  ```

**prettier-plugin-organize-attributes**
- 功能：按照 Vue 風格指南排序模板屬性
- 順序：v-for → v-if → v-show → v-model → 其他指令 → 普通屬性

**prettier-plugin-tailwindcss**
- 功能：自動排序 Tailwind CSS 類名
- 按照官方推薦順序：布局 → 外觀 → 交互

## 💡 最佳實踐建議：

1. **團隊協作**：所有團隊成員使用相同配置，避免代碼風格衝突
2. **Git Hooks**：在提交前自動格式化，確保代碼庫一致性  
3. **編輯器整合**：配置保存時自動格式化，提高開發效率
4. **與 ESLint 配合**：ESLint 負責代碼質量檢查，Prettier 負責代碼格式化