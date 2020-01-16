---
title: '빠른 시작: 마이크에서 음성 인식, C++(Linux) - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: b2b59eb4c81f28a0bb74aca11d925405256c16a5
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75928223"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에:

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=linux)
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.

## <a name="add-sample-code"></a>샘플 코드 추가

1. `helloworld.cpp`라는 C++ 원본 파일을 만들고 다음 코드를 파일에 붙여넣습니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

1. 이 새 파일에서 `YourSubscriptionKey` 문자열을 음성 서비스 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

> [!NOTE]
> Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.

## <a name="build-the-app"></a>앱 빌드

> [!NOTE]
> 아래 명령을 _단일 명령줄_로 입력합니다. 이 작업을 수행하는 가장 쉬운 방법은 각 명령 옆에 있는 **복사** 단추를 누른 다음, 셸 프롬프트에 붙여넣는 것입니다.

* **x64**(64비트) 시스템에서 다음 명령을 실행하여 애플리케이션을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* **x86**(32비트) 시스템에서 다음 명령을 실행하여 애플리케이션을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* **ARM64**(64비트) 시스템에서 다음 명령을 실행하여 애플리케이션을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>앱 실행

1. Speech SDK 라이브러리를 가리키도록 로더의 라이브러리 경로를 구성합니다.

   * **x64**(64비트) 시스템에서 다음 명령을 입력합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86**(32비트) 시스템에서 다음 명령을 입력합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * **ARM64**(64비트) 시스템에서 다음 명령을 입력합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. 애플리케이션을 실행합니다.

   ```sh
   ./helloworld
   ```

1. 아무 말이나 하라는 메시지가 콘솔 창에 나타납니다. 영어 구 또는 문장을 말씀하세요. 음성은 음성 서비스로 전송되어 텍스트로 변환되고, 동일한 창에 표시됩니다.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
