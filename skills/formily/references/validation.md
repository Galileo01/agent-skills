# 表单校验 (Form Validation)

Formily 支持多种形式的校验配置，通过 `x-validator` 属性传入，支持字符串、对象、函数三种格式，也支持数组组合使用。
更多用法  参照 [fieldvalidator](https://core.formilyjs.org/zh-CN/api/models/field#fieldvalidator)

## 1. 字符串型校验器 (ValidatorFormats)

直接使用 Formily 内置或自定义注册的格式字符串。
常见内置格式：`url`, `email`, `ipv6`, `ipv4`, `number`, `integer`, `qq`, `phone`, `idcard`, `money`, `zh`, `date`, `zip` 等。实现逻辑参考 [源码formats.ts](https://github.com/alibaba/formily/blob/master/packages/validator/src/formats.ts)

```tsx
<SchemaField.String
  name="email"
  title="邮箱"
  x-validator="email" 
/>
// 或者组合使用
<SchemaField.String
  name="phone"
  title="手机号"
  x-validator={['phone', 'number']}
/>
```

## 2. 对象型校验器 (IValidatorRules)

使用对象配置更详细的校验规则，如必填、正则、最大/最小值、长度限制等。

```typescript
//对象型校验器
interface IValidatorRules<Context = any> {
  triggerType?: 'onInput' | 'onFocus' | 'onBlur' // 触发时机，或者其他自定义的触发时机
  format?: ValidatorFormats // 上一节 介绍的内置格式 或者 自定义的格式
  validator?: ValidatorFunction<Context> // 函数型的校验器
  required?: boolean
  pattern?: RegExp | string
  max?: number   // 数字/数组长度/字符串长度 <= 某个数值  
  maximum?: number
  exclusiveMaximum?: number // 数字/数组长度/字符串长度 < 某个数值  , 无法取等
  exclusiveMinimum?: number // “exclusive” 排他的, 理解为 不含……的
  minimum?: number
  min?: number
  len?: number // 数字/数组长度/字符串长度 等于某个数值
  whitespace?: boolean // 不允许 为纯空白字符串  例如， "     " ，用于校验字符串
  enum?: any[]  // 在某些枚举范围内
  const?: any  // 为 某个具体的值
  multipleOf?: number  //  能被某个数整除
  uniqueItems?: boolean //  要求 数组内每个元素 都是唯一的
                        // isEqual判断逻辑见 源码-compare.ts ,支持 普通类型和复杂类型 
  maxProperties?: number // 要求 对象类型的 key 的个数 不大于某个数值
  minProperties?: number
  maxItems?: number // 实现上 同max， 推荐用于  数组元素个数校验
  maxLength?: number  // 实现上 同max， 推荐用于 字符串长度校验
  minItems?: number
  minLength?: number
  message?: string
  [key: string]: any 
}
```

```tsx
<SchemaField.String
  name="username"
  title="用户名"
  required
  x-validator={{
    required: true,
    whitespace: true,
    message: '用户名不能为空',
    pattern: /^[a-zA-Z0-9]+$/, // 正则匹配
    max: 10,
    min: 4,
  }}
/>
```

## 3. 函数型校验器 (ValidatorFunction)

使用函数进行自定义复杂逻辑校验，支持同步和异步校验。

函数签名：`type ValidatorFunction<Context = any> = (value: any, rule: IValidatorRules<Context>, ctx: Context, render: (message: string, scope?: any) => string) => ValidatorFunctionResponse | Promise<ValidatorFunctionResponse> | null;`

`ctx` 参数包含当前上下文信息：

```typescript
type Context = {
  field: GeneralField; // 当前 field 实例，可用于查询其他字段
  form: Form;          // form 实例
  value: any;          // 当前值
}
```

**示例：**

```tsx
<SchemaField.String
  name="password"
  title="密码"
  required
  x-decorator="FormItem"
  x-component="Input"
/>
<SchemaField.String
  name="confirm_password"
  title="确认密码"
  required
  x-decorator="FormItem"
  x-component="Input"
  x-validator={(value, rule, ctx) => {
    // 利用 ctx.field.query 查询其他字段
    const password = ctx.field.query('.password').value();
    
    if (value !== password) {
      return '两次输入的密码不一致';
    }
    return '';
  }}
/>

<SchemaField.String
  name="async_check"
  title="异步校验"
  x-validator={(value) => {
    return new Promise((resolve) => {
      setTimeout(() => {
        if (value === 'error') {
          resolve('校验失败');
        } else {
          resolve('');
        }
      }, 100);
    });
  }}
/>
```

## 组合使用示例

可以将上述三种形式组合在数组中使用：

```tsx
<SchemaField.String
  name="mixed"
  title="组合校验"
  required
  x-validator={[
    'email', // 字符串型
    { max: 50, message: '最长50个字符' }, // 对象型
    (value) => value.includes('test') ? '不能包含test' : '' // 函数型
  ]}
/>
```

## 触发校验

### `form.validate()` vs `form.submit()`

| 方法 | 描述 | 返回值 | 典型场景 |
| --- | --- | --- | --- |
| `form.validate()` | 纯粹执行校验逻辑，**不会触发提交回调**。 | `Promise<void>` (成功时 resolve，失败时 reject) | 需要检查表单状态但不提交数据时（如分步表单的“下一步”检查）。 |
| `form.submit()` | 执行校验，**校验通过后会触发 `onSubmit` 回调**。 | `Promise<any>` (返回 onSubmit 的结果) | 点击“提交”按钮，完成数据保存。 |

生成校验逻辑代码前，根据用户给到的业务场景，若涉及提交数据或未提到具体场景，直接使用 `form.submit()`；默认 使用 `form.submit()`。

**代码示例：**

```tsx
// 场景 A：仅校验
const handleNextStep = async () => {
  try {
    // 仅校验，不触发 onSubmit
    await form.validate();
    console.log('校验通过，进入下一步');
  } catch (e) {
    console.log('校验失败', e);
  }
};

// 场景 B：提交数据
const handleSubmit = async () => {
  try {
    // 校验 -> 触发 onSubmit -> 返回结果
    // 推荐传入泛型，推导 values 类型
    const values = await form.submit();
    console.log('提交成功', values);
  } catch (e) {
    console.log('提交失败（校验不通过或 onSubmit 报错）', e);
  }
};
```
