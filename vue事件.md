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
