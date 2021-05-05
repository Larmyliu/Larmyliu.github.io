## js宽松比较中的类型转换（x==y）

1. 字符串和数字之间的比较
   1. x是数字，y是字符串，返回x==ToNumber(y)
   2. x是字符串，y是数字，返回ToNumber(x)==y
2. 其他类型和布尔型之间的比较
   1. x是其他类型，y是布尔型，返回x==ToNumber(y)
   2. x是布尔型，y是其他类型，返回ToNumber(x)==y
3. null和undefined之间的比较
   1. 不论x，y的位置，都返回true
4. 对象和非对象之间的比较
   1. x是字符串或数字，y是对象，返回x==Toprimitive(y)
   2. x是对象，y是字符串或数字，返回Toprimitive(x)==y


注意：在强转换过程中，`[2].toString() ===2, [null].toString()===""`

算法细节：

ReturnIfAbrupt(x).

ReturnIfAbrupt(y).

If Type(x) is the same as Type(y), then
Return the result of performing Strict Equality Comparisonx === y.

If x is null and y is undefined, return true.

If x is undefined and y is null, return true.

If Type(x) is Number and Type(y) is String,
return the result of the comparison x == ToNumber(y).

If Type(x) is String and Type(y) is Number,
return the result of the comparison ToNumber(x) == y.

If Type(x) is Boolean, return the result of the comparisonToNumber(x) == y.

If Type(y) is Boolean, return the result of the comparison x == ToNumber(y).

If Type(x) is either String, Number, or Symbol and Type(y)is Object, then
return the result of the comparison x == ToPrimitive(y).

If Type(x) is Object and Type(y) is either String, Number, or Symbol, then
return the result of the comparison ToPrimitive(x) == y.

Return false



1. 如果x不是正常值（比如抛出一个错误），中断执行。
2. 如果y不是正常值，中断执行。
3. 如果Type(x)与Type(y)相同，执行严格相等运算x === y。
4. 如果x是null，y是undefined，返回true。
5. 如果x是undefined，y是null，返回true。
6. 如果Type(x)是数值，Type(y)是字符串，返回x == ToNumber(y)的结果。
7. 如果Type(x)是字符串，Type(y)是数值，返回ToNumber(x) == y的结果。
8. 如果Type(x)是布尔值，返回ToNumber(x) == y的结果。
9. 如果Type(y)是布尔值，返回x == ToNumber(y)的结果。
10. 如果Type(x)是字符串或数值或Symbol值，Type(y)是对象，返回x == ToPrimitive(y)的结果。
11. 如果Type(x)是对象，Type(y)是字符串或数值或Symbol值，返回ToPrimitive(x) == y的结果。

Alex Dorey的比较图![在这里插入图片描述](https://img-blog.csdnimg.cn/20200306234312567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
