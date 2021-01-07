---
title: Azure Security Center의 기본 대시보드 또는 '개요' 페이지
description: Security Center 개요 페이지의 기능에 대해 알아보기
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 899fddf08a817126ce775733da38ffd10ab3ab23
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952117"
---
# <a name="azure-security-centers-overview-page"></a>Azure Security Center 개요 페이지

Azure Security Center를 열 때 처음으로 표시되는 페이지는 개요 페이지입니다. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Security Center 개요 페이지":::

Security Center 개요 페이지를 사용하여 워크로드의 보안을 검색 및 평가하고, 위험을 식별하고 완화하세요.

개요 페이지는 하이브리드 클라우드 워크로드의 보안 상태에 대한 통합된 보기를 제공합니다. 또한 보안 경고, 적용 범위 정보 등을 보여 줍니다.


## <a name="features-of-the-overview-page"></a>개요 페이지의 기능

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Security Center 개요 페이지":::

**상단 메뉴 모음**은 다음을 제공합니다.
- **구독** - 이 단추를 선택하여 구독 목록을 보고 필터링할 수 있습니다. Security Center는 선택한 구독의 보안 상태를 반영하도록 디스플레이를 조정합니다.
- **새로운 기능** - 새로운 기능, 버그 수정 및 더 이상 사용되지 않는 기능에 대한 최신 정보를 습득할 수 있도록 [릴리스 정보](release-notes.md)를 엽니다.
- 연결된 클라우드 계정의 **대략적인 수치** - 아래의 기본 타일에 정보의 컨텍스트를 보여 줍니다. 활성 권장 사항 및 경고의 수도 보여 줍니다.
    [AWS 계정](quickstart-onboard-aws.md) 및 [GCP 프로젝트](quickstart-onboard-gcp.md)에 연결하는 방법에 대해 자세히 알아보세요.


페이지의 가운데에는 **4개의 중앙 타일**이 있으며, 각 타일은 각 항목에 대한 자세한 내용을 볼 수 있는 전용 대시보드로 연결됩니다.
- **보안 점수** - Security Center는 리소스, 구독 및 조직의 보안 이슈를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다. [자세히 알아봅니다](secure-score-security-controls.md).
- **규정 준수** - 대시보드에서 Security Center는 Azure 환경의 지속적인 평가에 기반하여 준수 상태에 대한 인사이트를 제공합니다. Security Center는 보안 모범 사례에 따라 하이브리드 클라우드 환경에서 위험 요소를 분석합니다. 이러한 평가는 지원되는 표준 세트에서 준수 제어에 매핑됩니다. [자세한 정보를 알아보세요](security-center-compliance-dashboard.md).
- **Azure Defender** - Security Center 내부에 통합된 Azure 및 하이브리드 워크로드에 대한 고급 인텔리전트 보호를 제공하는 CWPP(클라우드 워크로드 보호 플랫폼)입니다. 이 타일은 현재 선택된 구독의 연결된 리소스 적용 범위와 최근 경고를 심각도에 따라 색으로 구분하여 보여 줍니다. [자세히 알아봅니다](azure-defender.md).
- **인벤토리** - 이 타일은 모니터링되지 않는 VM 수와 Security Center에서 모니터링하는 리소스의 간단한 지표를 보여 줍니다. [자세히 알아봅니다](asset-inventory.md).


**Insights** 창에서는 사용자 환경에 맞게 사용자 지정된 다음과 같은 항목을 제공합니다.
- 가장 많이 공격받은 리소스
- 보안 점수를 늘릴 가능성이 가장 높은 보안 제어
- 영향을 받는 대부분의 리소스와 관련한 활성 권장 사항
- Azure Security Center 전문가의 최근 블로그 게시물

## <a name="next-steps"></a>다음 단계

이 페이지에서는 Security Center 개요 페이지를 소개했습니다. 관련 정보는 다음을 참조하세요.

- [자산 인벤토리 및 관리 도구를 사용하여 리소스 검색 및 관리](asset-inventory.md)
- [Azure Security Center의 보안 점수](secure-score-security-controls.md)