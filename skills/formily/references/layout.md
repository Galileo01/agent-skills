# 表单布局 (Form Layout)

推荐使用 `@formily/antd` 提供的布局组件：

- **FormLayout**: 用于统管全局或局部的布局样式，如标签对齐方式 (`labelAlign`)、标签宽度 (`labelCol`)、控件宽度 (`wrapperCol`) 等。
- **FormGrid**: 用于实现栅格布局，控制字段在同一行内的排列方式（如一行多列）。
- **FormItem**: 每个字段的包裹组件，支持通过 `layout` 属性单独覆盖布局模式（如 `vertical`, `horizontal`）。

```tsx
import * as React from 'react';
import { createForm } from '@formily/core';
import { createSchemaField, FormProvider } from '@formily/react';
import { FormItem, Input, FormLayout, FormGrid } from '@formily/antd';

// SchemaField 在组件外部创建
const SchemaField = createSchemaField({
  components: {
    FormItem,
    Input,
  },
});

// 示例：全局 horizontal 布局，内部使用 FormGrid 实现一行两列
<SchemaField>
  <SchemaField.Void
    name="layout"
    x-component="FormLayout"
    x-component-props={{
      labelCol: 6,
      wrapperCol: 18,
      layout: 'horizontal',
    }}
  >
    <SchemaField.Void
      name="grid"
      x-component="FormGrid"
      x-component-props={{
        maxColumns: 2,
      }}
    >
      <SchemaField.String
        name="field1"
        title="字段1"
        x-decorator="FormItem"
        x-component="Input"
      />
      <SchemaField.String
        name="field2"
        title="字段2"
        x-decorator="FormItem"
        x-component="Input"
      />
    </SchemaField.Void>
  </SchemaField.Void>
</SchemaField>
```
