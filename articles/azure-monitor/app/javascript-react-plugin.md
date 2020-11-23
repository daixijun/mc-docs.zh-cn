---
title: 适用于 Application Insights JavaScript SDK 的 React 插件
description: 如何安装和使用适用于 Application Insights JavaScript SDK 的 React 插件。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/10/2020
ms.author: v-johya
ms.openlocfilehash: 6760eebe6f019233f732f5bec736cefbd054907a
ms.sourcegitcommit: d30cf549af09446944d98e4bd274f52219e90583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94638313"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>适用于 Application Insights JavaScript SDK 的 React 插件

适用于 Application Insights JavaScript SDK 的 React 插件，支持：

- 跟踪路由更改
- React 组件使用情况统计信息

## <a name="getting-started"></a>入门

安装 npm 包：

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>基本用法

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>配置

| 名称    | 默认 | 描述                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | React 路由器历史记录。 有关详细信息，请参阅 [react-router 包文件](https://reactrouter.com/web/api/history)。 若要了解如何访问组件外部的历史记录对象，请参阅 [Reac 路由器常见问题解答](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>React 组件使用情况跟踪

若要检测各种 React 组件使用情况跟踪，请应用 `withAITracking` 高阶组件函数。

它将测量从 `ComponentDidMount` 事件到 `ComponentWillUnmount` 事件的时间。 但是，为了使其更准确，它将减去用户空闲时间 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time`。

若要在 Azure 门户中查看此指标，需要导航到 Application Insights 资源，选择 “指标”选项卡并配置空图表以显示自定义指标名称“React 组件参与时间（秒）”，选择指标的聚合（求和、平均等），并应用拆分“组件名称”。

![显示自定义指标“React 组件参与时间（秒）”的图表的屏幕截图，该图表按“组件名称”拆分](./media/javascript-react-plugin/chart.png)

还可以运行自定义查询来划分 Application Insights 数据，以根据需求生成报表和可视化效果。 在 Azure 门户导航到 Application Insights 资源，从“概述”选项卡的顶部菜单中选择“分析”，然后运行查询。

![自定义指标查询结果的屏幕截图。](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 新的自定义指标最多可能需要 10 分钟才能显示在 Azure 门户中。

## <a name="sample-app"></a>示例应用

查看 [Application Insights React 演示](https://github.com/Azure-Samples/application-insights-react-demo)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 JavaScript SDK，请参阅 [Application Insights JavaScript SDK 文档](javascript.md)。
- 若要了解 Kusto 查询语言和 Log Analytics 中的查询数据，请参阅[日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

