# 管理Python 的开发环境

Python已经有发展过了超过三十多年，既随着时代进步，也背负着历史包袱。作为开发者，首先要在开发环境和生产环境中管理Python运行时和第三方库的版本，以便使用可预期的版本，并且有可预期的环境。

从实际经验来看，在本地的开发环境中，开发者通常将Python直接安装在操作系统上，通常会尝试不同的版本，并使用不同版本的第三方库。

在生产环境中，通常把Python容器环境（Docker）中。由于容器是隔离的，每次启动容器时都可以使用干净的环境，因此并不大考虑第三方库的版本管理问题，只需要选择不同的Python版本即可。

因此，在开发环境和生产环境中，需要使用不同的策略。

## 管理本地开发环境中的 Python

### 使用 asdf 管理 Python版本

https://asdf-vm.com/

### 使用 pyenv 管理 Python 版本

pyenv是一个Python的版本管理工具，项目主页位于Github （<https://github.com/pyenv/pyenv>）。使用pyenv，可以在不污染系统Python环境的前提下，安装和切换不同的Python版本。

安装pyenv的步骤很简单。使用pyenv-installer（<https://github.com/pyenv/pyenv-installer>）工具可以一行命令搞定：

```bash
curl https://pyenv.run | bash
```

重启shell后即可生效。

pyenv的原理可见其项目主页的说明。主要命令见 <https://github.com/pyenv/pyenv/blob/master/COMMANDS.md>。

### 使用 virtualenv 管理虚拟的 Python 环境

这里的Python环境，指的是在确定了Python版本后，开辟出一个相对隔离的干净虚拟环境用于开发。为什么需要使用“虚拟环境”？举个例子，如果安装了同一个软件包的多个版本，那么使用 `import` 就会引入最新版本中的模块。试想，如果开发机器上有多个项目，对于同一个软件包，多个项目使用了不同的版本。那么各个项目所需的软件包之后，所有这些项目都不得不使用最新版本的软件包。为了解决这个问题，对于每个项目，可以使用隔离的环境，即虚拟环境，互不干扰。而 venv 模块就是用来创建这个虚拟环境的。

见 https://docs.python.org/3/library/venv.html 。

## 管理生产环境中的 Python

目前，在生产环境（即任何线上环境）中，首推使用容器。以Docker为例，在 `Dockerfile` 中描述镜像，指定特定版本的 Python，并使用 `pip` 命令安装所需的软件包即可 。在生产环境中，每次都会创建新的容器，因此运行环境并不会出现在多次使用后被污染的问题。

但需要注意，如果每次都使用 `pip` 安装软件包，那么可能会比较耗时。为了复用镜像和容器中的层，通常在构建镜像时，先复制软件包定义文件（即下文中的 `requirements.txt` 文件）并安装。然后再拷贝其他的源文件。即

```docker
FROM python:3.6.5-stretch

COPY requirements.txt /app/
RUN pip install -r requirements.txt

COPY . /app/
# ...
```


## 管理项目中的软件包

通常把第三方软件包定义在 `requirements.txt` 文件中，并使用 `pip` 来。在实际开发中，开发和生产环境，可能会使用不同的包，比如，在开发中需要使用测试框架 `nose`，在生产环境中需要使用Postgres数据库时，为了减少各个环境中软件包的数量，就可以只安装必要软件包即可。因此可以使用多个的文件。

比如，在 `requirements.txt` 中定义公用的软件包，在 `requirements-dev.txt` 安装开发环境中需要的包，在 `requirements-prd.txt` 中定义生产环境中所需的包。

比如，在开发环境中使用下面的命令：

```shell
pip install -r requirements.txt -r requirements-dev.txt
```

在生产环境中使用

```shell
pip install install -r requirements.txt -r requirements-prod.txt
```

由于依赖传递的问题，使用 `pip` 按安装 `requirement.txt` 里的直接依赖，以及依赖的依赖。这导致这些间接依赖的版本是不确定的。因此，应该在版本库里显示地指定所有的依赖的版本。此时，在安装了依赖之后，还应该使用 `pip freeze` 列出所有的依赖，并保存起来。

比如，在安装了开发环境的依赖后，使用下面的命令：

```shell
pip freeze > requirements-frozen-dev.txt
```

## 连接非默认的软件包服务器

TODO
