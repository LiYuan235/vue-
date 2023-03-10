# 计算属性-computed

之前的插值语法和方法绑定都可以实现计算属性的功能，但是效率不高

```
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>姓名案例——methods</title>
    <script src="../vue.js"></script>
</head>
<body>
    <!-- 准备好一个容器 -->
    <div id="root">
        姓:<input type="text" v-model="firstname"><br><br> 
        名:<input type="text" v-model="lastname"><br><br>
        姓名：<span>{{fullName}}</span>
    </div>
    <script type="text/javascript">
        Vue.config.ProductionTip=false
        let vm=new Vue(
            {
                el:'#root',
                data:{
                    firstname:"Li",
                    lastname:"Ming"
                },
                computed:{
                    fullName:{
                        //当有人初次读取fullName时，get就会调用
                        //所依赖的数据发生变化的时候,get就会调用
                        get(){
                            return this.firstname+'-'+this.lastname
                        },
                        //当fullname被修改时，set被调用
                        set(value){
                            const arr=value.split('-')
                            this.firstname=arr[0]
                            this.lastname=arr[1]
                            return value
                        }
                    }
                }
            }
        )
    </script>
</body>
</html>
```
**总结**
1. 定义：要用的属性不存在，要通过已有属性计算得到；
2. 原理：底层借助了Object.defineproperty方法提供的getter和setter;
3. get函数什么时候执行？
    - 初次读取的时候会执行一次；
    - 当依赖的数据发生变化的时候会再次调用；
4. 优势：和methods实现相比，内部存在缓存机制（复用），效率更高，调试方便；
5. 备注：
    - 计算属性最终会出现在vm上，直接读取使用；
    - 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时以来的数据发生变化；

# 监视属性
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>天气案例</title>
    <script src="../vue.js"></script>
</head>
<body>
    <!-- 准备好一个容器 -->
    <div id="root">
        <h2>今天天气很{{info}}</h2>
        <button @click="changeWeather">切换天气</button>
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false
        new Vue({
            el:'#root',
            data:{
                isHot:true
            },
            computed:{
                info:{get(){
                    return this.isHot?'炎热':'凉爽'
                }}
            },
            methods: {
                changeWeather(){
                    this.isHot=!this.isHot
                }
            },
            watch:{
                isHot:{
                    immediate:true,//初始化时让handler调用一下
                    //handler什么时候调用？当isHot发生改变时
                    handler(newValue,oldValue){
                        console.log("isHot发生了改变",newValue,oldValue)
                    }
                }
            }
        })
    </script>
</body>
</html>
```
## 深度监视
```
deep:true
```
**总结**
1. 深度监视
    - Vue中的watch默认不监测对象内部的改变（一层）；
    - 配置deep:true可以监测对象内部的改变（多层）；
2. 备注：
    - Vue自身可以监测对象内部值的改变，但vue提供的watch默认不可以；
    - 使用watch时候根据数据的具体结构决定是否采用深度监视；
## Computed与watch的区别
1. computed能完成的功能，watch都可以完成；
2. watch能完成的功能，computed不一定能完成，例如：watch可以执行异步操作；
*两个重要的小原则：*
    - 所被vue管理的函数，最好写成普通函数的形式，这样this的指向才是vm或者组件实例对象；
    - 所有不被vue管理的函数（定时器的回调，ajax的回调）最好写成箭头函数的形式，这样this的指向才是vm或者组件实例对象；
# 绑定样式
