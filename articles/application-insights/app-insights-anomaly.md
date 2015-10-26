<properties 
	pageTitle="Application Insights: 자동 관리 이상 감지" 
	description="Application Insights는 앱 원격 분석의 심층 분석을 수행하여 잠재적 성능 문제에 대해 경고합니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="awills"/>

#  Application Insights: 자동 관리 이상 감지

*Application Insights는 미리 보기 상태입니다.*


Application Insights는 심층 분석 앱 원격 분석을 수행하고 잠재적 성능 문제에 대해 경고할 수 있습니다. 여러분은 아마도 이메일을 통해 이상 경고 중 하나를 수신했기 때문에 이 글을 읽고 있을 것입니다.

## 이상 경고 정보

* *이 경고를 받은 이유는 무엇입니까?*
 * Application Insights는 주기적으로 패턴 인식 규칙을 사용하여 데이터를 분석합니다. 응용 프로그램에서 성능 문제를 나타낼 수 있는 이상을 찾습니다.
* *알림은 분명히 문제가 있음을 의미합니까?*
 * 아니요. 보다 밀접하게 확인하는 것이 좋겠다는 제안입니다. 
* *어떻게 해야 하나요?*
 * [제시된 데이터를 보고](#responding-to-an-alert) 문제가 있는지 여부를 고려합니다. 그렇지 않은 경우 큰 문제는 아닙니다.
* *그렇다면, 내 데이터를 확인하고 있습니까?*
 * 아니요. 서비스는 완전 자동입니다. 사용자는 알림만 받게 됩니다. 사용자의 데이터는 [비공개](app-insights-data-retention-privacy.md)입니다.


## 검색 프로세스

* *어떤 종류의 이상이 감지됩니까?*
 * 사용자 스스로 확인하기에 시간이 많이 걸리는 패턴입니다. 예를 들어, 특정 조합의 위치, 시간 및 플랫폼에서의 좋지 않은 성능입니다.
* *나만의 이상 감지 규칙을 만들 수 있습니까?*
 * 이러한 종류의 심층 분석에 대해서는 만들 수 없습니다. (하지만 메트릭 임계값을 초과할 때 알리도록 [경고를 설정](app-insights-alerts.md)할 수 있습니다.)
* *얼마나 자주 분석합니까?*
 * 원격 분석이 많지 않은 앱 리소스에 대해서는 분석하지 않습니다. 디버깅 세션에 대한 경고는 거의 발생하지 않습니다.


## 경고에 응답

이메일 또는 이상 목록에서 이상 보고서를 엽니다.

![](./media/app-insights-anomaly/02.png)

알림:

* 설명
* 사용자에게 얼마나 많이 또는 얼마나 자주 영향을 주는 지 알려주는 영향 명령문입니다.

차트를 클릭하여 자세한 내용이 있는 블레이드를 엽니다.

시간 범위와 원격 분석을 탐색하는 필터를 수정합니다.




## 알림 이메일

* *알림을 수신하려면 이 서비스에 가입해야 합니까?*
 * 아니요. 우리의 bot은 주기적으로 모든 Application Insights 사용자로부터 데이터를 조사하여 문제를 검색하는 경우 알림을 보냅니다.
* *구독을 취소하거나 동료에게 대신 보낸 알림을 가져올 수 있습니까?*
 * 경고 또는 이메일의 링크를 클릭합니다. 이상 설정을 엽니다.
 
    ![](./media/app-insights-anomaly/01.png)

    현재 [Application Insights 리소스에 대한 쓰기 액세스 권한](app-insights-resources-roles-access-control.md)이 있는 사용자에게는 보내지 않습니다.
* *이 메시지를 너무 많이 받고 싶지 않습니다.*
 * 하루에 3개로 제한됩니다. 모든 메시지는 중복으로 보내지 않습니다.
* *아무일도 하지 않는 경우 미리 알림을 받습니까?*
 * 아니요, 각 문제에 대해 한 번만 메시지가 나타납니다.
* *이메일이 삭제되었습니다. 포털의 어디에서 알림을 찾을 수 있습니까?*
 * 앱의 Application Insights 개요에서 **이상** 타일을 클릭합니다. 






 

<!---HONumber=Oct15_HO3-->