
在 Skia 的 CanvasKit.js 中，C++ 代码是通过使用 Emscripten 提供的函数来获取前端的 WebGL 上下文。

Emscripten 是一个将 C/C++ 代码编译为 WebAssembly 的开源工具链，它提供了与 JavaScript 之间的交互能力。CanvasKit.js 是 Skia 的 WebAssembly 版本，它将 Skia 引擎编译为 WebAssembly 模块，以在浏览器中运行。

在 CanvasKit.js 中，C++ 代码可以通过以下方式获取 WebGL 上下文：

1. 首先，C++ 代码会使用 Emscripten 提供的函数 `emscripten_webgl_get_current_context()` 获取当前的 WebGL 上下文。这个函数返回一个指向 WebGL 上下文的指针。
    
2. 然后，C++ 代码会使用 Emscripten 提供的函数 `emscripten_webgl_make_context_current()` 将获取到的 WebGL 上下文设置为当前上下文。这样，后续的绘图操作就会在这个上下文中进行。
    

通过这些 Emscripten 提供的函数，C++ 代码能够获取到前端的 WebGL 上下文，并在其中执行绘图操作。

![[Pasted image 20240422185130.png]]

![[Pasted image 20240422185607.png]]