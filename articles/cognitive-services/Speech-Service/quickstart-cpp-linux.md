---
title: C++ 및 Linux용 음성 SDK 빠른 시작 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services에서 Linux 및 C++로 음성 SDK를 사용하여 신속하게 시작할 수 있도록 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753601"
---
# <a name="quickstart-for-c-and-linux"></a>C++ 및 Linux용 빠른 시작

Cognitive Services 음성 SDK의 현재 버전은 `0.4.0`입니다.

Linux용 Cognitive Services 음성 SDK는 64비트 및 32비트 응용 프로그램을 빌드하는 데 사용할 수 있습니다. 필요한 파일을 https://aka.ms/csspeech/linuxbinary에서 tar-file로 다운로드할 수 있습니다.

> [!NOTE]
> C++ 및 Windows용 빠른 시작을 찾는 경우 [여기](quickstart-cpp-windows.md)로 이동합니다.
> C# 및 Windows용 빠른 시작을 찾는 경우 [여기](quickstart-csharp-windows.md)로 이동합니다.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 이러한 지침에서는 PC(x86 또는 x64)의 Ubuntu 16.04에서 실행 중인 것으로 가정합니다.
> 다른 Ubuntu 버전 또는 다른 Linux 배포에서 필요한 단계를 조정해야 합니다.

## <a name="prerequisites"></a>필수 조건

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>이진 패키지 가져오기

[!include[License Notice](includes/license-notice.md)]

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

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>샘플 코드

다음 코드는 마이크에서 영어 음성을 인식합니다.
`quickstart-linux.cpp`라는 파일에 배치합니다.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

코드의 구독 키를 구입한 구독 키로 바꿉니다.

## <a name="building"></a>빌딩

> [!NOTE]
> 아래의 빌드 명령을 _단일 줄_로 복사하고 붙여넣었는지 확인합니다.

* 다음 명령을 실행하여 x64 컴퓨터에 응용 프로그램을 빌드합니다.

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* 다음 명령을 실행하여 x86 컴퓨터에 응용 프로그램을 빌드합니다.

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>실행 중

응용 프로그램을 실행하려면 음성 SDK 라이브러리를 가리키도록 로더의 라이브러리 경로를 구성해야 합니다.

* x64 컴퓨터에서 다음을 실행합니다.

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* x86 컴퓨터에서 다음을 실행합니다.

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

다음과 같이 응용 프로그램을 실행합니다.

```sh
./quickstart-linux
```

정상적으로 작동하면 다음과 유사한 출력이 표시됩니다.

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>샘플 다운로드

샘플의 최신 집합은 [Cognitive Services 음성 SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조합니다.

## <a name="next-steps"></a>다음 단계

* 추가 예제는 [예제 페이지](samples.md)를 참조합니다.
