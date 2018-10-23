---
title: '빠른 시작: Cognitive Services Speech SDK를 사용하여 Android의 Java에서 음성 인식'
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Speech SDK를 사용하여 Android의 Java에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: f2d5ca467777b8b351b2cf51abe71a003374bcf7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344221"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Android의 Java에서 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services Speech SDK를 사용하여 음성을 텍스트로 변환하는 Android용 Java 응용 프로그램을 만드는 방법을 설명합니다.
이 응용 프로그램은 Microsoft Cognitive Services Speech SDK Maven Package, 버전 1.0.1 및 Android Studio 3.1을 기준으로 합니다.
Speech SDK는 현재 32비트 또는 64비트 ARM 프로세서를 탑재한 Android 단말기와 호환됩니다.

> [!NOTE]
> Speech Devices SDK 및 Roobo 장치에 대한 내용은 [Speech Devices SDK](speech-devices-sdk.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 음성 서비스 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [음성 서비스를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-and-configure-a-project"></a>프로젝트 만들기 및 구성

1. Android Studio를 실행하고 시작 창에서 **새 Android Studio 프로젝트 시작**을 선택합니다.

    ![Android Studio 시작 창 스크린샷](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **새 프로젝트 만들기** 마법사가 나타납니다. **Create Android Project**(Android 프로젝트 만들기) 화면에서 **응용 프로그램 이름**으로 **Quickstart**를 입력하고 **회사 도메인**으로 **samples.speech.cognitiveservices.microsoft.com**을 입력한 후 프로젝트 디렉터리를 선택합니다. C++ 및 Kotlin 확인란을 선택 취소하고 **다음**을 선택합니다.

   ![새 프로젝트 만들기 마법사의 스크린샷](media/sdk/qs-java-android-02-create-android-project.png)

1. **대상 Android 장치** 화면에서 **휴대폰 및 태블릿**만 선택합니다. 그 아래의 드롭다운 목록에서 **API 23: Android 6.0 (Marshmallow)** 를 선택하고 **다음**을 선택합니다.

   ![새 프로젝트 만들기 마법사의 스크린샷](media/sdk/qs-java-android-03-target-android-devices.png)

1. **모바일에 작업 추가** 화면에서 **빈 작업**을 선택하고 **다음**을 클릭합니다.

   ![새 프로젝트 만들기 마법사의 스크린샷](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. **작업 구성** 화면에서 작업 이름으로 **MainActivity**를 사용하고 레이아웃 이름으로 **activity\_main**을 사용합니다. 두 확인란을 선택하고 **완료**를 선택합니다.

   ![새 프로젝트 만들기 마법사의 스크린샷](media/sdk/qs-java-android-05-configure-activity.png)

Android Studio가 새 Android 프로젝트를 준비하는 데 잠시 시간이 걸립니다. 다음으로, Speech SDK에 대해 알고 Java 8을 사용하는 프로젝트를 구성합니다.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services 음성 SDK의 현재 버전은 `1.0.1`입니다.

Android용 Speech SDK는 필요한 라이브러리와 이 SDK를 사용하는 데 필요한 Android 권한을 포함하는 [AAR(Android 라이브러리)](https://developer.android.com/studio/projects/android-library)로 패키지됩니다.
https://csspeechstorage.blob.core.windows.net/maven/의 Maven 리포지토리에서 호스트됩니다.

Speech SDK를 사용하도록 프로젝트를 설정합니다. Android Studio 메뉴 모음에서 **파일** > **프로젝트 구조**를 선택하여 프로젝트 구조 창을 엽니다. 프로젝트 구조 창에서 다음과 같이 변경합니다. 

1. 창의 왼쪽 목록에서 **프로젝트**를 선택합니다. 작은따옴표로 묶은 Maven 리포지토리 URL을 추가하여 **기본 라이브러리 리포지토리** 설정을 편집합니다. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![프로젝트 구조 창 스크린샷](media/sdk/qs-java-android-06-add-maven-repository.png)

1. 같은 화면의 왼쪽에서 **앱**을 선택합니다. 그런 다음, 창 맨 위의 **종속성** 탭을 선택합니다. 녹색 더하기 기호(+)를 선택하고 드롭다운 메뉴에서 **라이브러리 종속성**을 선택합니다.

   ![프로젝트 구조 창 스크린샷](media/sdk/qs-java-android-07-add-module-dependency.png)

1. 나타나는 창에서 Android용 Speech SDK의 이름과 버전인 `com.microsoft.cognitiveservices.speech:client-sdk:1.0.1`을 입력합니다. 그런 다음 **확인**을 선택합니다.
   Speech SDK는 이제 다음과 같이 종속성 목록에 추가됩니다.

   ![프로젝트 구조 창 스크린샷](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. **속성** 탭을 선택합니다. **원본 호환성** 및 **대상 호환성** 둘 다에 대해 **1.8**을 선택합니다.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. **확인**을 선택하여 프로젝트 구조 창을 닫고 프로젝트에 변경 내용을 적용합니다.

## <a name="create-user-interface"></a>사용자 인터페이스 만들기

응용 프로그램의 기본 사용자 인터페이스를 만들어 보겠습니다. 기본 활동을 위한 레이아웃 `activity_main.xml`을 편집합니다. 레이아웃에는 기본적으로 응용 프로그램 이름이 있는 제목 표시줄과 "Hello World!" 텍스트가 포함된 TextView가 포함됩니다.

* TextView 요소를 클릭합니다. 오른쪽 위 모서리에 있는 해당 ID 특성을 `hello`로 변경합니다.

* `activity_main.xml` 창 왼쪽 위에 있는 색상표에서 단추를 텍스트 위의 빈 공간으로 끌어옵니다.

* 오른쪽의 단추 특성에서 `onClick` 특성 값에 `onSpeechButtonClicked`를 입력합니다.  이 이름으로 버튼 이벤트를 처리하기 위한 메서드를 작성할 것입니다.  오른쪽 위 모서리에 있는 해당 ID 특성을 `button`로 변경합니다.

* 레이아웃 제약 조건을 유추하도록 디자이너 맨 위에 있는 마법 지팡이 아이콘을 사용합니다.

  ![마법 지팡이 아이콘의 스크린샷](media/sdk/qs-java-android-10-infer-layout-constraints.png)

이제 UI의 텍스트 및 그래픽 모양이 다음과 유사하게 표시됩니다.

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

## <a name="add-sample-code"></a>샘플 코드 추가

1. `MainActivity.java` 소스 파일을 엽니다. 이 파일의 모든 코드를 다음으로 바꿉니다.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 메서드에는 네이티브 플랫폼 바인딩을 초기화하고 마이크 및 인터넷 권한을 요청하는 코드도 포함되어 있습니다. 원시 플랫폼 바인딩 구성은 한 번만 필요합니다. 응용 프로그램을 초기화하는 동안 초기 단계에서 수행되어야 합니다.
   
   * `onSpeechButtonClicked` 메서드는 앞에서 설명한 것처럼 단추 클릭 처리기입니다. 단추를 누르면 음성 텍스트 변환 전사가 트리거됩니다.

1. 동일한 파일에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 또한 `YourServiceRegion` 문자열을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예: 평가판 구독의 `westus`).

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 개발 PC에 Android 장치를 연결합니다. 장치에서 [개발 모드 및 USB 디버깅](https://developer.android.com/studio/debug/dev-options)이 사용하도록 설정되어 있는지 확인합니다.

1. 응용 프로그램을 빌드하려면 Ctrl + F9를 누르거나 메뉴 모음에서 **빌드** > **프로젝트 만들기**를 선택합니다.

1. 응용 프로그램을 시작하려면 Shift+F10을 누르거나 **실행** > **'앱' 실행**을 선택합니다.

1. 나타나는 배포 대상 창에서 Android장치를 선택합니다.

   ![배포 대상 선택 창 스크린샷 ](media/sdk/qs-java-android-12-deploy.png)

응용 프로그램에서 단추를 눌러 음성 인식 섹션을 시작합니다. 다음 15초의 영어 음성이 음성 서비스로 전송되어 스크립트로 바뀝니다. 결과가 Android 응용 프로그램과, Android Studio의 logcat 창에 표시됩니다.

![Android 응용 프로그램 스크린샷](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/java-android` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Java용 Speech SDK를 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
