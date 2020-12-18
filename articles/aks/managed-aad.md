---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure AD
services: container-service
ms.topic: article
origin.date: 08/26/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: yes
ms.testdate: 11/30/2020
ms.author: v-yeche
ms.openlocfilehash: 4677d5752d2e333a9eb4811401e07046f542f63a
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004120"
---
<!--Verified successfully on 11/24/2020-->
# <a name="aks-managed-azure-active-directory-integration"></a>AKS 托管的 Azure Active Directory 集成

AKS 托管的 Azure AD 集成设计用来简化 Azure AD 集成体验，用户以前需要创建客户端应用和服务器应用，而且还需要由 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为你管理客户端应用和服务器应用。

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

群集管理员可以根据用户的标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅 [Open ID Connect 文档][open-id-connect]。

若要详细了解 Azure AD 集成流程，请参阅 [Azure Active Directory 集成概念文档](concepts-identity.md#azure-active-directory-integration)。

## <a name="limitations"></a>限制 

* 无法禁用 AKS 托管的 Azure AD 集成
* AKS 托管的 Azure AD 集成不支持未启用 Kubernetes RBAC 的群集
* 不支持更改与 AKS 托管的 Azure AD 集成关联的 Azure AD 租户

## <a name="prerequisites"></a>必备条件

* Azure CLI 2.11.0 或更高版本
* 版本不低于 [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) 的 Kubectl，或者 [kubelogin](https://github.com/Azure/kubelogin)
* 如果使用的是 [helm](https://github.com/helm/helm)，则最低版本为 helm 3.3。

> [!Important]
> 你必须使用最低版本为 1.18.1 的 Kubectl，或者使用 kubelogin。 如果未使用正确的版本，你会遇到身份验证问题。

若要安装 kubectl 和 kubelogin，请使用以下命令：

```azurecli
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

对于其他操作系统，请遵循[这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

## <a name="before-you-begin"></a>开始之前

对于你的群集，你需要一个 Azure AD 组。 此组需要用作群集的管理员组，以便授予群集管理员权限。 你可以使用现有 Azure AD 组，也可以创建一个新组。 记录你的 Azure AD 组的对象 ID。

```azurecli
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

若要为群集管理员创建新的 Azure AD 组，请使用以下命令：

```azurecli
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>创建已启用 Azure AD 的 AKS 群集

使用以下 CLI 命令创建 AKS 群集。

创建 Azure 资源组：

```azurecli
# Create an Azure resource group
az group create --name myResourceGroup --location chinaeast2
```

创建一个 AKS 群集，并为你的 Azure AD 组启用管理访问权限

```azurecli
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功创建 AKS 托管的 Azure AD 群集后，响应正文中会包含以下部分
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

创建群集后，即可开始访问该群集。

## <a name="access-an-azure-ad-enabled-cluster"></a>访问已启用 Azure AD 的群集

你需要具有 [Azure Kubernetes 服务群集用户](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role)内置角色才能执行以下步骤。

获取用于访问群集的用户凭据：

```azurecli
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
按照说明进行登录。

使用 kubectl get nodes 命令查看群集中的节点：

```azurecli
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
配置 [Azure 基于角色的访问控制 (Azure RBAC)](./azure-ad-rbac.md)，以便为群集配置其他安全组。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>排查 Azure AD 的访问权限问题

> [!Important]
> 下面所述的步骤会绕过正常的 Azure AD 组身份验证。 请仅在紧急情况下使用它们。

如果你被永久阻止，不能访问具有群集访问权限的有效 Azure AD 组，你仍可以获取管理员凭据以直接访问群集。

若要执行这些步骤，你需要有权访问 [Azure Kubernetes 服务群集管理员](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)内置角色。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>在现有群集上启用 AKS 托管的 Azure AD 集成

你可以在现有的启用了 Kubernetes RBAC 的群集上启用 AKS 托管的 Azure AD 集成。 确保将管理员组设置为在群集上保留访问权限。

```azurecli
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

成功激活 AKS 托管的 Azure AD 群集后，响应正文中会包含以下部分

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

按照[此处][access-cluster]的步骤操作，再次下载用户凭据以访问群集。

<a name="upgrading-to-aks-managed-azure-ad-integration"></a>
## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>升级到 AKS 托管的 Azure AD 集成

如果你的群集使用旧式 Azure AD 集成，则可以升级到 AKS 托管的 Azure AD 集成。

```azurecli
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功迁移 AKS 托管的 Azure AD 群集后，响应正文中会包含以下部分

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

如果要访问群集，请按照[此处][access-cluster]的步骤进行操作。

## <a name="non-interactive-sign-in-with-kubelogin"></a>通过 kubelogin 进行非交互式登录

有一些当前无法通过 kubectl 执行的非交互式方案，例如持续集成管道。 你可以使用 [`kubelogin`](https://github.com/Azure/kubelogin) 通过非交互式服务主体登录来访问群集。

## <a name="next-steps"></a>后续步骤

<!--Not Available on * Learn about [Azure RBAC integration for Kubernetes Authorization][azure-rbac-integration]-->

* 了解 [Azure AD 与 Kubernetes RBAC 的集成][azure-ad-rbac]。
* 使用 [kubelogin](https://github.com/Azure/kubelogin) 来访问 kubectl 中未提供的 Azure 身份验证功能。
* 详细了解 [AKS 和 Kubernetes 标识概念][aks-concepts-identity]。

<!--Not Available on [Azure Resource Manager (ARM) templates ][aks-arm-template]-->

<!-- LINKS - external -->

[kubernetes-webhook]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!--Not Available on [aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters-->

<!-- LINKS - Internal -->

<!--Not Available on [azure-rbac-integration]: manage-azure-rbac.md-->

[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: https://docs.azure.cn/cli/aks#az_aks_create
[az-aks-get-credentials]: https://docs.azure.cn/cli/aks#az_aks_get_credentials
[az-group-create]: https://docs.azure.cn/cli/group#az_group_create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: https://docs.azure.cn/cli/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration

<!-- Update_Description: new article about managed aad -->
<!--NEW.date: 11/30/2020-->