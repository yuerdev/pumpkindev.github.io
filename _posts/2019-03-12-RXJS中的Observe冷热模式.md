关于RXJS的冷热观察者模式，简单的来说。他们的区别就是：冷观察者自行创建生产者，热观察使用创建好的生产者。

- 如果通知的生产者是观察者订阅 observable 时创建的，那么 observable 就是冷的。例如，`timer` observable 就是冷的，每次订阅时都会创建一个新的定时器。
- 如果通知的生产者不是每次观察者订阅 observable 时创建的，那么 observable 就是热的。例如，使用 `fromEvent` 创建的 observable 就是热的，产生事件的元素存在于 DOM 之中，它不是观察者订阅时所创建的。

冷的 observables 是单播的，每个观察者所接收到的通知都是来自不同的生产者，生产者是观察者订阅时所创建的。一般来说，angular的http请求是冷的，只有在调用subscribe时才去创建，若冷模式需要多播，在RXJS中引入了subject。

热的 observables 是多播的，每个观察者所接收到的通知都是来自同一个生产者。

## 关于冷模式

我们来看一个很普通的例子

```js
let obs = Rx.Observable.create(observer => observer.next(new Date().valueOf()));

obs.subscribe(v => console.log("1st subscriber: " + v));
obs.subscribe(v => console.log("2nd subscriber: " + v));
```

输出结果为

```js
1st subscriber: 1552406262384
2nd subscriber: 1552406262394
```

可以看出，new Date()被执行了两次，也就是意味着每次在订阅的时候。才开始新建observable

## 关于热模式

将上面代码做变化，如下

```js
let obs = Rx.Observable
            .interval(1000)
            .publish();
obs.connect();

setTimeout(() => {
  obs.subscribe(v => console.log("1st subscriber:" + v));
  setTimeout(
    () => obs.subscribe(v => console.log("2nd subscriber:" + v)), 1000);

},2000);
```

输出结果

```js
1st subscriber:2
1st subscriber:3
2nd subscriber:3
```

此时的`obs`是完完全全的热模式，它会在创建后立即产生数据而不管有没有订阅者。

## RXJS操作符（待完善）

publish,refCount,Count,Share

## 实用技巧



```js
 http.get('contacts.json')
  .map(response => response.json().items)
  .share();
```













[1]: http://link.zhihu.com/?target=https%3A//medium.com/%40benlesh/on-the-subject-of-subjects-in-rxjs-2b08b7198b93
[2]: https://blog.thoughtram.io/angular/2016/06/16/cold-vs-hot-observables.html

