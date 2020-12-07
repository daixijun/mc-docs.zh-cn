---
title: 快速入门 - 在 Azure 门户中创建 Linux VM
description: 本快速入门介绍了如何使用 Azure 门户创建 Linux 虚拟机。
author: Johnnytechn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 12/01/2020
ms.author: v-johya
ms.custom: mvc
ms.openlocfilehash: 2a5c413a4dec7b79a862b10fda3959e65b5e5493
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431636"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Linux 虚拟机

可以通过 Azure 门户创建 Azure 虚拟机 (VM)。 Azure 门户是基于浏览器的用户界面，用于创建 Azure 资源。 本快速入门介绍如何使用 Azure 门户部署运行 Ubuntu 18.04 LTS 的 Linux 虚拟机 (VM)。 若要查看运行中的 VM，也可以通过 SSH 登录到该 VM 并安装 NGINX Web 服务器。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sign-in-to-azure"></a>登录 Azure

如果尚未登录到 [Azure 门户](https://portal.azure.cn)，请进行登录。

## <a name="create-virtual-machine"></a>创建虚拟机

1. 在搜索中键入“虚拟机”。
1. 在“服务”下，选择“虚拟机” 。
1. 在“虚拟机”页上，选择“添加” 。 此时将打开“创建虚拟机”页。
1. 在“基本信息”选项卡中的“项目详细信息”下，确保选择了正确的订阅，然后选择 **新建** 资源组。 对于名称.*，请键入 myResourceGroup。 

    ![“项目详细信息”部分的屏幕截图，显示为虚拟机选择 Azure 订阅和资源组的位置](./media/quick-create-portal/project-details.png)

1. 在“实例详细信息”下，键入“myVM”作为“虚拟机名称”，选择“中国东部 2”作为“区域”，并选择“Ubuntu 18.04 LTS”作为“映像”。 保留其他默认值。

    ![“实例详细信息”部分的屏幕截图，可在其中提供虚拟机的名称并选择其区域、映像和大小](./media/quick-create-portal/instance-details.png)

<!--Correct in MC: select **SSH public key**, type your user name, then paste in your public key. Remove any leading or trailing white space in your public key.-->
1. 在“管理员帐户”  下，选择“SSH 公钥”  ，键入用户名，然后粘贴公钥。 删除公钥中任何开头或结尾处的空格。

    ![管理员帐户部分的屏幕截图，可以在其中选择身份验证类型并提供管理员凭据](./media/quick-create-portal/administrator-account.png)

1. 在“入站端口规则” > “公共入站端口”下，选择“允许所选端口”，然后从下拉列表中选择“SSH (22)”和“HTTP (80)”。 

    ![“入站端口规则”部分的屏幕截图，可在其中选择允许建立入站连接的端口](./media/quick-create-portal/inbound-port-rules.png)

1. 保留其余默认值，然后选择页面底部的“查看 + 创建”按钮。

1. 在“创建虚拟机”页上，可以查看要创建的 VM 的详细信息。 准备好以后，选择“创建”。

<!--Not available in MC: Generate new key pair-->
1. 部署完成后，选择“转到资源”。

1. 在新 VM 的页面上，选择公共 IP 地址并将其复制到剪贴板。


    ![显示如何复制虚拟机的 IP 地址的屏幕截图](./media/quick-create-portal/ip-address.png)

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

创建与 VM 的 SSH 连接。

1. 如果使用的是 Mac 或 Linux 计算机，请打开 Bash 提示符。 如果使用的是 Windows 计算机，请打开 PowerShell 提示符。 

1. 根据提示符，打开与虚拟机的 SSH 连接。 将 IP 地址替换为 VM 中的 IP 地址，并将 `.pem` 的路径替换为密钥文件的下载路径。

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

<!--Not available in MC: select the **Use a key stored in Azure**-->
## <a name="install-web-server"></a>安装 Web 服务器

若要查看运行中的 VM，请安装 NGINX Web 服务器。 在 SSH 会话中更新包源，然后安装最新的 NGINX 包。

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

完成后，键入 `exit` 以离开 SSH 会话。


## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 键入 VM 的公共 IP 地址作为 Web 地址。 可以在 VM 概览页上或此前使用过的 SSH 连接字符串中找到公共 IP 地址。

![在浏览器中显示 NGINX 默认站点的屏幕截图](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>清理资源

当不再需要时，可以删除资源组、虚拟机和所有相关资源。 为此，请选择虚拟机的资源组，选择“删除”，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一台简单的虚拟机、一条网络安全组规则组和规则，并安装了一台基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。

> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)

<!--Update_Description: update meta propreties, wording update, update link -->
