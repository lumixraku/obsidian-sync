## AnimationMixer

```

  const animations = gltf.animations;

  // 创建 AnimationMixer 并添加到场景
  const mixer = new THREE.AnimationMixer(model);
  scene.add(model);

  // 创建 AnimationAction 并播放动画
  const clipAction = mixer.clipAction(animations[0]); // 使用第一个动画
```