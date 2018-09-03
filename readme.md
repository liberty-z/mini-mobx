
现代前端框架都引入了数据的响应式系统：模型层只是普通的js对象，修改它则自动更新视图，这让状态管理简单而直观。

mobx是一个响应式的状态管理库，我按照自己的思路实现了几个大概的核心功能，加了一个watch的语法。依托于es6的适配器。

实现的思路大致如下：

**observable**：**劫持得到变量的访问和赋值权限**，首先将对象和对象的属性通过 `Object.defineProperty` 中的   `set` 和 `get` 做劫持。将原变量的值保存到一个新的 `value` 上，那么每次对该变量的取值或者赋值操作都会走劫持的 `get` 或者 `set` 方法。当变量在执行 `get` 方法时，如果当前有观察者在观察，就会将该观察者加入到该变量的观察者数组中，在执行 `set` 方法时，就会触发该变量所有观察者数组的值。

**computed**:  **computed方法内依赖的observable一旦发生了变化，就会重新计算该computed的值**，computed里面的方法为观察者，里面依赖的observable变量为被观察者。**收集依赖**，让computed方法开始观察，然后执行computed方法，这个时候computed方法内部的所有变量都会调用一次get( 因为在执行方法，所以方法里面的变量都要取值，都走get)，这个时候内部的observable就会把computed方法作为观察者加入到自己的观察者数组中，然后computed方法结束观察。

**watch**:  **watch的变量一旦发生变化，watch的方法体就会执行**。watch方法开始观察，然后通过访问一下watch的变量，让watch的变量执行get，然后该变量将watch的方法加入到自己的观察者数组中，然后watch方法结束观察。

中间集成了一个管理**observable**和**watch**之间映射的方法，用来管理依赖。

#### 数组的特殊响应化处理(还未处理)