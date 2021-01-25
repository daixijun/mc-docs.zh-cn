---
title: IoT Edge 上的实时视频分析发行说明 - Azure
description: 本主题提供 IoT Edge 上的实时视频分析版本的发行说明、改进、bug 修复和已知问题。
ms.topic: conceptual
origin.date: 08/19/2020
ms.date: 01/18/2021
ms.openlocfilehash: b66cf177b19130e9c2e832e9e8cb6ed8adb3a5e8
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230989"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 上的实时视频分析发行说明

本文提供以下事项的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

<hr width=100%>

## <a name="december-14-2020"></a>2020 年 12 月 14 日
此版本是 IoT Edge 上实时视频分析的公共预览刷新版本。 发行标记为

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>模块更新
* 添加了对每个图形拓扑使用多个 HTTP 扩展处理器和 gRPC 扩展处理器的支持。
* 添加了对接收器节点的磁盘空间管理的支持。
* `MediaGraphGrpcExtension` 节点现在支持 [extensionConfiguration](grpc-extension-protocol.md) 属性，允许在单个 gRPC 服务器中使用多个 AI 模型。
* 添加了对以 [Prometheus 格式](https://prometheus.io/docs/practices/naming/)收集实时视频分析模块指标的支持。 详细了解如何[收集指标并在 Azure Monitor 中查看。](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* 帧速率筛选器处理器已弃用。  
    * 帧速率管理现在在图形扩展处理器节点本身中可用。

## <a name="september-22-2020"></a>2020 年 9 月 22 日

此模块 2020 年 9 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 添加了对 Linux ARM64 设备的支持 - 使用[手动步骤](deploy-iot-edge-device.md)部署到此类设备。

## <a name="august-19-2020"></a>2020 年 8 月 19 日

此模块 2020 年 8 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 现在，你可以使用 gRPC 框架在 IoT Edge 的实时视频分析和自定义扩展之间获得高数据内容传输性能。 请参阅[此内容](analyze-live-video-use-your-grpc-model-quickstart.md)以开始使用。
* 实时视频分析的区域部署更广泛，且只更新了云服务。  
* 实时视频分析现已可用。 下面是所有可用区域的[列表](https://azure.microsoft.com/global-infrastructure/services/?regions=china-non-regional,china-east,china-east-2,china-north,china-north-2&products=media-services)。  
* 快速入门的[设置](https://aka.ms/lva-edge/setup-resources-for-samples)已更新，并支持新区域。
    * 对于已设置资源的用户，不需要进行任何操作

### <a name="bug-fixes"></a>Bug 修复 

* 在设置脚本中不再使用已弃用的 Azure 扩展

<hr width=100%>

## <a name="july-13-2020"></a>2020 年 7 月 13 日

此模块 2020 年 7 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 现在，你可以创建具有资产接收器节点以及信号门处理器节点下游的文件接收器节点的图形拓扑。 请参阅[此内容](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files)获取示例。

### <a name="bug-fixes"></a>Bug 修复

* 对所需属性验证的改进

<hr width=100%>

## <a name="june-1-2020"></a>2020 年 6 月 1 日

此版本是 IoT Edge 上的实时视频分析的第一个公共预览版本。 发行标记为

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>支持的功能

* 使用所选的 AI 模块分析实时视频流，还可以选择在边缘设备上或云中录制视频
* 在 IoT Edge [支持](../../iot-edge/support.md)的 Linux AMD64 操作系统上使用
* 使用 Azure 门户或 Visual Studio Code 通过 IoT 中心部署和配置模块
* 通过以下直接方法调用来远程或本地管理[图形拓扑和图形实例](media-graph-concept.md#media-graph-topologies-and-instances)

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
