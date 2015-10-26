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
	ms.date="07/30/2015" 
	ms.author="tdykstra"/>

# API 앱 정의

API 앱은 클라우드 및 온-프레미스에서 API를 빌드, 호스팅, 사용 및 배포하기 위한 풍부한 플랫폼 및 에코 시스템을 제공합니다. 사용자의 API를 API 앱으로 배포하고 엔터프라이즈급 보안, 단순 액세스 제어, 하이브리드 및 SaaS 연결, 자동 SDK 생성, 그리고 [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)과의 원활한 통합에서 이점을 얻을 수 있습니다.

API 앱은 웹앱, 모바일 앱 및 논리 앱도 포함하는 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)의 일부입니다.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## API 앱을 사용하는 이유

API 앱은 RESTful 웹 API 개발, 배포, 게시, 사용 및 관리를 위한 기능을 제공합니다. 앱 서비스는 공개 미리 보기에서 현재 사용할 수 있는 다음과 같은 기능을 제공합니다.

- **쉽게 사용** - 통합된 [Swagger](http://swagger.io/) 지원을 통해 다양한 클라이언트가 쉽게 API를 사용할 수 있도록 합니다. API 앱 SDK는 C#, Java 및 Javascript를 비롯한 다양한 언어로 API용 클라이언트 코드를 생성할 수 있습니다.

- **단순 액세스 제어** - 기본 제공 인증 서비스가 Azure Active Directory 또는 Facebook 및 Twitter와 같은 타사 서비스를 지원합니다. 코드를 변경하지 않고 API 앱을 인증되지 않은 액세스로부터 보호할 수 있습니다. [Azure 모바일 서비스](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication)가 제공하는 인증 서비스에 익숙한 경우, API 앱은 해당 프레임워크를 기반으로 API 앱에 의해 호스팅되는 API로 확장합니다. 앱 서비스 SDK를 사용하면 권한 부여 코드에 대한 간단한 구문을 사용할 수 있습니다. 자세한 내용은 [Azure 앱 서비스에서 API 앱 및 모바일 앱에 대한 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.

- **쉽게 SaaS 플랫폼에 연결** - Azure 마켓플레이스의 [커넥터 API 앱](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)은 SalesForce, Office 365, Twitter, Facebook, Dropbox 등과 상호 작용하기 위해 작성하는 코드를 단순화하기 위해 Microsoft 및 타사에서 제공됩니다.

- **논리 앱과 통합** - 사용자가 만드는 API 앱은 [앱 서비스 논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)을 통해 사용할 수 있습니다.

- **Visual Studio 통합** - Visual Studio의 전용 도구는 API 앱을 [생성](app-service-dotnet-create-api-app.md), [배포](app-service-dotnet-deploy-api-app.md), [디버그](app-service-dotnet-remotely-debug-api-app) 및 관리하는 작업을 간소화합니다.

기존 API를 가져올 수 있으므로, API 앱 기능을 활용하기 위해 기존 API의 코드를 변경할 필요가 없습니다. 사용자의 코드를 API 앱에 배포하십시오. API에 ASP.NET, Java, PHP, Node.js 또는 Python을 사용할 수 있습니다.

또한 API 앱은 [앱 서비스 웹앱의 기능](../app-service-web/app-service-web-overview.md)도 포함합니다.

>[AZURE.NOTE] [Azure API Management](/services/api-management/)는 끝점 통합 및 제한 등의 기능을 제공하는 별도의 서비스입니다. API 앱으로 API 관리를 사용할 수 있습니다.
>
>API 앱은 현재 공개 미리 보기로 제공됩니다. [앱 서비스 웹앱](../app-service-web/app-service-web-overview.md)은 글로벌 규모로 안전한 중요 업무용 응용 프로그램을 빌드 및 호스트하도록 설계된 GA(일반 공급) 서비스입니다. 현재 API를 빌드하기 위한 GA 서비스를 찾고 있는 경우 웹앱이 유용한 옵션입니다. API 앱이 GA로 전환되면 기존 웹앱을 통해 API 앱의 기능을 활용할 수 있는 추가 기능이 제공될 것입니다.

### 이후 사용 가능한 API 앱 기능

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

 

<!---HONumber=Oct15_HO3-->