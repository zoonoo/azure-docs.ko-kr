---
title: '빠른 시작: 마이크에서 음성 인식, C++(macOS) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 macOS에서 C++로 음성을 인식하는 방법 알아보기
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 9b19cd1ff8340a22afb6713289bc3d7be42857ea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500659"
---
## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=macos)

## <a name="add-sample-code"></a>샘플 코드 추가

1. `helloworld.cpp`라는 C++ 원본 파일을 만들고 다음 코드를 파일에 붙여넣습니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. 이 새 파일에서 `YourSubscriptionKey` 문자열을 Speech Services 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

## <a name="build-the-app"></a>앱 빌드

> [!NOTE]
> 아래 명령을 _단일 명령줄_로 입력합니다. 이 작업을 수행하는 가장 쉬운 방법은 각 명령 옆에 있는 **복사** 단추를 누른 다음, 셸 프롬프트에 붙여넣는 것입니다.

* 다음 명령을 실행하여 애플리케이션을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>앱 실행

1. Speech SDK 라이브러리를 가리키도록 로더의 라이브러리 경로를 구성합니다.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. 애플리케이션을 실행합니다.

   ```sh
   ./helloworld
   ```

1. 아무 말이나 하라는 메시지가 콘솔 창에 나타납니다. 영어 구 또는 문장을 말씀하세요. 음성은 Speech Services로 전송되어 텍스트로 변환되고, 동일한 창에 표시됩니다.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]