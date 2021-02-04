---
title: Unity 용 Azure 공간 앵커 설치
description: Azure 공간 앵커를 사용 하도록 Unity 프로젝트 구성
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550626"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity 프로젝트에서 Azure 공간 앵커 구성

이 가이드에서는 Unity 프로젝트에서 Azure 공간 앵커 SDK를 시작 하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항

Azure 공간 앵커는 현재 다음 구성의 LTS (Unity 2019.4)를 지원 합니다.

* AR Foundation 3.1을 사용 하는 Unity 2019.4은 Azure 공간 앵커 2.4.0 +에서 지원 됩니다.

## <a name="configuring-a-project"></a>프로젝트 구성

### <a name="download-packages"></a>패키지를 다운로드합니다.
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>패키지 가져오기
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Android에만 해당: gradle 파일 구성 Maintemplate.json

1. **편집** > **프로젝트 설정** > **플레이어** 로 이동합니다.
2. **플레이어 설정** 에 대 한 **검사기 패널** 에서 **Android** 아이콘을 선택 합니다.
3. **빌드** 섹션 아래에서 **사용자 지정 주 Gradle 템플릿** 확인란을 선택 하 여에서 사용자 지정 Gradle 템플릿을 생성 `Assets\Plugins\Android\mainTemplate.gradle` 합니다.
4. 텍스트 편집기에서 `mainTemplate.gradle` 파일을 엽니다.
5. 섹션에서 `dependencies` 다음 종속성을 붙여넣습니다.

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

모든 작업이 완료 되 면 `dependencies` 섹션은 다음과 같이 표시 됩니다.

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: Unity에서 앵커 만들기 및 찾기](./create-locate-anchors-unity.md)
