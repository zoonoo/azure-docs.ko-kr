---
title: Azure Security Center의 기본 대시보드 또는 ' 개요 ' 페이지
description: Security Center 개요 페이지의 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 637f7dde63d562f71aa31561a960ed3502a80229
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937172"
---
# <a name="azure-security-centers-overview-page"></a>Azure Security Center 개요 페이지

Azure Security Center 열 때 표시 되는 첫 번째 페이지는 개요 페이지입니다. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Security Center 개요 페이지":::

Security Center 개요 페이지를 사용 하 여 작업의 보안을 검색 및 평가 하 고 위험을 식별 하 고 완화 합니다.

개요는 하이브리드 클라우드 워크 로드의 보안 상태에 대 한 통합 된 보기를 제공 합니다. 또한 보안 경고, 검사 정보 등을 보여 줍니다.


## <a name="features-of-the-overview-page"></a>개요 페이지의 기능

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Security Center 개요 페이지의 위쪽 표시줄":::

**최상위 메뉴 모음은** 다음을 제공 합니다.
- **구독** -이 단추를 선택 하 여 구독 목록을 확인 하 고 필터링 할 수 있습니다. Security Center는 선택한 구독의 보안 상태를 반영하도록 디스플레이를 조정합니다.
- **새로운** 기능-새로운 기능, 버그 수정 및 더 이상 사용 되지 않는 기능으로 최신 상태를 유지할 수 있도록 [릴리스 정보](release-notes.md) 를 엽니다.
- 아래 주 타일에 정보의 컨텍스트를 표시 하는 연결 된 클라우드 계정의 **상위 수준 숫자** 입니다. 또한 활성 권장 사항 및 경고의 수를 제공 합니다.
    [AWS 계정](quickstart-onboard-aws.md) 및 [gcp 프로젝트](quickstart-onboard-gcp.md)를 연결 하는 방법에 대해 자세히 알아보세요.


페이지의 가운데에는 각각 전용 대시보드에 연결 되는 **네 개의 중앙 타일이**있습니다. 자세한 내용은 다음을 참조 하세요.
- 보안 **점수** -Security Center 보안 문제에 대 한 리소스, 구독 및 조직을 지속적으로 평가 합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다. [자세히 알아보기](secure-score-security-controls.md).
- **준수** -Security Center는 Azure 환경의 지속적인 평가를 기반으로 규정 준수 상태에 대 한 정보를 제공 합니다. Security Center는 보안 모범 사례에 따라 하이브리드 클라우드 환경에서 위험 요소를 분석합니다. 이러한 평가는 지원 되는 표준 집합의 규정 준수 제어에 매핑됩니다. [자세히 알아보세요](security-center-compliance-dashboard.md).
- **Azure Defender** -azure 및 하이브리드 워크 로드에 대 한 고급, 인텔리전트 보호를 위해 Security Center 내에 통합 된 cwpp (클라우드 워크 로드 보호 플랫폼)입니다. 타일에는 현재 선택 된 구독에 대 한 연결 된 리소스의 적용 범위와 심각도 별로 색으로 구분 된 최근 경고가 표시 됩니다. [자세히 알아보기](azure-defender.md).
- **인벤토리** -타일에는 모니터링 되지 않는 vm의 수와 Security center 이혜준에서 모니터링 하는 리소스의 간단한 barometer 표시 됩니다. [자세히 알아보기](asset-inventory.md).


**Insights** 창에서는 다음을 포함 하 여 사용자 지정 된 환경에 대 한 항목을 제공 합니다.
- 가장 공격을 받는 리소스
- 보안 점수를 늘릴 가능성이 가장 높은 보안 제어
- 영향을 가장 많은 리소스가 있는 활성 권장 사항
- Azure Security Center 전문가의 최근 블로그 게시물

## <a name="next-steps"></a>다음 단계

이 페이지에서는 Security Center 개요 페이지를 소개 했습니다. 관련 정보는 다음을 참조하세요.

- [Asset inventory 및 관리 도구를 사용 하 여 리소스 탐색 및 관리](asset-inventory.md)
- [Azure Security Center의 보안 점수](secure-score-security-controls.md)