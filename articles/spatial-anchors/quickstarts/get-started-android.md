---
title: 빠른 시작 - Azure Spatial Anchors를 사용하여 Android 앱 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Spatial Anchors를 사용하여 Android 앱을 빌드하는 방법을 알아봅니다.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64e5e78f93488b2c375c617e8857c84ba2171f00
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822398"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>빠른 시작: Azure Spatial Anchors를 사용하여 Android 앱 만들기

이 빠른 시작에서는 Java 또는 C++/NDK에서 [Azure Spatial Anchors](../overview.md)를 사용하여 Android 앱을 만드는 방법을 다룹니다. Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 완료되면, ARCore Android 앱이 있어 공간 앵커를 저장하고 회수할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Spatial Anchors 계정 만들기
> * Spatial Anchors 계정 식별자 및 계정 키 구성
> * Android 디바이스 배포 및 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>가 설치된 Windows 또는 macOS 머신.
  - NDK 샘플을 빌드하려면 Android Studio에 NDK 및 CMake 3.6 SDK Tools를 설치해야 합니다.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">개발자 사용</a> 및 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 지원</a> Android 디바이스.
- 앱은 ARCore 1.5(ARCore 1.6+에 대한 지원은 곧 제공될 예정)를 대상으로 해야 합니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>샘플 프로젝트 열기

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Android NDK 샘플을 빌드하는 경우 [여기](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.5.0/libraries/include/arcore_c_api.h)에서 `arcore_c_api.h`를 다운로드하여 `Android\NDK\libraries\include`에 배치해야 합니다.

Android Studio를 엽니다.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

**기존 Android Studio 프로젝트 열기**를 선택하여 `Android/Java/`에 있는 프로젝트를 선택합니다.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

**기존 Android Studio 프로젝트 열기**를 선택하여 `Android/NDK/`에 있는 프로젝트를 선택합니다.

***

## <a name="configure-account-identifier-and-key"></a>계정 식별자 및 키 구성

다음 단계는 앱을 구성하기 위해 Spatial Anchors 리소스를 설정할 때 이전에 기록한 계정 식별자 및 계정 키를 사용하는 것입니다.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

`Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`를 엽니다.

`SpatialAnchorsAccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`SpatialAnchorsAccountId` 필드를 찾아 `Set me`를 계정 식별자로 바꿉니다.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

`Android/NDK/app/src/main/cpp/spatial_services_application.cc`를 엽니다.

`SpatialAnchorsAccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`SpatialAnchorsAccountId` 필드를 찾아 `Set me`를 계정 식별자로 바꿉니다.

***

## <a name="deploy-the-app-to-your-android-device"></a>Android 디바이스에 앱 배포

Android 디바이스의 전원을 켜고 로그인한 후 USB 케이블을 사용하여 PC에 연결합니다.

Android Studio 도구 모음에서 **실행**을 선택합니다.

![Android Studio 배포 및 실행](./media/get-started-android/android-studio-deploy-run.png)

**배포 대상 선택** 대화 상자에서 Android 디바이스를 선택하고 **확인**을 선택하여 Android 디바이스에서 앱을 실행합니다.

앱의 지침에 따라 앵커를 배치하고 회수합니다.

Android Studio 도구 모음에서 **중지**를 선택하여 앱을 중지합니다.

![Android Studio 중지](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [자습서: 여러 디바이스에서 Spatial Anchors 공유](../tutorials/tutorial-share-anchors-across-devices.md)
