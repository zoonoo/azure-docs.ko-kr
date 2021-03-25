---
title: Azure Active Directory의 복원 력을 위한 응용 프로그램 로그인 상태를 모니터링 합니다.
description: 쿼리 및 알림을 만들어 응용 프로그램의 로그인 상태를 모니터링 합니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47691ae404f65f04ace36485cb01fc5617d00a9a
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105031769"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>복원 력을 위한 응용 프로그램 로그인 상태 모니터링

인프라 복원 력을 높이려면 중요 한 응용 프로그램에 대 한 응용 프로그램 로그인 상태 모니터링을 설정 하 여 영향을 받는 인시던트가 발생 하는 경우 경고를 수신 합니다. 이러한 작업을 지원 하기 위해 로그인 상태 통합 문서에 따라 경고를 구성할 수 있습니다. 

이 통합 문서를 통해 관리자는 테 넌 트의 응용 프로그램에 대 한 인증 요청을 모니터링할 수 있습니다. 다음과 같은 주요 기능을 제공 합니다.

* 거의 실시간 데이터를 사용 하 여 모든 앱 또는 개별 앱을 모니터링 하도록 통합 문서를 구성 합니다.

* 인증 패턴이 변경 될 때 알리도록 경고를 구성 하 여 조사 하 고 조치를 취할 수 있습니다.

* 주에 대 한 예를 들어 통합 문서의 기본 설정인 기간의 추세를 비교 합니다.

> [!NOTE]
> 사용 가능한 모든 통합 문서 및 사용을 위한 필수 구성 요소를 보려면 [보고서에 Azure Monitor 통합 문서를 사용 하는 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)을 참조 하세요.

영향을 주는 이벤트가 발생 하는 동안 다음과 같은 두 가지 작업을 수행할 수 있습니다.

* 사용자가 로그인 할 수 없기 때문에 응용 프로그램에 대 한 로그인 수가 precipitously 수 있습니다.

* 로그인 오류의 수가 증가할 수 있습니다. 

이 문서에서는 사용자 로그인에 대 한 중단을 모니터링 하도록 로그인 상태 통합 문서를 설정 하는 과정을 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure AD 테넌트.

* Azure AD 테 넌 트의 전역 관리자 또는 보안 관리자 역할이 있는 사용자입니다.

* Azure Monitor 로그에 로그를 보내기 위한 Azure 구독의 Log Analytics 작업 영역입니다. 

   * [Log Analytics 작업 영역을 만드는](../../azure-monitor/logs/quick-create-workspace.md) 방법 알아보기

* Azure Monitor 로그와 통합 된 Azure AD 로그

   * [AZURE AD 로그인 로그를 Azure Monitor 스트림과 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 하는 방법에 대해 알아봅니다.

## <a name="configure-the-app-sign-in-health-workbook"></a>앱 로그인 상태 통합 문서 구성 

통합 문서에 액세스 하려면 **Azure Portal** 열고 **Azure Active Directory** 을 선택한 다음 **통합 문서** 를 선택 합니다.

사용량, 조건부 액세스 및 문제 해결 아래에 통합 문서가 표시 됩니다. 앱 로그인 상태 통합 문서가 사용 섹션에 나타납니다.

통합 문서를 사용 하면 최근에 수정한 통합 문서 섹션에 표시 될 수 있습니다.

![Azure Portal의 통합 문서 갤러리를 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


앱 로그인 상태 통합 문서를 사용 하면 로그인을 통해 발생 하는 작업을 시각화할 수 있습니다. 

기본적으로 통합 문서는 두 개의 그래프를 제공 합니다. 이러한 그래프는 현재 앱과 동일한 기간을 기준으로 발생 하는 상황을 비교 합니다. 파란색 선은 최신이 고 주황색 줄은 이전 주입니다.

![로그인 상태 그래프를 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**첫 번째 그래프는 시간별 사용량 (성공한 사용자 수)** 입니다. 사용자의 현재 수를 일반적인 사용 기간과 비교 하면 조사가 필요할 수 있는 사용에 대 한 삭제를 발견할 수 있습니다. 성공한 사용 속도의 삭제는 실패율이 성능 및 사용률 문제를 감지 하는 데 도움이 될 수 있습니다. 예를 들어 사용자가 로그인을 시도 하기 위해 응용 프로그램에 연결할 수 없는 경우에는 오류가 발생 하지 않고 사용 중인 삭제만 발생 합니다. 다음 섹션에서이 데이터에 대 한 샘플 쿼리를 찾을 수 있습니다.

**두 번째 그래프는 시간당 실패율** 입니다. 실패율이 급증 하면 인증 메커니즘과 관련 된 문제가 있음을 나타낼 수 있습니다. 사용자가 인증을 시도할 수 있는 경우에만 실패율을 측정할 수 있습니다. 사용자가 액세스 권한을 얻을 수 없는 경우 오류가 표시 되지 않습니다.

사용량 또는 실패율이 지정 된 임계값을 초과 하는 경우 특정 그룹에 알리는 경고를 구성할 수 있습니다. 다음 섹션에서이 데이터에 대 한 샘플 쿼리를 찾을 수 있습니다.

## <a name="configure-the-query-and-alerts"></a>쿼리 및 경고 구성

Azure Monitor에서 경고 규칙을 만들고 정기적으로 저장 된 쿼리 또는 사용자 지정 로그 검색을 자동으로 실행할 수 있습니다.

그래프에 반영 된 쿼리를 기반으로 전자 메일 경고를 만들려면 다음 지침을 따르십시오. 아래 샘플 스크립트는 다음과 같은 경우 전자 메일 알림을 보냅니다.

* 성공한 사용은 이전 섹션의 시간별 사용량 그래프와 같이 2 일 전 같은 시간에 90%에 의해 삭제 됩니다. 

* 실패율은 이전 섹션의 시간당 실패율 그래프와 같이 2 일 전에 같은 시간에 90% 씩 늘어납니다. 

 기본 쿼리를 구성 하 고 경고를 설정 하려면 다음 단계를 완료 합니다. 예제 쿼리를 구성의 기반으로 사용 합니다. 이 섹션의 끝에 쿼리 구조에 대 한 설명이 표시 됩니다.

Azure Monitor를 사용 하 여 로그 경고를 만들고 보고 관리 하는 방법에 대 한 자세한 내용은 [로그 경고 관리](../../azure-monitor/alerts/alerts-log.md)를 참조 하세요.

1. 통합 문서에서 **편집** 을 선택한 다음 그래프의 오른쪽 바로 위에 있는 **쿼리 아이콘** 을 선택 합니다.   

   [![통합 문서 편집을 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   쿼리 로그가 열립니다.

   [![쿼리 로그를 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. 새 Kusto 쿼리를 위한 샘플 스크립트 중 하나를 복사 합니다.  
   * [실패 율 증가를 쿼리 하는 kusto 쿼리](#kusto-query-for-increase-in-failure-rate)
   * [사용 중인 삭제를 쿼리 하는 kusto 쿼리](#kusto-query-for-drop-in-usage)

3. 창에 쿼리를 붙여넣고 **실행** 을 선택 합니다. 아래 이미지에 표시 된 완료 된 메시지를 확인 하 고 해당 메시지 아래에 결과를 표시 합니다.

   [![쿼리 결과 실행을 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. 쿼리를 강조 표시 하 고 + **새 경고 규칙** 을 선택 합니다. 
 
   [![새 경고 규칙 화면을 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. 경고 조건을 구성 합니다. 조건 섹션에서 **평균 사용자 지정 로그 검색이 논리 정의 개수 보다 클 때마다** 링크를 선택 합니다. 신호 논리 구성 창에서 경고 논리로 스크롤합니다.

   [![경고 구성 화면을 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **임계값**: 0. 이 값은 모든 결과를 경고 합니다.

   * **평가 기간 (분)**: 2880. 이 값은 한 시간을 표시 합니다.

   * **빈도 (분)**: 60. 이 값은 이전 시간에 대해 계산 기간을 시간당 한 번으로 설정 합니다.

   * **완료** 를 선택합니다.

6. **작업** 섹션에서 다음 설정을 구성 합니다.  

    [![경고 규칙 만들기 페이지를 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * **작업** 아래에서 **작업 그룹 선택** 을 선택 하 고 경고에 대 한 알림을 받을 그룹을 추가 합니다.

   * **작업 사용자 지정** 에서 **전자 메일 경고** 를 선택 합니다.

   * **제목 줄** 을 추가 합니다.

7. **경고 규칙 세부 정보** 에서 다음 설정을 구성 합니다.

   * 설명이 포함 된 이름 및 설명을 추가 합니다.

   * 경고를 추가할 **리소스 그룹** 을 선택 합니다.

   * 경고의 기본 **심각도** 를 선택 합니다.

   * 즉시 라이브 하려면 **경고 규칙을 만들 때 사용** 을 선택 하 고 **경고 표시 안 함** 을 선택 합니다.

8. **경고 규칙 만들기** 를 선택합니다.

9. **저장** 을 선택 하 고 쿼리 이름을 입력 한 다음 **경고 범주의 쿼리로 저장** 합니다. 그런 다음 **저장** 을 다시 선택 합니다.  

   [![쿼리 저장 단추를 보여 주는 스크린샷](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>쿼리 및 경고 구체화

최대의 효율성을 위해 쿼리 및 경고를 수정 합니다.

* 경고를 테스트 해야 합니다.

* 중요 한 알림을 받을 수 있도록 경고 민감도 및 빈도를 수정 합니다. 관리자가 너무 많거나 중요 한 사항이 누락 되는 경우 경고에 대 한 desensitized 될 수 있습니다. 

* 관리자의 전자 메일 클라이언트에 경고가 발생 하는 전자 메일이 허용 된 보낸 사람 목록에 추가 되었는지 확인 합니다. 그렇지 않으면 전자 메일 클라이언트의 스팸 필터로 인해 알림을 누락 시킬 수 있습니다. 

* Azure Monitor의 경고 쿼리는 지난 48 시간 동안의 결과만 포함할 수 있습니다. [이것은 설계상의 현재 제한 사항](https://github.com/MicrosoftDocs/azure-docs/issues/22637)입니다.

## <a name="sample-scripts"></a>샘플 스크립트

### <a name="kusto-query-for-increase-in-failure-rate"></a>실패 율 증가를 쿼리 하는 kusto 쿼리

   아래쪽의 비율을 필요에 따라 조정 하 고 어제와 동일한 시간을 기준으로 지난 1 시간의 트래픽 변화를 나타낼 수 있습니다. 0.5는 트래픽에 50% 차이가 있음을 의미 합니다.

```kusto

let today = SigninLogs

| where TimeGenerated > ago(1h) // Query failure rate in the last hour
 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

// Optionally filter by a specific application

//| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rowNumber = row_number();

let yesterday = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

// Optionally filter by a specific application

//| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday

| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday

| project TimeGenerated, failureRate, failureRateYesterday

// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday

| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>사용 중인 삭제를 쿼리 하는 kusto 쿼리

다음 쿼리에서는 지난 1 시간의 트래픽을 어제 같은 시간으로 비교 합니다.
해당 일에 예상 되는 일에는 토요일, 일요일, 월요일을 제외 하 고 이전 날에 동시에 트래픽 변화를 발생 시킬 수 있습니다. 

아래쪽의 비율을 필요에 따라 조정 하 고 어제와 동일한 시간을 기준으로 지난 1 시간의 트래픽 변화를 나타낼 수 있습니다. 0.5는 트래픽에 50% 차이가 있음을 의미 합니다.

*비즈니스 운영 모델에 맞게 이러한 값을 조정 해야* 합니다.

```Kusto
 let today = SigninLogs // Query traffic in the last hour

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

// Optionally filter by AppDisplayName to scope query to a single application

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour

| sort by TimeGenerated desc

| serialize rn = row_number();

let yesterday = SigninLogs // Query traffic at the same hour yesterday

| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday

| project TimeGenerated, AppDisplayName, UserPrincipalName

// Optionally filter by AppDisplayName to scope query to a single application

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

today
| join // Join data from today and yesterday together
(
yesterday
)
on rn

// Calculate the difference in number of users in the last hour compared to the same time yesterday

| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)

 extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday

// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.

| extend day = dayofweek(now())

| where day != time(6.00:00:00) // exclude Sat

| where day != time(0.00:00:00) // exclude Sun

| where day != time(1.00:00:00) // exclude Mon

| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>경고를 관리 하는 프로세스 만들기

쿼리 및 경고를 설정한 후에는 경고를 관리 하는 비즈니스 프로세스를 만듭니다.

* 통합 문서를 모니터링 하는 사용자 및 시기

* 경고가 생성 되 면 조사 하는 사람은 누구 인가요?

* 통신 요구 사항 누구에 게 연락 하 고 누구를 받을 수 있나요?

* 중단이 발생 하면 트리거되는 비즈니스 프로세스는 무엇 인가요?

## <a name="next-steps"></a>다음 단계

[통합 문서에 대 한 자세한 정보](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
