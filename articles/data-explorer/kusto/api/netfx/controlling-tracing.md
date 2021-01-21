---
title: Kusto 控制或取消 SDK 客户端跟踪 - Azure 数据资源管理器
description: 本文介绍如何在 Azure 数据资源管理器中控制和取消 Kusto SDK 客户端跟踪。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
origin.date: 10/23/2018
ms.date: 01/22/2021
ms.openlocfilehash: e1620e0fabc24f3edb62290fae408b0ce2b9d739
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611703"
---
# <a name="controlling-and-suppressing-kusto-sdk-client-side-tracing"></a>控制和取消 Kusto SDK 客户端跟踪

Kusto 客户端库使用常用的跟踪平台。 该平台使用大量跟踪源 (`System.Diagnostics.TraceSource`)，每个跟踪源在其构造过程中都会连接到一组默认的跟踪侦听器 (`System.Diagnostics.Trace.Listeners`)。

如果应用程序具有与默认 `System.Diagnostics.Trace` 实例关联的跟踪侦听器（例如，通过其 `app.config` 文件进行关联），则 Kusto 客户端库会向这些侦听器发出跟踪。

可以通过编程方式或配置文件来取消或控制跟踪。

## <a name="suppress-tracing-programmatically"></a>以编程方式取消跟踪

若要以编程方式从 Kusto 客户端库取消跟踪，请在加载相关库时调用此代码片段：

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="use-a-config-file-to-suppress-tracing"></a>使用配置文件取消跟踪 

若要通过配置文件从 Kusto 客户端库取消跟踪，请修改文件 `Kusto.Cloud.Platform.dll.tweaks`（在 `Kusto.Data` 库中随附）。

```xml
    //Overrides the default trace verbosity level
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

> [!NOTE]
> 要使调整生效，`key` 值中不能有负号

另一种方法是：

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="enable-the-kusto-client-libraries-tracing"></a>启用 Kusto 客户端库跟踪

若要在 Kusto 客户端库之外启用跟踪，请在应用程序的 app.config 文件中启用 .NET 跟踪。 例如，假定应用程序 `MyApp.exe` 使用 Kusto.Data 客户端库。 更改文件 MyApp.exe.config，使之包含以下项，在下次启动应用程序时就会启用 `Kusto.Data` 跟踪。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.diagnostics>
    <trace indentsize="4">
      <listeners>
        <add type="Kusto.Cloud.Platform.Utils.RollingCsvTraceListener2, Kusto.Cloud.Platform" name="RollingCsvTraceListener" initializeData="RollingLogs" />
        <remove name="Default" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

代码会配置一个跟踪侦听器，该侦听器将写入到名为“RollingLogs”的子目录中的 CSV 文件。 该子目录位于进程的目录中。

> [!NOTE]
> 也可以使用任何与 .NET 兼容的跟踪侦听器类

## <a name="enable-the-azure-ad-client-libraries-adal-tracing"></a>启用 Azure AD 客户端库 (ADAL) 跟踪

启用 Kusto 客户端库的跟踪后，由 Azure AD 客户端库进行的跟踪也将启用。 Kusto 客户端库会自动配置 ADAL 跟踪。
