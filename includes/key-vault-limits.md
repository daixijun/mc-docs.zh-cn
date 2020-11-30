---
author: rothja
ms.service: key-vault
ms.topic: include
origin.date: 04/21/2020
ms.date: 11/27/2020
ms.author: v-tawe
ms.openlocfilehash: 64a9529dd8d2779b709aa96c18eda9fe831ab93e
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300932"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>密钥事务数（每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup>）：

|密钥类型|软件密钥<br>CREATE 密钥|Software-key<br>所有其他事务|
|:---|---:|---:|---:|---:|
|RSA 2,048 位|10|2,000|
|RSA 3,072 位|10|500|
|RSA 4,096 位|10 个|250|
|ECC P-256|10|2,000|
|ECC P-384|10|2,000|
|ECC P-521|10|2,000|
|ECC SECP256K1|10|2,000|

<!-- HSM not support, use Software for example -->

> [!NOTE]
> 在上表中，我们看到，对于 RSA 2,048 位软件密钥，每 10 秒允许 2,000 个 GET 事务。
>
> 限制阈值是加权的，并且是针对其总和施加的。 例如，如上表所示，对 RSA 软件密钥执行 GET 操作时，使用 4,096 位密钥的开销是使用 2,048 位密钥的开销的 8 倍。 这是因为 2,000/250 = 8。
>
> 在给定的 10 秒间隔内，Azure Key Vault 客户端在遇到 `429` 限制 HTTP 状态代码之前，只能执行以下操作 *之一*：
> - 2,000 个 RSA 2,048 位软件密钥 GET 事务
> - 250 个 RSA 4,096 位软件密钥 GET 事务
> - 249 个 RSA 4,096 位软件密钥 GET 事务和 8 个 RSA 2,048 位软件密钥 GET 事务

<!-- HSM not support, use Software for example -->

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>机密、托管存储帐户密钥，以及保管库事务：

| 事务类型 | 每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup> |
| --- | --- |
| 所有事务 |2,000 |

有关超出这些限制时如何处理限制的信息，请参阅 [Azure Key Vault 限制指南](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup> 所有事务类型的订阅范围限制是每个密钥保管库限制的 5 倍。 例如，每个订阅的“软件 - 其他”事务限制为每个订阅 10 秒内 10,000 个事务。

<!--
### Azure Private Link integration

| Resource | Limit |
| -------- | ----- |
| Private endpoints per key vault | 64 |
| Key vaults with private endpoints per subscription | 64 |
-->
