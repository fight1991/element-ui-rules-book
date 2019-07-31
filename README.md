# element-ui-rules-book
element-ui表单校验注意事项-表格为动态添加

#### 1.问题: 表单录入根据数据动态渲染时
将单独的rules绑定在 el-form-item上 通过prop--- 遍历的属性名 + index + 要检验的字段名>>>详见elementui文档
```js
<el-table-column prop="feePrice" label="计费单价" align="right" min-width="140">
    <template slot-scope="scope">
        <div class="table-select align-r" v-if="optionsType === 'edit'">
          <el-form-item
            :prop="'billReceivableBodyVOList.'+ scope.$index + '.feePrice'" // 这样就能确定唯一性
            :rules="valid.price">
            <el-input clearable v-model="scope.row.feePrice" @change="computeTaxPrice(scope.row)"></el-input>
          </el-form-item>
        </div>
        <div class="cell-div" v-else>{{scope.row.feePrice || '-'}}</div>
    </template>
</el-table-column>

// 校验规则
valid: {
    price: {validator: this.priceValid, message: '小数点支持前9位,后3位', trigger: 'blur'},
    num: {validator: this.numValid, message: '小数点支持前9位,后3位', trigger: 'blur'}
  }
```
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
列如:
<el-col :span="5">
    <el-form-item label=" " label-width="10px" 
      :prop="'quotationReceivableBodyVOList.' + index1 + '.feeOptionImportVOs.' + index2 + '.curr'" 
      :rules="(item2.feeOptionName && valid.curr) || {}"> // 规则为对象或者[],不能为布尔值
      <el-select  v-model="item2.curr" placeholder="币制"
        filterable clearable remote default-first-option
        :disabled="!item2.feeOptionName"
        @focus="tipsFill('currList','SAAS_CURR')"
        :remote-method="checkParamsList"
        style="width:100%">
        <el-option
          v-for="item in currList"
          :key="item.codeField"
          :label="item.codeField + '-' + item.nameField"
          :value="item.codeField">
        </el-option>
      </el-select>
    </el-form-item>
</el-col>

valid: { // 动态表单校验规则
    feePriceTrue: {required: true, pattern: /^\d{1,10}(\.\d{1,3})?$|^$/, message: '小数点前10位以内,后3位以内', trigger: 'blur'},
    feePriceFalse: {required: false, pattern: /^\d{1,10}(\.\d{1,3})?$|^$/, message: '小数点前10位以内,后3位以内', trigger: 'blur'},
    feeRate: {required: true, message: '请选择税率', trigger: 'change'},
    curr: {required: true, message: '请选择币制', trigger: 'change'},
    unit: {required: true, message: '请选择计量单位', trigger: 'change'}
  }
```
