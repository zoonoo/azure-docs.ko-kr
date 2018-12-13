---
title: '빠른 시작: Java(Windows 또는 Linux)에서 음성 인식'
titleSuffix: Azure Cognitive Services
description: Java(Windows 또는 Linux)에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: fmegen
ms.openlocfilehash: 2b39084443690b99391268acb6352763657e2a3c
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218579"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>빠른 시작: Speech Service SDK를 사용하여 Windows 또는 Linux 기반 Java에서 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 [Speech Service SDK](speech-sdk.md)를 사용하여 Java 콘솔 응용 프로그램을 만듭니다. PC에서 마이크를 사용하여 실시간으로 음성을 텍스트로 변환합니다. 응용 프로그램은 Speech SDK Maven 패키지와 64비트 Windows 또는 Ubuntu Linux 16.04 기반의 Eclipse Java IDE(v4.8)를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

> [!NOTE]
> Speech Devices SDK 및 Roobo 디바이스에 대한 내용은 [Speech Devices SDK](speech-devices-sdk.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 음성 서비스 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [음성 서비스를 무료로 체험해보기](get-started.md)를 참조하세요.


## <a name="create-and-configure-project"></a>프로젝트 만들기 및 구성

Ubuntu 16.04를 사용하는 경우 Eclipse를 시작하기 전에 다음 명령을 실행하여 필수 패키지가 설치되어 있는지 확인합니다.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Eclipse를 시작합니다.

1. Eclipse Launcher에서 **작업 영역** 필드에 새 작업 영역 디렉터리의 이름을 입력합니다. 그리고 **시작**을 선택합니다.

   ![Eclipse Launcher의 스크린샷](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. 잠시 후 Eclipse IDE의 주 창이 표시됩니다. 시작 화면이 표시되는 경우 시작 화면을 닫습니다.

1. Eclipse 메뉴 모음에서 **파일** > **새로 만들기** > **프로젝트**를 선택하여 새 프로젝트를 만듭니다.

1. **새 프로젝트** 대화 상자가 나타납니다. **Java 프로젝트**를 선택하고 **다음**을 선택합니다.

   ![Java 프로젝트가 강조 표시된 새 프로젝트 대화 상자의 스크린샷](media/sdk/qs-java-jre-02-select-wizard.png)

1. 새 Java 프로젝트 마법사가 시작됩니다. **프로젝트 이름** 필드에 **빠른 시작**을 입력하고, 실행 환경으로 **JavaSE-1.8**을 선택합니다. **마침**을 선택합니다.

   ![새 Java 프로젝트 마법사의 스크린샷](media/sdk/qs-java-jre-03-create-java-project.png)

1. **연결된 큐브 뷰를 열까요?** 라는 창이 나타나면 **큐브 뷰 열기**를 선택합니다.

1. **패키지 탐색기**에서 **빠른 시작** 프로젝트를 마우스 오른쪽 단추로 클릭합니다. 바로 가기 메뉴에서 **구성** > **Maven 프로젝트로 변환**을 선택합니다.

   ![패키지 탐색기의 스크린샷](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. **새 POM 만들기** 창이 나타납니다. **그룹 Id** 필드에 **com.microsoft.cognitiveservices.speech.samples**를 입력하고, **아티팩트 Id**에 **빠른 시작**을 입력합니다. 그런 다음, **마침**을 선택합니다.

   ![[새 POM 만들기] 창의 스크린샷](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. **pom.xml** 파일을 열고 편집합니다.

   * 파일 끝 부분에서 닫는 `</project>` 태그 앞에 다음과 같이 Speech SDK의 Maven 리포지토리에 대한 참조를 사용하여 `repositories` 요소를 만듭니다.

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * 또한 Speech SDK 버전 1.1.0을 종속성으로 사용하여 `dependencies` 요소를 추가합니다.

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * 변경 내용을 저장합니다.

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

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/java-jre` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Java용 Speech SDK를 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
