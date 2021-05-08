---
title: Azure Monitor에서 Log Analytics 작업 영역의 상태 모니터링
description: Operation 테이블의 데이터를 사용하여 Log Analytics 작업 영역의 상태를 모니터링하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538842"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 작업 영역의 상태 모니터링
Azure Monitor에서 Log Analytics 작업 영역의 성능 및 가용성을 유지 관리하려면 발생하는 모든 문제를 사전에 검색할 수 있어야 합니다. 이 문서에서는 [Operation](/azure/azure-monitor/reference/tables/operation) 테이블의 데이터를 사용하여 Log Analytics 작업 영역의 상태를 모니터링하는 방법을 설명합니다. 이 테이블은 모든 Log Analytics 작업 영역에 포함되며 작업 영역에서 발생하는 오류 및 경고를 포함합니다. 이 데이터를 정기적으로 검토하고 작업 영역에 중요한 인시던트가 있을 때 사전에 알리도록 경고를 만들어야 합니다.

## <a name="_logoperation-function"></a>_LogOperation 함수

Azure Monitor 로그는 문제가 발생한 작업 영역의 [Operation](/azure/azure-monitor/reference/tables/operation) 테이블에 모든 문제에 관한 세부 정보를 보냅니다. **_LogOperation** 시스템 함수는 **Operation** 테이블을 기반으로 하며 분석 및 경고에 관한 간단한 정보 세트를 제공합니다.

## <a name="columns"></a>열

**_LogOperation** 함수는 다음 테이블의 열을 반환합니다.

| 열 | Description |
|:---|:---|
| TimeGenerated | 인시던트가 발생한 시간(UTC)입니다. |
| 범주  | 작업 범주 그룹입니다. 작업 유형을 필터링하는 데 사용되고 더 정확한 시스템 감사 및 경고를 만드는 데 도움이 될 수 있습니다. 범주 목록은 아래 섹션을 참조하세요. |
| 작업(Operation)  | 작업 유형의 설명입니다. 이는 Log Analytics 한도, 작업 유형 또는 프로세스의 일부 중 하나를 나타낼 수 있습니다. |
| Level | 문제의 심각도 수준입니다.<br>- 정보: 특정 주의가 필요하지 않습니다.<br>- 경고: 프로세스가 예상대로 완료되지 않았으므로 주의가 필요합니다.<br>- 오류: 프로세스가 실패하고 긴급한 주의가 필요합니다. 
| 세부 정보 | 작업의 자세한 설명에는 특정 오류 메시지가 포함됩니다(있는 경우). |
| _ResourceId | 작업과 관련된 Azure 리소스의 리소스 ID입니다.  |
| Computer | 작업이 Azure Monitor 에이전트와 관련된 경우 컴퓨터 이름입니다. |
| CorrelationId | 연속 관련 작업을 그룹화하는 데 사용됩니다. |


## <a name="categories"></a>범주

다음 표에서는 _LogOperation 함수의 범주를 설명합니다. 

| 범주 | Description |
|:---|:---|
| 수집           | 데이터 수집 프로세스의 일부인 작업입니다. 자세한 내용은 다음을 참조하세요. |
| 에이전트               | 에이전트 설치와 관련된 문제를 나타냅니다. |
| 데이터 수집     | 데이터 수집 프로세스와 관련된 작업입니다. |
| 솔루션 대상 지정  | *ConfigurationScope* 유형의 작업이 처리되었습니다. |
| 평가 솔루션 | 평가 프로세스가 실행되었습니다. |


### <a name="ingestion"></a>수집
수집 작업은 Azure Log Analytics 작업 영역 한도 도달에 관한 알림을 포함하여 데이터 수집 중에 발생한 문제입니다. 이 범주의 오류 조건은 데이터 손실을 나타낼 수 있으므로 모니터링하는 것이 특히 중요합니다. 다음 표에서는 이 작업을 자세히 설명합니다. Log Analytics 작업 영역의 서비스 한도는 [Azure Monitor 서비스 한도](../service-limits.md#log-analytics-workspaces)를 참조하세요.


| 작업(Operation) | Level | 세부 정보 | 관련 문서 |
|:---|:---|:---|:---|
| 사용자 지정 로그 | 오류   | 사용자 지정 필드 열 한도에 도달했습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 사용자 지정 로그 | 오류   | 사용자 지정 로그 수집에 실패했습니다. | |
| 메타데이터입니다. | 오류 | 구성 오류가 검색되었습니다. | |
| 데이터 수집 | 오류   | 요청이 설정된 기간(일)보다 이전에 만들어졌으므로 데이터가 삭제되었습니다. | [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](./manage-cost-storage.md#alert-when-daily-cap-reached)
| 데이터 수집 | 정보    | 수집 머신 구성이 검색되었습니다.| |
| 데이터 수집 | 정보    | 새 날짜로 인해 데이터 수집이 시작되었습니다. | [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| 데이터 수집 | 경고 | 일일 한도에 도달하여 데이터 수집이 중지되었습니다.| [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| 데이터 처리 | 오류   | JSON 형식이 잘못되었습니다. | [HTTP 데이터 수집기 API로 Azure Monitor에 로그 데이터 전송(공개 미리 보기)](../logs/data-collector-api.md#request-body) | 
| 데이터 처리 | 경고 | 값이 최대 허용 크기로 잘렸습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 데이터 처리 | 경고 | 크기 한도에 도달하여 필드 값이 잘립니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) | 
| 수집 속도 | 정보 | 수집 속도 한도가 70%에 근접합니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 수집 속도 | 경고 | 수집 속도 한도가 한도에 근접합니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 수집 속도 | 오류   | 속도 한도에 도달했습니다. | [Azure Monitor 서비스 제한](../service-limits.md#log-analytics-workspaces) |
| 스토리지 | 오류   | 사용된 자격 증명이 잘못되었으므로 스토리지 계정에 액세스할 수 없습니다.  |



   

## <a name="alert-rules"></a>경고 규칙
Azure Monitor의 [로그 쿼리 경고](../alerts/alerts-log-query.md)를 사용하여 Log Analytics 작업 영역에서 문제가 검색되는 경우 사전에 알림을 받습니다. 비용을 최소화하면서 문제에 시기 적절하게 대응할 수 있는 전략을 사용해야 합니다. 구독에는 평가 빈도에 따른 비용과 함께 각 경고 규칙 관련 요금이 청구됩니다.

권장되는 전략은 문제 수준에 따라 두 개의 경고 규칙을 시작하는 것입니다. 오류의 경우 5분마다 같은 짧은 빈도를 사용하고 경고의 경우 24시간 같은 더 긴 빈도를 사용합니다. 오류는 잠재적 데이터 손실을 나타내므로 신속하게 대응하여 손실을 최소화하려고 합니다. 경고는 일반적으로 즉각적인 주의가 필요하지 않은 문제를 나타내므로 매일 검토할 수 있습니다.

[Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../alerts/alerts-log.md)의 프로세스를 사용하여 로그 경고 규칙을 만듭니다. 다음 섹션에서는 각 규칙의 세부 정보를 설명합니다.


| 쿼리 | 임계값 | 기간 | 빈도 |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

이 경고 규칙은 오류 또는 경고가 있는 모든 작업에 동일하게 대응합니다. 경고를 생성하는 작업에 더 익숙해짐에 따라 특정 작업에 다르게 대응하려고 할 수 있습니다. 예를 들어, 특정 작업에 관한 알림을 다른 사용자에게 보내려고 할 수 있습니다. 

특정 작업의 경고 규칙을 만들려면 **범주** 및 **작업** 열을 포함하는 쿼리를 사용합니다. 

다음 예제에서는 수집 볼륨 비율이 한도의 80%에 도달했을 때 경고 알림을 만듭니다.

- 대상: Log Analytics 작업 영역 선택
- 조건:
  - 신호 이름: 사용자 지정 로그 검색
  - 검색 쿼리: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - 기준: 결과의 수
  - 조건: 초과
  - 임계값: 0
  - 기간: 5분
  - 빈도: 5분
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)


다음 예제에서는 데이터 수집이 일일 한도에 도달했을 때 경고 알림을 만듭니다. 

- 대상: Log Analytics 작업 영역 선택
- 조건:
  - 신호 이름: 사용자 지정 로그 검색
  - 검색 쿼리: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - 기준: 결과의 수
  - 조건: 초과
  - 임계값: 0
  - 기간: 5분
  - 빈도: 5분
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)



## <a name="next-steps"></a>다음 단계

- [로그 경고](../alerts/alerts-log.md)에 관해 자세히 알아봅니다.
- 작업 영역의 [쿼리 감사 데이터를 수집](./query-audit.md)합니다.
