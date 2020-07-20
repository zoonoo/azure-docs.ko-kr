---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: gelecaro
ms.openlocfilehash: a1bc980f8334ca815a1805f33f3572cded4ba0ef
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156489"
---
이 가이드에서는 Linux용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>시스템 요구 사항

Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

* gcc
* [Go 이진(1.13 이상)](https://golang.org/dl/)

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

   * RHEL/CentOS 8에서:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> RHEL/CentOS 8에서 [Linux용 OpenSSL을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)의 지침을 따르세요.

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Go 환경 구성

1. 바인딩은 `cgo`에 의존하므로 Go가 SDK를 찾을 수 있도록 환경 변수를 설정해야 합니다.

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. 또한 SDK를 비롯한 애플리케이션을 실행하려면 라이브러리를 찾을 위치를 OS에 알려야 합니다.

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list-go.md)]