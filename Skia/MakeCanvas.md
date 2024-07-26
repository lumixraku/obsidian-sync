
Canvaskit 中有多种绘制模式。

MakeCanvas 创建一个 Emulate Canvas。和浏览器内置 Canvas 在绘制上的 API 几乎一样。往往用于 backend 生成绘制结果用。它不是一个 DOM 元素。一般用法是通过它绘制出图像，然后获取器 ImageData 用。

MakeCanvasSurface  这是最常见的创建 Surface 的方法，不过目前已经被标记为 deprecated。它会根据情况自动选择 CPU based surface 和 GPU based surface。


MakeSWCanvasSurface  创建 CPU based surface


MakeWebGLCanvasSurface  GPU based surface
MakeGPUCanvasSurface 也是 GPU based surface 但是构造参数和上一个不同。

