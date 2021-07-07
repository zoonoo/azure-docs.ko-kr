---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: c2222918106de8594881f105cc86ab1b45135027
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110166077"
---
이 가이드에서는 Linux용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>시스템 요구 사항

Linux; [지원되는 Linux 배포 및 대상 아키텍처](~/articles/cognitive-services/speech-service/speech-sdk.md)의 목록을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

* [Go 이진(1.13 이상)](https://golang.org/dl/)
* Linux 환경은 [시스템 요구 사항 및 설치 지침](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)에 따라 설정됩니다.


[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Go 환경 구성

Speech SDK를 찾기 위해 Go 환경을 설정하려면 다음 단계를 수행합니다. 두 단계에서 `<architecture>`를 CPU의 프로세서 아키텍처로 바꿉니다. 이는 `x86`, `x64`, `arm32` 또는 `arm64`가 됩니다.

1. 바인딩은 `cgo`에 의존하므로 Go가 SDK를 찾을 수 있도록 환경 변수를 설정해야 합니다.

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. SDK를 비롯한 애플리케이션을 실행하려면 라이브러리를 찾을 위치를 OS에 알려야 합니다.

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list-go.md)]
