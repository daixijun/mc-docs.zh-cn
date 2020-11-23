---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 08/13/2020
title: 如何导入自定义 CA 证书 - Azure Databricks
description: 了解如何将自定义 CA 证书导入到 Azure Databricks 群集中以供 Python 使用。
category: Spark Python
author: kr-arjun
db-author: arjunkr@databricks.com
ms.openlocfilehash: 18d8d6e910db3ee32f4e7da9aa6d9858aa1aaac7
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589542"
---
# <a name="how-to-import-a-custom-ca-certificate"></a>如何导入自定义 CA 证书

使用 Python 时，可能需要导入自定义 CA 证书，以避免连接到终结点时出现错误。

```console
ConnectionError: HTTPSConnectionPool(host='my_server_endpoint', port=443): Max retries exceeded with url: /endpoint (Caused by NewConnectionError('<urllib3.connection.VerifiedHTTPSConnection object at 0x7fb73dc3b3d0>: Failed to establish a new connection: [Errno 110] Connection timed out',))
```

若要将一个或多个自定义 CA 证书导入到 Azure Databricks 群集：

1. 请创建一个 init 脚本用于添加整个 CA 链和设置 `REQUESTS_CA_BUNDLE` 属性。

   在此示例中，PEM 格式的 CA 证书被添加到 `/user/local/share/ca-certificates/` 处的 `myca.crt` 文件中。 此文件在 `custom-cert.sh` init 脚本中引用。

   ```bash
   dbutils.fs.put("/databricks/init-scripts/custom-cert.sh", """#!/bin/bash

   cat << 'EOF' > /usr/local/share/ca-certificates/myca.crt
   -----BEGIN CERTIFICATE-----
   <CA CHAIN 1 CERTIFICATE CONTENT>
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   <CA CHAIN 2 CERTIFICATE CONTENT>
   -----END CERTIFICATE-----
   EOF

   update-ca-certificates

   PEM_FILE="/etc/ssl/certs/myca.pem"
   PASSWORD="<password>"
   KEYSTORE="/usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/cacerts"

   CERTS=$(grep 'END CERTIFICATE' $PEM_FILE| wc -l)

   # To process multiple certs with keytool, you need to extract
   # each one from the PEM file and import it into the Java KeyStore.

   for N in $(seq 0 $(($CERTS - 1))); do
     ALIAS="$(basename $PEM_FILE)-$N"
     echo "Adding to keystore with alias:$ALIAS"
     cat $PEM_FILE |
       awk "n==$N { print }; /END CERTIFICATE/ { n++ }" |
       keytool -noprompt -import -trustcacerts \
               -alias $ALIAS -keystore $KEYSTORE -storepass $PASSWORD
   done

   echo "export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt" >> /databricks/spark/conf/spark-env.sh
   """)
   ```

   > [!NOTE]
   >
   > `/usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/cacerts` 是密钥存储的默认位置。 如果修改了群集上 `cacerts` 的位置，则必须更新此值以匹配当前位置。

2. 将 init 脚本作为[群集范围内的 init 脚本](/databricks/clusters/init-scripts#--cluster-scoped-init-scripts)附加到群集。
3. 重启群集。