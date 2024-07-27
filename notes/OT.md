a ts lib for ot operation.

## 简介

The core OT API, which is a type with standard `apply`, `compose`, `transform`, etc functions.

在 OT (Operational Transformation) 算法中, `apply`, `compose` 和 `transform` 是三个重要的操作:

1. **apply**:
    
    - 这个操作是将一个操作应用到一个文档状态上, 从而更新文档的内容。
    - 比如, 一个插入操作 `insert(5, 'hello')` 会被应用到一个初始文档 `'abcdef'` 上, 得到更新后的文档 `'abchelloef'`。
2. **compose**:
    
    - 这个操作是合并两个操作, 生成一个等价的单一操作。
    - 比如, 先插入 `'hello'` 再删除第 6 个字符, 可以合并成一个 `insert(5, 'hello') + delete(10)` 的单一操作。
3. **transform**:
    
    - 这个操作是将一个操作 A 转换成一个等价的操作 B, 使得 B 可以应用到由 A 导致的文档状态上。
    - 比如, 当两个用户同时对同一份文档进行操作时, 需要将一个用户的操作转换成等价的操作, 使其能够正确应用到另一个用户的文档状态上。

这三个操作是 OT 算法实现协同编辑功能的核心, 确保在多个用户同时编辑同一文档时, 能够正确地合并和应用各自的操作, 从而维护文档的一致性。


## ot-json1
一个操作 json 的 OT 库，[https://github.com/ottypes/json1?tab=readme-ov-file]
但是 API 文档过于简略，初次看有点摸不着头脑。 下面就用这个库来举几个例子。

## Apply
apply moveOp:  change property name
```
const json1 = require('ot-json1')

// change object a.x to a.y
const op1 = json1.moveOp(['a', 'x'], ['a', 'y'])
let doc = {a: {x: 5}}
doc = json1.type.apply(doc, op1)   // {a: {y: 5}}

```

apply insertOp: insert a prop
```
const json1 = require('ot-json1')

// move a to b first and then add z: 'hi there' to b
const op2 = [
  json1.moveOp(['a'], ['b']),
  json1.insertOp(['b', 'z'], 'hi there')
].reduce(json1.type.compose, null)

let doc = {a: {x: 5}}
doc = json1.type.apply(doc, op2) // {b: {x: 5, z: 'hi there'}}

```

## Compose
compose: combine a lots of operations in order.
```
const json1 = require('ot-json1')

const op1 = json1.moveOp(['a', 'x'], ['a', 'y'])
const op2 = json1.moveOp(['a'], ['b'])
const op3 = json1.insertOp(['b', 'z'], 'hi there')
const opc = [
  op1,
  op2,
  op3
].reduce(json1.type.compose, null)


const opc2 = [
  op2,
  op1
].reduce(json1.type.compose, null)

let doc = {a: {x: 5}}
console.log(json1.type.apply(doc, opc)) // {b: {y: 5, z: "hi there"}}
console.log(json1.type.apply(doc, opc2)) // error: cannot insert into missing item


```


##  Transform
什么情况下要用到 transform？ 
从简介中看，transform 和 combine 操作很像。区别是 combine 只是单纯的合并， 不会自动的根据先前的变化调整 operation。

下面这样 combine 两个 operation 会报错
op2 先执行，op1 再执行。但是当 op1 执行的时候已经木有 a 这个 prop 了。
因此我们需要 transform op1， 让 op1 变成应用了 op2 的之后的 op1‘
```
let doc = {a: {x: 5}}
const op1 = json1.moveOp(['a', 'x'], ['a', 'y'])
const op2 = json1.moveOp(['a'], ['b'])
const opc = [
  op2,
  op1
].reduce(json1.type.compose, null)
console.log('opc', json1.type.apply(doc, opc)) // Error: Cannot insert into missing item
```



transform: 
```

const json1 = require('ot-json1')

const op1 = json1.moveOp(['a', 'x'], ['a', 'y'])
const op2 = json1.insertOp(['a', 'aa'], ['aaaa'])

// op2 applyed first, before apply op1, op1 should transform, 
// This command return transformed op1 after op2
const op1t = json1.type.transform(op1, op2, 'left')

// transformed op2 after op1
const op2t = json1.type.transform(op2, op1, 'left')


let doc = {a: {x: 5}}
// first op2 then op1
docOP2 = json1.type.apply(doc, op2) 
console.log('OP2', docOP2) // {a: {x: 5, aa: ['aaaa']}
console.log(json1.type.apply(docOP2, op1)) // {a: {y: 5, aa: ['aaaa']}}

console.log('first op1 then op2 ........')
docOP1 = json1.type.apply(doc, op1)
console.log('OP1', docOP1)
console.log(json1.type.apply(docOP1, op2t)) // {a: {y: 5, aa: ["aaaa"]}}


console.log('first op2 then op1 ........')
docOP2 = json1.type.apply(doc, op2)
console.log(json1.type.apply(docOP2, op1t)) // {a: {aa: ["aaaa"], y: 5}}



```




another demo
```
const json1 = require('ot-json1')

const op1 = json1.moveOp(['a', 'x'], ['a', 'y'])
const op2 = json1.moveOp(['a'], ['b'])

// apply op2 first then op1 should transform, return transformed op1
const op21 = json1.type.transform(op1, op2, 'left')
// transformed op2
const op12 = json1.type.transform(op2, op1, 'left')  

let doc = {a: {x: 5}}
// first OP1 then OP2, otherwise would got an error.
docOP1 = json1.type.apply(doc, op1) 
console.log('OP1 OP2', json1.type.apply(docOP1, op2)) // {b: {y: 5}}


docOP1 = json1.type.apply(doc, op1) // {a: {y: 5}}
console.log(json1.type.apply(docOP1, op12)) // {b: {y: 5}}

docOP2 = json1.type.apply(doc, op2) // {b: {x: 5}}
console.log(json1.type.apply(docOP2, op12)) // {b: {y: 5}}
```
