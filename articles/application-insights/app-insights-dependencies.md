<properties 
	pageTitle="Application Insights에서 종속성 문제 진단" 
	description="종속성으로 인한 오류 및 성능 저하 찾기" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="awills"/>
 
# Application Insights에서 종속성 문제 진단


*종속성*은 앱에서 호출하는 외부 구성 요소로, 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. Visual Studio Application Insights에서 응용 프로그램이 종속성을 기다리는 시간과 종속성 호출에 실패하는 빈도를 쉽게 확인할 수 있습니다.

## 사용 가능한 대상

기본적으로 종속성 모니터링은 현재 다음에서 사용할 수 있습니다.

* IIS 서버 또는 Azure에서 실행 중인 ASP.NET 웹앱 및 서비스

Java 웹앱 또는 장치 앱과 같은 다른 유형의 경우 TrackDependency API를 사용하여 고유한 모니터를 작성할 수 있습니다.

기본적으로 종속성 모니터는 현재 다음 유형의 종속성에 대한 호출을 보고합니다.

* SQL 데이터베이스
* ASP.NET 웹 및 wcf 서비스
* 로컬 또는 원격 HTTP 호출
* Azure DocumentDb, 테이블, Blob 저장소 및 큐

다시 말하지만, 자체 SDK 호출을 작성하여 다른 종속성을 모니터링할 수 있습니다.

## 종속성 모니터링 설정

종속성 모니터링을 가져오려면 다음을 수행해야 합니다.

* IIS 서버에서 [상태 모니터](app-insights-monitor-performance-live-website-now.md)를 사용하여 모니터링 활성화
* Azure 웹앱 또는 VM에 [Application Insights 확장](../insights-perf-analytics.md) 추가

Azure VM의 경우 Azure 제어판에서 확장 설치 또는 대부분의 컴퓨터에서 하듯이 상태 모니터 설치를 사용할 수 있습니다.

이미 배포된 웹앱에 위의 단계를 수행할 수 있습니다. 표준 종속성 모니터링을 가져오기 위해 소스 프로젝트에 Application Insights를 추가하지 않아도 됩니다.

## 종속성 성능 문제 진단

서버에서 요청 성능을 평가하려면:

![Application Insights의 응용 프로그램 개요 페이지에서 성능 타일을 클릭합니다.](./media/app-insights-dependencies/01-performance.png)

아래로 스크롤하여 요청 표를 확인합니다.

![평균 및 개수가 포함된 요청 목록](./media/app-insights-dependencies/02-reqs.png)

맨 위의 요청은 시간이 아주 오래 걸립니다. 시간이 어디에 소비되는지 확인해 보겠습니다.

개별 요청 이벤트를 보려면 해당 행을 클릭합니다.


![요청 항목 목록](./media/app-insights-dependencies/03-instances.png)

장기 실행 인스턴스를 클릭하면 세부 사항을 조사할 수 있습니다.

> [AZURE.NOTE]인스턴스를 선택하려면 약간 아래로 스크롤합니다. 파이프라인의 대기 시간은 최상위 인스턴스의 데이터가 완료되지 않았음을 의미할 수 있습니다.

이 요청과 관련된 원격 종속성 호출까지 스크롤합니다.

![원격 종속성에 대한 호출 찾기, 특별한 기간 식별](./media/app-insights-dependencies/04-dependencies.png)

이 요청을 서비스하는 데 걸린 시간은 대부분 로컬 서비스 호출에 소요된 시간인 것 같습니다.

해당 행을 선택하여 자세한 정보를 봅니다.


![해당 원격 종속성을 클릭하여 원인 식별](./media/app-insights-dependencies/05-detail.png)

세부 정보에는 문제를 진단하는 데 충분한 정보가 들어 있습니다.



## 오류

실패한 요청이 있으면 차트를 클릭합니다.

![실패한 요청 차트 클릭](./media/app-insights-dependencies/06-fail.png)

요청 유형 및 요청 인스턴스를 클릭하여 실패한 원격 종속성 호출을 찾습니다.


![요청 유형을 클릭하고, 인스턴스를 클릭하여 동일한 인스턴스의 다른 보기로 이동하고, 클릭하여 예외 세부 정보를 표시합니다.](./media/app-insights-dependencies/07-faildetail.png)


<!--Link references-->

<!---HONumber=62-->