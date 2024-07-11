https://www.bilibili.com/video/BV1Ki4y1a72S?p=25&vd_source=66ae80b89912671707a9b77834e03720

PCFSoftShadowmap  (recommend)

light should set `light.castShadow = true` before using shadow.


Poly Heaven: texture hub website


## color space
color space is a way to optimize how color are being stored to human eyes sensitivity.

SRGB  color space is a special color space adapt to eye sensitivity


## shadow acne 阴影失真

casting shadow on itself!  use normal  bias to fix it
bias usually for flat surfaces
normal bias usually for round surfaces
![[Pasted image 20240626230301.png]]
![[Pasted image 20240626230634.png]]
## castshadow receiveshadow
1. **castShadow**:
    
    - 用于控制物体是否会投射阴影。
    - 当设置为 `true` 时,该物体会在其他物体上投射阴影。
    - 启用此属性需要同时配置相应的阴影设置,如阴影贴图、阴影相机等。
2. **receiveShadow**:
    
    - 用于控制物体是否会接收阴影。
    - 当设置为 `true` 时,该物体会接收来自其他物体投射的阴影。
    - 启用此属性也需要相应的阴影设置支持。


