---
title: '빠른 시작: 음성 인식, Java(Windows, Linux) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 컴퓨터의 마이크에서 사용자 음성을 캡처하고 전사하는 간단한 Java 애플리케이션을 만드는 방법을 알아봅니다.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: fmegen
ms.openlocfilehash: 3dad2696332f57d9a0dc86927f91caa7352b1d7b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65884950"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>빠른 시작: Java용 Speech SDK를 사용하여 음성 인식

빠른 시작은 [음성 음성 변환](quickstart-translate-speech-java-jre.md) 및 [음성 우선 가상 도우미](quickstart-virtual-assistant-java-jre.md)에도 사용할 수 있습니다.

원하는 경우 다른 프로그래밍 언어 및/또는 환경을 선택합니다.<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 [Speech SDK](speech-sdk.md)를 사용하여 Java 콘솔 애플리케이션을 만듭니다. PC에서 마이크를 사용하여 실시간으로 음성을 텍스트로 변환합니다. 이 애플리케이션은 Speech SDK Maven 패키지와 64비트 Windows, 64비트 Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9) 또는 macOS 10.13 이상 기반의 Eclipse Java IDE(v4.8)를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

> [!NOTE]
> Speech Devices SDK 및 Roobo 디바이스에 대한 내용은 [Speech Devices SDK](speech-devices-sdk.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* 운영 체제: 64비트 Windows, 64비트 Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9) 또는 macOS 10.13 이상
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 또는 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).

Linux를 실행하는 경우 Eclipse를 시작하기 전에 이러한 종속 요소가 설치되어 있는지 확인합니다.

* Ubuntu에서:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Windows(64비트)를 실행하는 경우 플랫폼에 맞는 Microsoft Visual C++ 재배포 가능 패키지가 설치되어 있는지 확인합니다.
* [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지 다운로드](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>프로젝트 만들기 및 구성

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. Java 프로젝트에 새로운 빈 클래스를 추가하려면 **파일** > **새로 만들기** > **클래스**를 선택합니다.

1. **새 Java 클래스** 창에서, **패키지** 필드에 **speechsdk.quickstart**를 입력하고, **이름** 필드에 **기본**을 입력합니다.

   ![새 Java 클래스 창의 스크린샷](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java`의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

F11 키를 누르거나 **실행** > **디버그**를 선택합니다.
마이크에서 나오는 다음 15초 음성 입력이 인식되어 콘솔 창에 기록됩니다.

![인식에 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 Java 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [빠른 시작: 음성 변환, Java(Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
