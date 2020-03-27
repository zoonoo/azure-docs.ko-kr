---
title: '빠른 시작: Java(Windows, Linux, macOS)용 Speech SDK 플랫폼 설정 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 사용하여 Speech Service SDK와 함께 Java(Windows, Linux, macOS)를 사용하기 위한 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78925156"
---
이 가이드에서는 64비트 Java 8 JRE용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다. 패키지 이름을 직접 시작하려는 경우에는 Maven 중앙 리포지토리에 Java SDK가 없습니다. Gradle 또는 `pom.xml` 종속성 파일을 사용하는 경우 `https://csspeechstorage.blob.core.windows.net/maven/`을 가리키는 사용자 지정 리포지토리를 추가해야 합니다(패키지 이름은 아래 참조).

> [!NOTE]
> Speech Devices SDK 및 Roobo 디바이스에 대한 내용은 [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md)를 참조하세요.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>지원되는 운영 체제

- Java Speech SDK 패키지는 다음과 같은 운영 체 제용으로 제공됩니다.
  - Windows: 64비트 전용
  - Mac: macOS X 버전 10.13 이상
  - Linux: 64비트 전용 Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>사전 요구 사항

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 또는 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/)(Java 사전 설치 필요)
- 지원되는 Linux 플랫폼에는 특정 라이브러리가 설치되어 있어야 합니다(보안 소켓 레이어 지원을 위한 `libssl` 및 사운드 지원을 위한 `libasound2`). 이러한 라이브러리의 올바른 버전을 설치하는 데 필요한 명령은 아래 배포를 참조하세요.

  - Ubuntu에서 필요한 패키지를 설치하려면 다음 명령을 실행합니다.

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - Debian 9에서 필요한 패키지를 설치하려면 다음 명령을 실행합니다.

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - RHEL/CentOS 8에서 필요한 패키지를 설치하려면 다음 명령을 실행합니다.

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> RHEL/CentOS 8에서 [Linux용 OpenSSL을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)의 지침을 따르세요.

- Windows의 경우 플랫폼에 맞는 [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)가 필요합니다. 이 패키지를 처음 설치하는 경우 이 가이드를 계속하기 전에 Windows를 다시 시작해야 할 수 있습니다.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Eclipse 프로젝트 만들기 및 Speech SDK 설치

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
