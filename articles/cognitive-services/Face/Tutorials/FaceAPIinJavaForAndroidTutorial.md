---
title: '자습서: Android SDK를 사용하여 이미지에서 얼굴 감지 및 포착'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Face API를 사용하여 이미지에서 얼굴을 감지하고 포착하는 샘플 Android 앱을 만들 것입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 11/12/2018
ms.author: pafarley
ms.openlocfilehash: 4378d04d8909ecb0cd77c3196b74ecd51eb19638
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686231"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>자습서: 이미지에서 얼굴을 감지하고 포착하는 Android 앱 만들기

이 자습서에서는 Azure Face API를 사용하여 Java SDK를 통해 이미지에서 사람 얼굴을 감지하고 포착하는 샘플 Android 애플리케이션을 만들 것입니다. 이 애플리케이션은 선택한 이미지를 표시하고 감지된 각 얼굴 주위에 프레임을 그립니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> - Android 응용 프로그램 만들기
> - Face API 클라이언트 라이브러리 설치
> - 클라이언트 라이브러리를 사용하여 이미지에서 얼굴 감지
> - 감지된 각 얼굴 주위에 프레임 그리기

![빨간색 직사각형으로 포착된 얼굴이 있는 사진의 Android 스크린샷](../Images/android_getstarted2.1.PNG)

전체 샘플 코드는 GitHub의 [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) 리포지토리에서 받을 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

- Face API 구독 키. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Face API 서비스를 구독하고 키를 가져옵니다.
- API 수준 22 이상의 [Android Studio](https://developer.android.com/studio/)(Face 클라이언트 라이브러리에 필요).

## <a name="create-the-android-studio-project"></a>Android Studio 프로젝트 만들기

다음 단계에 따라 새 Android 애플리케이션 프로젝트를 만듭니다.

1. Android Studio에서 **새 Android Studio 프로젝트 시작**을 선택합니다.
1. **Android 프로젝트 만들기** 화면에서 필요한 경우 기본 필드를 수정한 다음, **다음**을 클릭합니다.
1. **대상 Android 디바이스** 화면에서 드롭다운 선택기를 사용하여 **API 22** 이상을 선택하고, **다음**을 클릭합니다.
1. **빈 활동**을 선택한 다음, **다음**을 클릭합니다.
1. **이전 버전과의 호환성**을 선택 취소한 다음, **마침**을 클릭합니다.

## <a name="add-the-initial-code"></a>초기 코드를 추가합니다.

### <a name="create-the-ui"></a>UI 만들기

*activity_main.xml*을 엽니다. 레이아웃 편집기에서 **텍스트** 탭을 선택하고, 콘텐츠를 다음 코드로 바꿉니다.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?range=1-18)]

### <a name="create-the-main-class"></a>기본 클래스 만들기

*MainActivity.java*를 열고 기존 `import` 문을 다음 코드로 바꿉니다.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=3-11)]

그런 다음, **MainActivity** 클래스의 콘텐츠를 다음 코드로 바꿉니다. 이렇게 하면 사용자가 그림을 선택할 수 있도록 새 작업을 시작하는 이벤트 처리기가 **단추**에 생성됩니다. **ImageView**에 사진이 표시됩니다.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=29-68)]

### <a name="try-the-app"></a>앱 시험

**onActivityResult** 메서드에서 **detectAndFrame** 호출에 대한 주석을 답니다. 메뉴에서 **실행**을 눌러 앱을 테스트합니다. 앱이 열리면 에뮬레이터 또는 연결된 디바이스의 맨 아래에서 **찾아보기**를 클릭합니다. 디바이스의 파일 선택 대화 상자가 나타납니다. 이미지를 선택하고 해당 이미지가 창에 표시되는지 확인합니다. 앱을 닫고 다음 단계로 넘어갑니다.

![얼굴이 있는 사진의 Android 스크린샷](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Face SDK 추가

### <a name="add-the-gradle-dependency"></a>Gradle 종속성 추가

**Project** 창에서 드롭다운 선택기를 사용하여 **Android**를 선택합니다. **Gradle 스크립트**를 확장한 다음, *build.gradle(Module: app)* 을 엽니다. 아래 스크린샷처럼 Face 클라이언트 라이브러리 `com.microsoft.projectoxford:face:1.4.3`에 대한 종속성을 추가한 다음, **지금 동기화**를 클릭합니다.

![앱 build.gradle 파일의 Android Studio 스크린샷](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>얼굴 관련 프로젝트 코드 추가

**MainActivity.java**로 돌아가서 다음 `import` 문을 추가합니다.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=13-14)]

**MainActivity** 클래스에서 **onCreate** 메서드 위에 다음 코드를 삽입합니다.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=17-27)]

`<Subscription Key>`를 해당 구독 키로 바꿔야 합니다. 또한 해당 키에 적합한 지역 식별자를 사용하여 `<API endpoint>`를 Face API 엔드포인트로 바꿉니다. 평가판 구독 키는 **westus** 지역에 생성됩니다. 다음은 API 엔드포인트 값의 예입니다.

```java
apiEndpoint = "https://westus.api.cognitive.microsoft.com/face/v1.0";
```

**프로젝트** 창에서 **앱**, **매니페스트**를 확장하고 *AndroidManifest.xml*을 엽니다. 다음 요소를 `manifest` 요소의 직계 하위 항목으로 삽입합니다.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?range=5)]

## <a name="upload-image-and-detect-faces"></a>이미지 업로드 및 얼굴 감지

이 앱은 [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API를 래핑하고 **얼굴** 인스턴스를 반환하는 **FaceServiceClient.detect** 메서드를 호출하여 얼굴을 감지합니다.

반환된 각 **얼굴**에는 해당 위치를 표시하는 사각형이 일련의 선택적 얼굴 특성과 함께 포함되어 있습니다. 이 예제에서는 얼굴 사각형만 요청됩니다.

다음 두 메서드를 **MainActivity** 클래스에 삽입합니다. 얼굴 감지가 완료되면 앱이 **drawFaceRectanglesOnBitmap** 메서드를 호출하여 **ImageView**를 수정합니다. 이 메서드는 다음에 정의하겠습니다.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=70-150)]

## <a name="draw-face-rectangles"></a>얼굴 사각형 그리기

다음 도우미 메서드를 **MainActivity** 클래스에 삽입합니다. 이 메서드는 각 **얼굴** 인스턴스의 사각형 좌표를 사용하여 감지된 각 얼굴 주위에 사각형을 그립니다.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=152-173)]

마지막으로, **onActivityResult**의 **detectAndFrame** 메서드 호출에 대한 주석 처리를 제거합니다.

## <a name="run-the-app"></a>앱 실행

응용 프로그램을 실행하고 얼굴이 있는 이미지를 찾습니다. Face 서비스가 응답할 수 있도록 몇 초 동안 기다립니다. 이미지의 각 얼굴에 빨간색 사각형이 표시됩니다.

![얼굴 주위에 빨간색 사각형이 그려진 Android 스크린 샷](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Face API Java SDK를 사용하는 기본 프로세스를 배웠으며, 이미지에서 얼굴을 감지 및 포착하는 애플리케이션을 만들었습니다. 다음으로, 얼굴 감지에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [이미지에서 얼굴을 감지하는 방법](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)