<properties 
	pageTitle="Azure 끝점에서 Application Insights 가용성 테스트로 마이그레이션" 
	description="클래식 Azure 끝점 모니터링 테스트를 Application Insights 가용성 테스트로 이동할 것입니다. 2016년 8월 22일부터 한 주 동안 전환할 예정입니다."
	services="application-insights" 
    documentationCenter=""
	authors="soubhagyadash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="awills"/>
 
# Azure 끝점 모니터링에서 Application Insights 가용성 테스트로 이동

Azure 앱에 대한 [끝점 모니터링](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/)을 사용합니까? 2016년 8월 22일부터 한 주 동안 새롭고 더욱 강력한 [가용성 테스트](app-insights-monitor-web-app-availability.md)로 교체할 예정입니다. 이미 일부 새 테스트를 만들었지만 2016년 8월 22일까지는 사용할 수 없습니다.

새 테스트를 편집하고 원하는 경우 스위치를 직접 수행할 수 있습니다. Default-ApplicationInsights-CentralUS 리소스 그룹의 [Azure 포털](https://portal.azure.com)에서 찾을 수 있습니다.


## 가용성 테스트는 무엇입니까?

가용성 테스트는 전세계 최대 16개 위치에서 HTTP 요청을 전송하여(단일 ping 테스트 또는 Visual Studio 웹 테스트) 웹 사이트 또는 서비스가 가동되고 실행 중인지를 지속적으로 확인하는 Azure의 기능입니다.

[클래식 Azure 포털](https://manage.windowsazure.com)에서 이 테스트는 끝점 모니터링이라고 했습니다. 범위에서 제한적이었습니다. 새 가용성 테스트는 성능이 상당히 개선되었습니다.

* 최대 10개의 Visual Studio 웹 테스트 또는 Application Insights 리소스당 ping 테스트.
* 웹 앱에 테스트 요청을 전송하기 위한 전세계 최대 16개의 위치. 테스트 성공 조건의 강화된 제어.
* Azure 웹 앱 외의 모든 웹 사이트 또는 서비스를 테스트합니다.
* 테스트 재시도: 일시적인 네트워크 문제로 인한 가양성 경고를 줄입니다.
* Webhook은 경고에 대한 HTTP POST 알림을 받을 수 있습니다.

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

[여기에서 가용성 테스트](app-insights-monitor-web-app-availability.md)에 대해 자세히 알아봅니다.

가용성 테스트는 모든 웹 응용 프로그램에 대한 확장 가능한 분석 서비스인 [Visual Studio Application Insights](app-insights-overview.md)의 일부입니다.



## 따라서 내 끝점 테스트에 어떤 변동 사항이 있습니까?

* 새 Application Insights 가용성 테스트에 끝점 모니터링 테스트를 복사했습니다.
* 새 가용성 테스트는 현재 비활성화되었으며 이전 끝점 테스트는 여전히 실행 중입니다.
* 경고 규칙은 마이그레이션되지 *않았습니다*. 새 테스트는 기본 규칙으로 초기에 설정되었습니다.
 * 두 개 이상의 위치에서 5분 내에 오류를 보고할 때 트리거합니다.
 * 구독 관리자에게 메일을 보냅니다.

[Azure 포털](https://portal.azure.com)에서 "Default-ApplicationInsights-CentralUS" 리소스 그룹에서 마이그레이션된 테스트를 찾을 수 있습니다. 테스트 이름은 접두사 "마이그레이션된-"이 붙습니다.

## 수행하려면 무엇이 필요한가요?

* 어떤 이유로 테스트 마이그레이션이 누락되더라도 새 가용성 테스트를 [쉽게 설정할 수 있습니다](app-insights-monitor-web-app-availability.md).

### 옵션 A: 아무 작업도 수행하지 않습니다. 우리에게 맡겨 둡니다.

**2016년 8월 22일부터 한 주 동안** 다음 작업을 수행합니다.

* 이전의 끝점 테스트를 비활성화합니다.
* 마이그레이션된 가용성 테스트를 활성화합니다.

### 옵션 B: 새 테스트를 관리 및/또는 활성화합니다.

* 새 [Azure 포털](https://portal.azure.com)에서 새 가용성 테스트를 검토하고 편집합니다.
 * 트리거 조건 검토
 * 메일 받는 사람 검토
* 새 테스트 활성화
* [클래식 포털](https://manage.windowsazure.com)에서 이전의 끝점 테스트를 삭제합니다. 중복된 경고를 방지하고 웹 사이트에서 테스트 트래픽의 부하를 줄이려면 이를 수행하는 것이 좋습니다. 그렇지 않은 경우 2016년 8월 22일에 삭제합니다.


### 옵션 C: 옵트아웃

가용성 테스트를 사용하지 않으려는 경우 [Azure 포털](https://portal.azure.com)에서 삭제할 수 있습니다. 알림 전자 메일의 맨 아래에 구독 취소 링크가 있습니다.

2016년 8월 22일에는 이전 끝점 테스트도 삭제합니다.

## 새 테스트를 편집하는 방법은 무엇입니까?

[Azure 포털](https://portal.azure.com)에 로그인하고 ' 마이그레이션된-' 웹 테스트를 찾습니다.

![리소스 그룹, Default-ApplicationInsights-CentralUS를 선택하고 '마이그레이션된' 테스트를 엽니다.](./media/app-insights-migrate-azure-endpoint-tests/20.png)

테스트를 편집 및/또는 활성화합니다.

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)


## 이것이 발생하는 이유는?

향상된 서비스입니다. 이전 끝점 서비스는 훨씬 제한적이었습니다. Azure VM 또는 웹 앱에 3개의 지리적 위치에서 간단한 ping 테스트를 위한 두 개의 URL을 제공할 수 있습니다. 새 테스트는 최대 16개의 위치에서 다중 단계 웹 테스트를 실행할 수 있고 하나의 응용 프로그램에 대해 최대 10개의 테스트를 지정할 수 있습니다. 모든 URL을 테스트할 수 있습니다. Azure 사이트일 필요가 없습니다.

새 테스트는 테스트하는 웹 앱 또는 VM에서 별도로 구성됩니다.

새 포털을 사용하는 동안 계속해서 제어할 수 있도록 테스트를 마이그레이션하는 중입니다.

## Application Insights란?

새 가용성 테스트는 [Visual Studio Application Insights](app-insights-overview.md)의 일부입니다. 다음은 [2분 분량의 비디오](http://go.microsoft.com/fwlink/?LinkID=733921)입니다.

## 새 테스트에 대해 비용을 지불합니까?

마이그레이션된 테스트는 기본 무료 계획의 Application Insights 리소스에서 설정되어 있습니다. 이를 통해 최대 500만 개의 데이터 요소의 컬렉션이 가능합니다. 테스트에서 현재 사용할 데이터 볼륨을 쉽게 다룹니다.

물론 Application Insights를 선택하고 더 많은 가용성 테스트를 만들거나 더 많은 해당 성능 모니터링 및 진단 기능을 채택하는 경우 더 많은 데이터 요소를 생성합니다. 그러나 결과는 무료 계획에 대한 할당량에 도달할 수 있게 됩니다. 표준 또는 프리미엄 계획에서 선택하지 않는 한 청구를 받지 않습니다.

[Application Insights 가격 책정 및 할당량 모니터링에 대해 자세히 알아봅니다](app-insights-pricing.md).

## 마이그레이션되는 것과 마이그레이션되지 않는 것은 무엇입니까?

이전 끝점 테스트에서 유지됩니다.

* 테스트할 끝점 URL.
* 요청이 전송되는 지리적 위치.
* 테스트 빈도는 5분 동안 유지됩니다.
* 테스트 시간 제한은 30초 동안 유지됩니다.

마이그레이션되지 않습니다.

* 경고 트리거 규칙. 하나의 위치에서 5분 내에 오류를 보고하는 경우 설정한 규칙은 트리거합니다.
* 경고 받는 사람. 구독 소유자 및 공동 소유자에게 경고 메일이 전송됩니다.

## 새 테스트를 찾는 방법은 무엇입니까?

원하는 경우 이제 새 테스트를 편집할 수 있습니다. [Azure 포털](https://portal.azure.com)에 로그인하고 **리소스 그룹**을 열고 **Default-ApplicationInsights-CentralUS**를 선택합니다. 해당 그룹에서 새 웹 테스트를 찾을 수 있습니다. [새 가용성 테스트에 대해 알아봅니다](app-insights-monitor-web-app-availability.md).

이 주소: App Insights 경고(ai-noreply@microsoft.com)에서 새 경고 메일이 전송됩니다.

## 아무 작업도 수행하지 않으면 어떻게 됩니까?

옵션 A가 적용됩니다. 마이그레이션된 테스트를 활성화하고 위에서 설명한 것처럼 기본 경고 규칙을 설정합니다. 위에서 설명한 것처럼 모든 사용자 지정 경고 규칙, 받는 사람을 추가해야 합니다. 레거시 끝점 모니터링 테스트를 비활성화합니다.

## 이에 대한 피드백을 어디에서 제공할 수 있습니까? 

여러분의 의견에 감사드립니다. [메일을 보내주세요](mailto:vsai@microsoft.com).

<!---HONumber=AcomDC_0727_2016-->