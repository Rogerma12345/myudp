# myudp

`myudp` 是一个用于整理和维护 UDP 相关工具构建资产的仓库，目前主要包含：

- 面向 OpenWrt 的 [`udp2raw-fakehttp`](custom-packages/udp2raw-fakehttp/Makefile) 自定义包定义
- 面向 Docker 的 [`fakesip`](docker/fakesip/Dockerfile) 构建文件

本仓库的定位是“构建、打包与集成”，并不重新实现这些上游项目的核心功能。

## 仓库内容

### [`custom-packages/`](custom-packages/)
用于存放 OpenWrt 自定义包。

当前包含：

- [`custom-packages/udp2raw-fakehttp/`](custom-packages/udp2raw-fakehttp/)
  - 提供 [`udp2raw-fakehttp`](custom-packages/udp2raw-fakehttp/Makefile) 的 OpenWrt 包定义
  - 上游源码来自 [`udp2raw`](custom-packages/udp2raw-fakehttp/Makefile)
  - 当前包信息可见 [`PKG_NAME:=udp2raw-fakehttp`](custom-packages/udp2raw-fakehttp/Makefile:3)、[`PKG_VERSION:=20250315.0`](custom-packages/udp2raw-fakehttp/Makefile:4)、[`PKG_SOURCE_VERSION:=c4995ea`](custom-packages/udp2raw-fakehttp/Makefile:9)
  - 包描述见 [`define Package/udp2raw-fakehttp/description`](custom-packages/udp2raw-fakehttp/Makefile:30)

### [`docker/`](docker/)
用于存放容器化构建文件。

当前包含：

- [`docker/fakesip/`](docker/fakesip/)
  - 提供 [`FakeSIP`](docker/fakesip/Dockerfile:20) 的多阶段 Docker 构建定义
  - 运行入口见 [`ENTRYPOINT ["fakesip"]`](docker/fakesip/Dockerfile:38)

## 上游项目

本仓库依赖以下上游项目：

- `udp2raw`：<https://github.com/MikeWang000000/udp2raw>
- `FakeSIP`：<https://github.com/MikeWang000000/FakeSIP>

本仓库主要维护这些项目的构建和打包相关文件。上游项目的功能实现、版本演进和原始许可证以其官方仓库为准。

## 使用说明

### 1. 在 OpenWrt 中使用 `udp2raw-fakehttp`

将 [`custom-packages/udp2raw-fakehttp/`](custom-packages/udp2raw-fakehttp/) 放入 OpenWrt 源码树或 SDK 的自定义包目录后，按 OpenWrt 常规方式执行菜单配置与编译。

此包会从上游仓库拉取指定提交版本，并在构建阶段调整上游 makefile。相关逻辑见：

- [`PKG_SOURCE_URL:=https://github.com/MikeWang000000/udp2raw.git`](custom-packages/udp2raw-fakehttp/Makefile:8)
- [`PKG_SOURCE_VERSION:=c4995ea`](custom-packages/udp2raw-fakehttp/Makefile:9)
- [`define Build/Configure`](custom-packages/udp2raw-fakehttp/Makefile:36)
- [`define Package/udp2raw-fakehttp/install`](custom-packages/udp2raw-fakehttp/Makefile:43)

### 2. 构建 `fakesip` Docker 镜像

在仓库根目录执行：

```bash
docker build -t myudp-fakesip ./docker/fakesip
```

查看帮助：

```bash
docker run --rm myudp-fakesip --help
```

如需指定上游版本，可在构建时传入 `PKG_SOURCE_VERSION`：

```bash
docker build -t myudp-fakesip --build-arg PKG_SOURCE_VERSION=master ./docker/fakesip
```

对应构建参数定义见 [`ARG PKG_SOURCE_VERSION=master`](docker/fakesip/Dockerfile:4)。

## 版本与可复现性

仓库中的部分构建配置会固定上游提交，而不是始终跟随最新版本，以提升构建结果的一致性与可复现性。例如 [`udp2raw-fakehttp`](custom-packages/udp2raw-fakehttp/Makefile) 当前固定的上游提交为 [`c4995ea`](custom-packages/udp2raw-fakehttp/Makefile:9)。

## License

本仓库自身内容采用 MIT License，详见 [`LICENSE`](LICENSE)。

需要注意：

- 本仓库中的打包脚本、构建文件和说明文档适用 MIT License
- 第三方上游项目仍分别遵循其各自的许可证
- 在分发或二次使用时，应同时核对上游项目的许可证要求

## 免责声明

本仓库仅用于合法授权环境下的构建、测试与集成工作。使用者应自行评估部署方式、网络环境及合规要求，并对实际使用后果负责。
