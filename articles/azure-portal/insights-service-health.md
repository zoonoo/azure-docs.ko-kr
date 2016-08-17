<properties
	pageTitle="Azure insights를 사용하여 서비스 상태 추적 | Microsoft Azure"
	description="Azure에서 성능 저하 또는 서비스 중단이 발생한 시기를 확인합니다. "
	authors="stepsic-microsoft-com"
	manager="kamrani"
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="stepsic"/>

# 서비스 상태 추적

Azure는 서비스 중단 또는 성능 저하가 있을 때마다 이 사실을 알립니다. Azure 포털에서 이러한 이벤트를 찾아볼 수 있으며, [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 또는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 사용하여 프로그래밍 방식으로 전체 이벤트 집합에 액세스할 수도 있습니다.

## 지역에 따라 서비스 상태를 참조하십시오.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **홈**에서 **서비스 상태** ![홈](./media/insights-service-health/Insights_Home.png)라는 타일이 표시됩니다.

3. 이 타일을 클릭하면 Azure에서 모든 지역 목록이 표시됩니다. 원하는 지역을 클릭하면 해당 지역에 대한 서비스 상태 기록을 볼 수 있습니다. ![홈](./media/insights-service-health/Insights_Regions.png)

4. 또한 테이블에서 해당 항목을 클릭하여 개별 인시던트에 대한 세부 사항을 볼 수 있습니다.

## 전체 서비스 상태 로그 찾아보기

2. **찾아보기**를 클릭하고 **감사 로그**를 선택합니다. ![찾아보기 허브](./media/insights-service-health/Insights_Browse.png)

3. 지난 7일간 구독에 영향을 준 이벤트를 모두 보여 주는 블레이드가 열립니다. 이 목록에 서비스 상태 항목이 나타나지만 이 목록은 길기 때문에 검색이 어려울 수도 있습니다.

4. **필터** 명령을 클릭합니다.

5. **이벤트 범주**를 선택하고 **서비스 상태**: ![모든 이벤트](./media/insights-service-health/Insights_Filter.png)를 선택합니다.

6. **업데이트됨**을 클릭합니다.

7. 이제 자신의 구독에 영향을 준 모든 서비스 상태 이벤트를 볼 수 있습니다.![리소스 그룹](./media/insights-service-health/Insights_HealthEvent.png)

8. 여기서 세부 정보 블레이드로 이동하여 이벤트의 세부 정보를 확인할 수 있습니다.

## 다음 단계

* 이벤트가 발생할 때마다 [경고 알림을 수신](insights-receive-alert-notifications.md)합니다.
* [서비스 메트릭을 모니터링](insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 페이지가 다운된 경우 이를 찾을 수 있도록 Application Insights를 사용하여 [웹 페이지의 가용성 및 응답성을 모니터링](../application-insights/app-insights-monitor-web-app-availability.md)합니다.

<!---HONumber=AcomDC_0803_2016-->