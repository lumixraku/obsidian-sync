https://www.bilibili.com/video/BV1Ki4y1a72S?p=11&vd_source=66ae80b89912671707a9b77834e03720

兰伯特材质 performance 优秀,  要比 Fong 材质性能上有更好表现
但是 Lambert 材质可能有条纹光线问题



## MagFilter & MinFilter
在Three.js中，`magFilter`和`minFilter`是用于纹理（Texture）对象的属性，用于控制纹理在放大和缩小时的过滤方式。

`magFilter`（放大过滤器）定义了当纹理被放大时的过滤方式，即当纹理覆盖在大于其原始尺寸的区域上时。常见的过滤选项包括：

- `THREE.NearestFilter`：最近邻过滤，使用纹理中离当前像素最近的像素值作为放大后像素的值，产生锐利的像素化效果。
- `THREE.LinearFilter`：线性过滤，使用纹理中附近像素的加权平均值作为放大后像素的值，产生较平滑的效果。

`minFilter`（缩小过滤器）定义了当纹理被缩小时的过滤方式，即当纹理覆盖在小于其原始尺寸的区域上时。常见的过滤选项包括：

- `THREE.NearestFilter`：最近邻过滤，使用纹理中离当前像素最近的像素值作为缩小后像素的值，产生锐利的像素化效果。
- `THREE.LinearFilter`：线性过滤，使用纹理中附近像素的加权平均值作为缩小后像素的值，产生较平滑的效果。
- `THREE.NearestMipmapNearestFilter`：最近邻mipmap过滤，使用最近邻的mipmap级别进行最近邻过滤。
- `THREE.LinearMipmapNearestFilter`：线性mipmap过滤，使用最近邻的mipmap级别进行线性过滤。
- `THREE.NearestMipmapLinearFilter`：最近邻mipmap过滤，使用两个最近邻mipmap级别的线性插值。
- `THREE.LinearMipmapLinearFilter`：线性mipmap过滤，使用两个最近邻mipmap级别的线性插值。

这些过滤选项提供了在放大和缩小时控制纹理质量和性能之间的权衡。选择适当的过滤方式取决于应用的需求和性能要求。


## Displacement
move vertices
https://www.bilibili.com/video/BV1Ki4y1a72S?p=16&spm_id_from=pageDriver&vd_source=66ae80b89912671707a9b77834e03720


## AlphaTest

`alphatest` 是WebGL中的一个功能，用于在渲染过程中对片段（fragment）的透明度进行测试。具体来说，它允许开发者设置一个阈值（threshold），只有当片段的透明度（alpha值）大于这个阈值时，片段才会被渲染到屏幕上。如果小于或等于这个阈值，片段将被丢弃。

这个功能通常用于：

- **透明度裁剪**：对于半透明物体，如果它们的某些部分几乎不透明，可以使用 `alphatest` 来避免渲染这些部分，从而提高性能。
- **创建蒙版效果**：通过设置一个合适的阈值，可以创建一种蒙版效果，使得只有物体的特定部分被渲染。
- **优化渲染**：在某些情况下，通过 `alphatest` 可以减少需要处理的片段数量，从而提高渲染效率。

使用 `alphatest` 可以有效地控制渲染过程，避免不必要的计算和渲染，特别是在处理大量半透明物体时非常有用。