<properties 
	pageTitle="API 앱 개요" 
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
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# API 앱 개요

API 앱은 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에서 제공하는 4개의 앱 형식 중 하나입니다.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

API 앱은 클라우드 및 온-프레미스에서 API를 빌드, 호스팅 및 사용하기 위한 풍부한 플랫폼을 제공합니다. 앱 서비스에서 사용자의 API를 API 앱으로 배포하고 엔터프라이즈급 보안, 단순 액세스 제어, 하이브리드 연결, 자동 SDK 생성, 그리고 [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)과의 원활한 통합에서 이점을 얻을 수 있습니다.

## API 앱을 사용하는 이유

API 앱은 공개 미리 보기에서 현재 사용할 수 있는 다음과 같은 기능을 제공합니다.

- **쉽게 사용** - 통합된 [Swagger](#concepts) 지원을 통해 다양한 클라이언트가 쉽게 API를 사용할 수 있도록 합니다. [CORS](#concepts)을 쉽게 구성하고 C#, Java 및 Javascript를 비롯한 다양한 언어로 API용 클라이언트 코드를 자동으로 생성할 수 있습니다.

- **간단한 액세스 제어** - 코드를 변경하지 않고 API 앱을 인증되지 않은 액세스로부터 보호할 수 있습니다. 기본 제공 인증 서비스는 다른 서비스 또는 사용자를 나타내는 클라이언트에서 액세스에 대해 API를 보호합니다. 지원되는 ID 공급자는 Azure Active Directory 및 Facebook이나 Twitter와 같은 타사 공급자를 포함합니다. 클라이언트는 ADAL(Active Directory 인증 라이브러리) 또는 모바일 앱 SDK를 사용할 수 있습니다. 자세한 내용은 [Azure 앱 서비스에서 API 앱 및 모바일 앱에 대한 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.

- **Visual Studio 통합** - Visual Studio의 전용 도구는 API 앱을 생성, 배포, 사용, 디버그 및 관리하는 작업을 간소화합니다.

- **논리 앱과 통합** - 사용자가 만드는 API 앱은 [앱 서비스 논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)을 통해 사용할 수 있습니다.

- **기존 API를 가져오기** - API 앱 기능을 활용하기 위해 기존 API의 코드를 변경할 필요가 없습니다. 사용자의 코드를 API 앱에 배포하십시오. API는 ASP.NET 및 C#, Java, PHP, Node.js 및 Python을 포함하여 앱 서비스에서 지원하는 모든 언어 또는 프레임워크를 사용할 수 있습니다.

다음은 중요한 몇 가지입니다. API 앱이 활용할 수 있는 더 많은 기능은 [웹앱 개요](../app-service-web/app-service-web-overview.md)를 참조하세요.

>[AZURE.NOTE][Azure API 관리](../api-management/api-management-key-concepts.md)를 사용하여 앱 서비스 API 앱에서 호스팅되는 API에 대한 클라이언트 액세스를 제어할 수 있습니다. API 앱이 인증 서비스를 제공하는 반면 액세스 관리 끝점 통합 및 제한 등 API 관리에서 제공하는 다른 기능을 제공하지 않습니다.
>
>API 앱은 현재 공개 미리 보기로 제공됩니다. [앱 서비스 웹앱](../app-service-web/app-service-web-overview.md)은 글로벌 규모로 안전한 중요 업무용 응용 프로그램을 빌드 및 호스트하도록 설계된 GA(일반 공급) 서비스입니다. 현재 API를 빌드하기 위한 GA 서비스를 찾고 있는 경우 웹앱이 유용한 옵션입니다. API 앱이 GA로 전환되면 기존 웹앱을 통해 API 앱의 기능을 활용할 수 있는 추가 기능이 제공될 것입니다.

## API 앱 개념 ##

- **Swagger** - API 앱에서 기본적으로 사용되는 RESTful API 검색 및 설명서를 위한 프레임워크입니다. 자세한 내용은 [http://swagger.io/](http://swagger.io/)(영문)를 참조하세요.
- **CORS(Cross Origin Resource Sharing)** - 브라우저에서 실행되는 JavaScript가 웹 페이지가 로드한 것과 다른 도메인에서 호스팅되는 API에 호출하도록 허용하는 메커니즘입니다.
- **트리거** - 특정 조건이 충족될 경우 [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)이 워크플로 프로세스를 시작하기 위해 호출할 수 있는 REST API입니다. 예를 들어 API 앱은 논리 앱이 Twitter 피드에서 특정 구를 찾기 위해 주기적으로 호출하는 메서드를 제공할 수 있습니다. 자세한 내용은 [API 앱 트리거](app-service-api-dotnet-triggers.md)를 참조하세요.
- **동작** - 워크플로가 트리거에 의해 시작된 후 [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)이 데이터를 처리하기 위해 호출할 수 있는 REST API입니다. 예를 들어 API 앱은 논리 앱이 Twitter 트리거에 의해 발견된 트윗에 응답하기 위해 호출하는 메서드를 제공할 수 있습니다. 동작은 Swagger API 정의를 통해 노출되는 API 메서드입니다.
- **게이트웨이** - 리소스 그룹의 모든 API 앱에 대한 인증 및 API 관리 기능을 처리하는 웹앱입니다.

## 시작

API 앱을 시작하려면 [API 앱 만들기 자습서](app-service-dotnet-create-api-app.md)를 따르세요.

API 앱의 알려진 문제 목록을 보려면 [API 앱의 알려진 문제 포럼 게시물](https://social.msdn.microsoft.com/Forums/ko-KR/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps)을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)를 참조하세요.

 

<!---HONumber=Nov15_HO2-->