---
title: 快速入门：用于 Java（Windows、Linux、macOS）的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，设置可将 Java（Windows、Linux、macOS）与语音服务 SDK 配合使用的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
origin.date: 10/15/2020
ms.date: 11/20/2020
ms.custom: devx-track-java
ms.author: v-tawe
ms.openlocfilehash: 840593006204a8b03231df2bcdaea678ab8d4fa0
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94977654"
---
本指南介绍如何安装用于 64 位 Java 8 JRE 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果只是需要包名称以便自行开始，那么请知悉在 Maven 中央存储库中未提供 Java SDK。 无论是使用 Gradle 还是 `pom.xml` 依赖项文件，都需要添加指向 `https://csspeechstorage.blob.core.windows.net/maven/` 的自定义存储库（请参阅下文了解包名称）。

> [!NOTE]
> 对于语音设备 SDK 和 Roobo 设备，请参阅[语音设备 SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

- Java 语音 SDK 包适用于以下操作系统：
  - Windows：仅 64 位
  - Mac：macOS X 10.13 或更高版本
  - Linux：请参阅[受支持的 Linux 分发和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)的列表。

## <a name="prerequisites"></a>先决条件

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/)（要求已安装 Java）

- 需要语音 SDK 版本 1.11.0 或更高版本。

- 支持的 Linux 平台会要求安装某些库（要求安装 `libssl` 以获取安全套接字层支持；要求安装 `libasound2` 以获取声音支持）。 请参阅下面的发行版，了解安装这些库的正确版本所需的命令。

  - 在 Ubuntu 上，请运行以下命令来安装所需的包：

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    如果 libssl1.0.0 不可用，则改为安装 libssl1.0.x（其中 x 大于 0）或 libssl1.1。

  - 在 RHEL/CentOS 上，请运行以下命令来安装所需的包：

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，按照[如何为语音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的说明进行操作。
> - 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的说明进行操作。

- 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 请注意，首次安装它时，可能需要重启 Windows 才能继续使用本指南。

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>创建 Eclipse 项目并安装语音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
