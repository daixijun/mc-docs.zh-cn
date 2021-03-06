---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: d9bb86875cc4bb28051e718399cc6495be59fecb
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "75599397"
---
`deploymentconfig.json` 文档中的条目映射到 [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py) 的参数。 下表描述了 JSON 文档中的实体与方法参数之间的映射：

| JSON 实体 | 方法参数 | 说明 |
| ----- | ----- | ----- |
| `computeType` | 不可用 | 计算目标。 对于 ACI，此值必须为 `ACI`。 |
| `containerResourceRequirements` | 不可用 | CPU 和内存实体的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要分配的 CPU 核心数。 默认值为 `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 为此 Web 服务分配的内存量 (GB)。 默认值为 `0.5` |
| `location` | `location` | 要将此 Webservice 部署到的 Azure 区域。 如果未指定，将使用工作区位置。 有关可用区域的详细信息，请参阅：[ACI 区域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | 是否为此 Webservice 启用身份验证。 默认为 False |
| `sslEnabled` | `ssl_enabled` | 是否为此 Webservice 启用 SSL。 默认为 False。 |
| `appInsightsEnabled` | `enable_app_insights` | 是否为此 Webservice 启用 AppInsights。 默认为 False |
| `sslCertificate` | `ssl_cert_pem_file` | 如果启用 SSL，则需要证书文件 |
| `sslKey` | `ssl_key_pem_file` | 如果启用 SSL，则需要密钥文件 |
| `cname` | `ssl_cname` | 如果启用 SSL，则需要 CNAME |
| `dnsNameLabel` | `dns_name_label` | 评分终结点的 DNS 名称标签。 如果未指定，则将为评分终结点生成唯一的 DNS 名称标签。 |

以下 JSON 是用于 CLI 的部署配置示例：

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```