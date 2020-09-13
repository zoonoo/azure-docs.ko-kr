---
title: Azure Monitor의 로그 | Microsoft Docs
description: 모니터링 데이터에 대 한 고급 분석에 사용 되는 Azure Monitor의 로그에 대해 설명 합니다.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032781"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor 로그 개요
Azure Monitor 로그는 다양 한 원본의 로그 데이터를 수집 및 구성 하 고 정교한 쿼리 언어를 사용 하 여 분석에 사용할 수 있도록 하는 Azure Monitor의 기능입니다. 서로 다른 원본의 데이터를 단일 작업 영역으로 통합 하 고 함께 분석 하 여 이러한 작업과 추세 분석, 경고 및 시각화를 수행할 수 있습니다.

## <a name="relationship-to-azure-monitor-metrics"></a>Azure Monitor 메트릭에 대 한 관계
Azure Monitor 메트릭은 시간 계열 데이터베이스에 숫자 데이터를 저장 합니다 .이를 통해이 데이터를 Azure Monitor 로그 보다 더 간단 하 게 만들고 거의 실시간 시나리오를 지원할 수 있으므로 특히 문제에 대 한 경고 및 빠른 검색에 유용 합니다. 그러나 메트릭은 특정 구조에 숫자 데이터만 저장할 수 있는 반면, 로그는 고유 구조를 사용 하 여 다양 한 데이터 형식을 저장할 수 있습니다. 또한 메트릭 데이터의 분석에 사용할 수 없는 로그 쿼리를 사용 하 여 로그 데이터에 대 한 복잡 한 분석을 수행할 수 있습니다.

숫자 데이터는 메트릭 외에도 데이터 원본에서 로그로 전송 되는 경우가 많습니다. 이 데이터를 로그에 수집 하 고 유지 하는 추가 요금은 있지만 로그 쿼리에 메트릭 데이터를 포함 하 고 다른 모니터링 데이터를 사용 하 여 분석할 수 있습니다.

## <a name="relationship-to-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 관계
Azure Monitor 로그는 Azure 데이터 탐색기를 기반으로 합니다. Log Analytics 작업 영역은 대략적으로 Azure 데이터 탐색기의 데이터베이스와 동일 하며, 테이블은 동일 하 게 구성 되며 둘 다 동일한 Kusto 쿼리 언어 (KQL)를 사용 합니다. Log Analytics를 사용 하 여 Azure Portal의 Azure Monitor 쿼리로 작업 하는 환경은 Azure 데이터 탐색기 웹 UI를 사용 하는 환경과 비슷합니다. [Log Analytics 작업 영역의 데이터를 Azure 데이터 탐색기 쿼리에 포함할](/azure/data-explorer/query-monitor-data)수도 있습니다. 


## <a name="structure-of-data"></a>데이터 구조
Azure Monitor 로그에 의해 수집 된 데이터는 각각 특정 원본의 데이터를 저장 하는 여러 테이블이 포함 된 [Log Analytics 작업 영역](./design-logs-deployment.md) 에 저장 됩니다. 작업 영역은 데이터의 지리적 위치, 데이터에 액세스할 수 있는 사용자를 정의 하는 액세스 권한, 가격 책정 계층 및 데이터 보존 등의 구성 설정을 정의 합니다. 모든 모니터링 데이터에 단일 작업 영역을 사용 하거나 요구 사항에 따라 여러 작업 영역을 만들 수 있습니다. 여러 작업 영역을 만들기 위한 고려 사항은 [Azure Monitor 로그 배포 디자인](design-logs-deployment.md) 을 참조 하세요.

각 작업 영역에는 여러 행의 데이터를 포함 하는 별도의 열로 구성 된 여러 테이블이 포함 되어 있습니다. 각 테이블은 데이터 원본에서 제공 하는 데이터의 행에서 공유 하는 고유한 열 집합으로 정의 됩니다. 

[![Azure Monitor 로그 구조](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights의 로그 데이터는 Azure Monitor 로그에도 저장 되지만 응용 프로그램이 구성 된 방식에 따라 다르게 저장 됩니다. 작업 영역 기반 응용 프로그램의 경우 데이터는 응용 프로그램 요청, 예외 및 페이지 보기와 같은 데이터를 저장 하는 표준 테이블 집합의 Log Analytics 작업 영역에 저장 됩니다. 여러 응용 프로그램에서 동일한 작업 영역을 사용할 수 있습니다. 클래식 응용 프로그램의 경우 데이터는 Log Analytics 작업 영역에 저장 되지 않습니다. 동일한 쿼리 언어를 사용 하 고 Azure Portal에서 동일한 Log Analytics 도구를 사용 하 여 쿼리를 만들고 실행 합니다. 그러나 클래식 응용 프로그램의 데이터는 서로 별도로 저장 됩니다. 테이블 이름과 열 이름이 서로 다르지만 일반적인 구조는 작업 영역 기반 응용 프로그램과 동일 합니다. 두 작업 영역에 대 한 자세한 비교는 [작업 영역 기반 리소스 변경 내용](../app/apm-tables.md) 을 참조 하세요.


> [!NOTE]
> Application Insights 환경 내에서 Application Insights 클래식 리소스 쿼리, 통합 문서 및 로그 기반 경고에 대해 계속 이전 버전과 완전히 호환됩니다. [새 작업 영역 기반 테이블 구조/스키마](../app/apm-tables.md)를 쿼리/확인하려면 먼저 Log Analytics 작업 영역으로 이동해야 합니다. 미리 보기 중에 Application Insights 창 내에서 **로그**를 선택하면 클래식 Application Insights 쿼리 환경에 액세스할 수 있습니다. 자세한 내용은 [쿼리 범위](../log-query/scope.md) 를 참조 하세요.


[![Application Insights에 대 한 Azure Monitor 로그 구조](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>로그 쿼리
데이터를 처리 하 고 결과를 반환 하는 읽기 전용 요청인 [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 Log Analytics 작업 영역에서 데이터를 검색 합니다. 로그 쿼리는 Azure 데이터 탐색기에서 사용 하는 쿼리 언어인 [KQL (Kusto Query language)](/azure/data-explorer/kusto/query/)로 작성 됩니다. 로그 쿼리를 편집 및 실행 하 고 결과를 대화형으로 분석 하는 Azure Portal 도구인 Log Analytics를 사용 합니다. 그런 다음 사용자가 만든 쿼리를 사용 하 여 쿼리 경고 및 통합 문서 로그와 같은 Azure Monitor의 다른 기능을 지원할 수 있습니다.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Azure Monitor 로그에 대 한 데이터 원본
Azure Monitor는 Azure Monitor 리소스 및 가상 컴퓨터에서 실행 되는 에이전트를 포함 하 여 다양 한 원본에서 로그 데이터를 수집 합니다. Log Analytics 작업 영역으로 데이터를 전송 하는 데이터 원본의 전체 목록은 [Azure Monitor에서 모니터링 되는 내용](../monitor-reference.md) 을 참조 하세요.



## <a name="next-steps"></a>다음 단계

- Log Analytics 작업 영역에서 데이터를 검색 하 고 분석 하는 [로그 쿼리에](../log-query/log-query-overview.md) 대해 알아봅니다.
- [Azure Monitor의 메트릭](data-platform-metrics.md)을 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.

