---
title: Azure Service Fabric 基础结构即代码最佳做法
description: 以基础结构即代码方式管理 Azure Service Fabric 的最佳做法和设计注意事项。
ms.topic: conceptual
origin.date: 01/23/2019
author: rockboyfor
ms.date: 01/11/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: b1c6aa06c4f43d43c402f43252d0dd46675e68ac
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023237"
---
<!--Verify successfully-->
# <a name="infrastructure-as-code"></a>基础结构即代码

在生产方案中，使用资源管理器模板创建 Azure Service Fabric 群集。 资源管理器模板可以更好地控制资源属性，并确保你具有一致的资源模型。

[GitHub 上的 Azure 示例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中提供了适用于 Windows 和 Linux 的示例资源管理器模板。 这些模板可用作群集模板的起点。 下载 `azuredeploy.json` 和 `azuredeploy.parameters.json` 并编辑它们以满足你的自定义要求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

<!--MOONCAKE: CUSTOMIZE -->

> [!NOTE]
> 必须修改从 GitHub 存储库“Azure-Samples”下载或引用的模板，使之适应 Azure 中国云环境。 例如，替换某些终结点（将“blob.core.windows.net”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“chinacloudapp.cn”）；必要时更改某些不受支持的 VM 映像、VM 大小、SKU 以及资源提供程序的 API 版本。

> [!NOTE]
> 例如，当我们尝试在 Azure 中安装 [5 节点安全 Windows Service Fabric 群集](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)时。
> 在成功下载相应的模板文件后，我们应当替换以下配置来满足 Azure 中国环境：
> * 替换 [azuredeploy.json](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/AzureDeploy.json) 中的 storageAccountEndPoint。
>     * 将 `"storageAccountEndPoint": "https://core.windows.net/"` 替换为 `"storageAccountEndPoint": "https://core.chinacloudapi.cn/"`。
> * 替换 [azuredeploy.parameters.json](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/AzureDeploy.Parameters.json) 中的 clusterLocation。
>     * 将 `WestUS` 替换为 `chinanorth`。
> * 替换 [New-ServiceFabricClusterCertificate.ps1](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure/New-ServiceFabricClusterCertificate.ps1) 中的 Location。
>     * 将 `WestUS` 替换为 `chinanorth`。

<!--MOONCAKE: CUSTOMIZE -->

若要部署你在上面下载的 `azuredeploy.json` 和 `azuredeploy.parameters.json` 模板，请使用以下 Azure CLI 命令：

<!--MOONCAKE: az group deployment create --resource-group is mandatory-->

```azurecli
ResourceGroupName="sfclustergroup"
Location="chinanorth"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

使用 PowerShell 创建资源

<!--MOONCAKE: New-AzResourceGroupDeployment -ResourceGroupName is mandatory-->

```powershell
$ResourceGroupName="sfclustergroup"
$Location="chinanorth"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric 资源

可以通过 Azure 资源管理器，将应用程序和服务部署到 Service Fabric 群集。 有关详细信息，请参阅[将应用程序和服务作为 Azure 资源管理器资源进行管理](./service-fabric-application-arm-resource.md)。 下面是要在资源管理器模板资源中包括的最佳做法 Service Fabric 应用程序特定资源。

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

若要使用 Azure 资源管理器部署应用程序，必须首先[创建一个 sfpkg](./service-fabric-package-apps.md#create-an-sfpkg) Service Fabric 应用程序包。 下面的 python 脚本是有关如何创建 sfpkg 的示例：

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure 虚拟机操作系统自动升级配置 
升级虚拟机是用户启动的操作，建议使用[虚拟机规模集操作系统自动升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)进行 Azure Service Fabric 群集主机修补程序管理；修补业务流程应用程序是替代解决方案，适用于在 Azure 外部托管的情况。虽然 POA 可以在 Azure 中使用，但考虑到在 Azure 中托管 POA 的开销，通常会首选虚拟机操作系统自动升级而不是 POA。 下面是计算虚拟机规模集资源管理器模板属性，用于启用 OS 自动升级：

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
使用带 Service Fabric 的 OS 自动升级时，将推出新的 OS 映像（每次一个更新域），以维持 Service Fabric 中运行的服务的高可用性。 若要利用 Service Fabric 中的自动 OS 升级，必须将群集配置为使用银级持久性层或更高层级。

确保将以下注册表项设置为 false，防止 Windows 主机启动不协调的更新：HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU。

下面是计算虚拟机规模集资源管理器模板属性，用于将 WindowsUpdate 注册表项设置为 false：
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric 群集升级配置
下面是 Service Fabric 群集资源管理模板属性，用于启用自动升级：
```json
"upgradeMode": "Automatic",
```
若要手动升级群集，请将 cab/deb 发行版下载到群集虚拟机，然后调用以下 PowerShell：
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

<!-- Update_Description: update meta properties, wording update, update link -->