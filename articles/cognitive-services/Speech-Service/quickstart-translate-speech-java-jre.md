---
title: '빠른 시작: 음성 변환, Java(Windows, Linux) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용자 음성을 캡처하고, 다른 언어로 변환하고, 명령줄에 텍스트를 출력하는 간단한 Java 애플리케이션을 만듭니다. 이 가이드는 Windows 및 Linux 사용자를 위해 설계되었습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 68567e673cf6fb53b8d9b6f7b9ef96aee691ac3a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604962"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>빠른 시작: Java용 Speech SDK를 사용하여 음성 번역

빠른 시작은 [음성 텍스트 변환](quickstart-java-jre.md) 및 [음성 우선 가상 도우미](quickstart-virtual-assistant-java-jre.md)에도 사용할 수 있습니다.

이 빠른 시작에서는 컴퓨터의 마이크에서 사용자 음성을 캡처하고, 음성을 변환하고, 변환된 텍스트를 실시간으로 명령줄에 변환하는 간단한 Java 애플리케이션을 만듭니다. 이 애플리케이션은 64비트 Windows, 64비트 Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9) 또는 macOS 10.13 이상에서 실행되도록 설계되었으며 Speech SDK Maven 패키지 및 Eclipse Java IDE를 사용하여 빌드됩니다.

음성 변환에 사용할 수 있는 언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

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

> [!NOTE]
> Speech Devices SDK 및 Roobo 디바이스에 대한 내용은 [Speech Devices SDK](speech-devices-sdk.md)를 참조하세요.

## <a name="create-and-configure-project"></a>프로젝트 만들기 및 구성

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. Java 프로젝트에 새로운 빈 클래스를 추가하려면 **파일** > **새로 만들기** > **클래스**를 선택합니다.

1. **새 Java 클래스** 창에서, **패키지** 필드에 **speechsdk.quickstart**를 입력하고, **이름** 필드에 **기본**을 입력합니다.

   ![새 Java 클래스 창의 스크린샷](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java`의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

F11 키를 누르거나 **실행** > **디버그**를 선택합니다.

마이크에서 나오는 음성 입력이 독일어로 작성되어 콘솔 창에 기록됩니다. 음성 캡처를 중지하려면 "Enter" 키를 누릅니다.

![인식에 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽고, 합성된 음성으로 변환된 텍스트를 출력하는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 Java 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [빠른 시작: 음성 인식, Java(Windows, Linux)](quickstart-java-jre.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
