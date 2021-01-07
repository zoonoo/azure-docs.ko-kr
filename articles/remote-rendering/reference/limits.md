---
title: 제한 사항
description: SDK 기능에 대한 코드 제한 사항
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 33f5314c80dc33dbec50dc21a71f4cb507979e12
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427431"
---
# <a name="limitations"></a>제한 사항

여러 기능에는 크기, 수 또는 기타 제한 사항이 있습니다.

## <a name="azure-frontend"></a>Azure Frontend

프런트 엔드 API (c + + 및 c #)에는 다음과 같은 제한 사항이 적용 됩니다.
* 프로세스 당 총 [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend) 인스턴스 수: 16.
* [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend)당 총 [azuresession](/dotnet/api/microsoft.azure.remoterendering.azuresession) 인스턴스 수: 16.

## <a name="objects"></a>개체

* 단일 형식 ([엔터티](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md)등)에 대해 허용 되는 총 개체: 16777215.
* 허용되는 총 활성 평면 잘라내기: 8.

## <a name="geometry"></a>기하 도형

* **애니메이션:** 애니메이션은 [게임 개체](../concepts/entities.md)의 개별 변환에 애니메이션으로 제한 됩니다. 스키닝 또는 꼭 짓 점 애니메이션의 골격 애니메이션은 지원 되지 않습니다. 원본 자산 파일의 애니메이션 트랙은 유지 되지 않습니다. 대신, 개체 변환 애니메이션은 클라이언트 코드에서 구동 되어야 합니다.
* **사용자 지정 셰이더:** 사용자 지정 셰이더 작성은 지원 되지 않습니다. 기본 제공 [색 자료](../overview/features/color-materials.md) 또는 [.pbr 자료](../overview/features/pbr-materials.md) 만 사용할 수 있습니다.
* 자산의 **최대 고유 재질 수** : 65535. 자동 자료 개수 감소에 대 한 자세한 내용은 [자료 중복](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) 제거 챕터를 참조 하세요.
* **단일 질감의 최대 차원** : 16384 x 16384. 변환 프로세스에 따라 더 큰 원본 질감이 크기가 줄어듭니다.

### <a name="overall-number-of-polygons"></a>전체 다각형 수

모든 로드된 모델의 허용 가능한 다각형 수는 [세션 관리 REST API](../how-tos/session-rest-api.md#create-a-session)에 전달된 VM의 크기에 따라 다릅니다.

| 서버 크기 | 최대 다각형 수 |
|:--------|:------------------|
|표준| 2천만 |
|프리미엄| 제한 없음 |

이 제한 사항에 대 한 자세한 내용은 [서버 크기](../reference/vm-sizes.md) 챕터를 참조 하세요.

## <a name="platform-limitations"></a>플랫폼 제한 사항

**Windows 10 Desktop**

* Win32/x64는 유일 하 게 지원 되는 Win32 플랫폼입니다. Win32/x86은 지원 되지 않습니다.

**HoloLens 2**

* [PV 카메라에서 렌더링](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) 기능은 지원되지 않습니다.