<properties 
	pageTitle="Azure 앱 서비스 정의 | Microsoft Azure" 
	description="Azure 앱 서비스를 사용하여 웹 및 모바일 앱을 개발, 배포 및 관리하는 방법을 알아봅니다." 
	keywords="앱 서비스, Azure 앱 서비스, 앱 서비스 비용, 규모, 확장성, 앱 배포, Azure 앱 배포, PaaS, Platform-as-a-Service "
	services="app-service" 
	documentationCenter="" 
	authors="omarkmsft" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="omark"/>

# Azure 앱 서비스 정의

*앱 서비스*는 플랫폼이나 장치에 웹 및 모바일 앱을 만들 수 있는 Microsoft Azure의 PaaS([platform-as-a-service](https://en.wikipedia.org/wiki/Platform_as_a_service)) 제품입니다. SaaS 솔루션(예: Office 365, Dynamics CRM, Salesforce, Twilio)을 사용하여 앱을 통합하고 온-프레미스 응용 프로그램(예: SAP, Oracle, Siebel)과 연결하며 엄격한 보안, 안정성 및 확장성 요구를 충족하는 동시에 비즈니스 프로세스를 자동화하는 작업을 용이하게 할 수 있습니다.

앱 서비스는 이전에 개별적으로 Azure 웹 사이트 및 Azure 모바일 서비스로 전달한 웹 및 모바일 기능을 포함합니다. 또한 비즈니스 프로세스를 자동화하고 클라우드 API를 호스팅하는 새 기능을 포함합니다.

## 앱 서비스의 앱 유형

앱 서비스는 응용 프로그램 코드 또는 워크플로 프로세스를 실행하기 위해 다음과 같은 앱 유형을 제공합니다.

- [**웹앱**](../app-service-web/app-service-web-overview.md) - 웹 사이트와 웹 응용 프로그램을 호스팅합니다.

- [**모바일 앱**](../app-service-mobile/app-service-mobile-value-prop.md) 모바일 앱 백 엔드를 호스팅합니다.
   
- [**API 앱**](../app-service-api/app-service-api-apps-why-best-platform.md) - 클라우드 API를 호스팅합니다.
 
- [**논리 앱**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - 코드를 작성하지 않고 클라우드 간의 데이터 액세스 및 사용을 자동화합니다.

단일 통합 서비스인 앱 서비스를 사용하면 여러 앱 유형을 단일 솔루션으로 쉽게 작성할 수 있습니다.

## 앱 서비스 계획 및 환경

[앱 서비스 계획](azure-web-sites-web-hosting-plans-in-depth-overview.md)은 앱에서 실행되는 계산 리소스를 나타냅니다. 더 낮은 가격 책정 계층에서는 공유 가상 컴퓨터(VM)에서 앱을 실행합니다. 더 높은 계층에서는 전용 VM에서 앱을 실행합니다. 다양한 VM 크기를 선택할 수 있고 가동 중지 시간 없이 가격 책정 계층을 변경할 수 있습니다. 확장성 및 네트워크 격리가 필요한 경우 앱을 [앱 서비스 환경](../app-service-web/app-service-app-service-environment-intro.md)에서 실행할 수 있습니다.

## 앱 서비스를 사용하는 이유는 무엇인가요?

다음은 앱 서비스의 몇 가지 주요 기능입니다.

- **완전히 관리되는 플랫폼** - 자동 OS 및 프레임워크 패치, 백업 및 재해 복구에 대한 기본 제공 지원입니다. 

- **기존 기술 사용** - 좋아하는 언어, 프레임워크 및 개발 환경으로 코딩합니다. 앱 서비스는 .NET, Node.js, Java, PHP 및 Python을 지원합니다.

- **빠른 배포** - 새 앱을 프로비전하고 몇 초만에 코드를 웹앱에 배포합니다.

- **연속 통합** - Visual Studio Team Services, GitHub, BitBucket으로 [연속 통합 및 배포](../app-service-web/app-service-continous-deployment.md)를 설정합니다.

- **스테이징 및 테스트 환경** - [스테이징된 배포](../app-service-web/web-sites-staged-publishing.md)를 구현하여 프로덕션 환경과 동일한 프로덕션 전 환경에서 코드를 검증합니다. 준비되었으면 교환 작업을 수행하여 가동 중지 시간 0으로 새 버전의 앱을 릴리스합니다.

- **프로덕션에서 테스트** - 다음 수준으로 스테이징된 배포를 사용하고 [A/B 테스트를 수행](../app-service-web/app-service-web-test-in-production-get-start.md)하여 구성 가능한 일부 라이브 트래픽으로 새 코드를 검증합니다.

- **인증 및 권한 부여** - 코드를 변경하지 않고 API 앱을 인증되지 않은 액세스로부터 보호합니다. 기본 제공 인증 서비스는 사용자, 사용자를 나타내는 클라이언트 또는 서비스에서 액세스하는 경우 앱을 보호합니다. 지원되는 ID 공급자에는 Azure Active Directory, Facebook, Twitter, Google, Microsoft 계정 등이 있습니다. 자세한 내용은 [Azure 앱 서비스에서 인증 및 권한 부여](app-service-authentication-overview.md)를 참조하세요.

- **모든 서비스에 연결** - 기본 제공 [커넥터](../connectors/apis-list.md)를 사용하여 몇 분 내에 엔터프라이즈 시스템 또는 SaaS(software-as-a-service)에 앱을 연결합니다. 엔터프라이즈 시스템(예: SAP, Siebel 및 Oracle), 인기 있는 엔터프라이즈 SaaS 서비스(예: Salesforce 및 Office 365), 인기 있는 인터넷 서비스(예: Facebook, Twitter 및 Dropbox) 등을 위한 50개 이상의 커넥터에서 선택합니다.

- **뛰어난 확장성** - 규모를 [확장](../app-service/app-service-scale.md)하거나 [축소](../azure-portal/insights-how-to-scale.md)하여 들어오는 고객 부하를 처리합니다. 수동으로 VM 수와 크기를 선택하거나 부하 또는 일정에 따라 자동 확장을 설정합니다. Microsoft의 글로벌 데이터 센터 인프라는 앱을 호스팅하고 데이터 및 호스팅 서비스를 여러 위치에서 간편하게 복제할 수 있도록 합니다.

- **엔터프라이즈급** - 앱 서비스는 중요 업무용 보안 응용 프로그램을 빌드 및 호스팅하도록 설계되었습니다. 안전하게 온-프레미스 리소스에 연결한 다음 [ISO, SOC 및 PCI 규격](https://www.microsoft.com/TrustCenter/)인 보안 클라우드 플랫폼에 호스트하는 Active Directory 통합 비즈니스 앱을 빌드합니다. 모두 엔터프라이즈 수준 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)입니다.

- **Azure 마켓플레이스** - 점점 증가하는 [응용 프로그램 템플릿 목록](https://azure.microsoft.com/marketplace/)에서 선택합니다. 한 번 클릭으로 WordPress, Joomla 및 Drupal과 같은 패키지를 설치하여 OSS 앱 커뮤니티를 최대한 활용합니다.

- **WebJobs** - 앱 서비스 VM에서 [프로그램이나 스크립트를 실행](../app-service-web/web-sites-create-web-jobs.md)합니다. 일정에 따라 작업을 계속 실행하거나 이벤트에 의해 트리거됩니다. Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md)는 작성하는 코드를 간소화하여 다른 Azure 및 타사 서비스와 통합합니다.

- **하이브리드 연결** - [하이브리드 연결](../biztalk-services/integration-hybrid-connection-overview.md) 및 [Azure 가상 네트워크](../app-service-web/web-sites-integrate-with-vnet.md)를 사용하여 온-프레미스 데이터에 액세스합니다.

- **Visual Studio 통합** - Visual Studio의 전용 도구는 웹앱, 모바일 앱 및 API 앱을 생성, 배포, 사용, 디버그 및 관리하는 작업을 간소화합니다.

## 앱 서비스 시작

신용 카드 없이도 약정이나 복잡한 과정을 거치지 않고 무료로 즉시 [임시 웹앱, 모바일 앱 또는 논리 앱을 만듭니다](http://go.microsoft.com/fwlink/?LinkId=523751).

또는 [무료 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)을 열고 시작 자습서 중 하나를 사용해 봅니다.

* [웹앱](https://azure.microsoft.com/documentation/services/app-service/web/)
* [모바일 앱](https://azure.microsoft.com/documentation/services/app-service/mobile/)
* [API 앱](https://azure.microsoft.com/documentation/services/app-service/api/)
* [논리 앱](https://azure.microsoft.com/documentation/services/app-service/logic/)

<!---HONumber=AcomDC_0601_2016-->