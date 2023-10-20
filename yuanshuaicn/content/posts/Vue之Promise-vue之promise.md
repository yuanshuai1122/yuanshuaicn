---
title: Vue之Promise
date: 2021-11-30 10:45:00.461
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Promise

## 一、简单介绍

在介绍Promise之前，得向大家解析几个名词

##### 1.同步和异步

**① 同步**
  当用户使用js和浏览器发生交互时，执行到某一个模块时系统发现需要向服务器提供网络请求，这个时候，js操作就会被阻塞，然后浏览器向服务器发送网络请求。
  我们都知道网络请求的速度会比较慢，在此期间，不管用户执行任何操作，浏览器都不会去执行，因为此时的浏览器正在向服务器发送请求，没有空去理会别的操作，这就是同步，简单可以理解成**浏览器的执行是按照某中顺序执行的，只有等上一步完成之后才会继续执行下一步操作**。
**② 异步**
  异步的含义和同步恰恰相反。当用户和浏览器发生交互，执行到某一模块的时候发现需要向服务器发送网络请求时，这个时候，浏览器向服务器发送请求之后，仍然可以执行别的操作。
  当浏览器向服务器发送的请求得到回应后，我们一般会声明一个函数，将请求的结果放到该函数中，用户执行完某些操作后再回调该函数就可以得到向服务器发送网络请求的数据。
  这就是异步，简单的可以理解成一心二用：**一边向服务器发送请求，一边执行相关的操作，最后通过回调某个函数来得到向服务器发动请求的数据。**如果只是一个简单的网络请求，这种方案没有什么麻烦，但是当网络请求变得复杂的时候，就会出现回调地狱 。

##### 2.回调地狱

简单的理解就是**函数的迭代**。我们举一个简单的例子。比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/394.png)

我们需要通过一个url1从服务器加载一个数据data1，发现data1并不是最终的结果而是包含了下一个请求的url2，然后通过data1取出url2，从服务器加载数据data2，data2中包含了下一个请求的url3，接着通过data2取出url3，从服务器加载数据data3，data3中包含了下一个请求的url4，最后发送网络请求url4，获取最终的数据data4。
  但是如果我们用Promise就不会出现回调地狱，下面我们一起看看什么是promise以及语法是什么

## 二、基本语法

##### 1.基本定义

  Promise是**异步编程**的一种解决方案。它是一个**类**，可以通过new的方式创建一个对象。

##### 2.使用场景

  正如它的基本定义一样，一般用在异步请求的场合，并且会将请求数据的模块放在一个地方，处理数据的模块放在另外一个地方，就不会像之前回调函数一样，将请求url数据和处理data1数据都放在同一个地方，小编在下面会给出具体的代码，目前只需要明白两点：
**第一：promise用来处理异步编程**
**第二：promise将请求模块和处理模块分开**
下面我们来看看promise如何使用

##### 3.使用语法

**① 使用new创建**
  我们知道promise是一个类，所以我们可以通过new的方式来创建promise，并且**创建的promise是一个函数**
**② 传入参数**
  当我们通过new方式创建promise函数时，就会被要求传入两个参数：**resolve和reject**，而这两个参数**本身又是一个函数**
**③ then函数**
  我们创建完promise之后，将有关的请求数据放在promise内部，然后将处理数据放在then函数，并且当用户执行了resolve函数就会调用then函数。更神奇的是，**then本身又是一个函数**。
有点抽象，我们看看下面的例子：

```js
setTimeout({
    console.log('hello,vue');
},1000)
```

以setTimeout为异步事件，经过一秒中后就打印“hello，vue”语句，我们可以假定 setTimeout 函数是向服务器发送的请求，而console.log('hello,vue')是对服务器发送请求的处理，下面使用promise封装过程如下：
**第一：声明promise，并将异步事件全部丢到promise函数中**

```js
new Promise((resolve,reject)=>{
    setTimeout({
        console.log('hello,vue');
    },1000)
})
```

上述代码中，创建了promise函数，两个参数resolve和reject由于本身又是一个函数，所以在这里使用**箭头函数**来声明，然后将异步事件全部丢进promise函数内部中。
**第二：封装数据**

```js
new Promise((resolve,reject) =>{
    setTimeout(()=>{
        resolve()
    },1000)
}).then(()=>{
    console.log('hello,vue')
})
```

上述代码中，setTimeout函数看作请求，该请求是经过1秒后执行打印语句，将请求放在了promie内部，然后打印语句看作是对请求的处理，放在了then函数中。执行顺序如下：**首先进入到promise内部，经过一秒中后执行resolve函数，该函数就会回调then函数，执行then函数内部的打印语句。**其效果如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/395.png)

在控制台中确实执行了打印语句。
是的，或许有会小伙伴问到，如果网络数据请求失败了怎么办？没关系，Promise中还有另外两个函数 reject 和catch。
reject的用法和resolve的用法是一样的，不一样的是，当请求某些数据失败的时候就会执行reject函数，该函数就会回调catch函数，具体代码如下：

```js
<script>
    new Promise((resolve,reject) =>{
        setTimeout(()=>{
            reject()
        },1000)
    }).catch(()=>{
        console.log('error')
    })
</script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/396.png)

因此，针对上述的内容，我们知道，Promise会将不同的处理交给不同的函数去执行，如果网络请求成功，就执行resolve函数，然后回调then函数；如果网络请求失败，就会执行reject函数，然后回调catch函数。所以代码的逻辑就变得十分的清晰明了。

#### 用promise改变过后的回调地狱

```js
<script>
    new Promise((resolve,reject) =>{
        setTimeout(()=>{
            resolve()
        },1000)
    }).then(()=>{
        console.log('hello,vue')
        return new Promise((resolve,reject) =>{
                setTimeout(()=>{
                    resolve()
                },1000)
            })
        }).then(() =>{
            console.log('hello,java')
            return new Promise((resolve,reject) =>{
                setTimeout(()=>{
                        resolve()
                    },1000)
                })
            }).then(() =>{
                console.log('hello,JavaScript')
            })
</script>
```

# Promise状态和链式调用

## 一、三种状态

##### 1.pending-等待状态

当浏览器向服务器**发送网络请求时**，就会处于该状态

##### 2.fulfill-满足状态

当我们主动**回调了resolve**时，就处于该状态，并且会回调.then()

##### 3.reject-拒绝状态

当我们主动**回调了reject时**，就处于该状态，并且会回调.catch()
  这三种状态大家伙大概了解一下就行 (✿◡‿◡)下面小编通过一张图来进一步为大家分析Promise的执行过程，该图如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/397.png)

Async operation 代表异步操作，首先会将有关异步处理事件打包好之后发送到Promise内部，这个时候Promise分别会经过下面三个状态:
**第一**,当向服务器发送的请求不可能立马得到回应，在发送请求和请求得到回应的阶段就是**处于Pening等待状态**；
**第二**，当请求回来的数据被成功接收之后，就**处于Fulfilled状态**，该状态下的Promise会执行resolve函数，然后回调then函数执行相关的请求处理；
**第三**，当请求被**拒绝**的时候，就**处于reject状态**，该状态下的Promise会执行reject函数，然后回调catch函数进行相关的请求处理。



此外，在这里向大家伙介绍Promise的另外一种写法：

```js
new Promise((resolve,reject) =>{
    setTimeout(()=>{
        resolve();
        reject()
    },1000)
}).then(()=>{
        console.log('hello,vue')
    },()=>{
        console.log('error message')
    })
```

这种写法就是**给then传递两个参数，第一个参数是处理请求成功的逻辑，第二个参数是处理请求失败的逻辑**，为什么可以这么缩写呢，因为在then的源码中就表示可以传递两个参数，不信我们一起来看看then的源码：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/398.png)

可以看到，有两个状态 onfulfilled 和 onrejected，意思就是当请求成功的时候执行第一个函数的内容，当请求失败的时候执行第二个函数的内容。
以上便是Promise简单的运行过程和三个基本状态，下面我们来看看Promise的链式调用。

## 二、链式调用

##### 1.链式调用1

这个链式调用呢，很简单，就是上次说要给大家伙看的彩蛋，其代码如下：

```js
<script>
    new Promise((resolve,reject) =>{
        setTimeout(()=>{
            resolve()
        },1000)
    }).then(()=>{
        console.log('hello,vue')
        return new Promise((resolve,reject) =>{
                setTimeout(()=>{
                    resolve()
                },1000)
            })
        }).then(() =>{
            console.log('hello,java')
            return new Promise((resolve,reject) =>{
                setTimeout(()=>{
                        resolve()
                    },1000)
                })
            }).then(() =>{
                console.log('hello,JavaScript')
            })
</script>
```

上述代码的执行步骤是，首先执行resolve函数调用then函数，先执行第一个then函数的打印语句，执行完成之后发现下面还有一个异步事件的操作，然后执行第二个异步事件的resolve函数来调用第二个then函数的打印语句，执行完成之后发现下面还有一个异步事件的操作，然后再次去执行第三个异步事件的resolve函数调用第三个then函数执行打印语句。
这就是链式调用的一种方式，下面小编为大家介绍另外一种方式

##### 2.链式调用2

**① 版本一**
  现在我们有一个需求，当向网络请求数据得到的结果是aaa时，进行两步操作，**第一步是自身对别的业务逻辑进行处理，第二是对拿到的结果aaa进行处理**，然后不断循环该过程，我们通过链式调用来达到这种目的，具体代码如下：

```js
<script>
    new Promise(resolve =>{
                setTimeout(()=>{
                    resolve('aaa')
                },1000)
            }).then(res =>{
                // 1.自己处理别的业务的代码
                console.log(res,'第一次自己处理别的业务的代码')
                // 2.对拿到的结果进行处理
                return new Promise(resolve =>{
                    resolve(res +'111')
                })
            }).then(res=>{
                console.log(res,'第二次自己处理别的业务的代码')
                return new Promise(resolve =>{
                    resolve(res +'222')
                })
            }).then(res =>{
                console.log(res,'第三次自己处理别的业务的代码')
            })
</script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/399.png)

上述代码中，之所以要将对结果的处理再放到一个Promise中，是因为不想和自己处理别的业务逻辑的代码放在一块，不然容易分辨不清那个模块处理什么内容。
**② 版本二**
上述的代码还可以进行简化，简化版如下：

```js
new Promise(resolve =>{
                setTimeout(()=>{
                    resolve('aaa')
                },1000)
            }).then(res =>{
                console.log(res,'第一次自己处理别的业务的代码')
                return  Promise.resolve(res +'111')
            }).then(res=>{
                console.log(res,'第二次自己处理别的业务的代码')
                return Promise.resolve(res +'222')
            }).then(res =>{
                console.log(res,'第三次自己处理别的业务的代码')   
            })
```

只是将上述代码中对结果处理的Promise进行了简化，不需要通过new Promise 的方式来获取resolve函数，而是直接通过Promise.resolve(）的方式来获取。
**③ 版本三**
另外还有一个更加简化版的，具体代码如下：

```js
new Promise(resolve =>{
                setTimeout(()=>{
                    resolve('aaa')
                },1000)
            }).then(res =>{
                console.log(res,'第一次自己处理别的业务的代码')
                return  res +'111'
            }).then(res=>{
                console.log(res,'第二次自己处理别的业务的代码')
                return res +'222'
            }).then(res =>{
                console.log(res,'第三次自己处理别的业务的代码')   
            })
```

这次的代码是直接将Promise.resolve都省略了，让他们内部自己运行Promise.resolve（）。三个简化版的结构是一样的，这里就不展示出来了。
  另外还有一个需要注意的问题，**如果是调用了reject函数，会直接跳转去执行catch函数，中间的代码就不会被执行了**，比如：

```jsx
new Promise(resolve =>{
                setTimeout(()=>{
                    resolve('aaa')
                },1000)
            }).then(res =>{
                console.log(res,'第一次自己处理别的业务的代码')
                return  Promise.reject(res +'111')
            }).then(res=>{
                console.log(res,'第二次自己处理别的业务的代码')
                return Promise.resolve(res +'222')
            }).then(res =>{
                console.log(res,'第三次自己处理别的业务的代码')   
            }).catch(()=>{
                console.log('error')    
            })
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/400.png)

总结如下：
**1.三种状态
2.链式调用的两种方式
3.链式调用方式的简化版**

# Promise的all方法

# 一、Promise的all方法

##### 1.应用场景

  假设现在有一个需求，该需求依赖与两个网络请求，只有当两个网络请求成功后才能实现这个需求。我们可以通过all方法来实现，具体见下面的分析。

##### 2.本质

  Promise中的all方法，是一个**函数**，并且传入的参数必须是**可迭代对象**，所谓的可迭代对象就是可以遍历的。

##### 3.使用方式

针对上面的需求，用all方法实现的具体代码如下：

```js
<script>
    Promise.all([
        new Promise((resolve,reject)=>{
            setTimeout(()=>{
                resolve('results1')
                },1000)
        }),
        new Promise((resolve,reject)=>{
            setTimeout(()=>{
                resolve('results2')
                },2000)
        })
    ]).then(results =>{
        console.log(results)
    })
</script>
```

针对上述的代码，从all、then两个函数入手，解析如下：
  首先，all 函数传入的参数定义为**数组类型**（数组是可迭代对象），将两个网络请求分别放在 promise 中。也可以简单的看成如下结构：Promise.all（[网络请求1，网络请求2]）
  然后就是then函数。then函数传入的**参数也是数组类型**，分别存放上述两个 resolve 的结果，所以最终在控制台上输出的结果如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/401.png)

以上便是Promise中all方法的简单介绍，下面结合前两天的内容，总结一下有关Promise的知识

# 二、Promise总结

##### 1.概念

  Promise是**异步编程**的一种解决方案，通过将**数据请求和数据处理分开**来达到代码逻辑清晰的目的 o(￣▽￣)ｄ

##### 2.本质

  Promise本质是一个**类**，所以可以通过new的方式来创建Promise。另外创建的Promise中，根据自己代码的需要传递两个参数 **resolve 和 reject**，这两个参数本身又是一个函数。最后是有关处理的两个函数** then 和 catch**

##### 3.三种状态

三种状态分别是：**Pending、Fulfilled、Reject。**
  **Pending--等待状态**，浏览器向服务器发送请求到首届请求结果的过程就处于该状态；**Fulfilled--满足状态**，浏览器成功接收服务器发送的请求结果时，就处于该状态，并且会执行resolve函数；**Reject--拒绝状态**，浏览器不接受服务器发送的请求时，就处于该状态，并且会执行reject函数。

##### 4.执行过程

下面结合一张图来分析Promise 的执行过程

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/402.png)

首先将**异步事件**的操作 Async operation **打包**到Promise中，Promise就会对异步事件进行处理，处理时分别经历一下几个状态：
  第一是**向服务器发送网络请求**，这个时候的Promise处于Pending状态，直到得到了网络请求的结果就会跳转到下一个状态；
  第二是**对网络请求的结果分别进行判断**。如果**成功接收**该结果，Promise就**处于Fulfilled状态**，该状态下的Promise会执行resolve函数并回调then函数执行相关的操作；如果**拒绝接收**该结果，Prmise就**处于Rejected状态**，该状态下的Promie就调用reject函数回调catch函数执行相关的处理。



##### 5.链式处理

所谓链式处理可以简单的理解成是**函数的迭代调用**，有四种写法，分别如下：

```jsx
            // 链式调用1
            new Promise((resolve,reject) =>{
                    setTimeout(()=>{
                        resolve()
                    },1000)
                }).then(()=>{
                    console.log('hello,vue')
                    return new Promise((resolve,reject) =>{
                            setTimeout(()=>{
                                resolve()
                            },1000)
                        })
                    }).then(() =>{
                        console.log('hello,java')
                        return new Promise((resolve,reject) =>{
                            setTimeout(()=>{
                                    resolve()
                                },1000)
                            })
                        }).then(() =>{
                            console.log('hello,JavaScript')
                        })
            
            // 链式调用2-1
            new Promise(resolve =>{
                setTimeout(()=>{
                    resolve('aaa')
                },1000)
            }).then(res =>{
                // 1.自己处理别的业务的代码
                console.log(res,'第一次自己处理别的业务的代码')
                
                // 2.对拿到的结果进行处理
                return new Promise(resolve =>{
                    resolve(res +'111')
                })
            }).then(res=>{
                console.log(res,'第二次自己处理别的业务的代码')
                
                return new Promise(resolve =>{
                    resolve(res +'222')
                })
            }).then(res =>{
                console.log(res,'第三次自己处理别的业务的代码')
            })
            
            // 链式调用2-2
            new Promise(resolve =>{
                setTimeout(()=>{
                    resolve('aaa')
                },1000)
            }).then(res =>{
                console.log(res,'第一次自己处理别的业务的代码')
                return  Promise.resolve(res +'111')
            }).then(res=>{
                console.log(res,'第二次自己处理别的业务的代码')
                return Promise.resolve(res +'222')
            }).then(res =>{
                console.log(res,'第三次自己处理别的业务的代码')   
            })
            
            // 链式调用2-3
            new Promise(resolve =>{
                setTimeout(()=>{
                    resolve('aaa')
                },1000)
            }).then(res =>{
                console.log(res,'第一次自己处理别的业务的代码')
                return  res +'111'
            }).then(res=>{
                console.log(res,'第二次自己处理别的业务的代码')
                return res +'222'
            }).then(res =>{
                console.log(res,'第三次自己处理别的业务的代码')   
            })
```

##### 6.all方法

  all方法主要用来解决**一个需求依赖与多个网络请求的情境**，只有得到多个的网络请求的结果才能实现需求。

以上就是Promise知识点的总结，有不恰当的地方还请大家指出