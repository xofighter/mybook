在看博客里，有个订阅功能，当你订阅后，当博主发布新的博客，你都能收到消息。这是如何实现的？是不是后台有个线程在不停的轮询？如果是这样的话，显然太耗资源，如果当博客在发布时，找到所有的订阅者，然后循环的把消息发给订阅者，这样就会显得更人性化了。这就是观察者模式，观察者模式有两个对象：博主，订阅者。当读者在订阅时，会把读者信息放到一个队列里面去，方便在博客主布时循环这个对列并发送消息。如此一分析，代码就出来了：

先是订阅者：

```
`public` `interface` `Observers {``    ` `    ``public` `void` `execute(String s);``    ` `}`
```

 

```
`public` `class` `Observer ``implements` `Observers{` `    ``@Override``    ``public` `void` `execute(String s) {``        ``System.out.println(s);``    ``}` `}`
```

 再就是发布博客中心了：

```
`public` `interface` `Stations {``    ` `    ``public` `void` `addObserver(Observers observers);``    ` `    ``public` `void` `removeObserver(Observers observers);``    ` `    ``public` `void` `notityObserver(String s);``    ` `}`
```

 

```
`public` `class` `Station ``implements` `Stations{``    ` `    ``private` `List<Observers> list = ``new` `ArrayList<Observers>();` `    ``@Override``    ``public` `void` `addObserver(Observers observers) {``        ``list.add(observers);``    ``}` `    ``@Override``    ``public` `void` `removeObserver(Observers observers) {``        ``list.remove(observers);``    ``}` `    ``@Override``    ``public` `void` `notityObserver(String s) {``        ``for` `(Observers observers : list) {``            ``observers.execute(s);``        ``}``    ``}` `}`
```

 执行一下：

```
`public` `class` `Executor {``    ` `    ``public` `static` `void` `main(String[] args) {``        ``Stations station = ``new` `Station();``        ``station.addObserver(``new` `Observer());``        ``station.addObserver(``new` `Observer());``        ``station.addObserver(``new` `Observer());``        ``station.addObserver(``new` `Observer());``        ``station.notityObserver(``"请使用zson"``);``    ``}``    ` `}`
```

 这就是整个观察者模式的基本实现了。

应用场景及作用：

1.pub/sub(发布/订阅)下使用

2.如果有两个业务模块，如果需要互相通知的话，可以用这种方式进行解耦

 