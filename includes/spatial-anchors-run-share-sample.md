---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 50e4799f09322eab05b4f8ddf7004c2e0078fdab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971491"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android 샘플은 디바이스 간 공유를 지원합니다.

Android Studio의 샘플 폴더에서 *SharedActivity.java* 파일을 엽니다. 

이전 단계에서 복사한 URL(ASP.NET 웹앱 Azure 배포에서)을 *SharedActivity.java* 파일의 `SharingAnchorsServiceUrl`에 대한 값으로 입력합니다. 

URL의 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS 샘플은 디바이스 간 공유를 지원합니다.

샘플 폴더에서 *SharedDemoViewController.m* 파일을 엽니다. 

이전 단계에서 가져온 URL(ASP.NET 웹앱 Azure 배포에서)을 *SharedDemoViewController.m* 파일의 `SharingAnchorsServiceUrl`에 대한 값으로 입력합니다. 

URL의 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

디바이스에 앱을 배포합니다. 

앱이 시작되면 **탭하여 공유 데모 시작** 옵션을 선택한 다음, 앱의 지침을 따릅니다. **탭하여 앵커 번호 찾기** 또는 **탭하여 앵커를 만들고 서비스에 저장**을 선택할 수 있습니다.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android와 iOS 샘플 모두 디바이스 간 공유를 지원합니다.

샘플 폴더에서 *AccountDetails.cs* 파일을 엽니다. 

이전 단계에서 가져온 URL(ASP.NET 웹앱 Azure 배포에서)을 *AccountDetails.cs* 파일의 `AnchorSharingServiceUrl`에 대한 값으로 입력합니다. 

URL의 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android 디바이스 설정

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS 디바이스 설정

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**프로젝트** 창에서 `Assets\AzureSpatialAnchors.Examples\Resources`로 이동합니다. 

**SpatialAnchorSamplesConfig**를 선택합니다. 그런 다음, **검사기** 창에서 `Sharing Anchors Service` URL(ASP.NET 웹앱 Azure 배포에서)을 `Base Sharing Url`에 대한 값으로 입력합니다. `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

**파일** > **저장**을 선택하여 장면을 저장합니다.

## <a name="deploy-to-your-device"></a>새 디바이스에 배포

### <a name="deploy-to-an-android-device"></a>Android 디바이스에 배포

Android 디바이스에 로그인하고 USB 케이블을 사용하여 컴퓨터에 연결합니다.

**파일** > **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**빌드 장면** 아래에서 각 장면 옆에 확인 표시가 있는지 확인합니다.

**프로젝트 내보내기**에 확인 표시가 없는지 확인합니다. **빌드 및 실행**을 선택합니다. *.apk* 파일을 저장하라는 메시지가 나타납니다. 아무 이름을 선택해도 됩니다.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>iOS 디바이스에 배포

**파일** > **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**빌드 장면** 아래에서 각 장면 옆에 확인 표시가 있는지 확인합니다.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Xcode에서 **중지**를 선택하여 앱을 중지합니다.
