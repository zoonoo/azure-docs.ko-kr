---
title: Azure 애플리케이션 Insights에서 경고 설정
description: 느린 응답 시간, 예외 및 웹앱의 기타 성능 또는 사용 변경에 대한 알림을 받습니다.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295090"
---
# <a name="set-alerts-in-application-insights"></a>Application Insights에서 경고 설정

[Azure Application Insights][start]는 웹앱의 성능 및 사용 메트릭이 변경되면 사용자에게 경고할 수 있습니다. 

Application Insights는 [다양한 플랫폼][platforms]에서 라이브 앱을 모니터링하여 성능 문제를 진단하고 사용 패턴을 이해할 수 있습니다.

경고에는 여러 가지 유형이 있습니다.

* [**메트릭 경고**](../../azure-monitor/platform/alerts-metric-overview.md) 는 메트릭이 응답 시간, 예외 수, CPU 사용량 또는 페이지 보기와 같은 일정 기간의 임계값을 초과 하는 경우를 알려 줍니다.
* [**로그 경고**](../../azure-monitor/platform/alerts-unified-log.md) 는 경고 신호가 사용자 지정 Kusto 쿼리를 기반으로 하는 경고를 설명 하는 데 사용 됩니다.
* [**웹 테스트**][availability]는 인터넷에서 사이트를 사용할 수 없거나 응답 속도가 느려지면 그 사실을 알려 줍니다. [자세히 알아보기][availability].
* [**사전 진단**](../../azure-monitor/app/proactive-diagnostics.md)은 비정상적인 성능 패턴에 대해 알려 주기 위해 자동으로 구성됩니다.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>사용자 지정 로그 검색을 사용 하 여 예외 경고를 설정 하는 방법

이 섹션에서는 쿼리 기반 예외 경고를 설정 하는 방법을 설명 합니다. 이 예에서는 지난 24 시간 동안 실패 한 비율이 10%를 초과 하는 경우 경고를 표시 한다고 가정해 보겠습니다.

1. Azure Portal에서 응용 프로그램 정보 리소스로 이동 합니다.
2. 왼쪽의 구성 아래에서 **경고**를 클릭 합니다.

    ![왼쪽의 구성 클릭 경고](./media/alerts/1appinsightalert.png)

3. 경고 탭의 맨 위에서 **새 경고 규칙**을 선택 합니다.

     ![경고 탭의 맨 위에서 새 경고 규칙을 클릭 합니다.](./media/alerts/2createalert.png)

4. 리소스를 자동으로 선택 해야 합니다. 조건을 설정 하려면 **조건 추가**를 클릭 합니다.

    ![조건 추가를 클릭 합니다.](./media/alerts/3addcondition.png)

5. 신호 논리 구성 탭에서 **사용자 지정 로그 검색** 을 선택 합니다.

    ![사용자 지정 로그 검색을 클릭 합니다.](./media/alerts/4customlogsearch.png)

6. 사용자 지정 로그 검색 탭에서 "쿼리 검색" 상자에 쿼리를 입력 합니다. 이 예에서는 아래 Kusto 쿼리를 사용 합니다.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![검색 쿼리 상자에 쿼리 입력](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > 다른 유형의 쿼리 기반 경고에도 이러한 단계를 적용할 수 있습니다. Kusto 쿼리 언어에 대 한 자세한 내용은이 [kusto 시작 문서](https://docs.microsoft.com/azure/kusto/concepts/) 또는이 [SQL에서 kusto 참고 자료 시트로 이동할](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet) 수 있습니다.

7. "경고 논리"에서 결과 수 또는 메트릭 측정을 기반으로 하는지 여부를 선택 합니다. 그런 다음 조건 (보다 큼, 같음, 보다 작음) 및 임계값을 선택 합니다. 이러한 값을 변경 하는 동안 조건 미리 보기 문장이 변경 된 것을 확인할 수 있습니다. 이 예에서는 "같음"을 사용 합니다.

    ![경고 논리에서 및 조건에 따라에 제공 된 옵션 중에서 선택 하 고 임계값을 입력 합니다.](./media/alerts/6alertlogic.png)

8. "평가 기준"에서 기간 및 빈도를 설정 합니다. 여기서 기간은 위의 쿼리에서 기간에 대해 입력 한 값과 일치 해야 합니다. 그런 다음 **완료**를 클릭 합니다.

    ![아래쪽에서 기간 및 빈도를 설정 하 고 완료를 클릭 합니다.](./media/alerts/7evaluate.png)

9. 이제 월별 예상 비용으로 만든 조건이 표시 됩니다. ["작업 그룹"](../platform/action-groups.md) 아래에서 새 그룹을 만들거나 기존 그룹을 선택할 수 있습니다. 원하는 경우 작업을 사용자 지정할 수 있습니다.

    ![작업 그룹에서 선택 또는 만들기 단추를 클릭 합니다.](./media/alerts/8actiongroup.png)

10. 마지막으로 경고 정보 (경고 규칙 이름, 설명, 심각도)를 추가 합니다. 완료 되 면 아래쪽에서 **경고 규칙 만들기** 를 클릭 합니다.

    ![경고 세부 정보에서 경고 규칙 이름을 입력 하 고, 설명을 작성 하 고, 심각도를 선택 합니다.](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>클래식 경고 전자 메일 알림 구독을 취소 하는 방법

이 섹션은 클래식 **가용성 경고**, **클래식 Application Insights 메트릭 경고**및 **클래식 실패 변칙 경고**에 적용 됩니다.

다음 중 하나가 적용 되는 경우 이러한 클래식 경고에 대 한 전자 메일 알림을 받게 됩니다.

* 전자 메일 주소는 경고 규칙 설정의 알림 전자 메일 받는 사람 필드에 나열 됩니다.

* 구독에 특정 역할을 보유 하는 사용자에 게 전자 메일 알림을 보내는 옵션은 활성화 되며 해당 특정 Azure 구독에 해당 하는 역할을 보유 합니다.

![경고 알림 스크린샷](./media/alerts/alert-notification.png)

보안 및 개인 정보를 효과적으로 제어 하기 위해 일반적으로 **알림 전자 메일 받는 사람** 필드에 클래식 경고에 대 한 알림 받는 사람을 명시적으로 지정 하는 것이 좋습니다. 이전 버전과의 호환성을 위해 특정 역할을 보유 하는 모든 사용자에 게 알리는 옵션이 제공 됩니다.

특정 경고 규칙에 의해 생성 된 전자 메일 알림을 구독 취소 하려면 **알림 전자 메일 받는 사람** 필드에서 전자 메일 주소를 제거 합니다.

전자 메일 주소가 명시적으로 나열 되지 않은 경우 특정 역할의 모든 구성원에 게 자동으로 알리는 옵션을 사용 하지 않도록 설정 하는 것이 좋습니다. 대신 알림 전자 메일 받는 사람 필드에서 해당 경고 규칙에 대 한 알림을 받아야 하는 모든 사용자 전자 메일을 나열 하는 것이 좋습니다.

## <a name="who-receives-the-classic-alert-notifications"></a>누가 (클래식) 경고 알림을 받나요?

이 섹션은 클래식 경고에만 적용되며, 원하는 수신자만 알림을 받도록 경고 알림을 최적화하는 데 도움이 됩니다. [클래식 경고](../platform/alerts-classic.overview.md) 와 새로운 경고 환경 간의 차이점에 대 한 자세한 내용은 [경고 개요 문서](../platform/alerts-overview.md)를 참조 하세요. 새 경고 환경에서 경고 알림을 제어 하려면 [작업 그룹](../platform/action-groups.md)을 사용 합니다.

* 클래식 경고 알림에는 특정 수신자를 사용하는 것이 좋습니다.

* Application Insights 메트릭(가용성 메트릭 포함)에 대한 경고의 경우 **대량/그룹** 확인란 옵션을 사용하도록 설정하면 구독에서 owner, contributor 또는 reader 역할의 사용자에게 경고가 전송됩니다. 실제로 구독의 Application Insights 리소스에 대한 액세스 권한이 있는 _모든_ 사용자가 범위에 포함되며 알림을 받습니다.

> [!NOTE]
> 현재 **대량/그룹** 확인란 옵션을 사용하고 있고 사용 안 함으로 설정하는 경우에는 변경 내용을 되돌릴 수 없습니다.

역할에 따라 사용자에게 알려야 하는 경우 새 경고 환경/거의 실시간 경고를 사용합니다. [작업 그룹](../platform/action-groups.md)을 사용하면 contributor/owner/reader 역할(단일 옵션으로 결합되지 않음)의 사용자에 대해 메일 알림을 구성할 수 있습니다.

## <a name="automation"></a>Automation
* [PowerShell을 사용하여 경고 설정 자동화](../../azure-monitor/app/powershell-alerts.md)
* [Webhook를 사용하여 경고에 대한 응답 자동화](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>참고 항목
* [가용성 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md)
* [경고 설정 자동화](../../azure-monitor/app/powershell-alerts.md)
* [사전 진단](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

