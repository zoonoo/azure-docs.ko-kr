<properties 
	pageTitle="최종 사용자 분석을 사용하는 방법" 
	description="Microsoft Azure 웹 사이트에 대한 최종 사용자 분석입니다." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>

# Microsoft Azure 웹 사이트에 대한 분석

사이트를 방문한 사람의 수가 궁금하십니까?  평균 페이지 로드 시간은 얼마인지 또는 어느 브라우저가 사용되는지 궁금하십니까?  웹 페이지에서 스크립트 몇 줄을 삽입하면 고객이 웹 사이트를 사용하는 방식에 대한 데이터를 수집할 수 있습니다. 

*비 Azure 웹 사이트에 대해서도 이렇게 할 수 있습니다. [Application Insights를 사용하여 웹 앱 사용을 모니터링합니다](app-insights-web-track-usage.md).*

![End User Analytics](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

## 최종 사용자 분석을 설정하는 방법

1. **웹 사이트** 블레이드에서 **최종 사용자 분석**이라는 파트를 클릭합니다.
2. **구성** 블레이드에서 전체 계측 스크립트를 선택하여 복사합니다.  
    ![Configuration](./media/insights-usage-analytics/Insights_CopyCode.png)
3. 스크립트를 각 웹 페이지에서 닫는 </head> 태그 바로 앞에 붙여넣습니다. 스크립트를 모든 웹 페이지에 삽입하는 것이 좋습니다. ASP.NET을 사용 중인 경우 응용 프로그램의 마스터 페이지에 스크립트를 삽입하면 됩니다.
4. 웹 응용 프로그램을 배포하고 사용합니다. 약 5-10분 후에 사용 현황 분석 정보가 표시되기 시작합니다.

## 데이터 탐색

브라우저 세션 파트에서는 다른 브라우저를 상세하게 확인한 후 브라우저 버전을 볼 수 있습니다.

![Browsers](./media/insights-usage-analytics/Insights_Browsers.png)

분석 파트는 다음을 표시합니다.

- 데스크톱 및 모바일을 포함한 다른 장치 유형에 대한 분석
- 지난주 동안의 상위 5개 페이지 및 페이지 로드 시간 그래프.  세션 및 보기의 개수도 제공됩니다.  
    ![Top Pages](./media/insights-usage-analytics/Insights_TopPages.png)
- 지난주 동안의 가장 느린 페이지(비즈니스 목표 및 목적을 충족하도록 개성하기 위해)


<!--HONumber=46--> 
