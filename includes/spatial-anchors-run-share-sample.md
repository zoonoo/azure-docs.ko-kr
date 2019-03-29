---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305199"
---
## <a name="set-up-your-device"></a>디바이스 설정

### <a name="set-up-an-android-device"></a>Android 디바이스 설정

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS 디바이스 설정

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>계정 식별자 및 키 구성

**프로젝트** 창에서 `Assets/AzureSpatialAnchorsPlugin/Examples`로 이동하여 `AzureSpatialAnchorsLocalSharedDemo.unity` 장면 파일을 엽니다.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**검사기** 창에서, `Base Sharing Url`의 값으로 `Sharing Anchors Service url`(ASP.NET 웹앱 Azure 배포의)을 입력하고 `index.html`을 `api/anchors`로 바꿉니다. `https://<app_name>.azurewebsites.net/api/anchors`와 비슷한 형식이어야 합니다.

**파일** > **저장**을 선택하여 장면을 저장합니다.

## <a name="to-deploy-the-app-to-an-android-device"></a>Android 디바이스에 앱을 배포하려면

Android 디바이스에 로그인하고, USB 케이블을 사용하여 디바이스를 컴퓨터에 연결합니다.

**파일** > **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**Scenes In Build**(빌드 중인 장면) 아래에서, `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 장면 옆에 확인 표시를 배치하고 다른 모든 장면의 확인 표시를 지웁니다.

**프로젝트 내보내기**에는 확인 표시가 없어야 합니다. **빌드 및 실행**을 선택합니다. `.apk` 파일을 저장하라는 메시지가 나타납니다. 아무 이름을 선택해도 됩니다.

앱의 지침을 따르세요. **앵커 만들기 및 공유** 또는 **공유 앵커 찾기**를 선택할 수 있습니다. 첫 번째 옵션을 사용하면 나중에 동일한 디바이스 또는 다른 디바이스에서 찾을 수 있는 앵커를 만들 수 있습니다. 이전에 동일한 디바이스에서 또는 다른 디바이스에서 이미 앱을 실행한 경우 두 번째 옵션을 사용하여 이전에 공유한 앵커를 찾을 수 있습니다.

## <a name="to-deploy-the-app-to-an-ios-device"></a>iOS 디바이스에 앱을 배포하려면

**파일** > **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**Scenes In Build**(빌드 중인 장면) 아래에서, `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 장면 옆에 확인 표시를 배치하고 다른 모든 장면의 확인 표시를 지웁니다.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

앱의 지침을 따르세요. **앵커 만들기 및 공유** 또는 **공유 앵커 찾기**를 선택할 수 있습니다. 첫 번째 옵션을 사용하면 나중에 동일한 디바이스 또는 다른 디바이스에서 찾을 수 있는 앵커를 만들 수 있습니다. 이전에 동일한 디바이스에서 또는 다른 디바이스에서 이미 앱을 실행한 경우 두 번째 옵션을 사용하여 이전에 공유한 앵커를 찾을 수 있습니다.

Xcode에서 **중지**를 선택하여 앱을 중지합니다.
