遇到一个问题，在使用element ui的多选框组件`el-checkbox-group和el-checkbox`的时候出现了无法选中选项的事情

```javascript
<el-checkbox-group v-model="checkList" :min="less" :max="most">
               <el-checkbox v-for="(item,index) in listDate.options" :key="index" :label="item">
               </el-checkbox>
</el-checkbox-group>
```
其中，listDate是通过父组件传递过来的值，因为我要实现的是可以控制多选框组最多和最少的选项，按照`el-checkbox-group`要求，checkList是数组，如果不绑定`min和max`是可以选中的，但是反正就会出现复选框选不中的情况。

查看别人的资料和官方文档
官网的深入响应式原理已经说明了这类问题的原因，并提供了解决方法

```javascript
for (let i=0;i<this.listDate.less;i++) {
                this.$set(this.checkList, i,this.listDate.options[i])
            }
```
用$set先把最少给绑定到数组内即可
