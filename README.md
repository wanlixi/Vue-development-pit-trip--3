# Vue-development-pit-trip--3
最近项目上用的移动端UI库---vux遇到的一些坑

### 应用场景非常频繁的日期选择插件和地区选择

##### 1.Datetime
这个组件很好，v-model绑定好之后，注意格式是1999-01-01的字符串，默认最小年份是2000年，这里需要设置min-year属性改变最小年限
on-change事件是日期插件弹出后，每滚动年月日任意一个都会触发，而一般用不到实时去获取日期的value值，而是点击提交表单按钮是v-model
绑定的值已经是更新过的。
```
<Datetime v-model="insuredBirthday" :min-year=1900 @on-change="changeInsuredDate" title="" :placeholder="insuredVeriTypeIsBirthCard"></Datetime>
```



##### 2.xAddress
```
<x-address @on-hide="hidePickerArea" @on-show="showPickerArea" title="" v-model="address" :list="addressData" @on-shadow-change="changePickerArea" :placeholder="addressLabel"></x-address>
```
这个组件封装比较好用，除on-shdow-change外提供了另外两个on-hide和on-show方法，on-shadow-change和上面我提到的日期插件on-change方法类似，
用不着实时获取value值，只要你滚到了这个插件，其v-model绑定的值会自动更新，但是此时获取的却不是 *xx省xx市xx区*，而是省市区对应的三个code码，
但是我们前端需要向后台传文字怎么办？

```
import { aAddress , ChinaAddressV3Data, Value2nameFilter as value2name } from 'vux
```
这里默认使用了vux集成的省市区json文件，其结构是一个一维数组：
```
[
  {
    name: '北京市',
    value: '111000'
  },
  {
    name: '东城区',
    value: '111011',
    parent: '111000'
  }
]
```
一般后台给的省市区json文件和我们的不一样，有可能是三位数组，可以让后台人员重新配置下，转换成name，value，parent的一维数组就可以了，对应后台来说应该是so easy！接下来说下 vux里面暴露出来的一个方法Value2nameFilter，这个就是用了将我们选择后获取的['1110000', '111000', '111011']转换为省市区文字，
具体使用方法如下：
```
this.address = value2name(this.address, chinaAddrssV3Data).split(' '); // '北京市 直辖市 东城区' => [ '北京市', '直辖市', '东城区']
```
这样得到的值，就可以直接赋给xAddress 的v-model可以使其自动选中
