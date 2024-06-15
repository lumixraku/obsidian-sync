Poetry 是一个 Python 包管理器，可用于管理项目依赖项、构建工具和虚拟环境。它还支持根据 `pyproject.toml` 文件中指定的 Python 版本创建虚拟环境。


`poetry install` just like `npm install`

`poetry shell` change python version!

`pyproject.toml` just like `package.json.  

`poetry.lock` just like `pnpm-lock.yaml`

**Poetry 可以自动切换 Python 版本**

Poetry 可以根据 `pyproject.toml` 文件中指定的 Python 版本创建虚拟环境。 要切换到不同的 Python 版本，请更新 `pyproject.toml` 文件中的 `python-version` 字段并重新运行 `poetry install` 命令。


使用 Poetry 或 Pipenv 时，您需要在虚拟环境中激活才能使用项目依赖项。要激活虚拟环境，请运行以下命令 `poetry shell`

PS **virtualenv** 是根据项目和 Python 版本号来区分的。 每个虚拟环境都存储在单独的目录中，该目录的名称通常为 `{project_name}-{python_version}`

```

➜  anna git:(main) poetry shell
Spawning shell within /Users/lilin/Library/Caches/pypoetry/virtualenvs/anna-v47_79if-py3.12
➜  anna git:(main) emulate bash -c '. /Users/lilin/Library/Caches/pypoetry/virtualenvs/anna-v47_79if-py3.12/bin/activate'

```







