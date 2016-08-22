<properties 
	pageTitle="웹앱 및 모바일 앱에 대한 Azure 앱 서비스 | Microsoft Azure" 
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

*앱 서비스*는 Microsoft Azure의 PaaS ([platform-as-a-service](https://en.wikipedia.org/wiki/Platform_as_a_service)) 제품입니다. 플랫폼 및 장치에 웹 및 모바일 앱을 만듭니다. SaaS 솔루션과 앱을 통합하고 온-프레미스 응용 프로그램을 사용하여 연결하고 비즈니스 프로세스를 자동화합니다. Azure는 공유 VM 리소스 또는 전용 VM을 선택하여 완전히 관리되는 VM (가상 컴퓨터) 에서 앱을 실행합니다.

앱 서비스는 이전에 개별적으로 Azure 웹 사이트 및 Azure 모바일 서비스로 전달한 웹 및 모바일 기능을 포함합니다. 또한 비즈니스 프로세스를 자동화하고 클라우드 API를 호스팅하는 새 기능을 포함합니다. 단일 통합 서비스인 앱 서비스를 통해 웹 사이트, 모바일 앱 백 엔드, RESTful API 및 비즈니스 프로세스 등 다양한 구성 요소를 단일 솔루션으로 작성할 수 있습니다.

다음 4분 비디오에서는 앱 서비스와 이전 Azure 제품의 연결점 및 새로운 기능에 대한 간략한 설명을 제공합니다.


+[AZURE.VIDEO app-service-history-lesson] 


## 앱 서비스를 사용하는 이유는 무엇인가요?

다음은 앱 서비스의 몇 가지 주요 기능입니다.

- **여러 언어 및 프레임워크** - 앱 서비스에서는 ASP.NET, Node.js, Java, PHP 및 Python을 최고 수준으로 지원합니다. 앱 서비스 VM에서 [Windows PowerShell 및 기타 스크립트 또는 실행 파일](../app-service-web/web-sites-create-web-jobs.md)을 실행할 수도 있습니다.

- **DevOps 최적화** - Visual Studio Team Services, GitHub, BitBucket으로 [연속 통합 및 배포](../app-service-web/app-service-continuous-deployment.md)를 설정합니다. [테스트 및 스테이징 환경](../app-service-web/web-sites-staged-publishing.md)을 통해 업데이트를 승격합니다. [A/B 테스트](../app-service-web/app-service-web-test-in-production-get-start.md)를 수행합니다. [Azure PowerShell](../powershell-install-configure.md) 또는 [플랫폼 간 CLI\(명령줄 인터페이스\)](../xplat-cli-install.md)를 사용하여 앱 서비스에서 앱을 관리합니다.
 
- **고가용성을 가진 글로벌 규모 조정** - 수동 또는 자동으로 규모를 [강화](../app-service-web/web-sites-scale.md) 또는 [확장](../azure-portal/insights-how-to-scale.md)합니다. Microsoft의 글로벌 데이터 센터 인프라의 모든 위치에서 앱을 호스팅하고 앱 서비스 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)를 사용하면 고가용성이 보장됩니다.

- **SaaS 플랫폼 및 온-프레미스 데이터에 연결** - 엔터프라이즈 시스템\(예: SAP, Siebel 및 Oracle\), SaaS 서비스\(예: Salesforce 및 Office 365\), 인기 있는 인터넷 서비스\(예: Facebook 및 Twitter\) 등을 위한 50개 이상의 [커넥터](../connectors/apis-list.md)에서 선택합니다. [하이브리드 연결](../biztalk-services/integration-hybrid-connection-overview.md) 및 [Azure 가상 네트워크](../app-service-web/web-sites-integrate-with-vnet.md)를 사용하여 온-프레미스 데이터에 액세스합니다.

- **보안 및 규정 준수** - 앱 서비스는 [ISO, SOC 및 PCI 규격](https://www.microsoft.com/TrustCenter/)입니다.

- **응용 프로그램 템플릿** - WordPress, Joomla, Drupal 등의 인기 있는 오픈 소스 소프트웨어를 설치하는 마법사를 사용할 수 있는 [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/)의 광범위한 응용 프로그램 템플릿 목록에서 선택합니다.

- **Visual Studio 통합** - Visual Studio의 전용 도구는 생성, 배포, 디버깅 작업을 간소화합니다.

## 앱 서비스의 앱 유형

앱 서비스는 여러 *앱 형식*을 제공하며 각각은 특정 종류의 워크로드를 호스트하도록 계획되었습니다.

- [**웹앱**](../app-service-web/app-service-web-overview.md) - 웹 사이트와 웹 응용 프로그램을 호스팅합니다.

- [**모바일 앱**](../app-service-mobile/app-service-mobile-value-prop.md) 모바일 앱 백 엔드를 호스팅합니다.
   
- [**API 앱**](../app-service-api/app-service-api-apps-why-best-platform.md) - 클라우드 API를 호스팅합니다.
 
- [**논리 앱**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - 코드를 작성하지 않고 클라우드 간의 데이터 액세스 및 사용을 자동화합니다.

여기서 *앱*이라는 단어는 워크로드를 전용으로 실행하는 호스팅 리소스를 의미합니다. 예를 들어 "웹앱"의 경우 함께 브라우저에 대한 기능을 제공하는 계산 리소스와 응용 프로그램 코드를 모두 웹앱으로 생각하는 것이 익숙할 것입니다. 하지만 앱 서비스에서 *웹앱*은 Azure에서 응용 프로그램 코드를 호스팅하기 위해 제공하는 계산 리소스입니다. 응용 프로그램이 웹 프런트 엔드 및 RESTful API 백 엔드로 구성된 경우 웹앱에 모두를 배포하거나 웹앱에 프런트 엔드 코드 및 API 앱에 백 엔드 코드를 배포할 수 있습니다. 응용 프로그램은 다른 종류의 여러 앱 서비스 앱으로 구성될 수 있습니다.

## 앱 서비스 계획

[앱 서비스 계획](azure-web-sites-web-hosting-plans-in-depth-overview.md)은 앱에서 실행되는 계산 리소스의 종류를 지정합니다. 트래픽 부하가 가벼운 경우 공유 VM\(가상 컴퓨터\)을 사용할 수 있습니다. 대량 로드의 경우 전용 VM을 여러 가지 크기 중에서 선택할 수 있습니다. 여러 앱 서비스 앱은 동일한 계획을 공유할 수 있고 계획을 사용하여 규모를 확장하고 축소합니다.

확장성 및 네트워크 격리가 필요한 경우 [앱 서비스 환경](../app-service-web/app-service-app-service-environment-intro.md)에서 앱을 실행할 수 있습니다.

## 가격

앱 서비스 비용에 대한 정보는 [앱 서비스 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.

## 앱 서비스 시작

신용 카드 없이도 약정이나 복잡한 과정을 거치지 않고 무료로 즉시 [임시 웹앱, 모바일 앱 또는 논리 앱을 만듭니다](http://go.microsoft.com/fwlink/?LinkId=523751).

또는 [무료 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)을 열고 시작 자습서 중 하나를 사용해 봅니다.

* [자습서: 웹앱 만들기](../app-service-web/app-service-web-get-started.md)
* [자습서: 모바일 앱 만들기](../app-service-mobile/app-service-mobile-android-get-started.md)
* [자습서: API 앱 만들기](../app-service-api/app-service-api-dotnet-get-started.md)
* [자습서: 논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->
