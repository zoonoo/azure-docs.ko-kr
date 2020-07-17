---
title: 파일 포함
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82072640"
---
### <a name="general-query-limits"></a>일반 쿼리 제한

| 제한 | Description |
|:---|:---|
| 쿼리 언어 | Azure Monitor는 Azure 데이터 탐색기와 동일한 [Kusto 쿼리 언어](/azure/kusto/query/) 를 사용 합니다. Azure Monitor에서 지원 되지 않는 KQL 언어 요소에 대 한 [Azure Monitor 로그 쿼리 언어 차이점](../articles/azure-monitor/log-query/data-explorer-difference.md) 을 참조 하세요. |
| Azure 지역 | 로그가 여러 Azure 지역에서 Log Analytics 작업 영역에 걸쳐 있을 때 과도 한 오버 헤드가 발생할 수 있습니다. 자세한 내용은 [쿼리 제한](../articles/azure-monitor/log-query/scope.md#query-limits) 을 참조 하세요. |
| 리소스 간 쿼리 | 100으로 제한 된 단일 쿼리에서 Application Insights 리소스 및 Log Analytics 작업 영역의 최대 수입니다.<br>뷰 디자이너에서는 리소스 간 쿼리가 지원 되지 않습니다.<br>로그 경고의 리소스 간 쿼리는 새 scheduledQueryRules API에서 지원됩니다.<br>자세한 내용은 [리소스 간 쿼리 제한](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) 을 참조 하세요. |

### <a name="user-query-throttling"></a>사용자 쿼리 제한
Azure Monitor에는 과도 한 쿼리를 보내는 사용자 로부터 보호 하기 위한 몇 가지 제한 제한이 있습니다. 이러한 동작은 잠재적으로 시스템 백 엔드 리소스를 오버 로드 하 고 서비스 응답성을 저하 시킬 수 있습니다. 다음 제한은 고객이 중단 으로부터 보호 하 고 일관 된 서비스 수준을 보장 하도록 설계 되었습니다. 사용자 제한 및 제한은 극단적인 사용 시나리오에만 영향을 주기 위해 설계 되었으며 일반적인 사용량과는 관련이 없습니다.


| 측정값 | 사용자 당 제한 | Description |
|:---|:---|:---|
| 동시 쿼리 | 5 | 사용자에 대해 실행 중인 쿼리가 5 개 있는 경우 모든 새 쿼리가 사용자 당 동시성 큐에 배치 됩니다. 실행 중인 쿼리 중 하나가 종료 되 면 다음 쿼리가 큐에서 끌어오고 시작 됩니다. 경고 규칙의 쿼리는 포함 되지 않습니다.
| 동시성 큐의 시간 | 2.5 분 | 쿼리가 2.5 분 이상 시작 하지 않고 큐에 배치 되는 경우 코드 429와 함께 HTTP 오류 응답으로 종료 됩니다. |
| 동시성 큐의 총 쿼리 | 40 | 큐에 있는 쿼리 수가 40에 도달 하면 추가 쿼리는 HTTP 오류 코드 429로 거부 됩니다. 이 수는 동시에 실행 될 수 있는 5 개의 쿼리에 추가 됩니다. |
| 쿼리 속도 | 30 초 당 200 쿼리 | 이는 단일 사용자가 모든 작업 영역에 쿼리를 제출할 수 있는 전체 요금입니다.  이 제한은 Azure 대시보드 및 Log Analytics 작업 영역 요약 페이지와 같은 시각화 요소에 의해 시작 된 쿼리 또는 프로그래밍 쿼리에 적용 됩니다. |

- [Azure Monitor에서 로그 쿼리 최적화](../articles/azure-monitor/log-query/query-optimization.md)에 설명 된 대로 쿼리를 최적화 합니다.
- 대시보드 및 통합 문서는 로드 하거나 새로 고칠 때마다 쿼리 버스트를 생성 하는 여러 쿼리를 단일 뷰에 포함할 수 있습니다. 요청 시 로드 되는 여러 뷰로 분리 하는 것이 좋습니다. 
- Power BI 원시 로그가 아닌 집계 된 결과만 추출 하는 것이 좋습니다.