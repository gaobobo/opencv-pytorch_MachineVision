# 机器视觉-OpenCV、PyTorch

> [!TIP]
>
> For English, see the [second-half part](#machine-vision---opencv-pytorch)

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release)

本仓库包含机器视觉作业 Jupyter Notebook 并保留了全部的运行结果，你可直接在线预览笔记本。

本仓库另提供了一在线运行环境 Binder 以满足交互式探索，你可直接点击[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release)以访问，或在[index.ipynb](./index.ipynb)打开特定章节的笔记本。

## 构建与运行

### 在线运行

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release)
提供了在线运行的环境。访问 [![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release)
后，等待构建完成，完成后会自动打开Jupyter Notebook窗口。

> [!IMPORTANT]
> 
> 默认不安装 PyTorch，原因是 Binder 服务器没有提供 GPU 不适合进行深度学习任务。

### 本地运行

若要在本地运行，需要克隆仓库并安装依赖。本仓库根目录提供了 Conda 环境配置[environment.yml](./environment.yml)，你可使用
`conda env create -f ./environment.yml` 命令构建环境。使用参数`-n`指定环境名称，默认环境名称为`opencv-pytorch_MachineVision`。

> [!TIP]
>
> 提供了 [environment_CN.yml](./environment_CN.yml)，其使用清华大学开源软件镜像站。

本仓库部分依赖使用的是自行构建的版本，这些依赖已经[包含在仓库中](./build/)。

完整步骤：

- 安装Git。在Linux操作系统中应当预装，其他系统前至[Git Download](https://git-scm.com/downloads)下载。
- 使用`git clone https://github.com/gaobobo/opencv-pytorch_MachineVision.git -b binder-release --depth 1` 将仓库克隆到本地，或从
[Github仓库页面](https://github.com/gaobobo/opencv-pytorch_MachineVision)下载仓库的ZIP存档。

> [!NOTE]
>
> 由于本仓库包含数据集，并且考虑到 Binder 可能频繁拉取 [Git LFS](https://git-lfs.com/) 文件导致触发 [GitHub 的限额](https://docs.github.com/en/billing/concepts/product-billing/git-lfs#free-use-of-git-lfs)，
> 因此没有使用Git LFS，导致完整克隆本仓库时速度较慢。建议只克隆 `binder-release` 分支并使用浅克隆以免本地存储库过大并加快克隆速度。

- 安装[Anaconda](https://www.anaconda.com/download/success)或[miniconda](https://www.anaconda.com/download/success)；
- `cd`到仓库根目录；
- 打开命令行并运行`conda env create -f ./environment.yml`命令。这会新建名为`opencv-pytorch_MachineVision`的环境并安装所需依赖；
- 使用`conda activate opencv-pytorch_MachineVision`激活环境；
- 输入`ipython notebook`运行Jupyter Notebook；
- 在弹出的浏览器窗口中，打开[index.ipynb](./index.ipynb)。
- 默认不安装 PyTorch，请在[PyTorch官网](https://pytorch.org/get-started/locally/)上选择适合的架构重新安装。

> [!IMPORTANT]
> 
> 在Windows系统中，需要先启动`Anaconda Promote`，然后在弹出的终端内运行`conda`的命令。

### 使用 Docker 部署

使用下列命令拉取镜像：

```shell
docker pull ghcr.io/gaobobo/opencv-pytorch_machinevision:latest
```

默认端口为 8888。

此后，需要手动克隆仓库抑或是直接通过 `nbgitpuller` 自动拉取：

```text
http://<HOST>:<PORT>/hub/user-redirect/git-pull?repo=https%3A%2F%2Fgithub.com%2Fgaobobo%2Fopencv-pytorch_MachineVision&urlpath=lab%2Ftree%2Fopencv-pytorch_MachineVision%2Findex.ipynb&branch=binder-release
```

### 构建包

#### opencv-contrib-python-headless-nonfree

本项目使用了 SURF 算法。由于该算法已经获取专利，因此无论 `opencv-contrib-python` 还是 `opencv-python` 均不包含该算法。

因此，本仓库重新构建了包含该算法及其他非自由功能的 `opencv-contrib-python-headless-nonfree` 包。该包包含了完整的非自由功能，包括 SURF 算法。

本仓库包含 Python 1.12 版本的包，使用下列命令安装：

```shell
pip install opencv-contrib-python-headless --force-reinstall --no-index --find-links ./build/opencv-nonfree/
```

本仓库提供了简单的自动构建工作流。请参阅 [/.github/workflows/README.md](/.github/workflows/README.md)以了解如何使用 GitHub Actions 自动构建。

#### ultralytics-non-opencv-torch

`ultralytics` 包为 YOLO 模型提供了全新的 CLI 与 Python API。不过，`ultralytics` 包默认会安装依赖`opencv-python`，这会和已有的其他版本的 `opencv-python` 冲突。

因此，本仓库构建了不含 `opencv-python` `torch` `torchvision` 依赖的 `ultralytics` 包。使用下列命令安装：

```shell
pip install ultralytics-non-opencv-torch --force-reinstall --no-index --find-links ./build/ultralytics-non-opencv-torch/
```

别忘记安装你喜爱的 OpenCV 版本。当然，`opencv-contrib-python-headless-nonfree` 也可以：

```shell
pip install opencv-contrib-python-headless --force-reinstall --no-index --find-links ./build/opencv-nonfree/
```

本仓库提供了简单的自动构建工作流。请参阅 [/.github/workflows/README.md](/.github/workflows/README.md)以了解如何使用 GitHub Actions 自动构建。

## 贡献本项目

本项目仅作为课程作业，因此不考虑贡献。

## 许可

本项目采用 [AGPL-3.0 许可](./LICENSE) 许可。该项目包含第三方的资源，这些资源可能以其他的许可发布，请参阅 [LICENSE-THIRD-PART](./LICENSE-THIRD-PART) 了解更多。

### `ultralytics-non-opencv` 许可说明

根据 [AGPL-3.0 许可](https://github.com/ultralytics/ultralytics/blob/main/LICENSE) 许可，需要开源修改后的源码。

本仓库以补丁 `.patch` 的形式提供源码。预想获取完整源码：

- 克隆 [ultralytics/ultralytics](https://github.com/ultralytics/ultralytics) 存储库。本仓库基于 `v8.3.136` 标签构建；
- `cd` 至上述仓库目录
- 使用`git apply <TO-THIS-REPO>/build/ultralytics-non-opencv-torch.patch` 以修补。此时，代码库应与修改后的源码相同。

---

# Machine Vision - OpenCV, PyTorch

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release)

This repository contains machine vision assignments in Jupyter Notebook format, with all execution results preserved. You can preview the notebook directly online.

Additionally, the repository offers an interactive environment via Binder. You can access it directly by clicking on [![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release), or open specific sections of the notebook in [index.ipynb](./index.ipynb).

## Build and Run

### Run Online

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release)
An online running environment is provided. After accessing [![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/gaobobo/opencv-pytorch_MachineVision/binder-release?urlpath=git-pull%3Frepo%3Dhttps%253A%252F%252Fgithub.com%252Fgaobobo%252Fopencv-pytorch_MachineVision%26urlpath%3Dlab%252Ftree%252Fopencv-pytorch_MachineVision%252Findex.ipynb%26branch%3Dbinder-release), wait for the build to finish. Once completed, the Jupyter Notebook window will open automatically.

[!IMPORTANT]

PyTorch is not installed by default, because Binder servers do not provide GPUs and are not suitable for deep learning tasks.

### Run Locally

To run locally, you need to clone the repository and install dependencies. The root directory of this repository provides a Conda environment configuration in [environment.yml](./environment.yml), which you can use to create the environment with the command:
`conda env create -f ./environment.yml`. Use the `-n` option to specify the environment name, with the default being `opencv-pytorch_MachineVision`.

> \[!TIP]
>
> An alternative [environment\_CN.yml](./environment_CN.yml) is provided, using the Tsinghua University open-source software mirror.

Some dependencies in this repository are custom-built versions and are [included in the repository](./build/).

Steps:

* Install Git. It should be pre-installed on Linux systems. For other systems, download it from [Git Download](https://git-scm.com/downloads).
* Clone the repository to your local system with `git clone https://github.com/gaobobo/opencv-pytorch_MachineVision.git -b binder-release --depth 1`, or download the ZIP archive from the [GitHub repository page](https://github.com/gaobobo/opencv-pytorch_MachineVision).

> \[!NOTE]
>
> Since this repository includes datasets and to avoid frequent pulls of [Git LFS](https://git-lfs.com/) files which may trigger [GitHub limits](https://docs.github.com/en/billing/concepts/product-billing/git-lfs#free-use-of-git-lfs), Git LFS is not used. As a result, cloning the entire repository may be slow. It is recommended to clone only the `binder-release` branch and use shallow cloning to avoid excessive local repository size and speed up the cloning process.

* Install [Anaconda](https://www.anaconda.com/download/success) or [miniconda](https://www.anaconda.com/download/success).
* `cd` to the root directory of the repository.
* Open the command line and run `conda env create -f ./environment.yml` to create the environment and install required dependencies.
* Use `conda activate opencv-pytorch_MachineVision` to activate the environment.
* Enter `ipython notebook` to run Jupyter Notebook.
* In the browser window that opens, open [index.ipynb](./index.ipynb).
* PyTorch is not installed by default, please visit [PyTorch Official](https://pytorch.org/get-started/locally/)
  to install properly version.

> \[!IMPORTANT]
>
> On Windows, you need to first launch `Anaconda Prompt`, then run the `conda` commands in the terminal that opens.

### Use Docker to Deploy

Pull Docker image:

```shell
docker pull ghcr.io/gaobobo/opencv-pytorch_machinevision:latest
```

The default port is 8888。

### Build Packages

#### opencv-contrib-python-headless-nonfree

This project uses the SURF algorithm, which is patented. Therefore, neither `opencv-contrib-python` nor `opencv-python` includes this algorithm.

Thus, this repository has rebuilt the `opencv-contrib-python-headless-nonfree` package, which includes the SURF algorithm and other non-free features.

The package for Python version 1.12 is available. Install it with the following command:

```shell
pip install opencv-contrib-python-headless --force-reinstall --no-index --find-links ./build/opencv-nonfree/
```

This repository also provides a simple automated build workflow. Please refer to [/.github/workflows/README.md](/.github/workflows/README.md) for instructions on how to use GitHub Actions to build automatically.

#### ultralytics-non-opencv-torch

The `ultralytics` package provides a new CLI and Python API for YOLO models. However, the default `ultralytics` package installs `opencv-python` as a dependency, which may conflict with other versions of `opencv-python` already installed.

Therefore, this repository has built a version of the `ultralytics` package that does not include the `opencv-python` `torch` `torchvision` dependency. Install it using the following command:

```shell
pip install ultralytics-non-opencv-torch --force-reinstall --no-index --find-links ./build/ultralytics-non-opencv-torch/
```

Don’t forget to install your favorite version of OpenCV. Of course, you can also use `opencv-contrib-python-headless-nonfree`:

```shell
pip install opencv-contrib-python-headless --force-reinstall --no-index --find-links ./build/opencv-nonfree/
```

This repository also provides a simple automated build workflow. Please refer to [/.github/workflows/README.md](/.github/workflows/README.md) for instructions on how to use GitHub Actions to build automatically.

## Contributing to This Project

This project is purely for coursework and does not consider contributions.

## License

This project is licensed under the [AGPL-3.0 License](./LICENSE). It includes third-party resources that may be licensed under different terms. Please refer to [LICENSE-THIRD-PART](./LICENSE-THIRD-PART) for more details.

### `ultralytics-non-opencv` License Notice

According to the [AGPL-3.0 License](https://github.com/ultralytics/ultralytics/blob/main/LICENSE), modified source code must be open-sourced.

The modified source code is provided in patch `.patch` form. To obtain the full source code:

* Clone the [ultralytics/ultralytics](https://github.com/ultralytics/ultralytics) repository. This repository is built based on the `v8.3.136` tag.
* `cd` to the cloned repository directory.
* Apply the patch using `git apply <TO-THIS-REPO>/build/ultralytics-non-opencv-torch.patch`. The codebase should now match the modified source.
