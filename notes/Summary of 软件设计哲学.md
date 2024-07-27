https://go7hic.github.io/A-Philosophy-of-Software-Design/#/


## About  Author
John Ousterhout 是斯坦福大学的 Bosack Lerner 计算机科学教授。他是 Tcl 脚本语言的创建者，并且以在分布式操作系统和存储系统中的工作而闻名。Ousterhout 在耶鲁大学获得了物理学学士学位，并在卡内基梅隆大学获得了计算机科学博士学位。他是美国国家工程院院士，并获得了无数奖项，包括 ACM 软件系统奖，ACM Grace Murray Hopper 奖，美国国家科学基金会总统年轻研究者奖和 UC Berkeley 杰出教学奖。

## Chapter 1 Introduction

本书两个目标：
- 首先是描述软件复杂性的性质：“复杂性”是什么意思，为什么重要，以及当程序具有不必要的复杂性时如何识别？
- 本书的第二个也是更具挑战性的目标是介绍可在软件开发过程中使用的技术，以最大程度地减少复杂性。

改善设计技能的最好方法之一就是学会识别危险信号， 这些信号的出现表明设计存在一些问题。

## Chapter 2 The Nature of Complexity

造成软件难以维护的原因是复杂性，尤其是难以遇见的复杂性。
这是因为人们的认知负荷导致。


## Chapter 3 Working Code Isn’t Enough (Strategic vs. Tactical Programming)
如果您想要一个好的设计，则必须采取更具战略性的方法。

> 我觉得这里的战略性意思就是指的目标和眼光，大意是应该看的更长远。

好的设计不是没有代价的， 需要你持续不断的投入。

## Chapter 4 Modules Should Be Deep

模块应该是深邃的

关于模块的划分
- **理想状态**：模块完全独立，无需了解其他模块即可工作。
- **现实挑战**：模块必须通过函数或方法调用协同工作，形成依赖关系。

一个 shallow 模块的例子
此方法会使情况变得更糟，而不是更好
```
private void addNullValueForAttribute(String attribute) { 
    data.put(attribute, null); 
}
```

后面仍然是用 Java 的文件读取来举例，仍然是一个违背深模块的例子
文中提到在 Java 中读取文件需要用到三个类（使用 Buffer 的话，并且大部分人在读取文件的时候都需要用 Buffer 机制）
但是在得到文件内容后， 后续的所有操作都是在 objectStream 上进行， fileStream 和 bufferedStream 就仅仅在这一刻用， 后续都没有用到。 这是不好的设计。
```

FileInputStream fileStream = new FileInputStream(fileName); 

BufferedInputStream bufferedStream = new BufferedInputStream(fileStream);

ObjectInputStream objectStream = new ObjectInputStream(bufferedStream);
```

作者认为好的例子是像 Unix 中读取文件接口设计
```

int open(const char* path, int flags, mode_t permissions);

ssize_t read(int fd, void* buffer, size_t count);
```



## Chapter 5 Information Hiding (and Leakage) 
信息的隐藏和泄露

一个功能， 通过太多的类去实现瓦格纳我那个造成信息的泄露。
例如一个 HTTP 请求通过两个类实现， 一个讲网络连接的请求读取为字符串，另一个做字符串解析。这两个类都需要了解 HTTP 请求的大多数结构，并且解析代码在两个类中都是重复的。
**由于这些类共享大量信息，因此最好将它们合并为一个同时处理请求读取和解析的类。**


通常可以通过使类稍大一些来改善信息隐藏。这样做的一个原因是将与特定功能相关的所有代码（例如，解析 HTTP 请求）组合在一起。

一个典型的容易导致信息泄露的例子， 不仅暴露了这个类的内部数据，还有可能导致数据被外部修改。
```
public Map<String, String> getParams() {
    return this.params;
}
```

好的设计应该是下面这样
```

public String getParameter(String name) { ... } 

public int getIntParameter(String name) { ... }

```


## Chapter 6  General-Purpose Modules are Deeper
通用模块应该非常深入

不推荐示范
作者表示这种方法在用户界面和文本类之间造成了信息泄漏（This approach created information leakage between the user interface and the text class.）
```
void deleteSelection(Selection selection);
```

推荐设计
```
void insert(Position position, String newText); 

void delete(Position start, Position end);
```


通用接口比专用接口具有许多优点。它们往往更简单，使用的方法更少。使模块具有某种通用性是降低整体系统复杂性的最佳方法之一


## Chapter 7 Different Layer, Different Abstraction
当相邻的层具有相似的抽象时，问题通常以传递方法的形式表现出来。

传递方法 (Pass-through methods) 是一种不执行任何操作的方法，只是将其参数传递给另一个方法，通常使用与直通方法相同的 API。这通常表示各类之间没有明确的职责划分。

它使得类变得浅薄， 并且在没有增加功能的情况下使得接口变得更多更复杂。它的存在表明类之间的责任划分存在混淆

下面图 a 就是错误用法， b c d 是对应的解法
![[Pasted image 20240717171500.png]]

变量传递
传递变量增加了复杂性，因为它们强制所有中间方法知道它们的存在， 并且修改参数时也很麻烦。

下面 a 是错误用法， b c d 是问题解决方案
方案 b 就是创建一个新对象 shared object， 把所有需要层层传递的对象都放在 shared object 中。
方案 c 是用全局对象来保存要传递的对象。
方案 d 是用上下文来保存要传递的对象。这是作者推荐的方案。

![[Pasted image 20240717171959.png]]

## Chapter 8 Pull Complexity Downwards

模块具有简单的接口比简单的实现更为重要。
尽量不要配置参数。

## Chapter 9 Better Together Or Better Apart?

代码逻辑相关就合并， 不相关就分开。下面是代码相关的一些表现
- 它们共享信息
- 它们一起使用
- 它们概念上重叠
- 不看任何一个就无法理解

Chapter 5  中关于 HTTP 的例子就是典型的信息共享。两个方法都需要对 HTTP 格式有了解

Chapter 4 中 Java 文件读取是一个应当一起使用的例子。把它们汇聚在一起能减少接口。

举例：
编辑器的撤销能力应该独立成一个类而不是作为文本类的一部份。

关于 method 拆分
避免出现 d 的情况， 这会导致 shallow method
![[Pasted image 20240717193947.png]]

## Chapter 10 Define Errors Out Of Existence

"define errors out of existence" 翻译为中文是“定义错误使之不存在” 

The best way to eliminate exception handling complexity is to define your APIs so that there are no exceptions to handle: define errors out of existence. 
消除异常处理复杂性的最佳方式是通过定义你的API，使得没有异常需要处理：定义错误使之不存在。

另一种办法是掩盖异常
异常由底层模块处理，上层模块对底层异常无感知。
an exceptional condition is detected and handled at a low level in the system, so that higher levels of software need not be aware of the condition.

用 NFS 文件服务器举例， NFS 文件服务器应该不要抛出错误而是 hang 住。


还有一种解法，异常聚集
The idea behind exception aggregation is to handle many exceptions with a single piece of code; rather than writing distinct handlers for many individual exceptions, handle them all in one place with a single handler.
将分散在代码中零散的异常处理尽量都放在一块。 

## Chapter 11  Design it Twice

对系统做了一次设计后， 不妨再做一次。
The design-it-twice approach not only improves your designs, but it also improves your design skills.

## Chapter 12 Why Write Comments? The Four Excuses

反驳1 好的代码是自解释的
这是一个美丽的神话！！因为有大量设计信息无法用代码表示
例如 Chapter 4 中提到的提取字符串方法， 仅凭方法声明， 没有办法确定提取的字符串是否包含 end 所指的字符。


**注释是抽象的基础**
抽象的目的是隐藏复杂性：抽象是实体的简化视图。

反驳2  我没有时间写注释
好的注释对软件的可维护性有很大的影响，因此花费在它们上面的精力将很快收回成本

反驳3 注释会过时
的确注释有时候会过时， 作者认为使文档保持最新状态并不需要付出特别巨大的成本。

注释的好处：
注释背后的总体思想是捕获设计者所想但不能在代码中表示的信息。

没有注释，后接手的开发者将不得不重新编写或猜测开发人员意图。这将花费额外的时间，并且如果新开发者误解了原始设计者的意图，则存在错误的风险。

没有足够的注释，开发者可能必须阅读大量代码才能重构设计人员的想法。文档还可以通过阐明系统的结构来减少未知的未知数，从而可以清楚地了解与任何给定更改相关的信息和代码。

Chapter 2 提到导致复杂性的主要原因是依赖性和模糊性。好的文档可以阐明依赖关系，并且可以填补空白以消除模糊性。


## Chapter 13 Comments Should Describe Things that Aren’t Obvious from the Code

注释的最重要原因之一是抽象，其中包括许多从代码中看不到的信息。
写好注释的第一步，是确立大家都会遵守的注释惯例。

注释不应该是代码的重复说明， 这样的注释就不能提供任何价值。
关于变量的注释有一种常见错误就是就变量名的重复
例如
```
/* * Obtain a normalized resource name from REQ. */ 
private static String[] getNormalizedResourceNames( HTTPRequest req) ...

```

好的注释的第一步是在评论中使用与所描述实体不同的词。而不仅仅是重复其名称。例如
```
/* * The amount of blank space to leave on the left and * right sides of each line of text, in pixels. */ 

private static final int textHorizontalPadding = 4;
```


注释应该包含什么信息？ 
注释从目的上来说可以分为两类：
- low-level & precision comments 
- high-level comments & enhance 


low-level 提供更多精确的描述，比如
- 变量的具体含义
- 边界条件，是开区间还是笔区间？
- 如果允许使用空值，则意味着什么

什么是精确注释？ 下面举两个对比的例子



```
// 常见的注释
// Current offset in resp Buffer 
uint32_t offset;


// 精确的注释，解释了 current 的含义
// Position in this buffer of the first object that hasn't // been returned to the client.
uint32_t offset;

```


high-level 忽略了细节，并帮助开发者理解了代码的整体意图和结构。此方法通常用于方法内部的注释以及接口注释， 往往在阐述作者的意图， 说明这么编码的原因。


下面这一段注释过于细节，并且是代码逻辑的重复描述， 
```
// If there is a LOADING readRpc using the same session
// as PKHash pointed to by assignPos, and the last PKHash
// in that readRPC is smaller than current assigning
// PKHash, then we put assigning PKHash into that readRPC.

int readActiveRpcId = RPC_ID_NOT_ASSIGNED;

for (int i = 0; i < NUM_READ_RPC; i++) {

	if (session == readRpc[i].session && readRpc[i].status == LOADING
	&& readRpc[i].maxPos < assignPos
	&& readRpc[i].numHashes < MAX_PKHASHES_PERRPC) {

		readActiveRpcId = i;
		break;

	}

}

```

好的例子是这样
```
// Try to append the current key hash onto an existing 
// RPC to the desired server that hasn't been sent yet.
```

**high-level  注释要思考用什么最简单的东西来解释代码中的所有内容，以及这段代码最重要的是什么。**

为接口写注释应该使用 high-level 注释。
注释最重要的作用之一就是定义抽象。Chapter 4 提到，抽象是实体的简化视图，它保留了基本信息，但省略了可以安全忽略的细节。
但是代码不适合描述抽象， 它包含实现细节，这些细节在抽象中不应该看到。描述抽象的唯一方法是使用注释。如果您想要呈现良好抽象的代码，则必须用注释记录这些抽象。


## Chapter 14 Choosing Names

如何鉴定什么是好的命名？ 
单单通过命名，不看其声明，文档或使用该命名的任何代码，开发者能够猜到该命名指的是什么的吗？
但是一个变量名能包含的信息有限，命名的挑战是仅找到捕获实体最重要方面的几个单词。


错误示范: getCount 这个命名过于宽泛笼统
```
/**
 * Returns the total number of indexlets this object is managing.
 */
int IndexletManager::getCount() {...}


```

错误示范：命名太过具体
作者这里建议命名就用 range，毕竟能被删除的不一定是选区。
```
void delete(Range selection) {...}
```


## Chapter 15 Write The Comments First(Use Comments As Part Of The Design Process)

作者的实践方式
- 新创建一个类后，首先编写类接口注释。
- 接下来，为最重要的公共方法编写接口注释和签名，但将方法主体保留为空。
- 对这些注释进行了迭代，直到基本结构感觉正确为止。
- 此刻，为类中最重要的类实例变量编写了声明和注释。
- 最后，实现方法，并根据需要添加实现注释。
- 在实现方法时，我通常会发现需要其他方法和实例变量。对于每个新方法，我在方法主体之前编写接口注释。例如变量，我在编写变量声明的同时填写了注释。

作者并不认为在一开始就编写注释成本高，作者认为这么做并不会话费太多时间而且很有趣。

## Chapter 16 Modifying Existing Code

- 注释应该在相关代码附近， 而不是都在方法最开头。
- 如果一段文档涉及多个模块，没有一个明确的地方来写注释，可以新建一个 desingNote 来说明。
- 不要在模块 B 中写模块 A 的注释。比如 B 会调用模块 A，但是把调用的作用和结果在 B 中注释。正确的做法是将这样的注释放在 A 中。
- 运用 `// see LINK_TO_OTHER ` 这样的方法来避免重复注释。

## Chapter 17 Consistency

通过文档以及约束（自动检查脚本）来保证代码一致性。

一致性不仅意味着相似的事情应该以相似的方式完成，而且不同的事情也应该以不同的方式完成。

## Chapter 18 Code Should be Obvious

文中的 Obvious 更多的是指代码易读易懂。

If code is obvious,
- it means that someone can read the code quickly, without much thought, and their first guesses about the behavior or meaning of the code will be correct.
- a reader doesn’t need to spend much time or effort to gather all the information they need to work with the code.

一些能让代码易读易懂的 tips
- 良好的命名
- 一致性的实现方式
- 合理的使用空白


## Chapter 19 Software Trends

### 面向对象 & 继承是过去 3、40 年中最为重要的新思想之一。
文中将父类有基本默认实现，子类有额外实现的继承成为 「实现继承」（implementation inheritance.） 另一种称为接口继承，也就接口只定义，子类负责实现接口。

这种方式有一个弊端，那就是父类的信息会被子类共享，导致类之间信息泄漏。 每次对父类的修改都需要考虑对所有子类的影响。

应当谨慎的使用这种方式的继承，使用之前先考虑组合的方式能否满足需求。如果不行，可以尝试将父类管理的状态与子类管理的状态分开。某些变量完全由父类中的方法管理，子类仅以只读方式或通过父类中的其他方法使用它们。这适用于隐藏在类层次结构中的信息的概念，以减少依赖性。

### 单元测试
单元测试可以独立运行，而不需要为系统设置生产环境。单元测试在软件设计中起着重要作用，因为它们有助于重构

### TDD 测试驱动的开发

作者不是很推崇这种开发方式。测试驱动开发的问题在于，它将注意力集中在使特定功能起作用，而不是寻找最佳设计上。这是一种纯净而简单的战术编程，具有所有缺点。

### 设计模式
出现设计模式是因为它们解决了常见的问题，并且因为它们提供的解决方案被普遍认同。
设计模式的最大风险是过度使用。当自定义方法更加简洁时，请勿尝试将问题强加到设计模式中。

从表面上看，许多建议听起来不错，但是如果深入研究，会发现其中一些会使复杂性恶化，而不是更好。

## Chapter 20 Designing for Performance

前面讨论的大部分问题都在关注在复杂性。作者认为高性能的系统必定「简洁 simplicity」。

用数据说话，深入的衡量，以详细确定影响整体绩效的因素。

关键路径：
一段代码执行慢，需要先找到挂件路径。然后从关键路径中除去特殊情况。当代码运行缓慢时，通常是因为它必须处理各种情况。


## Chapter 21 Conclusion

这本书主要在讨论复杂性。处理复杂性是软件设计中最重要的挑战。这是使系统难以构建和维护的根本原因。

- Complexity is incremental: you have to sweat the small stuff, 复杂性是渐进的：你必须为小事情付出努力

- Working code isn’t enough, 工作代码还不够

- Make continual small investments to improve system design, 持续进行少量投资以改善系统设计

- Modules should be deep 模块应该是深邃的

- Interfaces should be designed to make the most common usage as simple as possible, 接口的设计应尽可能使用简洁的，常见的用法

- It’s more important for a module to have a simple interface than a simple implementation, 一个模块具有一个简单的接口比一个简单的实现更重要

- General-purpose modules are deeper, 通用模块更深入

- Separate general-purpose and special-purpose code, 通用和专用代码分开

- Different layers should have different abstractions, 不同的层应具有不同的抽象

- Pull complexity downward, 降低复杂度。

- Define errors (and special cases) out of existence, 定义错误使其不存在

- Design it twice, 设计两次

- Comments should describe things that are not obvious from the code, 注释应描述代码中不明显的内容

- Software should be designed for ease of reading, not ease of writing, 软件的设计应易于阅读而不是易于编写

- The increments of software development should be abstractions, not features， 软件开发的增量应该是抽象而不是功能