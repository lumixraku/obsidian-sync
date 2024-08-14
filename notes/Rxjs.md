这里是我平常会用到的 Rxjs 中的 API。

先介绍 pipe 以及 pipe 中高频使用 Operator
## pipe
用于将多个操作符组合在一起,形成一个可复用的数据处理管道的机制。

example
```
// 只有 focus 为 true 才进入到 subscribe
focus$.pipe(filter((f) => !!f)).subscribe(() => {
    //...
})
```

pipe 中的参数类型是 OperatorFunction， map  distinctUnitChanged 等都是 OperatorFunction
https://rxjs.dev/api/operators/distinct

Definition of pipe
```
pipe<A, B, C, D>(
  op1: OperatorFunction<T, A>,
  op2: OperatorFunction<A, B>,
  op3: OperatorFunction<B, C>,
  op4: OperatorFunction<C, D>
): Observable<D>
```
`op1` 接受原始类型 `T` 的数据,并返回类型 `A` 的数据。
`op2` 接受 `op1` 返回的类型 `A` 的数据,并返回类型 `B` 的数据。
`op3` 接受 `op2` 返回的类型 `B` 的数据,并返回类型 `C` 的数据。
`op4` 接受 `op3` 返回的类型 `C` 的数据,并返回类型 `D` 的数据。
最终,该 `pipe()` 方法会返回一个 Observable,它会发出类型 `D` 的数据。


## map && switchMap
⚠️ Attention
map 在 rxjs 中是映射，并不想 JS 中要求数据源是 Array，两者不一样。

ex for map
```
this.searchInput.valueChanges
  .pipe(
    map(term => this.getSearchResults(term))
  )
  .subscribe(results => {
    // 处理搜索结果
  });
```


switchMap
switchMap 和 map相同的地方： 对 Observable 发出的每个值应用一个函数,该函数返回一个新的 Observable。
不同之处：当有更新的 Observable 到来时， `switchMap` 操作符会取消先前 Observable 的订阅, 只保持最新返回的 Observable 的订阅。
**这一点很像 debounce**  它们都涉及到取消之前未完成的工作,并开始新的工作。
```

import { fromEvent, of } from 'rxjs';
import { switchMap, delay } from 'rxjs/operators';

const resizeEvent = fromEvent(window, 'resize');

resizeEvent.pipe(
  // 切换到一个新的 Observable
  switchMap(() => {
    return of(null).pipe(delay(300));
  })
).subscribe(() => {
  console.log('Window resized!');
});
```


## bufferTime
不是 setTimeout， 虽然也是间隔一段时间后触发。但它是会存储这一段时间收到的数据，到点后发送出去。

fromEvent(document.body, 'click')
  .pipe(
    // 每 2500 毫秒收集一次点击事件
    bufferTime(2500),
    // 对收集到的点击事件进行处理
    map((clickList) => {
      console.log('Processed clicks:', clickList.length);
      // 在这里执行具体的处理逻辑
      return clickList;
    })
  )
  .subscribe((processedClicks) => {
    console.log('Processed click list:', processedClicks);
  });

```
```

## distinctUntiChanged
```

this.currentUnits$.pipe(
	map((units) => units.get(type) ?? null), 
	distinctUntilChanged()
)

```

1. 订阅 `this.currentUnits$` Observable,获取它发出的 Map 对象。
2. 从 Map 对象中提取 `type` 对应的值。如果没有找到,则返回 `null`。
3. 使用 `distinctUntilChanged()` 操作符,确保只有当提取的值发生变化时,才会将其传递给下游订阅者。

## takeUntil
它可以用来管理订阅的生命周期。`SubA.pipe(takeUntil(SubB))` 的意思是， 订阅者一直 take SubA 数据流，直到 SubB 出现就取消。
```
this.myObservable$
.pipe(
	takeUntil(this.unsubscribe$)
)
.subscribe(/* ... */);
```


## startWith
在数据流开始时插入一个或多个初始值。  （并不是字符过滤哦😯）
https://rxjs.dev/api/operators/startWith
```
timer(1000)
  .pipe(
    map(() => 'timer emit'),
    startWith('timer start')
  )
  .subscribe(x => console.log(x));
 
// results:
// 'timer start'
// 'timer emit'
```


# Scheduler
控制 Observable 发送值的时机。

## merge
将多个 Observable 合并在一起
```
import { merge, Subscriber, Observable } from 'rxjs';

const observable1 = new Observable( (subscriber: Subscriber<number>) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.complete();
});

const observable2 = new Observable( (subscriber: Subscriber<number>) => {
  subscriber.next(3);
  subscriber.next(4);
  subscriber.complete();
});

const mergedObservable = merge(observable1, observable2);
mergedObservable.subscribe(value => {
  console.log(value);
});


```

## combineLatest &&  combineLatestWith

将多个 Observable 的最新值组合成一个新的 Observable。
任意一个 Observable 有新值的时候，将这个值和现有所有  Observable 最近收到的值组合成 array 送出。

https://rxjs.dev/api/index/function/combineLatest

官方的例子比较复杂， 可以感受下面这个例子

```
import { interval, combineLatest } from 'rxjs';
import { map, take } from 'rxjs/operators';

const source1$ = interval(1000).pipe(map(x => `Source 1: ${x}`), take(3));
const source2$ = interval(1500).pipe(map(x => `Source 2: ${x}`), take(3));
const source3$ = interval(2000).pipe(map(x => `Source 3: ${x}`), take(3));

combineLatest([source1$, source2$, source3$])
  .subscribe(([value1, value2, value3]) => {
    console.log(`Combined value: [${value1}, ${value2}, ${value3}]`);
  });
```

combineLastestWith 的作用和 combineLastest 完全相同。但是 combineLastestWith 是实例方法，挂载在 Observable 上。

```

const username$ = fromEvent(usernameInput, 'input').pipe( map((event: any) => event.target.value) ); 

const password$ = fromEvent(passwordInput, 'input').pipe( map((event: any) => event.target.value) );


username$.combineLatestWith(password$)
  .subscribe(([username, password]) => {
    const isFormValid = username.length >= 6 && password.length >= 8;
    console.log(`Form is ${isFormValid ? 'valid' : 'invalid'}`);
  });
```

# Observe & Observable
- Observable 是"被订阅"的对象,它发出数据。 最常见的 Observable 就是 Subject， Subject extends Observable
- Observer 是"订阅者",它接收并处理从 Observable 发出的数据。

- Observable 需要通过 `subscribe()` 方法来订阅,订阅者接收数据。
- Subject 同时实现了 Observable 和 Observer 接口,既可以被订阅,也可以主动推送数据。

## 信息隐藏🫥
在 RxJS 中使用 `Subject` 时,通常会搭配 `asObservable()` 方法来返回一个 Observable 的引用

主要是隐藏 Subject 的写入能力，使用 `asObservable()` 返回的是只读的 Observable 引用,外部只能订阅,无法向 Subject 写入数据,  这样可以更好地控制数据流向。

```
const _ruleChange$ = new Subject() 
const rule$ = _ruleChange$.asObservable()

export rule$

// other file ........
rule$.subscrible(...)


```
