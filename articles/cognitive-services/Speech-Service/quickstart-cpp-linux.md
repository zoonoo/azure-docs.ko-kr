---
title: '빠른 시작: Cognitive Services Speech SDK를 사용하여 Linux의 C++에서 음성 인식'
titleSuffix: Microsoft Cognitive Services
description: 'Cognitive Services Speech # SDK를 사용하여 Linux의 C++에서 음성을 인식하는 방법 알아보기'
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: b8bc24123fe75e876f607b07e37423ae68d92ee4
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42093808"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Linux의 C++에서 음성 인식

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services Speech SDK를 사용하여 Linux(Ubuntu 16.04)에서 음성을 텍스트로 변환하는 C++ 콘솔 응용 프로그램을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* 작동하는 마이크가 있는 Ubuntu 16.04 PC
* 이 샘플을 빌드하고 실행하는 데 필요한 패키지를 설치하려면 다음을 실행합니다.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Speech SDK 가져오기

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services 음성 SDK의 현재 버전은 `0.6.0`입니다.

Linux용 Cognitive Services 음성 SDK는 64비트 및 32비트 응용 프로그램을 빌드하는 데 사용할 수 있습니다.
필요한 파일을 https://aka.ms/csspeech/linuxbinary에서 tar-file로 다운로드할 수 있습니다.
SDK를 다음과 같이 다운로드하고 설치합니다.

1. 음성 SDK 이진 파일 및 헤더를 배치하려는 디렉터리(절대 경로)를 선택합니다.
   예를 들어 홈 디렉터리에서 경로 `speechsdk`를 선택합니다.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 아직 없는 경우 디렉터리를 만듭니다.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 음성 SDK 이진 파일이 있는 `.tar.gz` 아카이브를 다운로드하고 압축합니다.

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 추출된 패키지의 최상위 디렉터리의 내용을 확인합니다.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   타사 알림 및 라이선스 파일과 함께 헤더용 `include` 디렉터리 및 라이브러리용 `lib` 디렉터리를 표시해야 합니다.

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. `helloworld.cpp` 파일에 다음 코드를 추가합니다.

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

## <a name="building"></a>빌딩

> [!NOTE]
> 아래의 빌드 명령을 _단일 줄_로 복사하고 붙여넣었는지 확인합니다.

* **x64** 컴퓨터에서 다음 명령을 실행하여 응용 프로그램을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* **x86** 컴퓨터에서 다음 명령을 실행하여 응용 프로그램을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>샘플 실행

1. Speech SDK 라이브러리를 가리키도록 로더의 라이브러리 경로를 구성합니다.

   * **x64** 컴퓨터에서 다음을 실행합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86** 컴퓨터에서 다음을 실행합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. 다음과 같이 응용 프로그램을 실행합니다.

   ```sh
   ./helloworld
   ```

1. 다음과 유사한 결과가 표시됩니다.

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/cpp-linux` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [샘플 가져오기](speech-sdk.md#get-the-samples)
