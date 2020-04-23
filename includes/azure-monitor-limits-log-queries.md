---
title: 포함 파일
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072640"
---
### <a name="general-query-limits"></a>일반 쿼리 제한

| 제한 | 설명 |
|:---|:---|
| 쿼리 언어 | Azure 모니터는 Azure 데이터 탐색기와 동일한 [Kusto 쿼리 언어를](/azure/kusto/query/) 사용합니다. Azure Monitor에서 지원되지 않는 KQL 언어 요소에 대한 Azure [모니터 로그 쿼리](../articles/azure-monitor/log-query/data-explorer-difference.md) 언어 차이를 참조하십시오. |
| Azure 지역 | 데이터가 여러 Azure 리전에서 로그 분석 작업 영역에 걸쳐 있을 때 로그 쿼리는 과도한 오버헤드를 경험할 수 있습니다. 자세한 내용은 [쿼리 제한을](../articles/azure-monitor/log-query/scope.md#query-limits) 참조하십시오. |
| 리소스 쿼리 교차 | 단일 쿼리에서 최대 애플리케이션 인사이트 리소스 및 로그 분석 작업 영역의 수는 100개로 제한됩니다.<br>리소스 간 쿼리는 보기 디자이너에서 지원되지 않습니다.<br>로그 경고의 리소스 간 쿼리는 새 scheduledQueryRules API에서 지원됩니다.<br>자세한 내용은 [리소스 간 쿼리 제한을](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) 참조하십시오. |

### <a name="user-query-throttling"></a>사용자 쿼리 제한
Azure Monitor에는 과도한 쿼리를 보내는 사용자로부터 보호하기 위해 여러 가지 제한 제한이 있습니다. 이러한 동작은 잠재적으로 시스템 백 엔드 리소스를 과부하시키고 서비스 응답성을 위태롭게 할 수 있습니다. 다음 제한은 중단으로부터 고객을 보호하고 일관된 서비스 수준을 보장하도록 설계되었습니다. 사용자 제한 및 제한은 극단적인 사용 시나리오에만 영향을 주도록 설계되었으며 일반적인 사용과 관련이 없어야 합니다.


| 측정값 | 사용자당 제한 | 설명 |
|:---|:---|:---|
| 동시 쿼리 | 5 | 사용자에 대해 실행 중인 쿼리가 이미 5개 있는 경우 새 쿼리는 사용자별 동시성 큐에 배치됩니다. 실행 중인 쿼리 중 하나가 종료되면 다음 쿼리가 큐에서 가져와시작됩니다. 경고 규칙의 쿼리는 포함되지 않습니다.
| 동시성 큐의 시간 | 2.5분 | 쿼리가 시작되지 않고 2.5분 이상 큐에 있으면 코드 429를 사용 하 여 HTTP 오류 응답으로 종료 됩니다. |
| 동시성 큐의 총 쿼리 | 40 | 큐의 쿼리 수가 40개에 도달하면 HTTP 오류 코드 429로 추가 쿼리가 거부됩니다. 이 숫자는 동시에 실행할 수 있는 5개의 쿼리에 추가됩니다. |
| 쿼리 속도 | 30초당 200개의 쿼리 | 단일 사용자가 모든 작업 영역에 쿼리를 제출할 수 있는 전체 비율입니다.  이 제한은 Azure 대시보드 및 Log Analytics 작업 영역 요약 페이지와 같은 시각화 부분에서 시작된 프로그래밍 방식 쿼리 또는 쿼리에 적용됩니다. |

- [Azure Monitor의 로그 쿼리 최적화에](../articles/azure-monitor/log-query/query-optimization.md)설명된 대로 쿼리를 최적화합니다.
- 대시보드 및 통합 문서는 로드하거나 새로 고칠 때마다 쿼리 버스트를 생성하는 단일 보기에 여러 쿼리를 포함할 수 있습니다. 필요에 따라 로드되는 여러 뷰로 나누는 것이 좋습니다. 
- Power BI에서는 원시 로그가 아닌 집계된 결과만 추출하는 것이 좋습니다.