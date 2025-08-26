# 构建包含非自由功能的 OpenCV

> [!TIP]
> 
> For English, see [second half part](#building-opencv-with-non-free-features).

OpenCV 默认不包含任何非自由的功能，包括专利算法，如 SURF 。若想使用这些功能，除了使用旧版本的OpenCV，还可以重新构建带有非自由功能的 OpenCV。

本仓库提供了简单的工作流，这些工作流使用 GitHub Actions 运行。构建完成后，工作流会新建一系列分支和拉取请求，以便将编译后的二进制文件引入仓库。

## 使用旧版本的 OpenCV

旧版本的 OpenCV 包含了某些专利算法，如 SURF 。以 SURF 为例，`opencv-contrib-python` 的 3.4.3 是最后一个引入该算法的版本。

由于 `opencv-python 3.4.x` 在最新的版本中只支持 Python 3.7 ，因此如果使用 Conda 构建该环境，那么配置文件应至少包含：

```yaml
dependencies:
  - python==3.7
  - pip
  - pip:
      - opencv-contrib-python-headless<3.4.3
```

## 构建 OpenCV

欲要在较新的 Python 中使用或需要使用 `opencv-contrib-python` 的新特性，那么需要手动构建。幸运的是，OpenCV 提供了 [opencv-python](https://github.com/opencv/opencv-python)
项目专门用于构建适用于 Python 的 Wheel 。

### 手动构建


设置环境变量 `CMAKE_ARGS="-DOPENCV_ENABLE_NONFREE=ON"` 后正常编译。

敬请参阅 [opencv-python 仓库的构建指南](https://github.com/opencv/opencv-python/tree/4.x?tab=readme-ov-file#manual-builds)。

### 使用 GitHub Actions 构建

> [!IMPORTANT]
> 
> 该指南适用于 GitHub Actions ，不适用于其他 CI/CD 平台。

- 首先，将 [opencv-nonfree-build_unix.yml](./opencv-nonfree-build_unix.yml) 和 [opencv-nonfree-build_win-x64.yml](./opencv-nonfree-build_win-x64.yml)
  置于仓库主分支的 `/.github/workflows` 下，并将更改推送至 GitHub 。

> [!IMPORTANT]
> 
> - 不能放置于任何的子目录，只能是 `/.github/workflows` 下。
> - 必须在主分支存在该文件。如果需要测试，请向主分支推送空文件并稍后将更改合并到主分支。

- 打开 GitHub 上的仓库设置（Settings）并允许 GitHub Actions 推送、修改分支（Actions > General > Workflow permissions > Allow 
  GitHub Actions to create and approve pull requests）。
- 打开 Actions 选项卡，在左侧选择工作流。
  - `opencv-nonfree-build_unix.yml` 是适用于 Linux x64, Linux ARM, MacOS Intel, MacOS M 平台；
  - `opencv-nonfree-build_win-x64.yml` 是适用于 Windows x64 平台；

> [!IMPORTANT]
> 
> - 本仓库尚未提供适用于 Windows ARM 平台的包，原因是自动构建时会出现问题。敬请参阅 [opencv-python 仓库的构建指南](https://github.com/opencv/opencv-python/tree/4.x?tab=readme-ov-file#manual-builds)
>   并尝试手动构建。
> - 所构建的包为“无头”包，即 `opencv-contrib-python-headless` 。

- 点击“Run Workflow”并设置：
    - Use workflow from: 使用哪一个分支的工作流。这也影响稍后创建的拉取请求合并到的分支；
    - Checkout opencv/opencv-python repos' ref：opencv-python 仓库所检出时的指针。支持分支名称、SHA、标签；
    - The path relative this repo that python wheels save to：将构建结果保存到当前仓库的位置；
    - python version that build environment：构建时使用的 Python 版本。这直接影响构建包所适用的 Python 版本。

- 等待构建完成后，会向仓库新建一拉取请求。请审阅并合并。
  - `opencv-nonfree-build_unix.yml` 构建需要约 30 分钟；
  - `opencv-nonfree-build_win-x64.yml` 构建需要约 20 分钟。

## 安装

使用如下命令安装：

`pip install opencv-contrib-python-headless --no-index --force-reinstall --find-links <本地源/HTML路径>`

其中：

- `--no-index`：不使用任何 Simple API 源，即 PyPI 等。防止意外安装来自其他源的包；
- `--force-reinstall`：无论任何情况，均重新安装；
- `--find-links`：非 Simple API 标准安装源；
  - 本地源：指向构建输出的目录，如 `./path/to/local` 或 `file://./path/to/local`；
  - HTML路径：包含指向二进制文件的链接。只解析文件中的链接。

---

# Building OpenCV with Non-Free Features

OpenCV by default does not include any non-free features, including patented algorithms like SURF. If you wish to use these features, you can either use an older version of OpenCV or rebuild OpenCV with non-free features enabled.

This repository provides a simple workflow that uses GitHub Actions to build OpenCV. After the build is complete, the workflow will create a series of branches and pull requests to integrate the compiled binaries into the repository.

## Using an Older Version of OpenCV

Older versions of OpenCV include certain patented algorithms, such as SURF. For example, `opencv-contrib-python` version 3.4.3 is the last version to include this algorithm.

Since `opencv-python 3.4.x` only supports Python 3.7 in its latest version, if you're using Conda to build the environment, your configuration file should include at least:

```yaml
dependencies:
  - python==3.7
  - pip
  - pip:
      - opencv-contrib-python-headless<3.4.3
```

## Building OpenCV

If you want to use OpenCV with a newer version of Python or require newer features of `opencv-contrib-python`, you will need to build it manually. Fortunately, OpenCV provides the [opencv-python](https://github.com/opencv/opencv-python) project specifically for building Python wheels.

### Manual Build

After setting the environment variable `CMAKE_ARGS="-DOPENCV_ENABLE_NONFREE=ON"`, you can compile OpenCV normally.

Please refer to the [opencv-python build guide](https://github.com/opencv/opencv-python/tree/4.x?tab=readme-ov-file#manual-builds) for more details.

### Building with GitHub Actions

> [!IMPORTANT]
> This guide applies to GitHub Actions and not other platforms.

* First, place the files [opencv-nonfree-build\_unix.yml](./opencv-nonfree-build_unix.yml) and [opencv-nonfree-build\_win-x64.yml](./opencv-nonfree-build_win-x64.yml) into the `/.github/workflows` directory of the main branch and push the changes to GitHub.

> [!IMPORTANT]
>
> * These files must be placed in `/.github/workflows` and not in any subdirectory.
> * The files must exist on the main branch. To test, you can push an empty file to the main branch and later merge it.

* Open the repository settings on GitHub (Settings) and allow GitHub Actions to push and modify branches (Actions > General > Workflow permissions > Allow GitHub Actions to create and approve pull requests).
* Go to the Actions tab and select the workflow from the left side.

  * `opencv-nonfree-build_unix.yml` is for Linux x64, Linux ARM, MacOS Intel, and MacOS M platforms.
  * `opencv-nonfree-build_win-x64.yml` is for Windows x64 platforms.

> [!IMPORTANT]
>
> * This repository does not currently provide packages for Windows ARM platforms due to issues with automated builds. Please refer to the [opencv-python build guide](https://github.com/opencv/opencv-python/tree/4.x?tab=readme-ov-file#manual-builds) and attempt to build manually.
> * The built packages will be headless, i.e., `opencv-contrib-python-headless`.

* Click "Run Workflow" and configure the settings:

  * Use workflow from: Select the branch from which to run the workflow. This also affects the branch the pull request will be merged into.
  * Checkout opencv/opencv-python repos' ref: Select the pointer for the opencv-python repository. It can be a branch name, SHA, or tag.
  * The path relative to this repo that python wheels save to: Specify where to save the build results within the repository.
  * Python version that build environment: Select the Python version used for the build. This will directly affect which Python versions the build is compatible with.

* Wait for the build to finish, then a pull request will be created in the repository. Please review and merge.

  * `opencv-nonfree-build_unix.yml` build takes about 30 minutes.
  * `opencv-nonfree-build_win-x64.yml` build takes about 20 minutes.

## Installation

Install using the following command:

`pip install opencv-contrib-python-headless --no-index --force-reinstall --find-links <local-source/HTML-path>`

Where:

* `--no-index`: Prevents using any Simple API sources like PyPI, ensuring no packages are installed from other sources.
* `--force-reinstall`: Forces reinstallation regardless of existing installations.
* `--find-links`: Specifies a non-Simple API standard installation source:

  * Local source: A directory pointing to the build output, such as `./path/to/local` or `file://./path/to/local`.
  * HTML path: A directory containing links to binary files. Only links in the file will be parsed.
