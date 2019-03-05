---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753520"
---
## <a name="open-the-sample-project-in-unity"></a>Unity에서 샘플 프로젝트 열기

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Android 디바이스를 설정하려면

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>iOS 디바이스를 설정하려면

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>계정 식별자 및 키 구성

**프로젝트** 창에서 `Assets/AzureSpatialAnchorsPlugin/Examples`로 이동하여 `AzureSpatialAnchorsLocalSharedDemo.unity` 장면 파일을 엽니다.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

또한 **검사기** 창에서 `Sharing Anchors Service url`(ASP.NET 웹앱에서 Azure 배포)을 `Base Sharing Url`에 대한 값으로 입력하고 `index.html`을 `api/anchors`로 바꿉니다. 따라서 `https://<app_name>.azurewebsites.net/api/anchors`처럼 표시되어야 합니다.

**파일** -> **저장**을 선택하여 장면을 저장합니다.

## <a name="to-deploy-to-an-android-device"></a>Android 디바이스에 배포하려면

Android 디바이스의 전원을 켜고, 로그인하고, USB 케이블을 사용해 PC에 연결합니다.

**파일** -> **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**Scenes In Build** 아래에서 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 장면 옆에 확인 표시를 배치하고 다른 모든 장면에서 확인 표시를 지웁니다.

**프로젝트 내보내기** 확인란에 확인 표시가 없는지 확인합니다. **빌드 및 실행**을 클릭합니다. `.apk` 파일을 저장하라는 메시지가 표시되면 이름을 선택할 수 있습니다.

앱의 지침을 따르세요. **앵커 만들기 및 공유** 또는 **공유 앵커 찾기**를 선택할 수 있습니다. 첫 번째 옵션을 사용하면 나중에 동일한 디바이스 또는 다른 디바이스에 있을 수 있는 앵커를 만들 수 있습니다. 두 번째 옵션은 이전에 앱을 동일한 디바이스 또는 다른 디바이스에서 실행한 경우 이전에 공유된 앵커를 찾을 수 있습니다.

## <a name="to-deploy-to-an-ios-device"></a>iOS 디바이스에 배포하려면

**파일** -> **빌드 설정**을 선택하여 **빌드 설정**을 엽니다.

**Scenes In Build** 아래에서 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 장면 옆에 확인 표시를 배치하고 다른 모든 장면에서 확인 표시를 지웁니다.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

앱의 지침을 따르세요. **앵커 만들기 및 공유** 또는 **공유 앵커 찾기**를 선택할 수 있습니다. 첫 번째 옵션을 사용하면 나중에 동일한 디바이스 또는 다른 디바이스에 있을 수 있는 앵커를 만들 수 있습니다. 두 번째 옵션은 이전에 앱을 동일한 디바이스 또는 다른 디바이스에서 실행한 경우 이전에 공유된 앵커를 찾을 수 있습니다.

Xcode에서 **중지**를 눌러 앱을 중지합니다.
