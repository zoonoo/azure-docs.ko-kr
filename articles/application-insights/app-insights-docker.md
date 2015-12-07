<properties 
	pageTitle="Application Insights에서 Docker 호스트 모니터링" 
	description="컨테이너화된 앱의 원격 분석과 함께 Docker 호스트 성능 카운터, 이벤트 및 예외를 Application Insights에 표시할 수 있습니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# Application Insights에서 Docker 호스트 모니터링

[Docker](https://www.docker.com/) 호스트의 성능 카운터 및 예외 보고서를 Application Insights에서 차트로 표시할 수 있습니다. 호스트의 컨테이너에 [Application Insights](app-insights-overview.md) 앱을 설치하면 다른 이미지뿐만 아니라 호스트에 대한 전체 성능 카운터가 표시됩니다.

Docker를 사용하여 모든 종속성이 포함된 경량 컨테이너에 앱을 배포합니다. Docker 엔진을 실행하는 모든 호스트 컴퓨터에서 앱이 실행됩니다.

Application Insights는 Docker 호스트 및 컨테이너의 성능 및 활동을 모니터링할 수 있습니다.

![예제](./media/app-insights-docker/00.png)

물론, 컨테이너에 있는 앱의 소스를 소유한 경우 각각에 [Application Insights SDK를 설치](app-insights-java-live)하여 자세한 성능 로깅 및 사용 추적을 가져옵니다.

## Application Insights 리소스 만들기

Application Insights 리소스에서 데이터를 보고 분석합니다. 리소스는 Microsoft Azure 포털에서 호스트됩니다.

1.	[Microsoft Azure](https://azure.com)를 구독해야 합니다. 서비스를 사용하지 않는 경우 요금을 지불하지 않는 종량제 옵션이 있으며, Application Insights의 무료 계층을 사용할 수 있습니다.
2.	[Azure 포털](https://portal.azure.com)에 로그인한 다음 새 Application Insights 리소스를 만듭니다. 응용 프로그램 종류로 기타를 선택합니다. 이 선택 항목은 표시되는 차트의 초기 선택에만 영향을 주며 별로 중요하지 않습니다.

    ![예제](./media/app-insights-docker/01-new.png)
3.	언제든지 찾아보기, 모두를 사용하여 새 모니터링 리소스에 액세스할 수 있습니다. 그러나 포털로 이동할 때마다 표시되는 홈 대시보드에 앱 타일을 배치하도록 선택했을 수도 있습니다.

    이제 새 리소스를 엽니다.
4.	Docker 타일을 추가합니다. **편집**, **타일 추가**를 차례로 선택하고 갤러리에서 Docker 타일을 끕니다. 처음에는 빈 차트가 표시됩니다.

    ![예제](./media/app-insights-docker/03.png)

5. **Essentials** 드롭다운을 클릭하고 계측 키를 복사합니다. 이 키를 사용하여 SDK에 원격 분석을 보낼 위치를 알립니다.

원격 분석을 확인하기 위해 곧 돌아와야 하므로 해당 브라우저 창을 열어둡니다.


## 호스트에 Application Insights를 설치합니다.
 
이제 원격 분석을 표시할 위치가 있으므로 원격 분석을 수집하여 보낼 앱을 설정할 수 있습니다. 1. Docker 호스트에 로그인합니다. 2. Docker 허브에서 Microsoft Application Insights 이미지를 검색하고 호스트로 끌어옵니다. 3. 계측 키를 설정합니다.

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. 이미지를 실행합니다.

## 원격 분석 보기

Azure 포털에서 Application Insights 리소스로 돌아갑니다.

특히 Docker 엔진에서 다른 컨테이너가 실행되고 있는 경우 Docker 컨테이너에서 데이터가 도착하는 것을 곧 확인할 수 있습니다.

자세한 내용을 보려면 차트를 클릭합니다.

다음은 표시될 수 있는 몇 가지 보기입니다.

### 호스트별 성능 카운터, 이미지별 활동


![예제](./media/app-insights-docker/10.png)

자세한 내용을 보려면 이미지 이름을 클릭합니다. 보기를 사용자 지정하려면 차트, 그리드 제목을 차례로 클릭하거나 차트 추가를 사용합니다.

[메트릭 탐색기에 대해 자세히 알아봅니다](app-insights-metrics-explorer.md).

## 개별 이벤트


![예제](./media/app-insights-docker/12.png)

개별 이벤트를 조사하려면 [검색](app-insights-diagnostic-search.md)을 클릭합니다. 검색 및 필터링하여 원하는 이벤트를 찾습니다. 자세한 내용을 보려면 이벤트를 클릭합니다.
 
## 컨테이너 이름별 예외
 

![예제](./media/app-insights-docker/14.png)



## 질문과 대답

*Docker에서 얻을 수 없는 어떤 기능을 Application Insights가 제공하나요?*

* 컨테이너 및 이미지별로 성능 카운터의 자세한 분석 결과를 제공합니다.
* 하나의 대시보드에서 컨테이너 및 앱 데이터를 통합합니다.
* 추가 분석을 위해 데이터베이스, Power BI 또는 다른 대시보드로 [원격 분석을 내보냅니다](app-insights-export-telemetry.md).

*앱 자체에서 원격 분석을 가져오려면 어떻게 해야 하나요?*

* Application Insights SDK를 앱에 설치합니다. [Java 웹앱](app-insights-java-get-started.md), [Windows 웹앱](app-insights-asp-net.md)에 대한 방법을 알아봅니다.

<!---HONumber=AcomDC_1125_2015-->