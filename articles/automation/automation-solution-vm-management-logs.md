---
title: Azure Automation 작업 시간 외 VM 시작/중지에서 로그 쿼리
description: 이 문서에서는 Azure Monitor를 사용하여 작업 시간 외 VM 시작/중지에 의해 생성된 로그 데이터를 쿼리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: de013b6ccd924f50ffe12fcba1285b121eece5f7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827559"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>작업 시간 외 VM 시작/중지에서 로그 쿼리

Azure Automation은 연결된 Log Analytics 작업 영역에 작업 로그 및 작업 스트림이라는 두 가지 유형의 레코드를 전달합니다. 이 문서에서는 Azure Monitor에서 [쿼리](../azure-monitor/log-query/log-query-overview.md)에 사용할 수 있는 데이터를 검토합니다.

## <a name="job-logs"></a>작업 로그

|속성 | Description|
|----------|----------|
|Caller |  작업을 시작한 사람입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
|Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobLogs입니다.|
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
|Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobStreams입니다.|
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

다음 테이블은 작업 외 VM 시작/중지에 의해 수집된 작업 레코드에 대한 샘플 로그 검색을 제공합니다.

|쿼리 | Description|
|----------|----------|
|성공적으로 완료된 ScheduledStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|성공적으로 완료되지 않은 ScheduledStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|성공적으로 완료된 SequencedStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|성공적으로 완료되지 않은 SequencedStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>다음 단계

* 기능을 설정하려면 [작업 시간 외 VM 중지/시작 구성](automation-solution-vm-management-config.md)을 참조하세요.
* 기능 배포 중 로그 경고에 대한 자세한 내용은 [Azure Monitor를 사용하여 로그 경고 만들기](../azure-monitor/platform/alerts-log.md)를 참조하세요.
* 기능 오류를 해결하려면 [작업 시간 외 VM 시작/중지 문제 해결](troubleshoot/start-stop-vm.md)을 참조하세요.