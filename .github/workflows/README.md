# 构建包含非自由功能的 OpenCV 和 无 OpenCV Ultralytics 包

> [!TIP]
>
> For English, see [second half part](#building-opencv-with-non-free-features-and-opencv-free-ultralytics-packages).

## 如何在 OpenCV 使用专利算法

OpenCV 默认不包含任何非自由的功能，包括专利算法，如 SURF 。若想使用这些功能，除了使用旧版本的OpenCV，还可以重新构建带有非自由功能的 OpenCV。

本仓库提供了简单的工作流，这些工作流使用 GitHub Actions 运行。构建完成后，工作流会新建一系列分支和拉取请求，以便将编译后的二进制文件引入仓库。

### 安装包含非自由功能的预编译包

本仓库提供了 `opencv-contrib-python-headless` 的非自由版本 `opencv-contrib-python-headless-nonfree` 。你可直接下载相应版本并指定本地源来
安装。

使用如下命令安装：

```shell
pip install opencv-contrib-python-headless-nonfree --find-links /build/opencv-nonfree/
```

其中：

- `--find-links`：使用非 Simple API 标准安装源；
  - 本地源：指向构建输出的目录，如 `./path/to/local` 或 `file://./path/to/local`，或文件；
  - HTML路径：包含指向二进制文件的链接。只解析文件中的链接。

> [!NOTE]
>
> 本仓库的 `opencv-contrib-python-headless-nonfree` 包均由 GitHub Actions 自动构建。你可通过[审计提交](https://github.com/gaobobo/opencv-pytorch_MachineVision/pulls?q=is%3Apr+is%3Aclosed+author%3Aapp%2Fgithub-actions)、
> [自行构建](#手动构建)、[参阅构建脚本](./)来确定安全性。

### 使用旧版本的 OpenCV

旧版本的 OpenCV 包含了某些专利算法，如 SURF 。以 SURF 为例，`opencv-contrib-python` 的 3.4.3 是最后一个引入该算法的版本。

由于 `opencv-python 3.4.x` 在最新的版本中只支持 Python 3.7 ，因此如果使用 Conda 构建该环境，那么配置文件应至少包含：

```yaml
dependencies:
  - python==3.7
  - pip
  - pip:
      - opencv-contrib-python-headless<3.4.3
```

### 构建包含非自由功能的 OpenCV

欲要在较新的 Python 中使用或需要使用 `opencv-contrib-python` 的新特性，那么需要手动构建。幸运的是，OpenCV 提供了 [opencv-python](https://github.com/opencv/opencv-python)
项目专门用于构建适用于 Python 的 Wheel 。

#### 手动构建

设置环境变量 `CMAKE_ARGS="-DOPENCV_ENABLE_NONFREE=ON"` 后正常编译即可。

敬请参阅 [opencv-python 仓库的构建指南](https://github.com/opencv/opencv-python/tree/4.x?tab=readme-ov-file#manual-builds)。

#### 使用 GitHub Actions 构建

> [!IMPORTANT]
> 
> 该指南适用于 GitHub Actions ，不适用于其他 CI/CD 平台。

- 首先，将下列文件置于仓库主分支的 `/.github/workflows` 下，并将更改推送至 GitHub：
  - [opencv-nonfree-build_liunx.yml](./opencv-nonfree-build_linux.yml)
  - [opencv-nonfree-build_macos.yml](./opencv-nonfree-build_macos.yml)
  - [opencv-nonfree-build_win-x64.yml](./opencv-nonfree-build_win-x64.yml)
  
> [!IMPORTANT]
>
> - 不能放置于任何的子目录，只能是 `/.github/workflows` 下；
> - 必须在主分支存在该文件。如果需要测试，请向主分支推送空文件并稍后将更改合并到主分支。

- 打开 GitHub 上的仓库设置（Settings）并允许 GitHub Actions 推送、修改分支（Actions > General > Workflow permissions > Allow
  GitHub Actions to create and approve pull requests）。
- 打开 Actions 选项卡，在左侧选择工作流。
  - `opencv-nonfree-build_unix.yml` 是适用于 Linux x64, Linux ARM, MacOS Intel, MacOS M 平台；
  - `opencv-nonfree-build_win-x64.yml` 是适用于 Windows x64 平台；

> [!IMPORTANT]
>
> 所构建的包为“无头”包，即 `opencv-contrib-python-headless` 。

- 点击“Run Workflow”并设置：
  - Use workflow from: 使用哪一个分支的工作流。这也影响稍后创建的拉取请求合并到的分支；
  - Checkout opencv/opencv-python repos' ref：`opencv-python` 仓库所检出时的指针。支持分支名称、SHA、标签；
  - The path relative this repo that python wheels save to：将构建结果保存到当前仓库的位置；
  - python version that build environment：构建时使用的 Python 版本。这直接影响构建包所适用的 Python 版本；
  - Package name's suffix：构建包的后缀名。用于解决包名冲突。

- 等待构建完成后，会向仓库新建一拉取请求。请审阅并合并。
  - `opencv-nonfree-build_linux.yml` 构建需要约 20 分钟；
  - `opencv-nonfree-build_macos.yml` 构建需要约 40 分钟；
  - `opencv-nonfree-build_win-x64.yml` 构建需要约 20 分钟。

## 使用不包含 OpenCV 依赖的 Ultralytics 包

Ultralytics 包为 YOLO 模型提供了完整的解决方案，包括训练、验证、预测、部署，支持多种任务与高级管理功能。

Ultralytics 包需要依赖 `opencv-python`。不过，在实际使用与服务器部署中，更加常用`opencv-contrib-python` 和 `opencv-contrib-python-headless`。这会导致在安装 `ultralytics` 包时，可能会安装多个不同版本的 OpenCV 并可能导致冲突。

Ultralytics 提供了几种[可行的解决方案](https://docs.ultralytics.com/zh/quickstart/#custom-installation-methods)。
本仓库包含了移除 `opencv-python` `torch` `torchvision` 依赖的 `ultralytics` 二进制单文件Wheel，只需要从本地源安装：

```shell
pip install ultralytics-non-opencv-torch --find-links /build/ultralytics-non-opencv-torch/
```

其中：

- `--find-links`：使用非 Simple API 标准安装源；
  - 本地源：指向构建输出的目录，如 `./path/to/local` 或 `file://./path/to/local`，或文件；
  - HTML路径：包含指向二进制文件的链接。只解析文件中的链接。

并额外安装任意版本的 OpenCV：

```shell
pip install opencv-python|opencv-python-headless|opencv-contrib-python|opencv-contrib-python-headless
```

当然，也可以安装本仓库自带的包含非自由功能的 `opencv-contrib-python-headless` ：

```shell
pip install opencv-contrib-python-headless-nonfree --find-links /build/opencv-nonfree/
```

> [!NOTE]
>
> 本仓库的 `ultralytics-non-opencv-torch` 包均由 GitHub Actions 自动构建。你可通过[审计提交](https://github.com/gaobobo/opencv-pytorch_MachineVision/pulls?q=is%3Apr+is%3Aclosed+author%3Aapp%2Fgithub-actions)、
> [自行构建](#使用不包含-opencv-依赖的-ultralytics-包)、[参阅构建脚本](./)来确定安全性。

### 构建不包含 OpenCV 依赖的 Ultralytics 包

- 首先，将下列文件置于仓库主分支的 `/.github/workflows` 下，并将更改推送至 GitHub：
  - [ultralytics-non-opencv-build.yml](./ultralytics-non-opencv-torch-build.yml)
  
> [!IMPORTANT]
> 
> - 不能放置于任何的子目录，只能是 `/.github/workflows` 下；
> - 必须在主分支存在该文件。如果需要测试，请向主分支推送空文件并稍后将更改合并到主分支。

- 打开 GitHub 上的仓库设置（Settings）并允许 GitHub Actions 推送、修改分支（Actions > General > Workflow permissions > Allow
  GitHub Actions to create and approve pull requests）。
- 打开 Actions 选项卡，在左侧选择工作流 `ultralytics-non-opencv-torch-build.yml`。
- 点击“Run Workflow”并设置：
  - Use workflow from: 使用哪一个分支的工作流。这也影响稍后创建的拉取请求合并到的分支；
  - Checkout ultralytics/ultralytics repos' ref：`ultralytics/ultralytics` 仓库所检出时的指针。支持分支名称、SHA、标签；
  - The path relative this repo that python wheels save to：将构建结果保存到当前仓库的位置；
  - python version that build environment：构建时使用的 Python 版本。该包构建的是通用版本。

- 等待构建完成后，会向仓库新建一拉取请求。请审阅并合并。构建需要约 1 分钟。

> [!CAUTION]
>
> `ultralytics/ultralytics` 仓库使用的是 [AGPL-3.0 许可](https://github.com/ultralytics/ultralytics/blob/main/LICENSE)。
> 这可能要求必须以相同的许可分发包含该库或其衍生作品的源码与修改。
>
> 本脚本在构建时会引入一[补丁](/build/ultralytics-non-opencv/ultralytics-non-opencv-torch.patch)，
> 以满足 AGPL-3.0 许可的相关规定。

---

# Building OpenCV with Non-Free Features and OpenCV-Free Ultralytics Packages

## Building OpenCV with Non-Free Features

OpenCV does not include any non-free features by default, including patented algorithms such as SURF. To use these features, besides using older versions of OpenCV, you can rebuild OpenCV with non-free features enabled.

This repository provides simple workflows that run using GitHub Actions. After building is complete, the workflows create new branches and pull requests to introduce the compiled binary files into the repository.

### Installing Pre-compiled Packages with Non-Free Features

This repository provides a non-free version of `opencv-contrib-python-headless` called `opencv-contrib-python-headless-nonfree`. You can directly download the corresponding version and specify a local source for installation.

Install using the following command:

```shell
pip install opencv-contrib-python-headless-nonfree --find-links /build/opencv-nonfree/
```

Where:

- `--find-links`: Uses non-Simple API standard installation source;
  - Local source: Points to the build output directory, such as `./path/to/local` or `file://./path/to/local`, or file;
  - HTML path: Contains links pointing to binary files. Only parses links in the file.

> [!NOTE]
>
> All `opencv-contrib-python-headless-nonfree` packages in this repository are automatically built by GitHub Actions. You can verify security by [auditing commits](https://github.com/gaobobo/opencv-pytorch_MachineVision/pulls?q=is%3Apr+is%3Aclosed+author%3Aapp%2Fgithub-actions),
> [building manually](#manual-build), or [reviewing build scripts](./).

### Using Older Versions of OpenCV

Older versions of OpenCV include certain patented algorithms, such as SURF. Taking SURF as an example, version 3.4.3 of `opencv-contrib-python` is the last version to include this algorithm.

Since `opencv-python 3.4.x` only supports Python 3.7 in the latest versions, if using Conda to build this environment, the configuration file should at least contain:

```yaml
dependencies:
  - python==3.7
  - pip
  - pip:
      - opencv-contrib-python-headless<3.4.3
```

### Building OpenCV with Non-Free Features

To use newer versions of Python or need to use new features of `opencv-contrib-python`, manual building is required. Fortunately, OpenCV provides the [opencv-python](https://github.com/opencv/opencv-python) project specifically for building Python-compatible wheels.

#### Manual Build

Set the environment variable `CMAKE_ARGS="-DOPENCV_ENABLE_NONFREE=ON"` and compile normally.

Please refer to the [build guide in the opencv-python repository](https://github.com/opencv/opencv-python/tree/4.x?tab=readme-ov-file#manual-builds).

#### Building with GitHub Actions

> [!IMPORTANT]
> 
> This guide applies to GitHub Actions and is not applicable to other CI/CD platforms.

- First, place the following files under `/.github/workflows` in the main branch of your repository and push the changes to GitHub:
  - [opencv-nonfree-build_liunx.yml](./opencv-nonfree-build_linux.yml)
  - [opencv-nonfree-build_macos.yml](./opencv-nonfree-build_macos.yml)
  - [opencv-nonfree-build_win-x64.yml](./opencv-nonfree-build_win-x64.yml)
  
> [!IMPORTANT]
>
> - Cannot be placed in any subdirectory, must be under `/.github/workflows`;
> - The file must exist in the main branch. If testing is needed, push an empty file to the main branch and merge changes to the main branch later.

- Open repository settings on GitHub (Settings) and allow GitHub Actions to push and modify branches (Actions > General > Workflow permissions > Allow GitHub Actions to create and approve pull requests).
- Open the Actions tab and select the workflow on the left.
  - `opencv-nonfree-build_unix.yml` is for Linux x64, Linux ARM, MacOS Intel, MacOS M platforms;
  - `opencv-nonfree-build_win-x64.yml` is for Windows x64 platform;

> [!IMPORTANT]
>
> The built packages are "headless" packages, i.e., `opencv-contrib-python-headless`.

- Click "Run Workflow" and configure:
  - Use workflow from: Which branch's workflow to use. This also affects the branch that the subsequently created pull request merges into;
  - Checkout opencv/opencv-python repos' ref: The pointer when checking out the `opencv-python` repository. Supports branch names, SHA, tags;
  - The path relative this repo that python wheels save to: Where to save build results in the current repository;
  - python version that build environment: Python version used during building. This directly affects the Python version the built package is suitable for;
  - Package name's suffix: Suffix for the built package name. Used to resolve package name conflicts.

- After the build is complete, a pull request will be created for the repository. Please review and merge.
  - `opencv-nonfree-build_linux.yml` build takes about 20 minutes;
  - `opencv-nonfree-build_macos.yml` build takes about 40 minutes;
  - `opencv-nonfree-build_win-x64.yml` build takes about 20 minutes.

## Using Ultralytics Packages without OpenCV Dependencies

The Ultralytics package provides a complete solution for YOLO models, including training, validation, prediction, deployment, supporting multiple tasks and advanced management functions.

The Ultralytics package requires dependency on `opencv-python`. However, in actual use and server deployment, `opencv-contrib-python` and `opencv-contrib-python-headless` are more commonly used. This can cause installation of multiple different versions of OpenCV when installing the `ultralytics` package and may lead to conflicts.

Ultralytics provides several [viable solutions](https://docs.ultralytics.com/zh/quickstart/#custom-installation-methods).
This repository contains `ultralytics` binary single-file wheels with `opencv-python` `torch` `torchvision` dependency removed, which only need to be installed from a local source:

```shell
pip install ultralytics-non-opencv-torch --find-links /build/ultralytics-non-opencv-torch/
```

Where:

- `--find-links`: Uses non-Simple API standard installation source;
  - Local source: Points to the build output directory, such as `./path/to/local` or `file://./path/to/local`, or file;
  - HTML path: Contains links pointing to binary files. Only parses links in the file.

And additionally install any version of OpenCV:

```shell
pip install opencv-python|opencv-python-headless|opencv-contrib-python|opencv-contrib-python-headless
```

Of course, you can also install the `opencv-contrib-python-headless` with non-free features included in this repository:

```shell
pip install opencv-contrib-python-headless-nonfree --find-links /build/opencv-nonfree/
```

> [!NOTE]
>
> All `ultralytics-non-opencv-torch` packages in this repository are automatically built by GitHub Actions. You can verify security by [auditing commits](https://github.com/gaobobo/opencv-pytorch_MachineVision/pulls?q=is%3Apr+is%3Aclosed+author%3Aapp%2Fgithub-actions),
> [building yourself](#using-ultralytics-packages-without-opencv-dependencies), or [reviewing build scripts](./).

### Building Ultralytics Packages without OpenCV Dependencies

- First, place the following file under `/.github/workflows` in the main branch of your repository and push the changes to GitHub:
  - [ultralytics-non-opencv-torch-build.yml](./ultralytics-non-opencv-torch-build.yml)
  
> [!IMPORTANT]
> 
> - Cannot be placed in any subdirectory, must be under `/.github/workflows`;
> - The file must exist in the main branch. If testing is needed, push an empty file to the main branch and merge changes to the main branch later.

- Open repository settings on GitHub (Settings) and allow GitHub Actions to push and modify branches (Actions > General > Workflow permissions > Allow GitHub Actions to create and approve pull requests).
- Open the Actions tab and select the workflow `ultralytics-non-opencv-build.yml` on the left.
- Click "Run Workflow" and configure:
  - Use workflow from: Which branch's workflow to use. This also affects the branch that the subsequently created pull request merges into;
  - Checkout ultralytics/ultralytics repos' ref: The pointer when checking out the `ultralytics/ultralytics` repository. Supports branch names, SHA, tags;
  - The path relative this repo that python wheels save to: Where to save build results in the current repository;
  - python version that build environment: Python version used during building. This package builds a universal version.

- After the build is complete, a pull request will be created for the repository. Please review and merge. The build takes about 1 minute.

> [!CAUTION]
>
> The `ultralytics/ultralytics` repository uses [AGPL-3.0 license](https://github.com/ultralytics/ultralytics/blob/main/LICENSE).
> This may require distributing source code and modifications of any work containing this library or its derivatives under the same license.
>
> This script introduces a [patch](/build/ultralytics-non-opencv/ultralytics-non-opencv-torch.patch) during building
> to comply with AGPL-3.0 license requirements.