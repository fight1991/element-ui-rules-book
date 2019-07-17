# element-ui-rules-book
element-ui表单校验注意事项-表格为动态添加

#### 1.问题: 表单录入根据数据动态渲染时
将单独的rules绑定在 el-form-item上 通过prop--- 遍历的属性名 + index + 要检验的字段名>>>详见elementui文档
#### 2.解决方式:
> 校验规则的层数 和 绑定的数据结构层数 要相同 
```js 
    rules:{name:.....}
    data: {
      name: 'zs'
    }
```
> 表单校验根据前面的数据来添加或取消校验时的注意事项:
```js
// 不能通过
:rules="true && {required:true,message:'必填',tricker:'blur'}" 
// dom渲染时 规则已绑定完成.通过控制true或false来控制,会导致规没有注册到表单                                                                   
// 可以通过  
 :rules="{required:true/false}" // 控制required项
```
