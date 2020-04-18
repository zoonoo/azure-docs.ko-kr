---
title: VM 시작/중지 솔루션에서 로그를 쿼리하는 방법
description: 이 문서에서는 Azure Monitor에서 시작/중지 VM 솔루션에서 생성된 로그 데이터를 쿼리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472f3762ca18f71ba95053576daf025d8477fee9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604732"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>VM 시작/중지 솔루션에서 로그를 쿼리하는 방법

Azure Automation은 연결된 로그 분석 작업 영역인 작업 로그 및 작업 스트림의 두 가지 유형의 레코드를 전달합니다. 이 데이터는 Azure 모니터에서 [쿼리에](../azure-monitor/log-query/log-query-overview.md) 사용할 수 있습니다.

## <a name="job-logs"></a>작업 로그

|속성 | Description|
|----------|----------|
|Caller |  작업을 시작한 사람입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
|범주 | 데이터 유형의 분류입니다. Automation의 경우 값은 JobLogs입니다.|
|CorrelationId | Runbook 작업의 상관 관계 ID인 GUID입니다.|
|JobId | Runbook 작업의 ID인 GUID입니다.|
|operationName | Azure에서 수행되는 작업 유형을 지정합니다. Automation의 경우 이 값은 Job입니다.|
|resourceId | Azure의 리소스 유형을 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
|ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다. Automation에 대한 값은 Azure Automation입니다.|
|ResourceType | Azure의 리소스 유형을 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|resultType | runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- Succeeded입니다.|
|resultDescription | runbook 작업 결과 상태를 설명합니다. 가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다.|
|RunbookName | Runbook의 이름을 지정합니다.|
|SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다. Automation의 경우 값은 OpsManager입니다.|
|StreamType | 이벤트의 유형을 지정합니다. 가능한 값은 다음과 같습니다.<br>- Verbose입니다.<br>- 출력<br>- 오류<br>- 경고|
|SubscriptionId | 작업의 구독 ID를 지정합니다.
|Time | runbook 작업이 실행된 날짜 및 시간입니다.|

## <a name="job-streams"></a>작업 스트림

|속성 | Description|
|----------|----------|
|Caller |  작업을 시작한 사람입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
|범주 | 데이터 유형의 분류입니다. Automation의 경우 값은 JobStreams입니다.|
|JobId | Runbook 작업의 ID인 GUID입니다.|
|operationName | Azure에서 수행되는 작업 유형을 지정합니다. Automation의 경우 이 값은 Job입니다.|
|ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
|resourceId | Azure의 리소스 ID를 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다. Automation에 대한 값은 Azure Automation입니다.|
|ResourceType | Azure의 리소스 유형을 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|resultType | 이벤트가 생성될 당시 Runbook 작업의 결과입니다. 가능한 값은 다음과 같습니다.<br>- InProgress입니다.|
|resultDescription | runbook의 출력 스트림을 포함합니다.|
|RunbookName | runbook의 이름입니다.|
|SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다. Automation의 경우 값은 OpsManager입니다.|
|StreamType | 작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>- 진행률<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다.|
|Time | runbook 작업이 실행된 날짜 및 시간입니다.|

**JobLogs** 또는 **JobStreams**의 범주 레코드를 반환하는 로그 검색을 수행하는 경우, 검색에 의해 반환되는 업데이트를 요약하는 타일 집합을 표시하는 **JobLogs** 또는 **JobStreams** 보기를 선택할 수 있습니다.

## <a name="sample-log-searches"></a>샘플 로그 검색

다음 테이블은 이 솔루션에 의해 수집된 작업 레코드에 대한 샘플 로그 검색을 제공합니다.

|쿼리 | Description|
|----------|----------|
|성공적으로 완료된 ScheduledStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|성공적으로 완료되지 않은 runbook ScheduledStartStop_Parent 대한 작업 찾기 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|성공적으로 완료된 SequencedStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|성공적으로 완료되지 않은 runbook SequencedStartStop_Parent 대한 작업 찾기 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>다음 단계

근무 외 솔루션 **중 VM 시작/중지에는** 미리 정의된 경고 집합이 포함되어 있지 않습니다. Azure 모니터를 사용하여 [로그 알림 만들기를](../azure-monitor/platform/alerts-log.md) 검토하여 DevOps 또는 운영 프로세스 및 프로시저를 지원하기 위해 작업 실패 경고를 만드는 방법을 알아봅니다.