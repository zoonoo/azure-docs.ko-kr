<properties
    pageTitle="Microsoft Azure의 경고 개요 | Microsoft Azure"
    description="경고를 통해 Azure 리소스 메트릭, 이벤트 또는 로그를 모니터링하고 사용자가 지정한 조건에 부합하면 알림을 받을 수 있습니다."
    authors="rboucher"
    manager=""
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/24/2016"
    ms.author="robb"/>


# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고 개요


이 문서에서는 경고에 대해 설명하고 그 이점과 경고 사용을 시작하는 방법을 소개합니다.  

## <a name="what-are-alerts?"></a>경고란?
경고는 Azure 리소스 메트릭, 이벤트 또는 로그를 모니터링하고 사용자가 지정한 조건에 부합하면 알림을 보내는 방법입니다.

다음을 기준으로 경고를 받을 수 있습니다.

- **메트릭 값**: 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉, 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.
- **활동 로그 이벤트**: 모든 이벤트에 대해 또는 특정 이벤트 수가 발생했을 때만 이 경고를 트리거할 수 있습니다.

## <a name="alerts-in-different-azure-services"></a>다른 Azure 서비스에서의 경고

경고는 다음과 같은 다양한 서비스에서 사용할 수 있습니다.

- **Application Insights**: 웹 테스트 및 메트릭 경고가 가능합니다. [Application Insights에서 경고 설정](../application-insights/app-insights-alerts.md) 및 [모든 웹 사이트의 가용성 및 응답성 모니터링](../application-insights/app-insights-monitor-web-app-availability.md)을 참조하세요.
- **Log Analytics(Operations Management Suite)**: 진단 로그를 Log Analytics로 라우팅할 수 있습니다. Operations Management Suite에서는 메트릭, 로그 및 기타 경고 유형을 지원합니다. 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md)를 참조하세요.   
- **Azure Monitor**: Azure Monitor에서는 메트릭 값과 활동 로그 이벤트 모두를 기반으로 한 경고가 가능합니다. Azure Monitor에는 [Azure Insights REST API](https://msdn.microsoft.com/library/dn931943.aspx)가 포함됩니다.  자세한 내용은 [Azure 포털, PowerShell 또는 명령줄 인터페이스를 사용하여 경고 만들기](insights-alerts-portal.md)를 참조하세요.

## <a name="alert-actions"></a>경고 작업
다음을 수행하도록 경고를 구성할 수 있습니다.

- 서비스 관리자, 공동 관리자 또는 사용자가 지정한 추가 이메일 주소로 이메일 알림을 보냅니다.
- 추가 자동화 작업을 시작할 수 있게 webhook를 호출합니다.

 ![경고를 설명합니다.](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>다음 단계

다음을 사용하여 경고 규칙에 대한 정보를 확인하고 구성할 수 있습니다.

- [Azure 포털](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [명령줄 인터페이스(CLI)](insights-alerts-command-line-interface.md)
- [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)



<!--HONumber=Oct16_HO2-->


