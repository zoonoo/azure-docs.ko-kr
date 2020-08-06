---
title: Unity 용 Azure 공간 앵커 설치
description: Azure 공간 앵커를 사용 하도록 Unity 프로젝트 구성
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812314"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity 프로젝트에서 Azure 공간 앵커 구성

## <a name="requirements"></a>요구 사항

Azure 공간 앵커는 현재 다음 구성의 LTS (Unity 2019.4)를 지원 합니다.

* AR Foundation 3.1을 사용 하는 Unity 2019.4은 Azure 공간 앵커 2.4.0 +에서 지원 됩니다.

## <a name="configuring-a-project"></a>프로젝트 구성

Unity 용 Azure 공간 앵커는 현재 Unity 자산 패키지 ()를 사용 하 여 분산 되어 `.unitypackage` 있습니다 .이는 [GitHub 릴리스에서](https://github.com/Azure/azure-spatial-anchors-samples/releases)찾을 수 있습니다.

### <a name="import-the-asset-package"></a>자산 패키지 가져오기

1. `AzureSpatialAnchors.unitypackage` [GitHub 릴리스에서](https://github.com/Azure/azure-spatial-anchors-samples/releases)대상으로 지정할 버전의 파일을 다운로드 합니다.
2. [여기](https://docs.unity3d.com/Manual/AssetPackagesImport.html) 의 지침에 따라 Unity 자산 패키지를 프로젝트로 가져옵니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: Unity에서 앵커 만들기 및 찾기](./create-locate-anchors-unity.md)
