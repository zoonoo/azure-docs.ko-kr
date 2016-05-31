<properties
	pageTitle="Azure API 관리에서 첫 번째 API 관리 | Microsoft Azure"
	description="API를 만들고, 작업을 추가하고, API 관리를 시작하는 방법에 대해 알아봅니다."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/25/2016"
	ms.author="sdanie"/>

# Azure API 관리에서 첫 번째 API 관리

## <a name="overview"> </a>개요

이 가이드에서는 빠르게 Azure API 관리를 사용하기 시작하고 첫 번째 API 호출을 만드는 방법을 보여 줍니다.

## <a name="concepts"> </a>Azure API 관리란?

Azure API 관리를 통해 원하는 백 엔드를 사용하고 해당 백 엔드에 따라 모든 기능을 갖춘 API 프로그램을 시작할 수 있습니다.

일반적인 시나리오는 다음과 같습니다.

* 액세스 API 키로 액세스 권한을 제어하고 제한을 사용하거나 JWT 토큰 유효성 검사 등의 고급 보안 정책을 사용하여 DoS 공격을 방지하는 **모바일 인프라 보안**
* 개발자 포털을 통해 파트너 등록을 빠르게 제공하고 API 외관을 구축하여 파트너 소비에 아직 적합하지 않은 내부 구현에서 분리하는 **ISV 파트너 시스템 사용**
* API 게이트웨이와 백 엔드 간의 보안된 채널에 모두 따라 조직에 중앙 위치를 제공하여 가용성 및 API의 최신 변경 내용에 대해 통신하고 조직 계정에 따라 액세스 권한을 제어하는 **내부 API 프로그램 실행**


시스템은 다음 구성 요소로 구성되어 있습니다.

* **API 게이트웨이**는 다음 작업을 수행하는 끝점입니다.
  * API 호출 수락 후 백 엔드로 라우팅합니다.
  * API 키, JWT 토큰, 인증서 및 기타 자격 증명을 확인합니다.
  * 사용 할당량 및 속도 제한을 적용합니다.
  * 코드 수정 없이 즉석에서 API를 변환합니다.
  * 설정된 위치에 백 엔드 응답을 캐시합니다.
  * 분석용으로 호출 메타데이터를 기록합니다.

* **게시자 포털**은 API 프로그램이 설치되는 관리 인터페이스입니다. 다음 작업을 수행하는 데 사용합니다.
	* API 스키마를 정의하거나 가져옵니다.
	* 제품에 API를 패키징합니다.
	* API에서 할당량 또는 변환 등의 정책을 설정합니다.
	* 분석의 정보를 활용합니다.
	* 사용자를 관리합니다.

* **개발자 포털**은 개발자를 위한 기본 웹 서비스 역할을 하며, 여기서 개발자는 다음 작업을 수행할 수 있습니다.
	* API 설명서를 읽습니다.
	* 대화형 콘솔을 통해 API를 사용해 봅니다.
	* 계정을 만들고 구독하여 API 키를 받습니다.
	* 자신의 사용량에 대한 분석에 액세스합니다.


## <a name="create-service-instance"> </a>API 관리 인스턴스 만들기

>[AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][]을 참조하세요.

API 관리 작업의 첫 번째 단계는 서비스 인스턴스를 만드는 것입니다. [Azure 클래식 포털][]에 로그인하고 **새로 만들기**, **앱 서비스**, **API 관리**, **만들기**를 클릭합니다.

![API 관리 새 인스턴스][api-management-create-instance-menu]

**URL**에서 서비스 URL에 사용할 고유한 하위 도메인 이름을 지정합니다.

서비스 인스턴스에 대해 원하는 **구독** 및 **지역**을 선택합니다. 선택한 후에 **다음** 단추를 클릭합니다.

![새 API 관리 서비스][api-management-create-instance-step1]

**조직 이름**으로 **Contoso Ltd.**를 입력하고 **관리자 메일** 필드에 메일 주소를 입력합니다.

>[AZURE.NOTE] 이 전자 메일 주소는 API 관리 시스템에서 알림을 보내는 데 사용됩니다. 자세한 내용은 [Azure API 관리에서 알림 및 메일 템플릿을 구성하는 방법][]을 참조하세요.

![새 API 관리 서비스][api-management-create-instance-step2]

API 관리 서비스 인스턴스는 Developer, Standard, Premium의 세 가지 계층으로 제공됩니다. 기본적으로 새 API 관리 서비스 인스턴스는 개발자 계층에서 만들어집니다. Standard 또는 Premium 계층을 선택하려면 **고급 설정** 확인란을 선택하고 다음 화면에서 원하는 계층을 선택합니다.

>[AZURE.NOTE] 개발자 계층은 고가용성이 문제가 되지 않는 개발, 테스트 및 파일럿 API 프로그램용입니다. Standard 및 Premium 계층에서는 예약 단위 수를 확장하여 더 많은 트래픽을 처리할 수 있습니다. Standard 및 Premium 계층은 API 관리 서비스에 가장 많은 처리 능력과 성능을 제공합니다. 임의 계층을 사용하여 이 자습서를 완료할 수 있습니다. API 관리 계층에 대한 자세한 내용은 [API 관리 가격][]을 참조하세요.

확인란을 클릭하여 서비스 인스턴스를 만듭니다.

![새 API 관리 서비스][api-management-instance-created]

서비스 인스턴스를 만든 후 다음 단계는 API를 만들거나 가져오는 것입니다.

## <a name="create-api"> </a>API 가져오기

API는 클라이언트 응용 프로그램에서 호출할 수 있는 작업 집합으로 구성됩니다. API 작업은 기존 웹 서비스로 프록시 처리됩니다.

API를 만들고 작업을 수동으로 추가하거나 API를 가져올 수 있습니다. 이 자습서에서는 Microsoft에서 제공하고 Azure에서 호스트하는 샘플 계산기 웹 서비스에 대한 API를 가져옵니다.

>[AZURE.NOTE] API를 만들고 작업을 수동으로 추가하는 방법에 대해서는 [API를 만드는 방법](api-management-howto-create-apis.md) 및 [API에 작업을 추가하는 방법](api-management-howto-add-operations.md)을 참조하세요.

API는 Azure 클래식 포털을 통해 액세스할 수 있는 게시자 포털에서 구성됩니다. 게시자 포털에 연결하려면 API 관리 서비스에 대해 Azure 클래식 포털에서 **관리**를 클릭합니다.

![게시자 포털][api-management-management-console]

계산기 API를 가져오려면 왼쪽의 **API 관리 메뉴**에서 **API**를 클릭한 다음 **API 가져오기**를 클릭합니다.

![API 가져오기 단추][api-management-import-api]

다음 단계를 수행하여 계산기 API를 구성합니다.

1. **URL에서**를 클릭하고 **사양 문서 URL** 텍스트 상자에 **http://calcapi.cloudapp.net/calcapi.json**을 입력하고 **Swagger** 라디오 단추를 클릭합니다.
2. **웹 API URL 접미사** 텍스트 상자에 **calc**를 입력합니다.
3. **제품(선택 사항)** 상자를 클릭하고 **Starter**를 선택합니다.
4. **저장**을 클릭하여 API를 가져옵니다.

![새 API 추가][api-management-import-new-api]

>[AZURE.NOTE] **API 관리**는 현재 가져오기에 대한 Swagger 문서의 1.2 및 2.0 버전을 지원합니다. [Swagger 2.0 사양](http://swagger.io/specification)이 `host`, `basePath` 및 `schemes` 속성이 선택 사항이라고 선언하더라도 Swagger 2.0 문서는 해당 속성을 **반드시** 포함해야 합니다. 그렇지 않으면 가져오지 않습니다.

API를 가져오면 API에 대한 요약 페이지가 게시자 포털에 표시됩니다.

![API 요약][api-management-imported-api-summary]

API 섹션에는 여러 탭이 있습니다. **요약** 탭은 API에 대한 기본 메트릭과 정보를 표시합니다. [설정](api-management-howto-create-apis.md#configure-api-settings) 탭은 API에 대한 구성을 보고 편집하는 데 사용됩니다. [작업](api-management-howto-add-operations.md) 탭은 API의 작업을 관리하는 데 사용됩니다. **보안** 탭은 기본 인증 또는 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 사용하여 백엔드 서버에 대한 게이트웨이 인증을 구성하고 [OAuth 2.0을 사용하여 사용자 권한 부여](api-management-howto-oauth2.md)를 구성하는 데 사용될 수 있습니다. **문제** 탭은 API를 사용하는 개발자가 보고한 문제를 보는 데 사용됩니다. **제품** 탭은 이 API를 포함하는 제품을 구성하는 데 사용됩니다.

기본적으로 각 API 관리 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.

-	**Starter**
-	**무제한**

이 자습서에서는 기본 계산기 API를 가져온 경우 해당 API를 Starter 제품에 추가했습니다.

API를 호출하려면 개발자는 먼저 제품에 대한 액세스 권한을 주는 제품을 구독해야 합니다. 개발자가 개발자 포털에서 제품을 구독할 수 있습니다. 또는 관리자가 게시자 포털에서 개발자가 제품에 구독하도록 만들 수 있습니다. 이 자습서의 이전 단계에서 API 관리 인스턴스를 만들었기 때문에 현재 관리자이므로 기본적으로 모든 제품에 구독되어 있습니다.

## <a name="call-operation"> </a>개발자 포털에서 작업 호출

개발자 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. 이 자습서에서는 기본 계산기 API의 **두 정수 추가** 작업을 호출합니다. 게시자 포털의 오른쪽 위에 있는 메뉴에서 **개발자 포털**을 클릭합니다.

![개발자 포털][api-management-developer-portal-menu]

맨 위 메뉴에서 **API**를 클릭하고 **기본 계산기**를 클릭하여 사용할 수 있는 작업을 확인합니다.

![개발자 포털][api-management-developer-portal-calc-api]

API 및 작업과 함께 가져온 매개 변수와 샘플 설명을 참고하세요. 이는 본 작업을 사용하는 개발자에게 설명서를 제공합니다. 작업을 수동으로 추가하는 경우 이러한 설명을 추가할 수도 있습니다.

**두 정수 추가** 작업을 호출하려면 **시도**를 클릭합니다.

![시도][api-management-developer-portal-calc-api-console]

매개 변수에 대한 값을 입력하거나 기본값을 유지한 다음 **보내기**를 클릭합니다.

![HTTP Get][api-management-invoke-get]

작업 호출 후에는 개발자 포털에 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

![응답][api-management-invoke-get-response]

## <a name="view-analytics"> </a>분석 보기

기본 계산기에 대한 분석을 보려면 개발자 포털의 오른쪽 위에 있는 메뉴에서 **관리**를 선택하여 게시자 포털로 다시 전환합니다.

![관리][api-management-manage-menu]

게시자 포털의 기본 보기는 **대시보드**이며 여기서 API 관리 인스턴스의 개요를 확인할 수 있습니다.

![대시보드][api-management-dashboard]

**기본 계산기** 차트 위로 마우스를 가져가서 주어진 기간의 API 사용 관련 특정 메트릭을 표시합니다.

>[AZURE.NOTE] 차트에 아무런 줄도 표시되지 않는 경우 개발자 포털로 다시 전환하여 API를 호출한 후 몇 분 정도 기다렸다가 대시보드로 다시 돌아옵니다.

**자세히 보기**를 클릭하여 API에 대한 요약 페이지를 표시합니다. 여기에는 더 크게 표시되는 메트릭 버전도 포함됩니다.

![분석][api-management-mouse-over]

![요약][api-management-api-summary-metrics]

자세한 메트릭과 보고서를 보려면 왼쪽의 **API 관리** 메뉴에서 **분석**을 클릭하세요.

![개요][api-management-analytics-overview]

**분석** 섹션에는 다음과 같은 네 개의 탭이 있습니다.

-	**개요**에서는 전체적인 사용 및 상태 메트릭뿐만 아니라 상위 개발자, 상위 제품, 상위 API 및 상위 작업도 제공합니다.
-	**사용량**에서는 지리적 표현을 포함하여 API 호출 및 대역폭을 심도 있게 확인할 수 있습니다.
-	**상태**에서는 상태 코드, 캐시 성공률, 응답 시간, API 및 서비스 응답 시간을 위주로 보여 줍니다.
-	**활동**에서는 개발자의 특정 작업, 제품, API 및 작업에 대한 자세한 내용을 보여 주는 보고서를 제공합니다.

## <a name="next-steps"> </a>다음 단계

-	[고급 API 구성 시작][] 자습서에서 다른 항목을 확인하세요.

[Azure 무료 평가판]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Azure API 관리에서 알림 및 메일 템플릿을 구성하는 방법]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[고급 API 구성 시작]: api-management-get-started-advanced.md
[API 관리 가격]: http://azure.microsoft.com/pricing/details/api-management/

[Azure 클래식 포털]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png

<!---HONumber=AcomDC_0525_2016-->