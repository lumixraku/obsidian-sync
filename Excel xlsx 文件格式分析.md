
在解析 `.xlsx` 文件时，了解其内部结构非常重要。`.xlsx` 文件实际上是一个基于 **Open Packaging Conventions (OPC)** 的压缩包，内部包含了多个文件和文件夹，每个部分负责存储不同类型的数据。

我们可以借助 [ooxml ](https://chromewebstore.google.com/detail/ooxml-tools/bjmmjfdegplhkefakjkccocjanekbapn)  来分析其结构
![[Pasted image 20241207153535.png]]
`_rels ` 指的是关系文件， Relationship Files.  主要用于定义不同部分之间的引用关系。
`workbook.xml` 当前 workbook, worksheet 的内容都放在 worksheets/{SHEET_NAME} 中。
`styles.xml` 所有的样式都放在这里，
在 worksheets 中只有引用 style 的 index， style 的实际内容都在 styles.xml 中



e.g. 
设置了 A1 单元格有全边框，然后再设置 B1:C2 无边框。
![[Pasted image 20241207170835.png]]

```
    <sheetData>
        <row r="1" spans="1:3" x14ac:dyDescent="0.2">
            <c r="A1" s="1"/>
            <c r="B1" s="2"/>
            <c r="C1" s="2"/>
        </row>
        <row r="2" spans="1:3" x14ac:dyDescent="0.2">
            <c r="B2" s="2"/>
            <c r="C2" s="2"/>
        </row>
    </sheetData>
```
这里 A1 单元格样式为 s=1, B1:C2 的样式为 s=2 

在 styles.xml 中， 样式主要由两部分组成， cellXfs 以及  cellStyles & cellStyleXfs
cellStyles & cellStyleXfs 主要是更基础的样式， 用于继承或内置样式。
我们 s=1 的样式在  cellXfs 中 index=1 的子项
```
    <cellXfs count="3">
        <xf numFmtId="0" fontId="0" fillId="0" borderId="0" xfId="0"/>
        <xf numFmtId="0" fontId="0" fillId="0" borderId="1" xfId="0" applyBorder="1"/>
        <xf numFmtId="0" fontId="0" fillId="0" borderId="0" xfId="0" applyBorder="1"/>
    </cellXfs>
```

这里又指向  borderId=1  也就是 borders 样式集中 index = 1 的样式
```
    <borders count="2">
        <border>
            <left/>
            <right/>
            <top/>
            <bottom/>
            <diagonal/>
        </border>
        <border>
            <left style="thin">
                <color theme="1"/>
            </left>
            <right/>
            <top style="thin">
                <color theme="1"/>
            </top>
            <bottom style="thin">
                <color theme="1"/>
            </bottom>
            <diagonal/>
        </border>
    </borders>
```



e.g.
设置 A4:B5 合并单元格，再设置全边框。之后设置紧邻的 C4 位全边框， 再设置 C4 下方的 C5 为无边框。 
![[Pasted image 20241207171417.png]]
![[Pasted image 20241207173243.png]]
A4 对应的 s = 2, 对应 border = 2
B5 对应 s = 3,  对应 border = 3
![[Pasted image 20241207173810.png]]
可见虽然肉眼看不到 A4 有边框， 但是 A4 是上下左右都有 border 属性
而 B5， 因为右侧设置了取消边框，因此 B5 right border 为空



LT 原则
仅设置 A1 为全边框， 
```
            <left style="thin">
                <color indexed="64"/>
            </left>
            <right style="thin">
                <color indexed="64"/>
            </right>
            <top style="thin">
                <color indexed="64"/>
            </top>
            <bottom style="thin">
                <color indexed="64"/>
            </bottom>
```


设置 A1 B1 A2 B2 全边框,  可见 4个 cell 都共用同一种 border style。
```
        <border>
            <left style="thin">
                <color indexed="64"/>
            </left>
            <right style="thin">
                <color indexed="64"/>
            </right>
            <top style="thin">
                <color indexed="64"/>
            </top>
            <bottom style="thin">
                <color indexed="64"/>
            </bottom>
            <diagonal/>
        </border>
    
```

```
    <cellXfs count="2">
        <xf numFmtId="0" fontId="0" fillId="0" borderId="0" xfId="0"/>
        <xf numFmtId="0" fontId="0" fillId="0" borderId="1" xfId="0" applyBorder="1"/>
    </cellXfs>
```

```
    <sheetData>
        <row r="1" spans="1:2" x14ac:dyDescent="0.2">
            <c r="A1" s="1"/>
            <c r="B1" s="1"/>
        </row>
        <row r="2" spans="1:2" x14ac:dyDescent="0.2">
            <c r="A2" s="1"/>
            <c r="B2" s="1"/>
        </row>
    </sheetData>
```

