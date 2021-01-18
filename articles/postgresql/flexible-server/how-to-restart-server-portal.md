---
title: 重启 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 中执行重启操作。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: how-to
origin.date: 09/22/2020
ms.date: 01/11/2021
ms.openlocfilehash: 08fc3e2f18208723f86c4fe8e1ad721c37f66363
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023945"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>重启 Azure Database for PostgreSQL 灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文提供了执行灵活服务器重启的分步过程。 此操作可用于应用需要数据库服务器重启的静态参数更改。 对于配置了区域冗余高可用性的服务器，此过程是相同的。 

> [!IMPORTANT]
> 如果配置了高可用性，主服务器和备用服务器会同时重启。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   必须具有灵活服务器。

## <a name="restart-your-flexible-server"></a>重启灵活服务器

按照这些步骤重启灵活服务器。

1.  在 [Azure 门户](https://portal.azure.cn/)中，选择想要重启的灵活服务器。

2.  单击左侧面板中的“概述”，然后单击“重启” 。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="重启选择":::

3.  随即显示弹出式确认消息。

4.  如果要继续，请单击“是”。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="重启确认":::
 
6.  将显示重启操作已启动的通知。

