---
title: Azure Active Directory에서 복원력을 위한 애플리케이션 로그인 상태 모니터링
description: 애플리케이션의 로그인 상태를 모니터링하기 위해 쿼리 및 알림을 만듭니다.
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
ms.openlocfilehash: d7c32940d5fc40249257d1d0bb38ef67c12dc2ef
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029688"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>복원력을 위한 애플리케이션 로그인 상태 모니터링

인프라 복원력을 높이기 위해 영향이 큰 인시던트가 발생할 경우 경고가 표시되도록 중요한 애플리케이션에 대해 애플리케이션 로그인 상태 모니터링을 설정합니다. 이러한 작업을 지원하기 위해 로그인 상태 통합 문서를 기준으로 경고를 구성할 수 있습니다. 

이 통합 문서를 통해 관리자가 테넌트에서 애플리케이션의 인증 요청을 모니터링할 수 있습니다. 다음과 같은 주요 기능을 제공합니다.

* 거의 실시간 데이터로 모든 또는 개별 앱을 모니터링하도록 통합 문서를 구성합니다.

* 조사를 수행하고 조치를 취할 수 있도록 인증 패턴이 변경되었을 때 이를 알리는 경고를 구성합니다.

* 통합 문서의 기본 설정인 주 단위와 같은 일정 기간 동안의 추세를 비교합니다.

> [!NOTE]
> 사용 가능한 모든 통합 문서를 보고 이를 사용하기 위한 필요 조건을 확인하려면 [보고서에 Azure Monitor 통합 문서를 사용하는 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)을 참조하세요.

영향이 큰 이벤트 중에는 다음 두 가지 결과가 발생할 수 있습니다.

* 사용자가 로그인할 수 없기 때문에 애플리케이션의 로그인 횟수가 급격하게 저하될 수 있습니다.

* 로그인 실패 횟수가 증가할 수 있습니다. 

이 문서에서는 사용자의 로그인 중단을 모니터링하기 위해 로그인 상태 통합 문서를 설정하는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure AD 테넌트.

* Azure AD 테넌트에 대해 전역 관리자 또는 보안 관리자 역할이 있는 사용자.

* Azure Monitor 로그에 로그를 전송하기 위한 Azure 구독의 Log Analytics 작업 영역. 

   * [Log Analytics 작업 영역을 만드는 방법](../../azure-monitor/logs/quick-create-workspace.md)을 알아봅니다.

* Azure Monitor 로그와 통합된 Azure AD 로그

   * [Azure AD 로그인 로그를 Azure Monitor 스트림과 통합하는 방법](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)을 알아봅니다.

## <a name="configure-the-app-sign-in-health-workbook"></a>앱 로그인 상태 통합 문서 구성 

통합 문서에 액세스하려면 **Azure Portal** 을 열고 **Azure Active Directory** 를 선택한 후 **통합 문서** 를 선택합니다.

사용량, 조건부 액세스 및 문제 해결 아래에 통합 문서가 표시됩니다. 앱 로그인 상태 통합 문서가 사용량 섹션에 표시됩니다.

통합 문서를 사용한 후에는 최근에 수정된 통합 문서 섹션에 표시될 수 있습니다.

![Azure Portal에서 통합 문서 갤러리를 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


앱 로그인 상태 통합 문서를 사용하면 로그인에 발생 중인 작업을 시각화할 수 있습니다. 

기본적으로 통합 문서에는 두 개의 그래프가 제공됩니다. 이러한 그래프는 현재 앱에서 발생하고 있는 작업과 1주일 전 동일한 기간에 발생한 작업을 비교해서 보여줍니다. 파란색 선은 현재를 나타내고 주황색 선은 1주일 전을 나타냅니다.

![로그인 상태 그래프를 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**첫 번째 그래프는 시간별 사용량(성공한 사용자 수)입니다**. 현재 성공한 사용자 수를 일반적인 사용 기간과 비교하면 조사가 필요할 수 있는 사용량 저하를 확인하는 데 도움이 될 수 있습니다. 성공한 사용률 저하는 실패 사용률로 확인할 수 없는 성능 및 사용률 문제를 감지하는 데 도움이 될 수 있습니다. 예를 들어 사용자가 로그인 시도를 위해 애플리케이션에 연결할 수 없으면 실패가 표시되지 않고 사용량만 저하됩니다. 다음 섹션에서는 이 데이터에 대한 샘플 쿼리를 확인할 수 있습니다.

**두 번째 그래프는 시간별 실패율입니다**. 실패율이 급증하면 인증 메커니즘 문제를 나타낼 수 있습니다. 실패율은 사용자가 인증을 시도할 수 있는 경우에만 특정 가능합니다. 사용자가 인증 시도를 위한 액세스 권한을 얻을 없으면 실패가 표시되지 않습니다.

사용량 또는 실패율이 지정된 임계값을 초과할 때 특정 그룹에 알림을 제공하는 경고를 구성할 수 있습니다. 다음 섹션에서는 이 데이터에 대한 샘플 쿼리를 확인할 수 있습니다.

## <a name="configure-the-query-and-alerts"></a>쿼리 및 경고 구성

Azure Monitor에서 경고 규칙을 만들고 정기적으로 저장된 쿼리 또는 사용자 지정 로그 검색을 자동으로 실행할 수 있습니다.

다음 안내에 따라 그래프에 표시된 쿼리를 기준으로 이메일 경고를 만들 수 있습니다. 아래의 샘플 스크립트는 다음 경우에 이메일 알림을 전송합니다.

* 이전 섹션의 시간별 사용량 그래프에서와 같이 성공한 사용량이 2일 전 동일 시간으로부터 90% 감소된 경우. 

* 이전 섹션의 시간별 실패율 그래프에서와 같이 실패율이 2일 전 동일 시간으로부터 90% 증가한 경우. 

 기본 쿼리를 구성하고 경고를 설정하기 위해 다음 단계를 완료합니다. 해당 구성의 기초로 샘플 쿼리를 사용합니다. 쿼리 구조에 대한 설명은 이 섹션의 끝에 표시되어 있습니다.

Azure Monitor를 사용하여 로그 경고를 만들고, 확인하고, 관리하는 방법에 대한 자세한 내용은 [로그 경고 관리](../../azure-monitor/alerts/alerts-log.md)를 참조하세요.

1. 통합 문서에서 **편집** 을 선택한 후 그래프 오른쪽 바로 위에 있는 **쿼리 아이콘** 을 선택합니다.   

   [![통합 문서 편집을 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   쿼리 로그가 열립니다.

   [![쿼리 로그를 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. 새 Kusto 쿼리의 샘플 스크립트 중 하나를 복사합니다.  
   * [실패율 증가에 대한 Kusto 쿼리](#kusto-query-for-increase-in-failure-rate)
   * [사용량 감소에 대한 Kusto 쿼리](#kusto-query-for-drop-in-usage)

3. 창에서 쿼리를 붙여넣고 **실행** 을 선택합니다. 아래 이미지에 표시된 완료됨 메시지와 메시지 아래의 결과를 확인합니다.

   [![실행된 쿼리 결과를 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. 쿼리를 강조 표시하고 + **새 경고 규칙** 을 선택합니다. 
 
   [![새 경고 규칙 화면을 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. 경고 조건을 구성합니다. ‎조건 섹션에서 **평균 사용자 지정 로그 검색이 정의된 논리 수보다 클 때마다** 링크를 선택합니다. 신호 논리 구성 창에서 경고 논리로 스크롤합니다.

   [![경고 구성 화면을 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **임계값**: 0. 이 값은 모든 결과에 대해 경고를 표시합니다.

   * **평가 기간(분)** : 2880. 이 값은 1시간을 살펴봅니다.

   * **빈도(분)** : 60. 이 값은 이전 시간에 대해 평가 기간을 시간당 한 번으로 설정합니다.

   * **완료** 를 선택합니다.

6. **작업** 섹션에서 다음 설정을 구성합니다.  

    [![경고 규칙 만들기 페이지를 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * **작업** 아래에서 **작업 그룹 선택** 을 선택하고 경고 알림을 제공하려는 그룹을 추가합니다.

   * **작업 사용자 지정** 에서 **이메일 경고** 를 선택합니다.

   * **제목 줄** 을 추가합니다.

7. **경고 규칙 세부 정보** 에서 다음 설정을 구성합니다.

   * 설명이 포함된 이름과 설명을 추가합니다.

   * 경고를 추가할 **리소스 그룹** 을 선택합니다.

   * 경고의 기본 **심각도** 를 선택합니다.

   * 즉시 활성화하려면 **경고 규칙을 만들 때 사용** 을 선택하고, 그렇지 않으면 **경고 표시 안 함** 을 선택합니다.

8. **경고 규칙 만들기** 를 선택합니다.

9. **저장** 을 선택하고, 쿼리 이름을 **경고 범주가 있는 쿼리로 저장** 으로 입력합니다. 그런 다음, **저장** 을 다시 선택합니다.  

   [![쿼리 저장 단추를 보여주는 스크린샷입니다.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>쿼리 및 경고 세분화

효율성을 최대화하기 위해 쿼리 및 경고를 수정합니다.

* 경고를 테스트해야 합니다.

* 중요한 알림을 받을 수 있도록 경고 민감도 및 빈도를 수정합니다. 경고가 너무 많이 표시되어 중요한 경고를 놓칠 경우 관리자가 경고에 대해 둔감해질 수 있습니다. 

* 관리자의 이메일 클라이언트에 전송되는 경고 이메일이 허용되는 보낸 사람 목록에 추가되었는지 확인합니다. 그렇지 않으면 이메일 클라이언트의 스팸 필터로 인해 알림을 놓칠 수 있습니다. 

* Azure Monitor의 경고 쿼리는 이전 48시간의 결과만 포함할 수 있습니다. [이 설정은 현재 의도적으로 지정된 제한 사항입니다](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="sample-scripts"></a>샘플 스크립트

### <a name="kusto-query-for-increase-in-failure-rate"></a>실패율 증가에 대한 Kusto 쿼리

   아래쪽 비율은 필요에 따라 조정될 수 있으며 어제 동시간대와 비교할 때 이전 시간 동안의 트래픽 변화율을 나타냅니다. 0.5는 트래픽 차이가 50%라는 것을 의미합니다.

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
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>사용량 감소에 대한 Kusto 쿼리

다음 쿼리에서는 어제 동시간 대와 비교되는 바로 전 시간의 트래픽을 비교합니다.
토요일, 일요일 및 월요일은 이전 일의 동시간 대 트래픽과 차이가 클 것으로 예상되기 때문에 제외되었습니다. 

아래쪽 비율은 필요에 따라 조정될 수 있으며 어제 동시간대와 비교할 때 이전 시간 동안의 트래픽 변화율을 나타냅니다. 0.5는 트래픽 차이가 50%라는 것을 의미합니다.

*비즈니스 운영 모델에 맞게 이러한 값을 조정해야 합니다*.

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
| extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>경고 관리를 위한 프로세스 만들기

쿼리 및 경고를 설정한 후에는 경고 관리를 위한 비즈니스 프로세스를 만듭니다.

* 통합 문서를 모니터링할 사람은 누구이고 시간은 언제인가요?

* 경고가 발생했을 때 이를 조사할 사람은 누구인가요?

* 통신 요구 사항은 무엇인가요? 통신을 만들 사람은 누구이고, 이를 수신할 사람은 누구인가요?

* 중단이 발생할 때 트리거해야 하는 비즈니스 프로세스는 무엇인가요?

## <a name="next-steps"></a>다음 단계

[통합 문서에 대한 자세한 정보](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
