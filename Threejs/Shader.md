
course:  lewis lepton tutorial

## Swizzling
这种特性称为**向量分量访问**或**矢量分量访问**。

1. `.x`, `.y`, `.z`, `.w`：这种方式通过字母来访问向量的各个分量。通常用于表示坐标或颜色等。
2. `.r`, `.g`, `.b`, `.a`：这种方式也常用于访问向量的分量,通常用于表示颜色分量。
3. `.s`, `.t`, `.p`, `.q`：这种方式通常用于表示纹理坐标。
4. `.red`, `.green`, `.blue`, `.alpha`：这种方式提供了更具描述性的分量访问方式。

```
vec3 foo = vec3(1.0, 2.0, 3.0);
vec2 bar = foo.xy;

vec4 foo = vec4(1.0, 2.0, 3.0, 4.0);
float bar = foo.w;

```

## GL_Position

very important variable, always exists
contain position of vertex on the screen.

coordinate in clip space to screen space

```
uniform mat4 projectMatrix;
uniform mat4 viewMatrix;
uniform mat4 modelMatrix;

gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0);

these can be easier: ===============================
uniform mat4 viewModelMatrix;
gl_Position = projectionMatrix * viewModelMatrix * vec4(position, 1.0);


```

uniform ---> because they are the same for all vertices, provided by Threejs


## attribute & uniform

在 GLSL (OpenGL Shading Language) 中,`attribute` 和 `uniform` 是两种不同类型的输入变量

相同之处
1. **限制**:
    
    - 它们都有数量上的限制,即单个着色器中最多可以使用的 `attribute` 和 `uniform` 变量数量是有上限的。
2. **赋值方式**:
    
    - 它们都需要通过特定的 API 函数来将值赋给它们,如 `glVertexAttribPointer()` 和 `glUniformMatrix4fv()`。
3. **声明方式**:
    
    - 它们都是使用 `in` 关键字在着色器中声明的输入变量。


它们之间有以下主要区别:

1. **定义方式**:
    
    - `attribute`: 在顶点着色器中定义,用于接收每个顶点独有的数据,如位置、法线、颜色等。
    - `uniform`: 在任何着色器类型中都可以定义,用于接收对整个图元/场景都是一样的数据,如变换矩阵、材质属性等。
2. **访问范围**:
    
    - `attribute`: 只能在顶点着色器中访问,不能在片元着色器中直接访问。
    - `uniform`: 可以在任何着色器类型(顶点、几何、片元)中访问。
3. **数据更新方式**:
    
    - `attribute`: 随着每个顶点的变化而变化,需要通过 VBO 从 CPU 传入。
    - `uniform`: 整个渲染过程中保持不变,可以在 CPU 侧随时更新并传入着色器。
4. **存储位置**:
    
    - `attribute`: 存储在顶点属性数组中。
    - `uniform`: 存储在着色器的 uniform 变量池中。


## Threejs with Attribute & Uniorm


setAttribute
```
const geometry = new THREE.BufferGeometry();
const vertices = new Float32Array([...]);
geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));
```

pass uniform
```
const material = new THREE.ShaderMaterial({
  uniforms: {
    time: { value: 0 },
    color: { value: new THREE.Color(0xffffff) }
  },
  vertexShader: vertexShaderCode,
  fragmentShader: fragmentShaderCode
});
```


## Varying
Pass data from vertex shader to fragment shader.