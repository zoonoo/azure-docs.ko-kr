---
title: '빠른 시작: Speech SDK C++(Linux) 플랫폼 설정 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 통해 Speech Service SDK를 사용하여 Linux에서 C++용 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 099c835ae2047538c882414660f699341af60b8b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "83673257"
---
이 가이드에서는 Linux용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>시스템 요구 사항

Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* 지원되는 Linux 플랫폼에는 특정 라이브러리가 설치되어 있어야 합니다(보안 소켓 레이어 지원을 위한 `libssl` 및 사운드 지원을 위한 `libasound2`). 이러한 라이브러리의 올바른 버전을 설치하는 데 필요한 명령은 아래 배포를 참조하세요.

   * Ubuntu에서:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Debian 9에서:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * RHEL/CentOS에서:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - RHEL/CentOS 7에서 [Speech SDK용 RHEL/CentOS 7을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)의 지침을 따르세요.
> - RHEL/CentOS 8에서 [Linux용 OpenSSL을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)의 지침을 따르세요.

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
