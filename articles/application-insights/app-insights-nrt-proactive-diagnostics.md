<properties 
	pageTitle="Application Insights에서 거의 실시간인 자동 관리 진단" 
	description="NRT 사전 진단은 서버 응답 시간이 비정상적인 동작을 나타낼 경우 자동으로 알립니다. 구성이 필요하지 않습니다." 
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
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# 거의 실시간인 자동 관리 진단

*이 기능은 초기 평가판에 있습니다.*

[ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)으로 *의견을 보내 주세요.*

[Visual Studio Application Insights](app-insights-overview.md)는 웹앱의 실패한 요청 속도가 크게 증가한 경우 거의 실시간에 자동으로 알립니다. 문제를 심사하고 진단할 수 있도록 실패한 요청 및 관련된 원격 분석의 특성에 대한 분석이 알림 영역에서 제공됩니다. 또한 추가 진단을 위해 Application Insights 포털에 링크가 제공됩니다. 기능이 기계 학습 알고리즘을 사용하여 초기 일반 실패율을 예측하려면 설정 또는 구성이 필요하지 않습니다. 작동하기 위해 트래픽의 특정 최소 볼륨이 필요합니다.

샘플 경고는 다음과 같습니다.

![실패에 대한 클러스터 분석을 보여주는 샘플 인텔리전트 경고](./media/app-insights-nrt-proactive-diagnostics/010.png)

실패 경고를 가져오려면 [ASP.NET용 Application Insights](app-insights-asp-net.md) 또는 [Java 웹앱용 Application Insights](app-insights-java-get-started.md)를 설정해야 합니다.

## 작동 방법

거의 실시간인 자동 관리 진단은 앱, 특히 실패한 요청 속도에서 받은 원격 분석을 모니터링합니다. 이 메트릭은 일반적으로 400개 이상의 응답 코드를 반환한 HTTP 요청 수를 나타냅니다(사용자 지정 코드를 [필터](app-insights-api-filtering-sampling.md#filtering)에 작성하지 않거나 고유한 [TrackRequest](app-insights-api-custom-events-metrics.md#track-request) 호출을 생성하지 않는 한).

이 메트릭의 이전 동작에 따르면 서비스는 예상되어야 하는 값의 범위를 예측합니다. 실제 값이 상당히 높은 경우 경고를 생성합니다.

경고가 발생하는 경우 서비스는 실패의 특성을 만드는 값의 패턴을 확인하기 위해 요청의 여러 차원에 대한 클러스터 분석을 수행합니다. 위 예제에서 분석은 대부분의 오류가 특정 요청 이름에 대한 것임을 발견했지만 오류가 호스트 또는 서버 인스턴스와 독립적이라는 점도 알아냈습니다.

그런 다음 분석기는 해당 요구와 연관된 추적 로그의 예제와 함께 확인된 클러스터의 요청과 관련된 예외 및 종속성 오류를 찾습니다.

다르게 구성하지 않으면 결과 분석은 전자 메일로 전송됩니다.

[수동으로 설정한 경고](app-insights-alerts.md)와 마찬가지로 경고의 상태를 검사하고 Application Insights 리소스의 경고 블레이드에서 구성할 수 있습니다. 하지만 다른 경고와 달리 적응 실패 경고를 설정하거나 구성할 필요가 없습니다. 원하는 경우 해당 대상 전자 메일 주소를 사용하지 않거나 변경할 수 있습니다.

## 경고를 수신하는 경우

경고는 실패한 요청 속도에서 변칙이 검색되었음을 나타냅니다. 앱 또는 해당 환경에 문제가 있을 가능성이 있습니다. 방금 업로드한 새 버전도 잘 작동하지 않거나 데이터베이스 또는 외부 리소스와 같은 종속성 제대로 작동하지 않을 수 있습니다.

영향을 받는 요청의 백분율 및 사용자 수에서 문제가 얼마나 긴급한지 결정할 수 있습니다.

대부분의 경우 요청 이름, 예외, 종속성 및 제공된 다른 데이터에서 신속하게 문제를 진단할 수 있습니다.

하지만 더 자세히 조사해야 하는 경우 각 섹션의 링크는 관련 요청, 예외, 종속성 또는 추적을 필터링하는 [검색 페이지](app-insights-diagnostic-search.md)로 바로 이동합니다. 또는 [Azure 포털](https://portal.azure.com)을 열고 앱에 대한 Application Insights 리소스에 이동하며 오류 블레이드를 열 수 있습니다.

## 최근 경고 검토

포털에서 경고를 검토하려면 **설정, 작업 이벤트**를 엽니다.

![경고 요약](./media/app-insights-nrt-proactive-diagnostics/040.png)

경고를 클릭하여 전체 세부 정보를 봅니다.


## 경고 구성 

> **구성 UX를 아직 사용할 수 없습니다.**
> 
> 대신 [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)으로 전자 메일을 보내주세요.
>
> 작동 방법은 다음과 같습니다.

경고 페이지를 엽니다. 적응 실패 경고는 수동으로 설정된 경고와 함께 포함되며 현재 경고 상태인지 여부를 볼 수 있습니다.

![개요 페이지에서 경고 타일을 클릭합니다. 또는 메트릭 페이지에서 경고 단추를 클릭합니다.](./media/app-insights-nrt-proactive-diagnostics/020.png)

경고를 클릭하여 구성합니다.

![구성](./media/app-insights-nrt-proactive-diagnostics/030.png)

적응 실패 경고를 비활성화할 수 있지만 삭제할 수 없습니다(또는 다른 적응 실패 경고를 만들 수 없음).


## 차이점은 무엇입니까...

NRT 사전 진단은 Application Insights의 다른 유사하지만 고유한 기능을 보완합니다.

* [메트릭 경고](app-insights-alerts.md)는 사용자에 의해 설정되며 CPU 점유율, 요청 속도, 페이지 로드 시간 등과 같은 광범위한 메트릭을 모니터링할 수 있습니다. 예를 들어 더 많은 리소스를 추가해야 하는 경우 경고하는 데 사용할 수 있습니다. 반면, NRT 사전 진단은 중요한 메트릭의 작은 범위를 다루며(현재 실패한 요청 속도만 해당) 웹앱의 실패한 요청 속도가 웹앱의 일반적인 동작에 비해 크게 증가하면 거의 실시간으로 알리도록 디자인되었습니다.
* 또한 [사전 감지](app-insights-proactive-detection.md)는 컴퓨터 인텔리전스를 사용하여 메트릭에서 특수한 패턴을 검색하고 사용자에 의한 구성은 필요하지 않습니다. 하지만 NRT 사전 진단과 달리 사전 감지의 목적은 예를 들어 특정 형식의 브라우저에 있는 특정 페이지에서 잘못 제공될 수 있는 사용 현황 다기관의 세그먼트를 찾는 것입니다. 분석은 매일 수행되고 결과가 있으면 경고보다 긴급하지 않을 수 있습니다. 이와 반대로 NRT 사전 진단에 대한 분석은 들어오는 원격 분석에서 지속적으로 수행되고 서버 실패율이 예상보다 높은 경우 몇 분 내에 알립니다.


## 피드백을 제공해주세요.

*이 기능은 초기 평가판에 있습니다. 여러분의 의견을 기다리고 있습니다. * [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)으로 의견을 보내주시거나 *또는 경고 메시지에서 사용자 의견 링크를 클릭합니다.*

<!---HONumber=AcomDC_0218_2016-->