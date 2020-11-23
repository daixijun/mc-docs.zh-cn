---
title: 使用应用程序网关启用基于 Cookie 的相关性
description: 本文介绍如何使用应用程序网关启用基于 Cookie 的相关性。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: v-junlch
ms.openlocfilehash: 42554ca817f145bb06615e3b9008cade8c4b5c06
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849428"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>使用应用程序网关启用基于 Cookie 的相关性
如 [Azure 应用程序网关文档](./application-gateway-components.md#http-settings)中所述，应用程序网关支持基于 Cookie 的相关性，这意味着它可以将后续流量从用户会话定向到同一服务器进行处理。

## <a name="example"></a>示例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

