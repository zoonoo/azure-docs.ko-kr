<properties 
	pageTitle="Application Insights의 가격 책정 및 할당량 관리" 
	description="필요한 가격 계획 선택" 
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
	ms.date="06/09/2015" 
	ms.author="awills"/>

# Application Insights의 가격 책정 및 할당량 관리

*Application Insights는 미리 보기 상태입니다.*

[Visual Studio Application Insights][start]의 [가격][pricing]은 응용 프로그램 당 데이터 볼륨을 기반으로 합니다. 기능의 일부만 제한하고 대부분을 사용할 수 있도록 하는 실질적인 무료 계층이 있습니다.

각 Application Insights 리소스는 별도의 서비스로 요금이 부과되고 Azure 구독에 대한 청구서에 추가됩니다.

[가격 체계를 참고하십시오][pricing].

## Application Insights 리소스에 대한 할당량 및 가격 책정 계획 검토

응용 프로그램 리소스의 설정에서 할당량 + 블레이드 가격을 열 수 있습니다.

![설정, 할당량 + 가격 선택](./media/app-insights-pricing/01-pricing.png)

## 할당량은 어떻게 작동합니까?

* 응용 프로그램이 달력의 매월에 Application Insights에 대한 원격 분석을 지정된 수량까지 보낼 수 있습니다. 실제 숫자에 대한 [가격 체계][pricing]를 참고하십시오. 
* 할당량은 택하고자 하는 가격 책정 계층에 따라 달라집니다.
* 할당량은 매월 첫 날에 자정 UTC부터 계산 됩니다.
* 데이터 요소 차트는 이번달 모두 사용된 할당량이 어느 정도인지를 보여줍니다.
* 할당량은 *데이터 요소*로 측정됩니다. 단일 데이터 요소는 코드에서 또는 표준 원격 분석 모듈 중 하나가 명시적으로 호출하든지, 추적 방법 중 하나를 호출합니다. 진단 검색에서 보이는 각 행은 데이터 요소입니다. 성능 카운터와 같은 각 메트릭의 측정은 데이터 요소입니다. 
* *세션 데이터*는 할당량 계산에서 제외됩니다. 여기에 사용자, 세션, 환경 및 장치 데이터의 수가 포함됩니다.


## 초과분

응용 프로그램이 월간 할당량보다 더많이 보내는 경우에 다음을 수행할 수 있습니다:

* 추가 데이터의 지급 자세한 내용은 [가격 체계][pricing]를 참고하십시오. 사전에 이 옵션을 선택할 수 있습니다. 이 옵션은 무료 가격 책정 계층에서 사용할 수 없습니다.
* 가격 책정 계층을 업그레이드하십시오.
* 아무 작업도 수행하지 않습니다. 세션 데이터는 계속해서 기록될 수 있지만 진단 검색 또는 메트릭 탐색기에 다른 데이터가 표시되지 않습니다.

## 무료 Premium 평가판

새 Application Insights 리소스를 처음 만들 때 무료 계층에서 시작합니다.

언제든지 30일 무료 Premium 평가판으로 전환할 수 있습니다. 이것은 프리미엄 평가판의 이점을 가져다줍니다. 다른 계층을 명시적으로 선택하지 않으면, 이전에 어떤 계층인지에 관계 없이 30일 후 자동으로 되돌아갑니다 평가 기간 중 언제든지 원하는 계층을 선택해도 30일 기간 끝까지 무료 평가판을 얻을 수 있습니다.


## 할당량이 어떻게 사용됩니까?

가격 책정 블레이드의 아래쪽에 있는 차트는 데이터 지점 유형별로 그룹화 된 응용 프로그램의 데이터 지점 사용을 보여줍니다.

![가격 책정 블레이드의 맨 아래](./media/app-insights-pricing/03-allocation.png)

자세한 내용을 보려면 차트를 클릭하거나 자세한 시간 범위는 차트를 가로질러 끕니다.

## Azure 구독용 청구서를 검토합니다.

Application Insights 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure 포털의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.

![측면 메뉴에서 대금 청구를 선택합니다.](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=July15_HO3-->