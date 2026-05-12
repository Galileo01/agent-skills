# 表单联动

Formily 提供了多种联动方式。从代码可读性和维护性角度出发，推荐优先使用函数式 `x-reactions`，复杂场景使用 `effects`，**不推荐**使用 Inline JSON 对象的 `x-reactions`。  
更多 schema 联动逻辑 参考 [schemareactions](https://react.formilyjs.org/zh-CN/api/shared/schema#schemareactions)

## 1. 优先推荐：函数式被动联动 (Functional Reactions)

最推荐的方式。直接在 `x-reactions` 属性中编写函数，逻辑直观，且支持类型提示。通常用于处理字段间的简单依赖关系（如显隐、禁用、值联动）。

**示例：**

```tsx
<SchemaField.String
  name="source"
  title="控制者"
  x-component="Select"
  enum={[{ label: '显示', value: 'show' }, { label: '隐藏', value: 'hide' }]}
/>

<SchemaField.String
  name="target"
  title="受控者"
  x-component="Input"
  x-reactions={(field) => {
    // 获取依赖字段的值
    // query 支持路径查询，'.source' 表示同级目录下的 source 字段
    const source = field.query('.source').value();
    
    // 根据依赖值控制当前字段状态
    field.visible = source === 'show';
    
    // 如果需要设置值
    if (source === 'hide') {
       field.value = '';
    }
  }}
/>
```

## 2. 复杂场景：主动联动 (Effects)

当联动逻辑非常复杂，涉及跨多个表单、异步操作、或者需要集中管理联动逻辑时，推荐使用 `effects`。这是一种事件订阅机制。
例如下边这个常见的场景：
手动 重新选择 一级分类 时 清空依赖的二级分类
需要注意 effect 参数field可能是 GeneralField 类型，需要转换为 Field。

**示例：**

```typescript
import { onFieldMount, onFieldValueChange, onFieldInputValueChange } from '@formily/core';

const form = createForm({
  effects: () => {
    // 手动 重新选择 一级分类 时 清空依赖的二级分类
    onFieldInputValueChange('category.parent_id', (field) => {
      const sub_category_field = field.query('.sub_category_id').take() as Field | undefined;

      sub_category_field?.setValue(undefined);
    });
  },
});
```

## 3. 禁止使用：对象式联动 (JSON Schema Reactions)

虽然 Formily 支持在 JSON Schema 中通过表达式字符串定义联动，但这种方式**可读性极差**，难以调试，且容易出现语法错误。

**禁止使用对象形式的 `x-reactions`。请一律使用函数式 `x-reactions` 或 `effects` 替代。**

**反例（禁止生成）：**

```tsx
// ❌ 可读性差，禁止生成
<SchemaField.String
  name="target"
  x-reactions={{
    dependencies: ['source'],
    fulfill: {
      state: {
        visible: '{{$deps[0] === "show"}}',
      },
    },
  }}
/>

// ❌ 可读性差，禁止生成
//  这两种写法本质上相同，都禁止
<SchemaField.String
  name="target"
  x-reactions={[{
    dependencies: ['source'],
    fulfill: {
      state: {
        visible: '{{$deps[0] === "show"}}',
      },
    },
  }]}
/>
```
