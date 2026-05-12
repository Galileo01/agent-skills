# 相关实例初始化

## 核心 API

- **createForm**: 用于创建一个 `Form` 实例，它是表单的核心状态管理容器。
- **createSchemaField**: 用于创建 `SchemaField` 组件，用于解析 JSON Schema 或 Markup Schema。
- **observable**: 来自 `@formily/reactive`，用于创建响应式对象（如 `scopeRef`），实现跨组件状态共享。

## 组件引入

**重要提示**：在编写代码前，请务必检查 `package.json` 依赖。

- 优先使用 `@formily/antd`。

```tsx
import * as React  from 'react';
//  组件引入 
import { createForm } from '@formily/core';
import { createSchemaField } from '@formily/react';
import { observable } from '@formily/reactive';
import { Form, FormItem, Input, Select, Radio, Checkbox } from '@formily/antd';

// SchemaField 在组件外部创建
const SchemaField = createSchemaField({
  components: {
    Input,
    Select,
    Radio,
    Checkbox,
    FormItem,
  },
});

export const App = () => {
  // Form 在组件外部创建
  // 默认可以 使用 useMemo承接 Form 实例，避免重复渲染导致的性能问题
  // 推荐 传入泛型，避免类型错误
  const form = React.useMemo(() => createForm<FormValues>(), []);
  // form 实例明确会重新创建的场景（例如动态schema） ，使用 useState
  const [form, setForm] = React.useState(() => createForm<FormValues>());

  // SchemaField 下需要使用 scope 得情况 ，使用 observable 创建  ，React.useRef 接收
  const scopeRef = React.useRef<Scope>(observable({}));
};

```
