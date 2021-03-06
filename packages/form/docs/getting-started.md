---
order: 1
title: 开始使用
type: Documents
---

@delon/form 是一个基于 [JSON Schema](http://json-schema.org/) 标准的动态构建表单。

## 特性

- 符合 JSON Schema 标准
- 基于 ng-zorro-antd 基础组件库
- 秉承 Ant Design 价值观
- 二十几种小部件
- 可自定义小部件满足业务需求
- 无任何第三方依赖，可适用所有 antd 项目

## 如何阅读

在开始之前需要知道文档的一些简单编写规则：

- 代码以 `schema.` 开头的表示 JSON Schema 对象属性
- 代码以 `ui.` 开头的表示 UI 对象属性
- 部分小部件数据源分为 **静态** 和 **实时** 两类
  - **静态** 理解为 `schema.enum` 值，是符合 JSON Schema 标准，且限数组格式 `any[]`
  - **实时** 理解为 `ui.asyncData` 值，非 JSON Schema 标准，格式 `(input?: any) => Observable<SFSchemaEnumType[]>`

## 如何使用

安装 `@delon/form` 依赖包：

```bash
npm install @delon/form --save
```

导入 `DelonFormModule` 模块：

```typescript
import { DelonFormModule } from '@delon/form';

@NgModule({
    imports: [
        DelonFormModule.forRoot()
    ]
})
export class AppModule { }
```

虽然默认 `@delon/form` 校验是 [ajv](http://epoberezkin.github.io/ajv/)，但这并不是唯一的选择，你可以覆盖 `SchemaValidatorFactory` 使用其他校验类库，所以 `ajv` 并没有强制依赖；其实从另一个点来看将这种第三方类库统一在 `scripts` 加载才是更合理的，因此你依然要自己安装 `ajv` 并在 `.angular-cli.json` 中导入它：

```bash
npm i --save ajv @types/ajv
```

.angular-cli.json

```json
"scripts": [
    "../node_modules/ajv/dist/ajv.bundle.js"
]
```

**DelonFormConfig**

参数 | 说明 | 类型 | 默认值
----|------|-----|------
ajv | [ajv](http://epoberezkin.github.io/ajv/#options) 参数 | `Ajv.Options` | -
ingoreKeywords | 是否忽略某些数据类型校验 `ERRORSDEFAULT` | `string[]` | `[ 'type', 'enum' ]`
liveValidate | 是否实时校验 | `boolean` | `true`
autocomplete | 指定表单 `autocomplete` 值 | `on,off` | `null`
firstVisual | 是否立即呈现错误视觉 | `boolean` | `false`
onlyVisual | 是否只展示错误视觉不显示错误文本 | `boolean` | `false`
errors | 自定义通用错误信息 | `{ [ key: string ]: string }` | `ERRORSDEFAULT`
size | 元素组件大小，用于 `nzSize` 值 | `default,large,small` | -
button | 按钮风格 | `SFButton` | `{submit:'提交',submit_type:'primary',reset:'重置',reset_type:'default'}`
uiDateStringFormat | date小部件：`type="string"` 且不指定 `schema.format` 和 `ui.format` 时日期格式 | `string` | `YYYY-MM-DD HH:mm:ss`
uiDateNumberFormat | date小部件：`type="number"` 且不指定 `schema.format` 和 `ui.format` 时日期格式，默认：`x` 13位Unix Timestamp | `string` | `x`
uiTimeStringFormat | time小部件：`type="string"` 且不指定 `schema.format` 和 `ui.format` 时日期格式 | `string` | `HH:mm:ss`
uiTimeNumberFormat | time小部件：`type="number"` 且不指定 `schema.format` 和 `ui.format` 时日期格式，默认：`x` 13位Unix Timestamp，日期统一使用 `1970-01-01` | `string` | `x`

构建一个邮箱、姓名表单：

```ts
@Component({
    selector: 'app-home',
    template: `
    <sf [schema]="schema" (formSubmit)="submit($event)"></sf>
    `
})
export class HomeComponent {

    schema: SFSchema = {
        properties: {
            email: {
                type: 'string',
                title: '邮箱',
                format: 'email',
                maxLength: 20
            },
            name: {
                type: 'string',
                title: '姓名',
                minLength: 3
            }
        }
    };

    submit(value: any) {

    }
}
```

## sf 组件接口

| 参数         | 说明                            | 类型                        | 默认值       |
|--------------|---------------------------------|-----------------------------|--------------|
| layout       | 表单布局，等同 `nzLayout`        | `string`                    | `horizontal` |
| schema       | **必填项** JSON Schema          | `SFSchema`                  | -            |
| ui           | UI Schema                       | `SFUISchema`                | -            |
| formData     | 表单默认值                      | `any`                       | -            |
| mode         | 表单模式，细节见常见问题         | `default,search,edit`       | `default`    |
| button       | 按钮                            | `SFButton, 'none'`          | `{}`         |
| firstVisual  | 是否立即呈现错误视觉            | `boolean`                   | `true`       |
| liveValidate | 是否实时校验，`false` 提交时检验 | `boolean`                   | `true`       |
| autocomplete | 指定表单 `autocomplete` 值      | `on,off`                    | `null`       |
| formChange   | 数据变更时回调                  | `EventEmitter<{}>`          | -            |
| formSubmit   | 提交表单时回调                  | `EventEmitter<{}>`          | -            |
| formReset    | 重置表单时回调                  | `EventEmitter<{}>`          | -            |
| formError    | 表单校验结果回调                | `EventEmitter<ErrorData[]>` | -            |

### 组件方法

| 参数          | 说明             | 返回值    |
|---------------|------------------|-----------|
| valid         | 表单是否有效     | `boolean` |
| value         | 表单值         | `any`     |
| refreshSchema | 刷新 JSON Schema | `void`    |
| reset         | 重置表单         | `void`    |

### 按钮说明

**注意事项**

- 值为 `null` 或 `undefined` 表示手动添加按钮，但保留容器
- 值为 `none` 表示手动添加按钮，且不保留容器
- 使用固定 `label` 标签宽度时，若无 `render.class` 则默认为居中状态

**自定义**

当你希望自定义按钮时，务必设置 `button` 值为 `null`。

```html
<sf #sf [button]="null">
    <button type="submit" nz-button [disabled]="!sf.valid">保存</button>
    <button (click)="sf.reset()" type="button" nz-button>重置</button>
</sf>
```

## 常见问题

### mode有什么作用？

`mode` 只是快捷作用，**且设置属性的优先级高于一切**，一共分三种模式：

- `default` 默认模式，什么也不做
- `search` 搜索模式，自动设置 `layout: inline`、`firstVisual: false`、`liveValidate: false`、`button.submit: '搜索'`
- `edit` 编辑模式，自动设置 `layout: horizontal`、`firstVisual: false`、`liveValidate: true`、`button.submit: '保存'`
