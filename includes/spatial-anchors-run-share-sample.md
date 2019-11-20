---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882330"
---
## <a name="androidtabandroid"></a>[Android](#tab/Android)

Java Android 샘플은 디바이스 간 공유를 지원합니다.
Android Studio의 샘플 폴더에서 `SharedActivity.java` 파일을 엽니다. 이전 단계에서 가져온 URL(ASP.NET 웹앱 Azure 배포에서)을 `SharedActivity.java` 파일의 `SharingAnchorsServiceUrl`에 대한 값으로 입력합니다. URL의 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="iostabios"></a>[iOS](#tab/iOS)

Objective-C iOS 샘플은 디바이스 간 공유를 지원합니다.
샘플 폴더에서 `SharedDemoViewController.m` 파일을 엽니다. 이전 단계에서 가져온 URL(ASP.NET 웹앱 Azure 배포에서)을 `SharedActivity.java` 파일의 `SharingAnchorsServiceUrl`에 대한 값으로 입력합니다. URL의 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarintabxamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android와 iOS 샘플 모두 디바이스 간 공유를 지원합니다.
샘플 폴더에서 `AccountDetails.cs` 파일을 엽니다. 이전 단계에서 가져온 URL(ASP.NET 웹앱 Azure 배포에서)을 `SharedActivity.java` 파일의 `AnchorSharingServiceUrl`에 대한 값으로 입력합니다. URL의 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unitytabunity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android 디바이스 설정

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS 디바이스 설정

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>계정 식별자 및 키 구성

**프로젝트** 창에서 `Assets/AzureSpatialAnchorsPlugin/Examples`로 이동하여 `AzureSpatialAnchorsLocalSharedDemo.unity` 장면 파일을 엽니다.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**프로젝트** 창에서 `Assets\AzureSpatialAnchors.Examples\Resources`로 이동합니다. `SpatialAnchorSamplesConfig`를 선택합니다. 그런 다음, **검사기** 창에서 `Sharing Anchors Service url`(ASP.NET 웹앱에서 Azure 배포)을 `Base Sharing Url`에 대한 값으로 입력하고 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

**파일** > **저장**을 선택하여 장면을 저장합니다.

## <a name="deploy-to-your-device"></a>새 디바이스에 배포

### <a name="deploy-to-android-device"></a>Android 디바이스에 배포

Android 디바이스에 로그인하고, USB 케이블을 사용하여 디바이스를 컴퓨터에 연결합니다.

**파일** > **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**빌드 장면** 아래에서 모든 장면 옆에 확인 표시가 있는지 확인합니다.

**프로젝트 내보내기**에는 확인 표시가 없어야 합니다. **빌드 및 실행**을 선택합니다. `.apk` 파일을 저장하라는 메시지가 나타납니다. 아무 이름을 선택해도 됩니다.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>iOS 디바이스에 배포

**파일** > **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**빌드 장면** 아래에서 모든 장면 옆에 확인 표시가 있는지 확인합니다.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Xcode에서 **중지**를 선택하여 앱을 중지합니다.
