<properties
	pageTitle="웹앱에 대한 프로덕션에서 테스트 시작"
	description="Azure 앱 서비스 웹앱에서 프로덕션(TiP) 기능의 테스트에 대해 알아봅니다."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="cephalin"/>

# 웹앱에 대한 프로덕션에서 테스트 시작

프로덕션에서 테스트 또는 라이브 고객 트래픽을 사용하는 웹앱 라이브 테스트는 앱 개발자가 [민첩한 개발](https://en.wikipedia.org/wiki/Agile_software_development) 방법론에 점차 통합하는 테스트 전략입니다. 테스트 환경에서 합성된 데이터와 달리 프로덕션 환경의 라이브 사용자 트래픽을 사용하는 앱의 품질을 테스트할 수 있습니다. 실제 사용자에게 새 앱을 노출하여 앱을 배포한 후에 앱에서 발생할 수 있는 실제 문제에 대한 알림을 받을 수 있습니다. 앱의 기능, 성능 및 값을 확인하고 볼륨, 속도 및 다양한 실제 사용자 트래픽을 업데이트하며 이는 테스트 환경에서 대략적으로 구하지 않습니다.

## 앱 서비스 웹앱에서 트래픽 라이팅

[Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)의 트래픽을 라우팅 기능을 사용하여 하나 이상의 [배포 슬롯](web-sites-staged-publishing.md)에 라이브 사용자 트래픽의 일부를 지원하고 [Azure Application Insights](/services/application-insights/)를 사용하여 앱을 분석하며 또는 [Azure HDInsight](/services/hdinsight/) 또는 [New Relic](/marketplace/partners/newrelic/newrelic/)같은 타사 도구로 변경을 확인합니다. 예를 들어 앱 서비스를 사용하여 다음과 같은 시나리오를 구현할 수 있습니다.

- 사이트 전체를 배포하기 전에 업데이트에서 기능 버그를 검색하거나 성능 병목 지점을 정확히 짚어봅니다.
- 베타 앱에서 사용성 메트릭을 측정하여 변경된 "통제된 테스트 항공편"을 수행합니다.
- 오류가 발생하는 경우 새 업데이트까지 점차적으로 규모를 확장하고 현재 버전에 정상적으로 돌아옵니다.
- 여러 배포 슬롯에 [A/B 테스트](https://en.wikipedia.org/wiki/A/B_testing) 또는 [다변량 테스트](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing)를 실행하여 앱의 비즈니스 결과를 최적화합니다.

### 웹앱에서 트래픽 라우팅을 사용하는 데 대한 요구 사항

- 웹앱은 여러 배포 슬롯에 대해 필요한 대로 **표준** 또는 **프리미엄** 계층에서 실행해야 합니다.
- 제대로 작동하려면 트래픽 라우팅은 쿠키를 사용자의 브라우저에서 사용하도록 해야 합니다. 트래픽 라우팅은 클라이언트 세션 수명에 대한 배포 슬롯에 클라이언트 브라우저를 고정하는 데 쿠키를 사용합니다.
- 트래픽 라우팅은 Azure PowerShell cmdlet를 통해 고급 TiP 시나리오를 지원합니다.

## 배포 슬롯에 라우팅 트래픽 세그먼트

모든 TiP 시나리오의 기본 수준에서 비프로덕션 배포 슬롯에 미리 정의된 비율의 라이브 트래픽을 라우팅합니다. 이렇게 하려면 다음 단계를 수행하세요.

>[AZURE.NOTE] 여기에서 단계는 [비-프로덕션 배포 슬롯](web-sites-staged-publishing.md) 및 웹앱 콘텐츠가 이미 [배포](web-sites-deploy.md)되었기를 원합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 웹앱의 블레이드에서 **설정** > **트래픽 라우팅**을 클릭합니다. ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. 라우트하려는 슬롯을 **저장**하면 트래픽 라우팅을 하려는 슬롯 및 원하는 총 트래픽의 비율을 선택합니다.

	![](./media/app-service-web-test-in-production/02-select-slot.png)

4. 배포 슬롯의 블레이드로 이동합니다. 이제 라우팅된 라이브 트래픽을 표시해야 합니다.

	![](./media/app-service-web-test-in-production/03-traffic-routed.png)

트래픽 라우팅이 구성되면 클라이언트의 지정된 백분율은 비-프로덕션 슬롯에 임의로 라우팅됩니다. 그러나 클라이언트가 특정 슬롯에 자동으로 라우팅되면 해당 슬롯에 클라이언트 세션의 수명 동안 "고정"되어야 합니다. 이 작업은 쿠키를 사용하여 사용자 세션을 고정합니다. HTTP 요청을 조사하여 모든 후속 요청에서 `TipMix` 쿠키를 찾을 수 있습입니다.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## 특정 슬롯에 클라이언트 요청을 강제로

자동 트래픽 라우팅 외에도 앱 서비스는 특정 슬롯에 요청을 라우팅할 수 있습니다. 사용자가 베타 앱에 참여 또는 탈퇴를 선택할 수 있도록 하려는 경우 유용합니다. 이를 수행하기 위해 `x-ms-routing-name` 쿼리 매개 변수를 사용합니다.

`x-ms-routing-name`을 사용하여 사용자를 특정 슬롯에 다시 라우팅하려면 슬롯이 트래픽 라우팅 목록에 이미 추가되도록 합니다. 명시적으로 슬롯에 라우팅하려 하기 때문에 설정한 실제 라우팅 백분율은 문제가 되지 않습니다. 원하는 경우 베타 앱에 액세스하기 위해 사용자가 클릭할 수 있는 "베타 링크"를 만들 수 있습니다.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### 베타 앱에서 사용자 탈퇴

예를 들어 사용자가 베타 앱에 참여하지 않도록 하려면 웹 페이지에 이 링크를 둘 수 있습니다.

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

문자열 `x-ms-routing-name=self`는 프로덕션 슬롯을 지정합니다. 클라이언트 브라우저가 링크에 액세스하면 프로덕션 슬롯으로 리디렉션될 뿐만 아니라 모든 후속 요청이 프로덕션 슬롯에 세션을 고정하는 `x-ms-routing-name=self` 쿠키를 포함합니다.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### 베타 앱에서 사용자 참여

예를 들어 사용자가 베타 앱에 참여하게 하려면 비-프로덕션 슬롯의 이름에 동일한 쿼리 매개 변수를 설정합니다.

		<webappname>.azurewebsites.net/?x-ms-routing-name=staging

## 추가 리소스 ##

-   [Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경 설정](web-sites-staged-publishing.md)
-	[Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](app-service-deploy-complex-application-predictably.md)
-   [Azure 앱 서비스를 사용하여 Agile 소프트웨어 개발](app-service-agile-software-development.md)
-	[웹 앱에서 DevOps 환경을 효과적으로 사용하기](app-service-web-staged-publishing-realworld-scenarios.md)

<!---HONumber=AcomDC_0803_2016-->