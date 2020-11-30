---
title: 配置 Azure 密钥保管库防火墙和虚拟网络 - Azure 密钥保管库
description: 配置 Key Vault 防火墙和虚拟网络的分步说明
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
origin.date: 10/01/2020
ms.date: 11/27/2020
ms.author: v-tawe
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7aef6d877613922d7dceca833c38d6aebbd33aa3
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300940"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>配置 Azure Key Vault 防火墙和虚拟网络

本文将提供有关如何配置 Azure Key Vault 防火墙的指导。 本文档将详细介绍 Key Vault 防火墙的不同配置，并提供有关如何将 Azure Key Vault 配置为与其他应用程序和 Azure 服务一起使用的分步说明。

## <a name="firewall-settings"></a>防火墙设置

本部分将介绍可用于配置 Azure Key Vault 防火墙的不同方式。

### <a name="key-vault-firewall-disabled-default"></a>禁用 Key Vault 防火墙（默认值）

默认情况下，当你创建新的密钥保管库时，Azure Key Vault 防火墙处于禁用状态。 所有应用程序和 Azure 服务都可以访问该密钥保管库并将请求发送到该密钥保管库。 请注意，此配置并不意味着任何用户都可以在你的密钥保管库上执行操作。 密钥保管库仍通过要求 Azure Active Directory 身份验证和访问策略权限来限制存储在密钥保管库中的机密、密钥和证书。 若要更详细地了解密钥保管库身份验证，请参阅[此处](./authentication-fundamentals.md)的密钥保管库身份验证基础知识文档。

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>启用 Key Vault 防火墙（仅限受信任的服务）

启用 Key Vault 防火墙时，系统将向你提供“允许受信任的 Microsoft 服务绕过此防火墙”的选项。 受信任的服务列表并不是全部的 Azure 服务。 例如，Azure DevOps 不在受信任的服务列表中。 这并不意味着未出现在受信任的服务列表中的服务不受信任或不安全。 受信任的服务列表中包含的服务符合这一条件：Microsoft 控制该服务上运行的所有代码。 由于用户可以在 Azure 服务（例如 Azure DevOps）中编写自定义代码，因此 Microsoft 不提供为该服务创建全面批准的选项。 此外，服务出现在受信任的服务列表中并不意味着所有方案都允许该服务。

若要确定你尝试使用的服务是否在受信任的服务列表中，请参阅[此处](./overview-vnet-service-endpoints.md#trusted-services)的以下文档。

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>启用 Key Vault 防火墙（IPv4 地址和范围 - 静态 IP）

如果你想要授权特定服务通过 Key Vault 防火墙访问密钥保管库，可将其 IP 地址添加到密钥保管库防火墙允许列表中。 此配置最适合使用静态 IP 地址或已知范围的服务。

若要允许某个 Azure 资源（例如 Web 应用或逻辑应用）的某个 IP 地址或范围，请执行以下步骤。

1. 登录到 Azure 门户
1. 选择资源（服务的特定实例）
1. 单击“设置”下的“属性”边栏选项卡
1. 查找“IP 地址”字段。
1. 复制此值或范围，并将其输入到密钥保管库防火墙允许列表中。

若要允许整个 Azure 服务通过 Key Vault 防火墙，请使用[此处](https://www.microsoft.com/download/details.aspx?id=41653)的 Azure 公开记录的数据中心 IP 地址列表。 在所需区域中找到与服务关联的 IP 地址，并使用上述步骤将这些 IP 地址添加到密钥保管库防火墙。

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>启用 Key Vault 防火墙（虚拟网络 - 动态 IP）

如果尝试允许 Azure 资源（如虚拟机）通过密钥保管库，则可能无法使用静态 IP 地址，并且你可能不希望允许 Azure 虚拟机的所有 IP 地址访问密钥保管库。

在这种情况下，应在虚拟网络中创建资源，然后允许来自特定虚拟网络和子网的流量访问密钥保管库。 为此，请执行以下步骤。

1. 登录到 Azure 门户
1. 选择要配置的密钥保管库
1. 选择“网络”边栏选项卡
1. 选择“+ 添加现有虚拟网络”
1. 选择要允许通过密钥保管库防火墙的虚拟网络和子网。

<!--
### Key Vault Firewall Enabled (Private Link)

To understand how to configure a private link connection on your key vault, please see the document [here](./private-link-service.md).

> [!IMPORTANT]
> After firewall rules are in effect, users can only perform Key Vault [data plane](secure-your-key-vault.md#data-plane-access-control) operations when their requests originate from allowed virtual networks or IPv4 address ranges. This also applies to accessing Key Vault from the Azure portal. Although users can browse to a key vault from the Azure portal, they might not be able to list keys, secrets, or certificates if their client machine is not in the allowed list. This also affects the Key Vault Picker by other Azure services. Users might be able to see list of key vaults, but not list keys, if firewall rules prevent their client machine.

> [!NOTE]
> Be aware of the following configuration limitations:
> * A maximum of 127 virtual network rules and 127 IPv4 rules are allowed. 
> * IP network rules are only allowed for public IP addresses. IP address ranges reserved for private networks (as defined in RFC 1918) are not allowed in IP rules. Private networks include addresses that start with **10.**, **172.16-31**, and **192.168.**. 
> * Only IPv4 addresses are supported at this time.
-->

## <a name="use-the-azure-portal"></a>使用 Azure 门户

下面介绍了如何使用 Azure 门户配置 Key Vault 防火墙和虚拟网络：

1. 浏览要保护的 Key Vault。
2. 选择“网络”，然后选择“防火墙和虚拟网络”信息栏 。
3. 在“允许的访问来源”下，选择“所选网络”。
4. 若要将现有虚拟网络添加到防火墙和虚拟网络规则，请选择“+ 添加现有虚拟网络”。
5. 在打开的新边栏选项卡中，选择可访问此 Key Vault 的订阅、虚拟网络和子网。 如果虚拟网络和选择的子网没有启用服务终结点，确认想要启用服务终结点，并选择“启用”。 此操作最多可能需要 15 分钟才能生效。
6. 在“IP 网络”下，可通过采用 [CIDR（无类域间路由）表示法](https://tools.ietf.org/html/rfc4632)键入 IPv4 地址范围或单个 IP 地址来添加 IPv4 地址范围。
7. 如果要允许 Microsoft 信任的服务跳过 Key Vault 防火墙，请选择“是”。 有关当前 Key Vault 信任的服务的完整列表，请参阅以下链接。 [Azure Key Vault 信任的服务](./overview-vnet-service-endpoints.md#trusted-services)
7. 选择“保存”。

还可添加新的虚拟网络和子网，然后通过选择“+ 添加新的虚拟网络”，为新创建的虚拟网络和子网启用服务终结点。 然后遵照提示操作。

## <a name="use-the-azure-cli"></a>使用 Azure CLI 

下面介绍了如何使用 Azure CLI 配置 Key Vault 防火墙和虚拟网络

1. [安装 Azure CLI](/cli/install-azure-cli) 并[登录](/cli/authenticate-azure-cli)。

2. 列出可用的虚拟网络规则。 如果尚未设置此 Key Vault 的任何规则，该列表将为空。
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点。
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. 为虚拟网络和子网添加网络规则。
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. 添加允许通信的 IP 地址范围。
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. 如果所有受信服务都可以访问此 Key Vault，请将 `bypass` 设置为 `AzureServices`。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. 将默认操作设置为 `Deny`，以启用网络规则。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

下面介绍了如何使用 PowerShell 配置 Key Vault 防火墙和虚拟网络：

1. 安装最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 并[登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

2. 列出可用的虚拟网络规则。 如果尚未设置此密钥保管库的任何规则，该列表将为空。
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点。
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 为虚拟网络和子网添加网络规则。
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. 添加允许通信的 IP 地址范围。
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. 如果所有受信服务都可以访问此 Key Vault，请将 `bypass` 设置为 `AzureServices`。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 将默认操作设置为 `Deny`，以启用网络规则。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>参考
* ARM 模板参考：[Azure Key Vault ARM 模板参考](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 命令：[az keyvault network-rule](/cli/keyvault/network-rule)
* Azure PowerShell cmdlets：[Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>后续步骤

* [Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)
* [保护密钥保管库](secure-your-key-vault.md)
