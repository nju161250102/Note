# Vue.js

## Vue基础
### 实例
```
var app = new Vue({
  //选项
})
```
通过构造函数启动根实例  
* 选项el：HTML元素/css选择器
* 选项data：声明应用内需要双向绑定的数据，可以指向已有对象

### 生命周期
* created：实例创建完成后调用
* mounted：el挂载后调用
* beforeDestory：销毁前调用
使用回调函数  

### 插值
双大括号显示实时绑定数据  
输出HTML：`<span v-html="dataLabel">`  
输出大括号不替换：v-pre  

### 表达式
JavaScript表达式  
不支持语句和流控制  

### 过滤器
插值尾部加|实现过滤  
```
filters {
	name: function(){}
}
```
可以串联  
可以接收参数  
用于处理简单文本转换  

### 指令
v-if：判断v-if="data"是否显示元素，搭配v-else, v-else-if  
判断多个元素使用template  
尽可能复用元素而不是重新渲染   
增加唯一的key属性避免复用  
v-for = "item in items"，items为data中的数据  
可选参数-索引："(item,index) in items"  
可以遍历对象的属性  
v-bind：动态更新HTML元素的属性  
v-on：绑定事件监听器，v-on:事件="方法名/内联语句"    
语法糖：v-bind: -> :, v-on: -> @  

### 计算属性
computed: name:function(){}  
使用get/set在修改时触发  
可以依据其他实例的属性  

### css绑定
v-bind:class = {'': classname}  
每项表达式为真时，对应的类名就会加载，可以绑定计算属性  
v-bind:class = "[a,b...]"  
绑定列表中多个元素  
v-bind:style = "{'css':x, ...}"  
一般写在data或者computed里  

### 数组更新
响应变化：push, pop, shift, unshift, splice, sort, reverse  
返回新数组：filter, concat, slice  
使用计算属性过滤/排序时不会改变原数组  

### 方法与事件
v-on:click @click  
可以传入特殊变量$event访问原生DOM事件  
修饰符：  
* .stop阻止事件冒泡