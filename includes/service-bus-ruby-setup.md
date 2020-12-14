---
ms.service: service-bus
ms.topic: include
origin.date: 11/09/2018
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: no
ms.testdate: 07/27/2020
ms.author: v-yeche
ms.openlocfilehash: 46e5cd74717f23d018ca757e37729a4a6a836336
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105663"
---
## <a name="create-a-ruby-application"></a>创建 Ruby 应用程序
有关说明，请参阅[在 Azure 上创建 Ruby 应用程序](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md)。

## <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用服务总线
若要使用服务总线，请下载并使用 Azure Ruby 包，其中包括一组便于与存储 REST 服务进行通信的库。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 获取该程序包
1. 使用命令行接口，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。
2. 在命令窗口中键入“gem install azure”以安装 gem 和依赖项。

### <a name="import-the-package"></a>导入包
使用常用的文本编辑器将以下内容添加到要在其中使用存储的 Ruby 文件的顶部：

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>设置服务总线连接
使用以下代码，设置命名空间、密钥名称、密钥、签名程序和主机的值：

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.chinacloudapi.cn"
```

将命名空间值设置为创建的值，而不是整个 URL 的值。 例如，使用 **"yourexamplenamespace"**，而不是 "yourexamplenamespace.servicebus.chinacloudapi.cn"。

使用多个命名空间时，可以在创建 `SharedAccessSigner` 对象时将密钥及其名称传递到构造函数

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.chinacloudapi.cn"
```

<!-- Update_Description: update meta properties, wording update, update link -->