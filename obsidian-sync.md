 I am using this extension.
 我正在使用这个 extension 来做同步，但是官方的 on board 方案有问题。

![[Pasted image 20240708120650.png]]
obsidian://show-plugin?id=github-sync


## my way to start!

first follow the offcial instruction
![[Pasted image 20240708121952.png]]

then make this folder `~/Documents/Obsidian Vault` to a git repo, add remote origin in the repo URL in extension settings. 


then add a .gitignore like this
```
.obsidian/plugins/github-sync/data.json
.obsidian/plugins/github-sync/main.js
.obsidian/plugins/github-sync/manifest.json
.obsidian/plugins/github-sync/styles.css

.obsidian/plugins
.obsidian/workspace.json
.obsidian/workspaces.json
.obsidian/plugins/github-sync/styles.css


.obsidian

```

