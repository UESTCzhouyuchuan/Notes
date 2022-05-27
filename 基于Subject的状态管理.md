## 核心概念

- 观察者类 Subject
    - 含有SubjectSubscription 数组
    - 可订阅事件 subscribe 可只执行一次的订阅事件
    - next函数执行所有订阅的事件（可多次执行）
- 订阅者/被观察者 + 观察者 组合类 SubjectSubscription
    - 可记录观察者subject和被观察者的回调函数
    - 含有unsubscribe 方法，会调用subject的removeObservers方法
- ReplaySubject Subejct的扩展类
    - bufferSize控制 事件多少，超过就移出头部并执行
    - clearSize控制时间数组长度，超出就保留后面的，clearSize是bufferSize的10倍

- BaseContext Context的基类
    - dispatch函数，获得更新后的state值，然后调用next函数触发回调
    - useContextState中，会将更新函数放入observer队列中，每次dispach都会更新state
    - useContextDeclaration 创建一个Context实例，也就是baseContext对象的实例
    - useContextInstance 复用原先的context 也就是useContextDeclaration创建出来的实例
        - 这个应用在深层组件中
        - 需要调用Context.Provider方法把后代组件包裹进去。