---
title: include 文件
description: include 文件
author: tfitzmac
ms.service: governance
ms.topic: include
origin.date: 03/26/2020
ms.date: 04/20/2020
ms.author: v-tawe
ms.custom: include file
ms.openlocfilehash: 8f650195fd1827db6b8637b85fe3056401a24a30
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300933"
---
| 资源 | 限制 |
| --- | --- |
| 每个目录的管理组数 | 10,000 |
| 每个管理组的订阅数 | 不受限制。 |
| 管理组层次结构的级别数 | 根级别加上 6 个级别<sup>1</sup> |
| 每个管理组的直接父管理组数 | 一种 |
| 每个位置的[管理组级别部署数](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 个级别不包括订阅级别。

<sup>2</sup>如果达到 800 个部署的限制，则会从历史记录中删除不再需要的部署。 若要删除管理组级别部署，请使用 [Remove-AzManagementGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 或 [az deployment mg delete](/cli/deployment/mg#az-deployment-mg-delete)。
