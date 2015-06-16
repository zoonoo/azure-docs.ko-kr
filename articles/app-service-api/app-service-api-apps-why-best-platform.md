<properties 
	pageTitle="API 앱 정의" 
	description="Azure 앱 서비스가 RESTful API를 개발, 게시 및 호스트하는 최상의 플랫폼인 이유를 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# API 앱 정의

API 앱은 웹앱, 모바일 앱 및 논리 앱을 포함하는 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md) 제품군의 일부입니다.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

이 제품군의 API 앱 부분에서는 클라우드 및 온-프레미스에서 API를 빌드, 사용 및 배포하기 위한 풍부한 플랫폼 및 에코 시스템을 제공합니다.

>[AZURE.NOTE]API 앱은 현재 공개 미리 보기로 제공됩니다. 글로벌 규모로 안전한 중요 업무용 응용 프로그램을 빌드 및 호스트하도록 설계된 GA(일반 공급) 서비스인 [앱 서비스 웹앱](../app-service-web/app-service-web-overview.md)을 기반으로 합니다. 현재 API를 빌드하기 위한 GA 서비스를 찾고 있는 경우 웹앱이 유용한 옵션입니다. API 앱이 GA로 전환되면 기존 웹앱을 통해 API 앱의 기능을 활용하기 위한 경로가 제공될 것입니다.

## API 앱을 사용하는 이유

API 앱은 RESTful 웹 API를 개발, 배포, 게시, 사용, 관리 및 수익화하는 환경을 향상시키는 추가 기능이 있는 [앱 서비스 웹앱](../app-service-web/app-service-web-overview.md)입니다.

따라서 API 앱은 Azure 앱 서비스 플랫폼의 모든 웹 호스팅 기능을 웹앱과 공유합니다.

- 자동 OS 패치
- 엔터프라이즈급 보안
- 고가용성
- 자동 크기 조정 및 부하 분산
- 후순위 처리용 [WebJobs](../app-service-web/websites-webjobs-resources.md)
- 쉽고 빠른 배포, 다양한 연속 배달 옵션 - API 앱에 사용할 수 있는 다양한 배포 옵션에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 배포](../app-service-web/web-sites-deploy.md)를 참조하세요. 

API 앱 플랫폼이 제공하는 추가 기능을 통해 쉽게 API를 개발, 호스트 및 사용할 수 있습니다.

- **API를 있는 그대로 사용** - API에 ASP.NET, Java, PHP, Node.js 또는 Python을 사용할 수 있습니다. API는 API 앱 플랫폼을 변경 없이 활용할 수 있습니다.

- **단순 액세스 제어** - 코드 변경 없이 Azure Active Directory 또는 Facebook 및 Twitter와 같은 타사 서비스를 통해 인증하도록 API를 구성합니다. 인증 코드에 간단한 구문을 사용합니다. 자세한 내용은 [API 앱 보호](app-service-api-dotnet-add-authentication.md)를 참조하세요.

- **쉽게 SaaS 플랫폼에 연결** - Azure 마켓플레이스의 [커넥터 API 앱](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)은 SalesForce, Office 365, Twitter, Facebook, Dropbox 등과 상호 작용하기 위해 작성하는 코드를 단순화하기 위해 Microsoft 및 타사에서 제공됩니다.

- **온-프레미스 데이터 액세스** - API 앱은 [하이브리드 연결](../integration-hybrid-connection-overview.md) 및 [VNET](../app-service-web/web-sites-integrate-with-vnet.md)을 통해 자체 데이터 센터의 데이터를 사용하는 API를 노출할 수 있습니다.

- **쉽게 사용** - 통합된 [Swagger](http://swagger.io/) 지원을 통해 다양한 클라이언트에서 쉽게 API를 사용할 수 있게 만듭니다. 또는 API 앱 SDK를 사용하여 C#, Java 및 Javascript를 비롯한 다양한 언어로 API용 클라이언트 코드를 생성합니다.

- **논리 앱과 통합** - 만든 API 앱은 앱 서비스 논리 앱을 통해 사용할 수 있습니다.

- **Visual Studio 통합** - Visual Studio의 전용 도구는 API 앱을 [생성](app-service-dotnet-create-api-app.md), [배포](app-service-dotnet-deploy-api-app.md), [디버그](app-service-dotnet-remotely-debug-api-app) 및 관리하는 작업을 간소화합니다.

조만간 API 앱 플랫폼은 쉽게 코드를 공유할 수 있게 하여 진정한 API 에코 시스템도 만들 것입니다.

- **공용 및 개인 마켓플레이스** - [Azure 마켓플레이스](http://azure.microsoft.com/marketplace/)를 통해 Microsoft 및 타사에서 개발한 미리 패키지된 API 앱을 쉽게 찾아서 Azure 구독에 배포할 수 있습니다. 또한 다른 개발자가 Azure 구독에 배포할 수 있도록 자신이 개발한 API 앱을 패키지 및 게시할 수 있습니다. Azure 마켓플레이스에 API를 게시할 때 해당 조직의 다른 구성원에게만 표시되도록 설정할 수 있습니다. 

- **자동 종속성 배포** - 마켓플레이스에서 Azure 구독으로 API 앱을 배포할 때마다-Azure에서 자동으로 종속 API 앱을 배포하고 필요한 리소스를 만듭니다. API 앱 패키지는 해당 앱이 종속된 API 앱과 필요한 Azure 리소스를 지정합니다.

- **자동 업데이트** - 공유한 API 앱 패키지 중 하나의 코드를 업데이트할 때 해당 API 앱을 설치하여 실행 중인 모든 사용자에게 업데이트를 푸시할 수 있습니다. 이 기능은 사소한 변경 및 업데이트 수신을 옵트인(opt in)한 사용자에 대해 작동합니다.

공용 마켓플레이스 및 자동 업데이트와 같은 대부분의 기능은 Microsoft에서 제공하는 API 앱에 이미 사용할 수 있습니다.

## API 앱 개념 ##

- **게이트웨이** - 리소스 그룹의 모든 API 앱에 대한 인증 및 API 관리 기능을 처리하는 웹앱입니다. 
- **Swagger** - API 앱에서 기본적으로 사용되는 RESTful API 검색 및 대화형 설명서를 위한 프레임워크입니다. 자세한 내용은 [http://swagger.io/](http://swagger.io/)(영문)를 참조하세요.
- **커넥터** - Salesforce 및 Office 365와 같은 SaaS 플랫폼에 쉽게 연결할 수 있게 해주는 API 앱의 한 유형입니다. 자세한 내용은 [커넥터 및 BizTalk API 앱 정의](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)를 참조하세요.
- **트리거** - 특정 조건이 충족될 경우 [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)이 워크플로 프로세스를 시작하기 위해 호출할 수 있는 REST API입니다. 예를 들어 API 앱은 논리 앱이 Twitter 피드에서 특정 구를 찾기 위해 주기적으로 호출하는 메서드를 제공할 수 있습니다. 자세한 내용은 [API 앱 트리거](app-service-api-dotnet-triggers.md)를 참조하세요.
- **동작** - 워크플로가 트리거에 의해 시작된 후 [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)이 데이터를 처리하기 위해 호출할 수 있는 REST API입니다. 예를 들어 API 앱은 논리 앱이 Twitter 트리거에 의해 발견된 트윗에 응답하기 위해 호출하는 메서드를 제공할 수 있습니다. 동작은 Swagger API 정의를 통해 노출되는 API 메서드입니다.

## 시작

API 앱을 시작하려면 [API 앱 만들기 자습서](app-service-dotnet-create-api-app.md)를 따르세요.

API 앱의 알려진 문제 목록을 보려면 [이 MSDN 포럼 게시물](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps)을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)를 참조하세요.


<!--HONumber=54--> 