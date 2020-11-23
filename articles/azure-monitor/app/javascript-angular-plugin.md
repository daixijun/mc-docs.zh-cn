---
title: 适用于 Application Insights JavaScript SDK 的 Angular 插件
description: 如何安装和使用适用于 Application Insights JavaScript SDK 的 Angular 插件。
services: azure-monitor
author: Johnnytechn
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/10/2020
ms.author: v-johya
ms.openlocfilehash: ec6d4006553600952295e502aaff449e49f3964e
ms.sourcegitcommit: d30cf549af09446944d98e4bd274f52219e90583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94638312"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>适用于 Application Insights JavaScript SDK 的 Angular 插件

适用于 Application Insights JavaScript SDK 的 Angular 插件支持：

- 跟踪路由器更改
- Angular 组件使用情况统计信息

> [!WARNING]
> Angular 插件与 ECMAScript 3 (ES3) 不兼容。

## <a name="getting-started"></a>入门

安装 npm 包：

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>基本用法

在应用的条目组件中设置 Application Insights 实例：

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

若要使用 `trackMetric` 方法跟踪 Angular 组件使用情况，请将 `AngularPluginService` 作为提供程序添加到 `app.module.ts` 文件中的提供程序列表中。

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

若要跟踪组件的生存期，请在该组件的 `ngOnDestroy` 方法中调用 `trackMetric`。 组件被销毁时，它将触发一个 `trackMetric` 事件，该事件将发送用户停留在页面上的时间和组件名称。

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解 JavaScript SDK，请参阅 [Application Insights JavaScript SDK 文档](javascript.md)
- [GitHub 上的 Angular 插件](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)

