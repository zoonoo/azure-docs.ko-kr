---
title: '빠른 시작: 음성 인식, C++(macOS) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 macOS에서 C++로 음성을 인식하는 방법 알아보기
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: wolfma
ms.openlocfilehash: 7f8822a34b0f87beaf522656a91e8d40c724a7a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485009"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 macOS에서 C++로 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 macOS 10.13 이상용 C++ 콘솔 애플리케이션을 만듭니다. Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 실시간으로 Mac의 마이크에서 음성을 텍스트로 변환할 수 있습니다. 이 애플리케이션은 [macOS용 Speech SDK](https://aka.ms/csspeech/macosbinary) 및 Mac의 기본 C++ 컴파일러(예: `g++`)를 사용하여 빌드됩니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Speech Services 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [Speech Services를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="install-speech-sdk"></a>Speech SDK 설치

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services 음성 SDK의 현재 버전은 `1.6.0`입니다.

macOS용 Speech SDK는 https://aka.ms/csspeech/macosbinary 에서 압축된 프레임워크 번들로 다운로드할 수 있습니다.

SDK를 다음과 같이 다운로드하고 설치합니다.

1. Speech SDK 파일을 추출할 디렉터리를 선택하고, 해당 디렉터리를 가리키도록 `SPEECHSDK_ROOT` 환경 변수를 설정합니다. 이 변수는 이후 명령에서 디렉터리를 쉽게 참조할 수 있게 해줍니다. 예를 들어 홈 디렉터리에서 `speechsdk` 디렉터리를 사용하려는 경우 다음과 비슷한 명령을 사용합니다.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 아직 디렉터리가 없으면 디렉터리를 만듭니다.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK 프레임워크가 포함된 `.zip` 아카이브를 다운로드하여 추출합니다.

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. 추출된 패키지의 최상위 디렉터리의 내용을 확인합니다.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   디렉터리 목록에는 타사 통지와 라이선스 파일은 물론 `MicrosoftCognitiveServicesSpeech.framework` 디렉터리도 포함해야 합니다.

## <a name="add-sample-code"></a>샘플 코드 추가

1. `helloworld.cpp`라는 C++ 원본 파일을 만들고 다음 코드를 파일에 붙여넣습니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. 이 새 파일에서 `YourSubscriptionKey` 문자열을 Speech Services 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

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

> [!div class="nextstepaction"]
> [GitHub에서 C++ 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)

