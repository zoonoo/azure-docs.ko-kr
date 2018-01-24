---
title: "Azure Monitor에서 새로운 경고(미리 보기) 환경 탐색 | Microsoft Docs"
description: "Azure에서 새로운 단순하고 확장 가능한 경고 환경으로 경고 작성, 보기 및 관리를 더 쉽게 만드는 방법 이해"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 5ded43548d9aea106c6e083476df4e735b8c00a6
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2017
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Azure Monitor에서 새로운 경고(미리 보기) 환경 탐색

## <a name="overview"></a>개요
 Azure에서 경고 환경은 새로운 모양을 가지며 기능을 업데이트했습니다. 이 새로운 환경은 Azure Monitor의 **경고(미리 보기)** 탭에서 사용할 수 있습니다. 다음은 새 경고(미리 보기) 환경 사용의 장점 중 일부입니다.

 - **발생한 경고 및 경고 규칙의 분리** - 경고(미리 보기) 환경에서 경고 규칙(경고를 트리거하는 조건의 정의)과 발생한 경고(발생하는 경고 규칙의 인스턴스)는 구별되므로 작동 및 구성 보기는 구분됩니다. 
 - **메트릭 및 로그 경고에 대한 통합된 제작 환경** - 새로운 경고(미리 보기) 제작 환경은 알림을 받는 적합한 대상을 더 간단하게 검색하도록 하는 경고 규칙 구성의 프로세스를 따라 사용자를 안내합니다. 
 - **Azure Portal에서 발생한 Log Analytics 경고 보기** - 경고(미리 보기) 환경에서 이제 구독에서 발생한 Log Analytics 경고를 볼 수도 있습니다.  

다음 섹션에서는 새로운 환경이 작동하는 방법을 더 자세히 설명합니다. 

## <a name="taxonomy"></a>분류
경고(미리 보기) 환경은 서로 다른 경고 유형에서 작성 환경을 통합하는 동안 경고 규칙 및 발생한 경고 개체를 구분하는 데 다음 개념을 사용합니다.

- **대상 리소스** - 대상은 Azure 리소스일 수 있습니다. 대상 리소스는 경고에 사용할 수 있는 범위 및 신호를 정의합니다. 예제 대상: 가상 머신, 저장소 계정, 가상 머신 확장 집합, Log Analytics 작업 영역 또는 솔루션 

- **조건** - 조건은 대상 리소스에 적용된 신호와 논리의 조합입니다. 예: CPU의 백분율 > 70%, 서버 응답 시간 > 4ms, 로그 쿼리의 결과 개수 > 100 등 

- **신호** - 신호는 대상 리소스에 의해 발생되며 여러 유형일 수 있습니다. 이 미리 보기는 메트릭 및 로그 신호 형식을 지원합니다.

- **논리** - 신호가 예상된 범위/값 내에 있는지 확인하는 사용자 정의 논리입니다.  
 
- **작업** - 알림 수신자에게 보내는 특정 호출(예: 주소로 이메일 보내기 또는 웹후크 URL에 게시). 알림은 보통 여러 작업을 트리거할 수 있습니다. 이 미리 보기에서 지원되는 경고 유형은 작업 그룹을 지원합니다.  
 
- **경고 규칙** - 경고를 트리거하는 조건의 정의입니다. 이 미리 보기에서 경고 규칙은 경고에 대한 대상 및 조건을 캡처합니다. 경고 규칙은 사용 또는 사용 안 함 상태에 있을 수 있습니다. 
 
- **발생한 경고** - 활성화된 경고 규칙이 발생할 때 생성되었습니다. 발생된 경고 개체는 발생된 또는 해결되지 않은 상태에 있을 수 있습니다.

    > [!NOTE]
    > 경고가 규칙 및 발생한 경고 모두를 나타내는 현재 경고 환경과 다르므로 경고, 활성 또는 사용 안 함 상태 중 하나일 수 있습니다.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>경고를 보고 관리하는 단일 위치
경고(미리 보기) 환경의 목표는 모든 Azure 경고를 보고 관리하는 단일 위치가 되는 것입니다. 다음 하위 섹션은 새 환경의 각 개별 화면의 기능을 설명합니다.

### <a name="alerts-preview-overview-page"></a>경고(미리 보기) 개요 페이지
**모니터 - 경고(미리 보기)** 개요 페이지는 발생한 모든 경고의 집계 요약 및 총 구성/설정 경고 규칙을 표시합니다. 또한 발생한 모든 경고의 목록을 보여 줍니다. 구독 또는 필터 매개 변수를 변경하면 집계 및 발생한 경고 목록을 업데이트합니다.

> [!NOTE]
> 경고(미리 보기)에 표시된 발생한 경고는 지원되는 메트릭 및 로그 경고로 제한되며 Azure Monitor는 이전 Azure Alerts의 것을 포함하여 발생하는 경고 수를 보여 줍니다.

 ![alerts-preview-overview](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>경고 규칙 관리
**모니터 - 경고(미리 보기)>규칙**은 Azure 구독에서 모든 경고 규칙을 관리하는 단일 페이지입니다. 모든 경고 규칙(활성화 또는 비활성화)을 나열하고 대상 리소스, 리소스 그룹, 규칙 이름 또는 상태에 따라 정렬될 수 있습니다. 또한 경고 규칙을 비활성화/활성화하거나 이 페이지에서 편집할 수 있습니다.  

 ![alerts-preview-rules](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>모든 모니터링 소스에서 하나의 경고 제작 환경
경고(미리 보기) 환경에서 경고는 모니터링 서비스 또는 신호 형식에 관계 없이 일관된 방식으로 작성될 수 있습니다. 발생한 모든 경고 및 관련된 자세한 정보는 단일 페이지에서 찾을 수 있습니다.  
 
경고 작성은 3단계 작업입니다. 사용자는 먼저 경고에 대산 대상을 선택하고 올바른 신호를 선택한 다음 경고 규칙의 일부로 신호에 적용할 논리를 지정합니다. 이 간소화된 제작 프로세스로 사용자는 Azure 리소스를 선택하기 전에 모니터링 원본 또는 지원되는 신호를 더 이상 알 필요가 없습니다. 일반적인 제작 환경은 선택한 대상 리소스에 따라 사용 가능한 신호의 목록을 자동으로 필터링하고 경고 논리의 생성을 안내합니다.

[여기](monitor-alerts-unified-usage.md)에서 다음 경고 유형을 만드는 방법에 대해 자세히 알아볼 수 있습니다. 
- 메트릭 경고(현재 환경에서 근 실시간 메트릭 경고라고 함)
- 로그 경고(Log Analytics)
 

## <a name="alert-types-supported-in-this-preview"></a>이 미리 보기에서 지원되는 경고 유형


| **신호 유형** | **모니터 원본** | **설명** | 
|-------------|----------------|-------------|
| 메트릭 | Azure Monitor | 현재 환경에서 [**근 실시간 메트릭 경고**](monitoring-near-real-time-metric-alerts.md)라고 하는 이러한 메트릭 경고는 1분의 주기로 메트릭 조건 평가를 지원하고 다중 메트릭 규칙을 허용합니다. 지원되는 리소스 종류의 목록은 [여기](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for)에서 사용 가능합니다. [여기](monitoring-overview-alerts.md#alerts-in-different-azure-services)에서 정의된 대로 다른 메트릭 경고는 경고(미리 보기) 환경에서 지원되지 않습니다.|
| 로그  | Log Analytics | 메트릭 및/또는 이벤트 데이터에 대한 로그 검색 쿼리가 특정 기준에 부합하면 알림을 받거나 자동화된 작업을 실행합니다.|
| 로그  | 활동 로그 | 경고(미리 보기) 환경에서 지원되지 않습니다. |
| 로그  | Application Insights | 경고(미리 보기) 환경에서 지원되지 않습니다. |
| 메트릭 | Application Insights | 경고(미리 보기) 환경에서 지원되지 않습니다. |
| 가용성 테스트 | Application Insights | 경고(미리 보기) 환경에서 지원되지 않습니다. |


## <a name="next-steps"></a>다음 단계
- [새 경고(미리 보기) 환경을 사용하여 경고를 만들고, 보고, 관리하는 방법 알아보기](monitor-alerts-unified-usage.md)
- [경고(미리 보기) 환경에서 로그 경고에 대해 알아보기](monitor-alerts-unified-log.md)
- [경고(미리 보기) 환경에서 메트릭 경고에 대해 알아보기](monitoring-near-real-time-metric-alerts.md)

