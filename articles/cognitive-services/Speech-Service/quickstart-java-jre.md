---
title: '빠른 시작: Java(Windows 또는 Linux)에서 음성 인식'
titleSuffix: Microsoft Cognitive Services
description: Java(Windows 또는 Linux)에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 923ab3378d5e2d833e11c5111d4dd9964fea6dc4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126616"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>빠른 시작: Java(Windows 또는 Linux)에서 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Speech SDK를 활용하는 JRE(Java Run-Time Environment)를 위한 Java 기반 콘솔 응용 프로그램을 만드는 방법을 설명합니다.
이 응용 프로그램은 Microsoft Cognitive Services SDK Maven Package를 기준으로 합니다.
통합 개발 환경(IDE)인 Eclipse를 사용합니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* 마이크가 작동하며 Eclipse를 실행할 수 있는 PC(Windows x64, Ubuntu 16.04 x64).
* Java 8용 64비트 JRE/JDK.
* [Eclipse](https://www.eclipse.org) 버전 4.8, 64비트 버전.
* Ubuntu 16.04에서 필요한 패키지의 설치를 위해 다음 명령을 실행합니다.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>프로젝트 생성 및 구성

1. Eclipse를 시작합니다.

1. Eclipse Launcher에서 **Workspace** 필드에 새 디렉터리의 이름을 입력합니다.
   그런 다음, **Launch**를 클릭합니다.

   ![Eclipse 작업 영역 만들기](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. 잠시 후 Eclipse IDE의 주 창이 표시됩니다.
   시작 화면이 있으면 닫습니다.

1. **File** \> **New** \> **Project**를 선택합니다.

1. **New Project** 마법사가 표시되면 **Java Project**를 선택하고 **Next**를 클릭합니다.

   ![마법사 선택](media/sdk/qs-java-jre-02-select-wizard.png)

1. 다음 창에서 프로젝트 이름에 **quickstart**를 입력하고 실행 환경으로 **JavaSE-1.8**(또는 그 이상)을 선택합니다.
   **Finish**를 클릭합니다.

   ![마법사 선택](media/sdk/qs-java-jre-03-create-java-project.png)

1. **Open Associated Perspective?** 라는 제목의 팝업 창이 표시되면**Open Perspective**를 선택합니다.

1. **Package explorer**에서 **quickstart** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Configure** \> **Convert to Maven Project**를 선택합니다.

   ![Maven 프로젝트로 변환](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. 표시되는 팝업 창에서 **Group Id**에 **com.microsoft.cognitiveservices.speech.samples**를 입력하고 **Artifact Id**에 **quickstart**를 입력합니다. **마침**을 선택합니다.

   ![Maven POM 구성](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. **pom.xml** 파일을 편집합니다.

  * 파일 끝 부분의 닫는 태그 `</project>` 앞에 Speech SDK의 Maven 리포지토리에 대한 참조를 사용하여 리포지토리 섹션을 만듭니다.

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * 또한 그 다음에 Speech SDK 버전 0.6.0을 종속성으로 사용하여 종속성 섹션을 추가합니다.

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * 변경 내용을 저장합니다.

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. **File** \> **New** \> **Class**를 선택하여 Java 프로젝트에 새로운 빈 클래스를 추가합니다.

1. **New Java Class** 창에서 **Package** 필드에 **speechsdk.quickstart**를 입력하고, **Name** 필드에 **Main**을 입력합니다.

   ![Main 클래스 만들기](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java`의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

F11 키를 누르거나 **실행** \> **디버그**를 선택합니다.
마이크에서 나오는 다음 15초 음성 입력이 인식되어 콘솔 창에 기록됩니다.

![성공한 인식 이후 콘솔 출력](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/java-jre` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [샘플 가져오기](speech-sdk.md#get-the-samples)
