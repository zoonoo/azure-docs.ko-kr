---
title: Unity용 Azure Spatial Anchors 설치
description: Azure Spatial Anchors를 사용하도록 Unity 프로젝트 구성
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: fe9160f22754c62888b2a61ce9751f596842604e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076707"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity 프로젝트에서 Azure Spatial Anchors 구성

이 가이드에서는 Unity 프로젝트에서 Azure Spatial Anchors SDK를 시작하는 방법을 보여줍니다.

## <a name="project-requirements"></a>프로젝트 요구 사항

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>프로젝트 구성

Unity 프로젝트에 Azure Spatial Anchors SDK를 포함하려면 먼저 Unity 패키지 관리자를 통해 [필요한](#project-requirements) 패키지를 설치해야 합니다.

### <a name="download-asa-packages"></a>ASA 패키지 다운로드
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>ASA 패키지 가져오기
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Android 전용: mainTemplate.gradle 파일 구성

1. **편집** > **프로젝트 설정** > **플레이어** 로 이동합니다.
2. **플레이어 설정** 의 **검사기 패널** 에서 **Android** 아이콘을 선택합니다.
3. **빌드** 섹션 아래에서 **사용자 지정 주 Gradle 템플릿** 확인란을 선택하여 `Assets\Plugins\Android\mainTemplate.gradle`에서 사용자 지정 gradle 템플릿을 생성합니다.
4. 텍스트 편집기에서 `mainTemplate.gradle` 파일을 엽니다.
5. `dependencies` 섹션에서 다음 종속성을 붙여넣습니다.

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

모든 작업이 완료되면 `dependencies` 섹션은 다음과 같이 표시됩니다.

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: Unity에서 앵커 만들기 및 찾기](./create-locate-anchors-unity.md)
