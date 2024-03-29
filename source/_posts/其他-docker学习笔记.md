---
title: docker学习笔记
date: 2024-02-18 10:56:00
tags: dcoker
categories: 其他
description: 记录学习docker后的笔记，本篇文章更多的是记录公认的简单知识，暂时没有书写自己的心得。阅读时长：5min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231207133017.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**学习docker后的笔记，笔者也是初学者，学艺不精，本篇文章更多的是记录公认的简单知识，暂时没有书写自己的心得。

### 什么是docker

Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。传统的虚拟机技术启动应用服务往往需要数分钟，而 Docker 容器应用，由于直接运行于宿主内核，无需启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间。
"宿主内核"指的是操作系统的核心部分，它是在硬件和应用程序之间的一个抽象层，负责管理计算机的资源和提供基本的服务。在Docker中，容器直接共享宿主机的内核，而不需要启动完整的操作系统。与传统虚拟机不同，虚拟机在宿主机上运行一个完整的操作系统，包括独立的内核。这导致虚拟机启动应用服务时需要启动整个操作系统，耗时较长。相比之下，Docker容器通过与宿主机共享内核，能够更快速地启动应用服务。



### **镜像**

- 镜像是一个只读的模板，包含了运行容器所需的文件系统、环境变量、软件设置以及应用程序的代码和依赖项等。
- 镜像可以看作是应用程序的打包，它包含了运行应用程序所需的一切。
- 镜像是静态的，一旦创建就不会改变。如果需要更新镜像，需要重新构建新的镜像版本。



### 容器

- 容器是从镜像创建的可运行实体，是镜像在运行时的实例化。
- 容器包含了镜像的内容以及正在运行的应用程序实例，同时也包含了运行时所需的文件系统和网络配置等。
- 容器是动态的，可以启动、停止、删除和重启。

和镜像的关键区别：

- **状态**：镜像是静态的，一旦创建就不会改变；容器是动态的，可以启动、停止和删除。
- **可变性**：镜像是只读的，不可更改；容器是可读写的，可以修改其状态。
- **用途**：镜像用于创建容器，容器是运行镜像的实例化。



### DockerCompose

Docker Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。通过一个单独的 YAML 文件来配置应用程序的服务、网络和卷等，使得在多容器环境中部署应用程序变得更加简单。

以下是 Docker Compose 的一些关键特性和用途：

1. **定义多容器应用程序**：使用 Docker Compose，您可以在一个 YAML 文件中定义您的整个应用程序的服务、网络、卷等组件，而无需编写复杂的 Dockerfile 或者 Docker 命令。
2. **便捷的启动和停止**：Docker Compose 允许您通过简单的命令一次性启动或停止整个应用程序的所有服务，包括它们之间的依赖关系，大大简化了应用程序的管理和操作。
3. **服务间通信**：通过 Docker Compose，您可以轻松地定义服务之间的通信方式，包括网络设置和链接等，使得不同服务之间的交互变得更加简单和直观。
4. **环境配置**：Docker Compose 支持通过环境变量和配置文件等方式对应用程序的环境进行灵活配置，使得应用程序在不同环境中的部署更加便捷和灵活。

总的来说，Docker Compose 是一个用于简化多容器 Docker 应用程序的定义、运行和管理的工具，通过统一的配置文件和简单的命令，使得在 Docker 环境中部署和管理复杂的应用程序变得更加容易。



### Docker网络

Docker 网络是 Docker 容器之间通信的基础设施，它允许在 Docker 主机上运行的多个容器之间建立网络连接，并进行数据传输。Docker 网络使得不同容器之间可以相互通信，从而支持分布式应用程序的部署和运行。

以下是 Docker 网络的一些关键特性和概念：

1. **默认网络**：当您安装 Docker 时，Docker 会自动创建一个名为 "bridge" 的默认网络。该网络允许在同一 Docker 主机上运行的容器之间进行通信。
2. **用户自定义网络**：除了默认网络外，您还可以创建自定义的 Docker 网络。用户自定义网络允许在多个 Docker 主机上的容器之间建立通信，甚至可以跨越不同的 Docker 宿主机。
3. **网络驱动程序**：Docker 支持多种网络驱动程序，用于实现不同类型的网络连接，比如 bridge、overlay、macvlan 等。每种驱动程序都有自己的特点和适用场景。
4. **连接容器**：通过 Docker 网络，您可以轻松地将多个容器连接到同一个网络中，从而实现容器之间的通信。连接容器使得分布式应用程序的部署和管理变得更加简单和灵活。

总的来说，Docker 网络是 Docker 容器之间通信的基础设施，通过它，您可以在 Docker 主机上运行的多个容器之间建立网络连接，并实现数据传输和通信。



### Docker卷

Docker 卷（Volumes）是 Docker 中用于持久化数据的机制，允许容器与主机之间或容器之间共享和存储数据。简单来说，Docker 卷就是将主机文件系统中的目录或文件与容器内的目录或文件进行关联，从而实现数据的持久化存储和共享。

以下是 Docker 卷的一些关键特性和用途：

1. **持久化存储**：Docker 卷允许容器中的数据在容器被删除或重新创建时保持不变，从而实现数据的持久化存储。这使得容器可以重启、迁移或扩展而不会丢失数据。
2. **容器间共享数据**：Docker 卷允许多个容器共享同一份数据，从而实现容器之间的数据共享。这对于多个容器之间需要共享配置文件、日志文件或其他数据的情况非常有用。
3. **主机与容器之间的数据传输**：Docker 卷还允许容器与主机之间进行数据传输，从而实现容器与主机之间的数据共享和同步。
4. **数据备份和恢复**：通过使用 Docker 卷，您可以轻松地备份和恢复容器中的数据，从而保护重要数据免受意外删除或损坏的影响。

总的来说，Docker 卷是 Docker 中用于持久化数据的机制，通过它，您可以在容器与主机之间或容器之间共享和存储数据，实现数据的持久化存储和共享。



### 修饰符

- `-t`（`--tty`）: 分配一个伪终端。这个选项通常与`-i`一起使用，以便为容器分配一个终端，这对于交互式会话很有用。
- `-i`（`--interactive`）: 保持STDIN开放，即使未附加也是如此。这允许你与运行的容器进行交互。
- `-d`（`--detach`）: 在分离模式下运行容器。使用这个选项，Docker会启动容器然后立即返回控制台，而不是附加到容器的标准输入/输出。
- `-p`（`--publish`）: 发布容器的端口到宿主机。格式为`<宿主机端口>:<容器端口>`。
- `--name`: 为容器指定一个名称，这样你可以通过名称而不是容器ID来引用它。
- `-v`（`--volume`）: 挂载宿主机的目录或卷到容器。格式为`<宿主机路径>:<容器路径>`。
- `--rm`: 当容器退出时自动移除容器。这对于临时或一次性任务很有用，因为它避免了之后还需要手动清理容器。
- `--env`（`-e`）: 设置环境变量。格式为`KEY=value`。

这些修饰符可以帮助你更精准地控制Docker容器的行为。例如，如果你想要以交互模式运行一个Ubuntu容器并分配一个终端，你可以这样做：

```bash
docker run -it ubuntu bash
```

在这个例子中，`-it`组合允许你在容器中打开一个bash shell，并与之交互。如果你想要在后台运行这个容器，可以使用`-d`：

```bash
docker run -d ubuntu
```

现在，容器会在后台启动，并且你可以使用`docker ps`来查看它的状态。



### yml后缀

`.yml` 或 `.yaml` 文件后缀代表了 YAML 文件，它们是一种常用于配置文件的数据序列化格式。YAML 是 "YAML Ain't Markup Language"（YAML不是标记语言）的递归缩写，这种格式以其可读性高和易于理解的结构而闻名。

YAML 文件通常用于编写配置文件，例如 Docker Compose 文件、Kubernetes 资源定义文件、持续集成/持续部署（CI/CD）管道配置，以及许多现代软件应用程序和服务的配置。YAML 文件中的数据以键值对的形式呈现，支持数组、散列表、标量等数据结构。

```shell
version: '3.8'
services:
  webapp:
    image: my-webapp:latest
    ports:
      - "5000:5000"
    environment:
      - DEBUG=false
```

在这个例子中，`version` 指定了文件遵循的 Docker Compose 文件格式版本，`services` 下定义了一个服务 `webapp`，其中包含了该服务的镜像、端口映射和环境变量配置。

YAML 文件的一个关键特点是它依赖缩进来表示数据层次结构，这使得它非常适合表示嵌套的数据结构。正确的缩进对于 YAML 文件的解析至关重要，通常使用空格（而不是制表符）来创建缩进。



###  Kubernetes

Kubernetes（也称为 K8s）是一个开源的容器编排系统，用于自动化应用程序容器的部署、扩展和管理。它最初是由 Google 设计并捐赠给 Cloud Native Computing Foundation（CNCF）来维护。Kubernetes 已经成为容器化应用程序的编排和管理的事实标准。

Kubernetes 的主要特点和功能包括：

1. **服务发现和负载均衡**：Kubernetes 可以使用 DNS 名称或者自己的 IP 地址自动发现容器，并且可以在容器之间自动分配流量，以便负载均衡。
2. **存储编排**：Kubernetes 允许你自动挂载存储系统，无论是本地存储、公共云提供商（如 AWS、GCP、Azure）还是网络存储系统（如 NFS、iSCSI）。
3. **自动部署和回滚**：你可以描述期望的部署状态，Kubernetes 可以自动改变实际状态到期望状态。如果有什么不对劲，Kubernetes 可以回滚到之前的状态。
4. **自动装箱（bin packing）**：Kubernetes 允许你指定每个容器需要多少 CPU 和内存（RAM）。Kubernetes 可以将容器放到集群中的节点上，以最优化资源利用率。
5. **自我修复**：Kubernetes 会重启失败的容器、替换和关闭不响应的容器，并且只有当容器准备就绪时才会向他们发送流量。
6. **密钥与配置管理**：Kubernetes 允许你存储和管理敏感信息，如密码、OAuth 令牌和 ssh 密钥，你可以在不重启容器的情况下更新应用配置和密钥。
7. **水平扩展**：简单的命令、用户界面或者基于 CPU 使用率等指标，Kubernetes 都能自动扩展应用程序的副本数。

Kubernetes 架构通常包括主节点（master node）和工作节点（worker nodes）。主节点负责管理集群状态，而工作节点则运行应用程序的容器。Kubernetes 的核心组件包括 API 服务器、调度器、控制器管理器和 etcd（用于存储集群状态的键值存储）等。

Kubernetes 的用户可以通过命令行工具 `kubectl` 或者 API 来与集群交互，定义应用程序的期望状态、查询资源状态或修改集群配置等。



### 参考链接

- [docker-从入门到实践](https://yeasy.gitbook.io/docker_practice/introduction/why)

