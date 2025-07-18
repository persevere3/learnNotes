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

