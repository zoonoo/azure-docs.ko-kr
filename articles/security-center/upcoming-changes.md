---
title: Azure Security Center에 예정된 중요한 변경
description: 알아야 하고 계획해야 할 수 있는 Azure Security Center에 예정된 변경입니다
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/01/2021
ms.author: memildin
ms.openlocfilehash: cff1215fadcd26f18b75c33bba6794c625729e5d
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113225591"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

| 계획된 변경                                                                                                                                                                                          | 변경 예상 날짜 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [20MB로 제한되는 CSV 내보내기](#csv-exports-to-be-limited-to-20-mb)                                                                                                                               | 2021년 7월                 |
| [ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)                                            | 2021년 7월                 |
| ['머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함' 권장 사항이 사용 중단됨](#deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines) | 2021년 7월                 |
| [SQL 데이터 분류 권장 사항 향상](#enhancements-to-sql-data-classification-recommendation)                                                                                       | 2021년 3분기                   |
| [보안 점수에 포함할 Azure Defender 보안 컨트롤을 사용하도록 설정](#enable-azure-defender-security-control-to-be-included-in-secure-score)                                                         | 2021년 3분기                   |
|                                                                                                                                                                                                         |                           |


### <a name="csv-exports-to-be-limited-to-20-mb"></a>20MB로 제한되는 CSV 내보내기

**변경 예상 날짜:** 2021년 7월

Security Center 권장 사항 데이터를 내보낼 때 현재 다운로드할 수 있는 데이터 양에 제한이 없습니다.

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="권장 사항 데이터를 내보내는 Security Center의 'CSV 보고서 다운로드' 단추입니다.":::

이 변경으로 인해 20MB로 제한됩니다.

더 많은 양의 데이터를 내보내야 하는 경우 선택하기 전에 사용 가능한 필터를 사용하거나 구독의 하위 집합을 선택하고 데이터를 일괄 처리로 다운로드합니다.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Azure Portal에서 구독 필터링.":::

[보안 권장 사항의 CSV 내보내기를 수행하는 방법](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations)에 대해 자세히 알아봅니다.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.

**변경 예상 날짜:** 2021년 7월

ISO 27001의 레거시 구현은 Security Center의 규정 준수 대시보드에서 제거됩니다. Security Center를 사용하여 ISO 27001 규정 준수를 추적하는 경우 모든 관련 관리 그룹 또는 구독에 대한 새 ISO 27001:2013 표준을 온보드하면 현재 레거시 ISO 27001이 대시보드에서 곧 제거됩니다.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001의 레거시 구현을 제거하는 방법에 대한 메시지를 보여주는 Security Center의 규정 준수 대시보드." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>'머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함' 권장 사항이 사용 중단됨

**변경 예상 날짜:** 2021년 7월

**머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함** 권장 사항이 Security Center의 CSPM(클라우드 보안 상태 관리) 포커스와 일치하지 않는 방식으로 보안 점수에 영향을 줍니다. 일반적으로 CSPM은 잘못된 보안 구성을 식별하는 것과 관련이 있습니다. 에이전트 상태 문제는 이 문제 범주에 맞지 않습니다.

또한 이 권장 사항은 Security Center와 관련된 다른 에이전트와 비교할 때 이례적입니다. 이는 상태 문제와 관련된 권장 사항이 있는 유일한 에이전트입니다.

이 권장 사항은 더 이상 사용되지 않습니다.

이 사용 중단으로 인해 Log Analytics 에이전트 설치 권장 사항도 약간 변경합니다(**Log Analytics 에이전트를 ...에 설치해야 함**).

이 변경 내용은 보안 점수에 영향을 줄 수도 있습니다. 대부분의 구독에서는 변경으로 인해 점수가 증가할 것으로 예상하지만, 설치 권장 사항을 업데이트하면 경우에 따라 점수가 저하될 수 있습니다.

> [!TIP]
> 또한 [자산 인벤토리](asset-inventory.md) 페이지는 머신이 모니터링되는지, 모니터링되지 않거나 부분적으로 모니터링되는지(상태 문제가 있는 에이전트를 나타내는 상태)에 대한 정보도 표시하기 때문에 이 변경의 영향을 받습니다. 

### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 데이터 분류 권장 사항 향상

**변경 예상 날짜:** Q3 2021

**데이터 분류 적용** 보안 제어에서 **SQL 데이터베이스의 중요 데이터를 분류해야 함** 권장 사항은 Microsoft의 데이터 분류 전략에 맞춰 향상된 새 버전으로 대체됩니다. 그 결과 권장 사항의 ID도 변경됩니다(현재 b0df6f56-862d-4730-8597-38c0fd4ebd59임).

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>보안 점수에 포함할 Azure Defender 보안 컨트롤을 사용하도록 설정

**변경 예상 날짜:** Q3 2021

Security Center의 보안 강화 권장 사항은 보안 컨트롤로 그룹화되어 있습니다. 각 컨트롤은 관련된 보안 권장 사항의 논리적 그룹으로, 취약한 공격 노출 영역을 반영합니다. 전반적인 보안 점수에 대한 각 보안 컨트롤의 기여도는 권장 사항 페이지와 [보안 컨트롤 및 해당 권장 사항](secure-score-security-controls.md#security-controls-and-their-recommendations)의 컨트롤 목록에 명확하게 나와 있습니다.

도입된 이후에 **Azure Defender 사용** 컨트롤의 가능한 최대 점수는 0점이었습니다. **이 변경이 수행되면 해당 컨트롤은 보안 점수에 기여하게 될 것입니다**.

Azure Defender를 사용하도록 설정하면 Security Center의 무료 모드 기능이 프라이빗 클라우드 및 기타 퍼블릭 클라우드에서 실행되는 작업으로 확장 적용되며, 하이브리드 클라우드 작업 전반에 걸쳐 통합 보안 관리 및 위협 방지 기능이 제공됩니다. Azure Defender의 주요 기능에는 서버에 대한 통합된 엔드포인트용 Microsoft Defender, 가상 머신 및 컨테이너 레지스트리의 취약성 검색, 고급 동작 분석 및 기계 학습을 기준으로 하는 보안 경고, 컨테이너 보안 기능이 포함됩니다. 전체 목록을 보려면 [Azure Security Center 무료 및 Azure Defender 사용 비교](security-center-pricing.md)를 참조하세요.

이 변경이 수행되면 Azure Defender로 보호되지 않는 구독의 보안 점수에 영향을 줍니다. 이러한 변경이 발생하기 전에 Azure Defender를 사용하도록 설정하여 점수에 영향을 주지 않도록 하는 것이 좋습니다. 

[빠른 시작: Azure Defender 사용](enable-azure-defender.md)에서 자세히 알아보세요.


## <a name="next-steps"></a>다음 단계

제품에 대한 모든 최근 변경 내용은 [Azure Security Center의 새로운 기능](release-notes.md)을 참조하세요.