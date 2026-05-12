# Formily 综合示例 (Comprehensive Example)

本示例展示了一个完整的表单开发场景，包含：

1. **TypeScript 类型定义**：定义表单数据结构。
2. **实例创建**：使用 `useMemo` 创建 Form 实例，配置 `effects` 处理复杂联动。
3. **布局结构**：使用 `FormLayout` 和 `FormGrid` 实现规范化布局。
4. **混合联动**：同时展示了 `x-reactions` (简单联动) 和 `effects` (复杂联动)。
5. **完整流程**：包含数据回填 (initialValues) 和 提交处理 (submit)。

```tsx
import * as React from 'react';
import { createForm, onFieldInputValueChange } from '@formily/core';
import { createSchemaField, FormProvider } from '@formily/react';
import { FormItem, Input, Select, DatePicker, FormLayout, FormGrid } from '@formily/antd';
import { Card, message, Button } from 'antd';

// 1. 定义表单数据类型
type FormValues = {
  username: string;
  department: string;
  role?: string;
  join_date: string;
  linkage_source: string;
  linkage_target: string;
}

// 2. 创建 SchemaField 组件
const SchemaField = createSchemaField({
  components: {
    FormItem,
    Input,
    Select,
    DatePicker,
    FormLayout,
    FormGrid,
  },
});

export const ComprehensiveForm = () => {
  // 3. 创建 Form 实例
  const form = React.useMemo(() => createForm<FormValues>({
    initialValues: {
      username: 'Guest',
    },
    // 复杂联动逻辑建议写在 effects 中
    effects: () => {
      // 示例：当部门(department)变化时，重置角色(role)
      onFieldInputValueChange('department', (field) => {
        // 使用 query 查询字段并重置值
        field.query('.role').take()?.setValue(undefined);
        // 实际业务中可能这里还需要触发 API 请求获取新的角色列表
      });
    },
  }), []);

  // 4. 提交处理
  const handleSubmit = async () => {
    try {
      // 触发校验并提交
      const values = await form.submit<FormValues>();
      console.log('Submitted values:', values);
      message.success('提交成功');
    } catch (e) {
      console.log('校验失败', e);
    }
  };

  return (
    <Card title="员工信息录入">
      <FormProvider form={form}>
        <SchemaField>
          <SchemaField.Void
            name="layout"
            x-component="FormLayout"
            x-component-props={{ labelCol: 6, wrapperCol: 18 }}
          >
            <SchemaField.Void
              name="grid"
              x-component="FormGrid"
              x-component-props={{ maxColumns: 2, columnGap: 24 }}
            >
            
              {/* 基础字段 */}
              <SchemaField.String
                name="username"
              title="员工姓名"
              required
              x-decorator="FormItem"
              x-component="Input"
              x-validator={[
                { required: true, message: '请输入姓名' },
                { max: 20, message: '最长20个字符' }
              ]}
            />

            <SchemaField.String
              name="join_date"
              title="入职日期"
              required
              x-decorator="FormItem"
              x-component="DatePicker"
            />

            {/* 联动示例 1：Effects 联动 (Department -> Role) */}
            <SchemaField.String
              name="department"
              title="所属部门"
              required
              x-decorator="FormItem"
              x-component="Select"
              enum={[
                { label: '技术部', value: 'tech' },
                { label: '产品部', value: 'product' },
              ]}
            />

            <SchemaField.String
              name="role"
              title="岗位角色"
              required
              x-decorator="FormItem"
              x-component="Select"
              // 这里的选项实际场景中可能是动态的
              enum={[
                { label: '前端开发', value: 'frontend' },
                { label: '后端开发', value: 'backend' },
                { label: '产品经理', value: 'pm' },
              ]}
            />

            {/* 联动示例 2：x-reactions 简单联动 (显隐控制) */}
            <SchemaField.String
              name="has_extra_info"
              title="补充更多信息"
              x-decorator="FormItem"
              x-component="Select"
              enum={[
                { label: '是', value: 'yes' },
                { label: '否', value: 'no' },
              ]}
            />

            <SchemaField.String
              name="extra_info"
              title="备注信息"
              x-decorator="FormItem"
              x-component="Input.TextArea"
              x-reactions={(field) => {
                const hasInfo = field.query('.has_extra_info').get('value');
                field.visible = hasInfo === 'yes';
              }}
            />

            </SchemaField.Void>
          </SchemaField.Void>
        </SchemaField>
      </FormProvider>

      {/* 按钮区 */}
      <div className="mt-6">
        <Button type="primary" onClick={handleSubmit}>提交录入</Button>
      </div>
    </Card>
  );
};
```
