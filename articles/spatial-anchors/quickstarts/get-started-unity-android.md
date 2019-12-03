---
title: '빠른 시작: Unity Android 앱 만들기'
description: 이 빠른 시작에서는 Spatial Anchors를 사용하여 Unity 지원 Android 앱을 빌드하는 방법을 알아봅니다.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 213cf9b64f2c43274192c22efa2fa5a7dfbce5e5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277071"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>빠른 시작: Azure Spatial Anchors를 사용하여 Unity Android 앱 만들기

이 빠른 시작에서는 [Azure Spatial Anchors](../overview.md)를 사용하여 Unity Android 앱을 만드는 방법을 다룹니다. Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 완료되면, Unity 지원 ARCore Android 앱이 있어 공간 앵커를 저장하고 회수할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Spatial Anchors 계정 만들기
> * Unity 빌드 설정 준비
> * Spatial Anchors 계정 식별자 및 계정 키 구성
> * Android Studio 프로젝트 내보내기
> * Android 디바이스 배포 및 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- Android 빌드 지원 및 Android SDK & NDK Tools 모듈을 포함하는 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 이상</a>이 설치된 Windows 또는 macOS 머신입니다.
  - Windows에서 실행하는 경우 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>도 필요합니다.
  - macOS에서 실행하는 경우 HomeBrew를 통해 Git이 설치됩니다. 터미널의 한 줄에 명령 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`을 입력합니다. 그런 다음, `brew install git`을 실행합니다.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">개발자 사용</a> 및 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 지원</a> Android 디바이스.
  - 컴퓨터가 Android 디바이스와 통신하려면 추가 디바이스 드라이버가 필요할 수 있습니다. 추가 정보 및 지침은 [여기](https://developer.android.com/studio/run/device.html)를 참조하세요.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Unity 샘플 프로젝트 다운로드 및 열기

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>계정 식별자 및 키 구성

**프로젝트** 창에서 `Assets/AzureSpatialAnchors.Examples/Scenes`로 이동하여 `AzureSpatialAnchorsBasicDemo.unity` 장면 파일을 엽니다.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

**파일** -> **저장**을 선택하여 장면을 저장합니다.

## <a name="export-the-android-studio-project"></a>Android Studio 프로젝트 내보내기

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**디바이스 실행**에서 디바이스를 선택하고 **빌드 및 실행**을 클릭합니다. 이름을 선택할 수 있는 `.apk` 파일을 저장하라는 메시지가 표시됩니다.

앱의 지침에 따라 앵커를 배치하고 회수합니다.

## <a name="troubleshooting"></a>문제 해결

앱 실행 시 백그라운드로 카메라가 보이지 않으면(대신 공백, 파랑 또는 기타 질감 등이 보이면) Unity에서 자산을 다시 가져와야 할 가능성이 높습니다. 앱을 중지합니다. Unity의 맨 위 메뉴에서 **자산 -> 모두 다시 가져오기**를 선택합니다. 그런 다음, 앱을 다시 실행합니다.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [자습서: 여러 디바이스에서 Spatial Anchors 공유](../tutorials/tutorial-share-anchors-across-devices.md)
