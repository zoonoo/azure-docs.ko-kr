---
title: Unity 용 Azure 공간 앵커 설치
description: Azure 공간 앵커를 사용 하도록 Unity 프로젝트 구성
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: da983719dc66656aa28cab4aea0bae558c2a7162
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530422"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity 프로젝트에서 Azure 공간 앵커 구성

이 가이드에서는 Unity 프로젝트에서 Azure 공간 앵커 SDK를 시작 하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항

Azure 공간 앵커는 현재 다음 구성의 LTS (Unity 2019.4)를 지원 합니다.

* AR Foundation 3.1을 사용 하는 Unity 2019.4은 Azure 공간 앵커 2.4.0 +에서 지원 됩니다.

## <a name="configuring-a-project"></a>프로젝트 구성

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Unity 패키지 관리자 패키지를 프로젝트에 추가 합니다.](#tab/UPMPackage)

Unity 용 Azure 공간 앵커는 현재 UPM (Unity 패키지 관리자) 패키지를 사용 하 여 배포 됩니다. 이러한 패키지는 [NPM 레지스트리에서](https://bintray.com/microsoft/AzureMixedReality-NPM)찾을 수 있습니다. Unity 프로젝트에서 범위가 지정 된 패키지 레지스트리를 사용 하는 방법에 대 한 자세한 내용은 [여기](https://docs.unity3d.com/Manual/upm-scoped.html)에서 공식 Unity 설명서를 참조 하세요.

#### <a name="add-the-registry-to-your-unity-project"></a>Unity 프로젝트에 레지스트리 추가

1. 파일 탐색기에서 Unity 프로젝트의 `Packages` 폴더로 이동합니다. 텍스트 편집기에서 프로젝트 매니페스트 파일 `manifest.json`을 엽니다.
2. 파일의 맨 위에 있는 `dependencies` 섹션과 동일한 수준에서 다음 항목을 추가하여 Azure Spatial Anchors 레지스트리를 프로젝트에 포함합니다. `scopedRegistries` 항목은 Azure Spatial Anchors SDK 패키지를 찾을 위치를 Unity에 알려줍니다.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Unity 프로젝트에 SDK 패키지를 추가 합니다.

| 플랫폼 | 패키지 이름                                    |
|----------|-------------------------------------------------|
| Android  | .com.. m a c. |
| iOS      | spatial-anchors-sdk. ios     |
| HoloLens | .com.. m i c. |

1. 프로젝트에서 지원할 각 플랫폼 (Android/iOS/HoloLens)에 대해 패키지 이름 및 패키지 버전을 포함 하는 항목을 `dependencies` 프로젝트 매니페스트의 섹션에 추가 합니다. 아래에서 예제를 참조하세요.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. `manifest.json` 파일을 저장하고 닫습니다. Unity로 돌아가면 Unity는 프로젝트 매니페스트 변경을 자동으로 감지하고 지정된 패키지를 검색해야 합니다. 프로젝트 보기에서 `Packages` 폴더를 확장하여 올바른 패키지를 가져왔는지 확인할 수 있습니다.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Android에만 해당: gradle 파일 구성 Maintemplate.json

1. **편집** > **프로젝트 설정** > **플레이어**로 이동합니다.
2. **플레이어 설정**에 대 한 **검사기 패널** 에서 **Android** 아이콘을 선택 합니다.
3. **빌드** 섹션 아래에서 **사용자 지정 주 Gradle 템플릿** 확인란을 선택 하 여에서 사용자 지정 Gradle 템플릿을 생성 `Assets\Plugins\Android\mainTemplate.gradle` 합니다.
4. 텍스트 편집기에서 `mainTemplate.gradle` 파일을 엽니다.
5. 섹션에서 `dependencies` 다음 종속성을 붙여넣습니다.

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

모든 작업이 완료 되 면 `dependencies` 섹션은 다음과 같이 표시 됩니다.

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[자산 패키지 가져오기](#tab/UnityAssetPackage)

> [!WARNING]
> Azure 공간 앵커 SDK의 Unity 자산 패키지 배포는 버전 2.5.0에서 더 이상 사용 되지 않으며 2.6.0로 더 이상 사용할 수 없습니다.

1. `AzureSpatialAnchors.unitypackage` [GitHub 릴리스에서](https://github.com/Azure/azure-spatial-anchors-samples/releases)대상으로 지정할 버전의 파일을 다운로드 합니다.
2. [여기](https://docs.unity3d.com/Manual/AssetPackagesImport.html) 의 지침에 따라 Unity 자산 패키지를 프로젝트로 가져옵니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: Unity에서 앵커 만들기 및 찾기](./create-locate-anchors-unity.md)
