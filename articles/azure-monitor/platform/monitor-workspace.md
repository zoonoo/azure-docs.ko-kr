---
title: Azure Monitor에서 Log Analytics 작업 영역의 상태를 모니터링 합니다.
description: 작업 테이블의 데이터를 사용 하 여 Log Analytics 작업 영역의 상태를 모니터링 하는 방법을 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: a4f578ca2e9fc448fb85b803cce46974a8c2e4dc
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326056"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 작업 영역의 상태를 모니터링 합니다.
Azure Monitor에서 Log Analytics 작업 영역의 성능 및 가용성을 유지 하려면 발생 하는 모든 문제를 사전에 검색할 수 있어야 합니다. 이 문서에서는 [작업](/azure-monitor/reference/tables/operation) 테이블의 데이터를 사용 하 여 Log Analytics 작업 영역의 상태를 모니터링 하는 방법을 설명 합니다. 이 테이블은 모든 Log Analytics 작업 영역에 포함 되어 있으며 작업 영역에서 발생 하는 오류 및 경고를 포함 합니다. 이 데이터를 정기적으로 검토 하 고 작업 영역에 중요 한 인시던트가 있을 때 사전에 알리도록 경고를 만들어야 합니다.

## <a name="_logsoperation-function"></a>_LogsOperation 함수
Azure Monitor 로그는 문제가 발생 한 작업 영역의 [작업](/azure-monitor/reference/tables/operation) 테이블에 모든 문제에 대 한 세부 정보를 보냅니다. **_LogsOperation** 시스템 함수는 **작업** 테이블을 기반으로 하며 분석 및 경고에 대 한 간단한 정보 집합을 제공 합니다.

## <a name="columns"></a>열

**_LogsOperation** 함수는 다음 표의 열을 반환 합니다.

| 열 | Description |
|:---|:---|
| TimeGenerated | 인시던트가 발생 한 시간 (UTC)입니다. |
| 범주  | 작업 범주 그룹입니다. 을 사용 하 여 작업 유형을 필터링 하 고 보다 정확한 시스템 감사 및 경고를 만들 수 있습니다. 범주 목록은 아래 섹션을 참조 하세요. |
| 작업(Operation)  | 작업 유형에 대 한 설명입니다. 이는 Log Analytics 제한, 작업 유형 또는 프로세스의 일부 중 하나를 나타낼 수 있습니다. |
| Level | 문제의 심각도입니다.<br>-Info: 특정 주의가 필요 하지 않습니다.<br>-경고: 프로세스가 예상 대로 완료 되지 않았으므로 주의가 필요 합니다.<br>-오류: 프로세스가 실패 하 고 긴급 주의가 필요 합니다. 
| 세부 정보 | 작업에 대 한 자세한 설명에는 특정 오류 메시지 (있는 경우)가 포함 됩니다. |
| _ResourceId | 작업과 관련 된 Azure 리소스의 리소스 ID입니다.  |
| Computer | 작업이 Azure Monitor 에이전트와 관련 된 경우 컴퓨터 이름입니다. |
| CorrelationId | 연속 관련 작업을 그룹화 하는 데 사용 됩니다. |


## <a name="categories"></a>범주
다음 표에서는 _LogsOperations 함수의 범주에 대해 설명 합니다. 

| 범주 | 설명 |
|:---|:---|
| 수집           | 데이터 수집 프로세스의 일부인 작업입니다. 자세한 내용은 다음을 참조하세요. |
| 에이전트               | 에이전트 설치에 문제가 있음을 나타냅니다. |
| 데이터 수집     | 데이터 컬렉션 프로세스와 관련 된 작업입니다. |
| 솔루션 대상 지정  | *Configurationscope* 형식의 작업이 처리 되었습니다. |
| 평가 솔루션 | 평가 프로세스가 실행 되었습니다. |


### <a name="ingestion"></a>수집
수집 작업은 Azure Log Analytics 작업 영역 제한에 도달 하는 것에 대 한 알림을 포함 하 여 데이터 수집 중에 발생 한 문제입니다. 이 범주의 오류 조건으로 인해 데이터 손실이 제안 될 수 있으므로 모니터링 하는 것이 특히 중요 합니다. 다음 표에서는 이러한 작업에 대해 자세히 설명 합니다. Log Analytics 작업 영역에 대 한 서비스 제한 [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) 을 참조 하세요.


| 작업(Operation) | Level | 세부 정보 | 관련 문서 |
|:---|:---|:---|:---|
| 사용자 지정 로그 | 오류   | 사용자 지정 필드 열 제한에 도달 했습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 사용자 지정 로그 | 오류   | 사용자 지정 로그를 수집 하지 못했습니다. | |
| 사용자 지정 로그 | 오류   | 메타데이터입니다. | |
| 데이터 | 오류   | 요청이 설정 된 기간 (일) 보다 이전에 만들어졌으므로 데이터가 삭제 되었습니다. | [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](manage-cost-storage.md#alert-when-daily-cap-reached)
| 데이터 수집 | 정보    | 수집 컴퓨터 구성이 검색 되었습니다.| |
| 데이터 수집 | 정보    | 새 날로 인해 데이터 수집이 시작 되었습니다. | [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](/manage-cost-storage.md#alert-when-daily-cap-reached) |
| 데이터 수집 | 경고 | 일일 한도에 도달 하 여 데이터 수집이 중지 되었습니다.| [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](/manage-cost-storage.md#alert-when-daily-cap-reached) |
| 수집 율 | 정보 | 수집 비율 제한이 70%에 근접 합니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 수집 율 | 경고 | 수집 속도로 제한에 근접 하 고 있습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 수집 율 | 오류   | 속도로 제한에 도달 했습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| JSON 구문 분석 | 오류   | JSON 형식이 잘못 되었습니다. | [HTTP 데이터 수집기 API로 Azure Monitor에 로그 데이터 전송(공개 미리 보기)](data-collector-api.md#request-body) | 
| JSON 구문 분석 | 경고 | 값이 최대 허용 크기로 잘렸습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 최대 열 크기 제한 | 경고 | 크기 제한에 도달 하 여 필드 값이 잘립니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) | 
| 스토리지 | 오류   | 사용 된 자격 증명이 잘못 되었으므로 저장소 계정에 액세스할 수 없습니다.  |
| 테이블   | 오류   | 최대 사용자 지정 필드 제한에 도달 했습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces)|


   

## <a name="alert-rules"></a>경고 규칙
Azure Monitor의 [로그 쿼리 경고](../platform/alerts-log-query.md) 를 사용 하 여 Log Analytics 작업 영역에서 문제가 검색 되는 경우 사전에 알림을 받을 수 있습니다. 비용을 최소화 하면서 문제에 대 한 시기 적절 한 방식으로 응답할 수 있는 전략을 사용 해야 합니다. 평가 빈도에 따라 비용이 포함 된 각 경고 규칙에 대 한 구독 요금이 청구 됩니다.

권장 되는 전략은 문제 수준에 따라 두 개의 경고 규칙으로 시작 하는 것입니다. 오류에 대해 5 분 마다 짧은 빈도를 사용 하 고 경고의 경우 24 시간 등의 더 긴 빈도를 사용 합니다. 오류는 잠재적 데이터 손실을 나타내므로 신속 하 게 응답 하 여 손실을 최소화 하려고 합니다. 경고는 일반적으로 즉각적인 주의가 필요 하지 않은 문제를 나타내므로 매일 검토할 수 있습니다.

로그 경고 규칙을 만들려면 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](../platform/alerts-log.md) 에서 프로세스를 사용 합니다. 다음 섹션에서는 각 규칙에 대 한 세부 정보를 설명 합니다.


| 쿼리 | 임계값 | 기간 | 빈도 |
|:---|:---|:---|:---|
| `_LogsOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogsOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

이러한 경고 규칙은 오류 또는 경고가 발생 한 모든 작업과 동일 하 게 응답 합니다. 경고를 생성 하는 작업에 더 익숙해지면 특정 작업에 대 한 응답을 다르게 할 수 있습니다. 예를 들어 특정 작업에 대 한 알림을 다른 사용자에 게 보낼 수 있습니다. 

특정 작업에 대 한 경고 규칙을 만들려면 **범주** 및 **작업** 열을 포함 하는 쿼리를 사용 합니다. 

다음 예에서는 수집 볼륨의 비율이 제한의 80%에 도달 했을 때 경고를 생성 합니다.

```kusto
_LogsOperation
| where Category == "Ingestion"
| where Operation == "Ingestion rate"
| where Level == "Warning"
```

다음 예에서는 데이터 컬렉션이 일일 한도에 도달 했을 때 경고를 생성 합니다. 
```kusto
Operation 
| where OperationCategory == "Ingestion" 
|where OperationKey == "Data Collection" 
| where OperationStatus == "Warning"
```




## <a name="next-steps"></a>다음 단계

- [로그 경고](alerts-log.md)에 대해 자세히 알아보세요.
- 작업 영역에 대 한 [쿼리 감사 데이터를 수집](../log-query/query-audit.md) 합니다.