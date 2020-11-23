---
title: 安装和预配后，验证是否成功并排除故障
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
origin.date: 10/06/2020
ms.date: 10/13/2020
ms.author: v-tawe
ms.custom: include file
ms.openlocfilehash: efef887ab3cc86998ba7db079f1da90e4b0c1f3a
ms.sourcegitcommit: 9438c9db77338cecacf37d2fc178e757df9de83d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595278"
---
## <a name="verify-successful-setup"></a>验证安装是否成功

检查 IoT Edge 服务的状态。 该服务应列为“正在运行”。  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

检查服务日志。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

如果刚刚完成了 IoT Edge 运行时的安装，可能会看到运行 **Deploy-IoTEdge** 之后、运行 **Initialize-IoTEdge** 之前发生的错误列表。 这些错误在预料之中，因为该服务在经过配置之前会尝试启动。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

运行[故障排除工具](../articles/iot-edge/troubleshoot.md#run-the-check-command)，检查最常见的配置和网络错误。

```powershell
iotedge check
```

在将第一个模块部署到设备上的 IoT Edge 之前， **$edgeHub** 系统模块不会部署到设备。 因此，自动检查会返回一个针对 `Edge Hub can bind to ports on host` 连接性检查的错误。 此错误可以忽略，除非它是在将模块部署到设备后发生的。

最后，列出正在运行的模块：

```powershell
iotedge list
```

完成新的安装后，应会看到唯一运行的模块是 **edgeAgent**。

## <a name="tips-and-troubleshooting"></a>提示和故障排除

在资源受限的设备上，强烈建议按照 [故障排除指南](../articles/iot-edge/troubleshoot.md)中的说明将 *OptimizeForPerformance* 环境变量设置为 *false*。

如果设备无法连接到 IoT 中心且网络具有代理服务器，请按照[配置 IoT Edge 设备以通过代理服务器进行通信](../articles/iot-edge/how-to-configure-proxy-support.md)中的步骤进行操作。

# <a name="linux"></a>[Linux](#tab/linux)

在 Linux 设备上，需要提升的权限才能运行 `iotedge` 命令。 安装运行时后，请从计算机中注销并重新登录以自动更新权限。 在此之前，请使用 `sudo` 通过提升的权限运行命令。

# <a name="windows"></a>[Windows](#tab/windows)

在运行 Windows 容器的 Windows 设备上，Moby 容器引擎安装为 IoT Edge 的一部分。 Moby 引擎旨在与 Docker Desktop 并行运行。 如果要与设备上的容器直接交互，可以使用 `docker` 命令。 但是，如果设备上还安装了 Docker Desktop，则必须以 Moby 引擎为具体目标。

例如，若要列出所有 Docker 映像，可使用以下命令：

```powershell
docker images
```

若要列出所有 Moby 映像，可以使用指向 Moby 引擎的指针修改上述命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 将在安装脚本的输出中列出，也可以在 config.yaml 文件的容器运行时设置节中找到它。

![config.yaml 中的 moby_runtime uri](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
