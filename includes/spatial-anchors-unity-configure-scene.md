---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998075"
---
다음 단계는 계정 식별자 및 계정 키를 사용하도록 앱을 구성하는 것입니다. [Spatial Anchors 리소스 설정](#create-a-spatial-anchors-resource) 시 텍스트 편집기에 복사했습니다.

**프로젝트** 창에서 `Assets\AzureSpatialAnchors.SDK\Resources`로 이동합니다. `SpatialAnchorConfig`를 선택합니다. 그런 다음, **검사기** 창에 `Account Key`를 `Spatial Anchors Account Key` 값으로, `Account ID`를 `Spatial Anchors Account Id` 값으로 입력합니다.

그런 다음, `SpatialAnchorManager.cs`를 엽니다. `CreateSessionAsync()`를 찾아 다음 이전 버전의 계정 도메인으로 대체하는 `session.Configuration.AccountDomain = "MyAccountDomain";` 줄을 추가합니다. 이 주석 `// Configure authentication` 바로 앞에 이 줄을 추가할 수 있습니다.
