---
title: 使用对称密钥进行预配 - Azure IoT Edge | Microsoft Docs
description: 安装后，使用连接字符串为 IoT Edge 设备预配对称密钥并对 IoT 中心进行身份验证
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.author: v-tawe
origin.date: 10/06/2020
ms.date: 11/13/2020
ms.openlocfilehash: da8de6542872e0d63fa578d75bc27f3232036e1b
ms.sourcegitcommit: 9438c9db77338cecacf37d2fc178e757df9de83d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595275"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>使用对称密钥身份验证设置 Azure IoT Edge 设备

本文提供了相关步骤，用于在 IoT 中心注册新的 IoT Edge 设备并将相应设备配置为使用对称密钥进行身份验证。

本文中的步骤演示手动预配过程，在手动预配过程中，可手动将每个设备连接到其 IoT 中心。 另一种方式是使用 IoT 中心设备预配服务进行自动预配，在需要预配许多设备时，这会很有用。

<!--TODO: Add auto-provision info/links-->

对于手动预配，可以通过两种方式来验证 IoT Edge 设备：

* **对称密钥**：在 IoT 中心创建新的设备标识时，该服务会创建两个密钥。 将其中一个密钥置于设备上，并在进行身份验证时将该密钥提供给 IoT 中心。

  此身份验证方法更容易上手，但不够安全。

* **X.509 自签名**：创建两个 X.509 标识证书并将其置于设备上。 在 IoT 中心创建新的设备标识时，需要提供两个证书的指纹。 当设备向 IoT 中心进行身份验证时，它将出示其证书，而 IoT 中心会验证证书是否与指纹匹配。

  此身份验证方法更安全，建议用于生产场景。

本文分步介绍使用对称密钥身份验证的注册和预配过程。 如果要了解如何使用 X.509 证书设置设备，请参阅[使用 X.509 证书身份验证设置 Azure IoT Edge 设备](how-to-manual-provision-x509.md)。

## <a name="prerequisites"></a>先决条件

在按照本文中的步骤进行操作之前，应已在设备上安装了 IoT Edge 运行时。 否则，请按照[安装或卸载 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的步骤进行操作。

## <a name="register-a-new-device"></a>注册新设备

连接到 IoT 中心的每个设备都有一个设备 ID，用于跟踪云到设备或设备到云的通信。 可以使用设备连接信息来配置设备，这些信息包括 IoT 中心主机名、设备 ID 以及设备用于向 IoT 中心进行身份验证的信息。

对于对称密钥身份验证，这些信息收集在连接字符串中，可以从 IoT 中心检索这些信息，然后将其置于 IoT Edge 设备上。

可以根据自己的喜好使用若干工具在 IoT 中心注册新的 IoT Edge 设备并检索其连接字符串。

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure 门户的必备组件

Azure 订阅中的免费或标准 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 门户中创建 IoT Edge 设备

在 Azure 门户的 IoT 中心，IoT Edge 设备的创建和管理独立于不支持 Edge 的 IoT 设备。

1. 登录 [Azure 门户](https://portal.azure.cn)，导航到 IoT 中心。

1. 在左侧窗格中，从菜单中选择“IoT Edge”，然后选择“添加 IoT Edge 设备” 。

   ![从 Azure 门户添加 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. 在“创建设备”页面上，提供以下信息：

   * 创建描述性设备 ID。
   * 选择“对称密钥”作为身份验证类型。
   * 使用默认设置自动生成身份验证密钥并将新设备连接到中心。

1. 选择“保存”。 

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 门户中查看 IoT Edge 设备

所有连接到 IoT 中心并已启用 Edge 的设备都列在 **IoT Edge** 页上。

![使用 Azure 门户查看 IoT 中心内的所有 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>在 Azure 门户中检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 在门户的 **IoT Edge** 页中，单击 IoT Edge 设备列表中的设备 ID。
2. 复制“主连接字符串”或“辅助连接字符串”的值   。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code 的必备组件

* Azure 订阅中的免费或标准 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://market.azure.cn)

### <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

可使用适用于 Visual Studio Code 的 Azure IoT 扩展来执行与 IoT 中心相关的操作。 为了使这些操作顺利执行，你需要登录到 Azure 帐户并选择 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图  。
1. 在资源管理器底部，展开“Azure IoT 中心”部分  。

   ![展开“Azure IoT 中心设备”部分](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

1. 单击“Azure IoT 中心”部分标题中的“...”   。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“选择 IoT 中心”  。
1. 如果尚未登录到 Azure 帐户，请按照相关提示执行此操作。
1. 选择 Azure 订阅。
1. 选择 IoT 中心。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>使用 Visual Studio Code 创建 IoT Edge 设备

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分  。
1. 单击“Azure IoT 中心设备”部分标题中的“...”   。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“创建 IoT Edge 设备”  。
1. 在打开的文本框中提供设备 ID。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”   。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>使用 Visual Studio Code 查看 IoT Edge 设备

Visual Studio Code 资源管理器的“Azure IoT 中心”部分中列出了连接到 IoT 中心的所有设备  。 IoT Edge 设备和非 Edge 设备可通过不同的图标加以区别。事实上， **$edgeAgent** 和 **$edgeHub** 模块会部署到每个 IoT Edge 设备。

![使用 VS Code 查看 IoT 中心内的所有 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>通过 Visual Studio Code 检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 右键单击“Azure IoT 中心”部分中的设备 ID  。
1. 选择“复制设备连接字符串”  。

   连接字符串会复制到剪贴板。

还可从右键菜单中选择“获取设备信息”，在输出窗口中查看包括连接字符串在内的所有设备信息  。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 环境中的 [Azure CLI](/cli/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.70 或更新版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>通过 Azure CLI 创建 IoT Edge 设备

使用 [az iot hub device-identity create](/cli/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令在 IoT 中心创建新设备标识。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

此命令包括三个参数：

* `--device-id` 或 `-d`：提供对 IoT 中心唯一的描述性名称。
* `hub-name` 或 `-n`：提供 IoT 中心的名称。
* `--edge-enabled` 或 `--ee`：声明该设备是 IoT Edge 设备。

   ![az iot hub device-identity create output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>通过 Azure CLI 查看 IoT Edge 设备

使用 [az iot hub device-identity list](/cli/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 命令在 IoT 中心查看所有设备。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

注册为 IoT Edge 设备的任何设备的 **capabilities.iotEdge** 属性都会设置为 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>通过 Azure CLI 检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。 使用 [az iot hub device-identity show-connection-string](/cli/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 命令返回单个设备的连接字符串：

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 参数的值区分大小写。 不要复制连接字符串两端的引号。

---

## <a name="provision-an-iot-edge-device"></a>预配 IoT Edge 设备

当 IoT Edge 设备在 IoT 中心具有标识和可用于身份验证的连接字符串后，你需要使用此信息来预配设备本身。

在 Linux 设备上，通过编辑 config.yaml 文件来提供连接字符串。 在 Windows 设备上，通过运行 PowerShell 脚本来提供连接字符串。

# <a name="linux"></a>[Linux](#tab/linux)

在 IoT Edge 设备上，打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

找到文件的预配配置，并取消注释“使用连接字符串手动预配配置”部分。 

使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。 请确保注释掉任何其他预配部分。请确保 **provisioning:** 行前面没有空格，并且嵌套项缩进了两个空格。

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * 如果使用的是 Linux 容器，则将 `-ContainerOs` 参数添加到标志。 使用先前运行的 `Deploy-IoTEdge` 命令，与所选容器选项保持一致。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * 如果将 IoTEdgeSecurityDaemon.ps1 脚本下载到设备上以进行脱机安装或特定版本安装，请确保参考该脚本的本地副本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 系统出现提示时，请提供在上一部分中检索到的设备连接字符串。 设备连接字符串将物理设备与 IoT 中心内的设备 ID 关联，并提供身份验证信息。

   该设备连接字符串采用以下格式（不包括引号）：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

手动预配设备时，可以使用其他参数来修改过程，包括：

* 定向流量，使其通过代理服务器
* 声明特定的 edgeAgent 容器映像，并提供凭据（如果该映像位于专用注册表中）

有关这些附加参数的详细信息，请参阅 [Windows 上 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>后续步骤

转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。