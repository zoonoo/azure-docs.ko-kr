---
title: '빠른 시작: Cognitive Services Speech SDK를 사용하여 Android의 Java에서 음성 인식'
titleSuffix: Microsoft Cognitive Services
description: 'Cognitive Services Speech # SDK를 사용하여 Android의 Java에서 음성을 인식하는 방법 알아보기'
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: bbf10d9bd0912dab22c81e2bb850ede52a3bd9e3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42093850"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Android의 Java에서 음성 인식

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services Speech SDK를 사용하여 음성을 텍스트로 변환하는 Android용 Java 응용 프로그램을 만드는 방법을 설명합니다.
이 응용 프로그램은 Microsoft Cognitive Services Speech SDK Maven Package, 버전 0.6.0 및 Android Studio 3.1을 기준으로 합니다.

> [!NOTE]
> Speech Devices SDK 및 Roobo 장치에 대해서는 [Speech Devices SDK](speech-devices-sdk.md) 페이지를 방문하세요.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* Android Studio를 실행할 수 있는 PC(Windows, Linux, Mac)
* [Android Studio](https://developer.android.com/studio/) 버전 3.1
* 작동하는 마이크가 장착되고 [개발용으로 설정된](https://developer.android.com/studio/debug/dev-options) ARM 기반 Android 장치(API 23: Android 6.0 Marshmallow 이상)

## <a name="create-an-android-studio-project"></a>Android Studio 프로젝트 만들기

Android Studio를 시작하고 **새 Android Studio 프로젝트 시작**을 선택합니다.

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

표시되는 **새 프로젝트 만들기** 마법사에서 다음과 같이 선택합니다.

1. **Android 프로젝트 만들기** 화면에서 **응용 프로그램 이름**으로 **Quickstart**를 입력하고 **회사 도메인**으로 **samples.speech.cognitiveservices.microsoft.com**으로 입력한 후 프로젝트 위치를 선택합니다. 확인란을 선택 취소한 상태로 두고 **다음**을 클릭합니다.

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. **대상 Android 장치** 화면에서 **휴대폰 및 태블릿**을 유일한 옵션으로 선택한 후 아래의 드롭다운 목록에서 **API 23: Android 6.0(marshmallow)** 을 선택하고 **다음**을 클릭합니다.

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. **Add an Activity to Mobile**(모바일에 활동 추가) 화면에서 **빈 활동**을 선택하고 **다음**을 클릭합니다.

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. **활동 구성** 화면에서 활동 이름으로 **MainActivity**를 사용하고 레이아웃 이름으로 **activity\_main**을 사용합니다. 두 확인란을 선택하고 **마침**을 클릭합니다.

   ![](media/sdk/qs-java-android-05-configure-activity.png)

잠시 동안 실행하면 새로 만든 Android Studio 프로젝트가 나타납니다.

## <a name="configure-your-project-for-the-speech-sdk"></a>Speech SDK에 대한 프로젝트 구성

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services 음성 SDK의 현재 버전은 `0.6.0`입니다.

Android용 Speech SDK는 사용에 필요한 Android 권한 뿐만 아니라 필요한 라이브러리를 포함하는 [AAR(Android 라이브러리)](https://developer.android.com/studio/projects/android-library)로 패키지됩니다.
https://csspeechstorage.blob.core.windows.net/maven/의 Maven 리포지토리에서 호스트됩니다.

아래에서는 Speech SDK를 사용하도록 프로젝트를 설정하는 방법을 설명합니다.

**파일** \> **프로젝트 구조** 아래에서 프로젝트 구조 창을 엽니다.
표시되는 창에서 다음과 같이 변경합니다(모든 단계를 완료 한 후에만 **확인** 클릭).

1. **프로젝트**를 선택하고 쉼표 및 작은따옴표로 묶은 Maven 리포지토리 URL `'https://csspeechstorage.blob.core.windows.net/maven/'`을 추가하여 **기본 라이브러리 리포지토리** 설정을 편집합니다.

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. 같은 화면에서 왼쪽에 있는 **앱** 모듈을 선택하고 위쪽에서 **종속성** 탭을 선택합니다. 그런 다음, 오른쪽 위 모서리에서 녹색 더하기 기호를 클릭하고 **라이브러리 종속성**을 선택합니다.

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. 나타나는 창에서 Android용 Speech SDK의 이름과 버전인 `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`을 입력하고 **확인**을 클릭합니다.
   Speech SDK는 이제 다음과 같이 종속성 목록에 추가됩니다.

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. 위쪽에서 **속성** 탭을 선택합니다. **원본 호환성** 및 **대상 호환성** 둘 다에 대해 **1.8**을 선택합니다.

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. 마지막으로, **확인**을 클릭하여 **프로젝트 구조** 창을 닫고 모든 업데이트를 적용합니다.

## <a name="create-a-minimal-ui"></a>최소 UI 만들기

기본 활동을 위한 레이아웃 `activity_main.xml`을 편집합니다.
기본적으로 응용 프로그램의 이름과 'Hello World!'라는 TextView가 포함된 제목 표시줄이 표시됩니다.

* TextView를 클릭합니다. 오른쪽 위 모서리에 있는 해당 ID 특성을 `hello`로 변경합니다.

* `activity_main.xml` 창 왼쪽 위에 있는 색상표에서 Button을 텍스트 위의 빈 공간으로 끌어옵니다.

* 오른쪽의 단추 특성에서 `onClick` 특성 값에 `onSpeechButtonClicked`를 입력합니다. 이 값은 단추 처리기의 이름으로 사용됩니다.
  오른쪽 위 모서리에 있는 해당 ID 특성을 `button`로 변경합니다.

* 레이아웃 제약 조건을 유추하려면 디자이너 맨 위에 있는 마법 지팡이 아이콘을 사용합니다.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

이제 UI의 텍스트 및 그래픽 버전이 다음과 유사하게 표시됩니다.

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. `MainActivity.java` 원본 파일을 편집하고 해당 코드를 다음으로 바꿉니다(package 문 아래).

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 메서드에는 네이티브 플랫폼 바인딩을 초기화할 뿐만 아니라 마이크 및 인터넷 권한을 요하는 코드도 포함되어 있습니다. 네이티브 플랫폼 바인딩 구성은 응용 프로그램 초기화 동안 초기에 한 번만 수행하면 됩니다.
   
   * 메서드 `onSpeechButtonClicked`는 이전에 단추 클릭 처리기에 연결되었습니다. 단추 누름은 실제 음성 인식을 트리거합니다.

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

* 빌드하려면 , Ctrl+F9를 누르거나 **빌드** \> **프로젝트 만들기**를 선택합니다.

* 개발 PC에 Android 장치를 연결합니다. [개발 모드 및 USB 디버깅이 사용되도록 설정](https://developer.android.com/studio/debug/dev-options)되어 있는지 확인합니다.

* 앱을 시작하려면 Shift+F10을 누르거나 **실행** \> **'앱' 실행**을 선택합니다.

* 나타나는 배포 대상 창에서 장치를 선택합니다.

  ![앱을 디버깅하기 시작](media/sdk/qs-java-android-12-deploy.png)

* 앱이 장치에서 시작됩니다.
  단추를 누르면 다음 15초가 인식되고 UI에 표시됩니다(Android Studio의 logcat 창에서 응답을 볼 수 있음).

  ![성공적인 인식 후 UI](media/sdk/qs-java-android-13-gui-on-device.png)

이 스크린샷은 Android Quickstart로 끝납니다. 전체 프로젝트 샘플 코드는 샘플 리포지토리에서 다운로드할 수 있습니다.

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/java-android` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [샘플 가져오기](speech-sdk.md#get-the-samples)
