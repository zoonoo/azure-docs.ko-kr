<properties
	pageTitle="웹앱 개요 | Microsoft Azure"
	description="Azure 앱 서비스로 웹 응용 프로그램을 개발 및 호스팅하는 방법에 대해 알아보세요."
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/25/2016"
	ms.author="tdykstra"/>

# 웹앱 개요

*앱 서비스 웹앱*은 웹 사이트와 웹 응용 프로그램 호스팅을 위해 최적화된 완전히 관리되는 계산 플랫폼입니다. Azure가 앱의 실행 및 크기 조정을 위해 인프라를 관리하는 동안 Microsoft Azure의 이 PaaS[(Platform-as-a-Service)](https://en.wikipedia.org/wiki/Platform_as_a_service)를 통해 비즈니스 논리에 집중할 수 있습니다.

5분 비디오 개요는 [Yochay Kiriaty와 Azure 앱 서비스 웹앱](https://azure.microsoft.com/documentation/videos/azure-app-service-web-apps-with-yochay-kiriaty/)을 참조하세요.

## 앱 서비스에서 웹앱은 무엇인가요?

앱 서비스에서 *웹앱*은 Azure에서 웹 사이트 또는 웹 응용 프로그램을 호스팅하기 위해 제공하는 계산 리소스입니다.

계산 리소스는 선택한 가격 책정 계층에 따라 공유 또는 전용 가상 컴퓨터(VM)일 수 있습니다. 응용 프로그램 코드는 다른 고객과 격리되는 관리되는 VM에서 실행됩니다.

사용자 코드는 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)(ASP.NET, Node.js, Java, PHP, Python)에서 지원되는 모든 언어 또는 프레임워크일 수 있습니다. 웹앱에서 [PowerShell 및 기타 스크립트 언어](web-sites-create-web-jobs.md#acceptablefiles)를 사용하는 스크립트를 실행할 수도 있습니다.

웹앱을 사용할 수 있는 일반적인 응용 프로그램 시나리오에 대한 예는 [웹앱 시나리오](https://azure.microsoft.com/documentation/scenarios/web-app/)를 참조하세요.

## 웹앱을 사용하는 이유

웹앱에 적용하는 앱 서비스의 주요 기능은 다음과 같습니다.

- **완전히 관리되는 플랫폼** - 자동 OS 및 프레임워크 패치, 백업 및 재해 복구에 대한 기본 제공 지원입니다. 

- **기존 기술 사용** - 좋아하는 언어, 프레임워크 및 개발 환경으로 코딩합니다. 앱 서비스는 .NET, Node.js, Java, PHP 및 Python을 지원합니다.

- **빠른 배포** - 새 앱을 프로비전하고 몇 초만에 코드를 웹앱에 배포합니다.

- **연속 통합** - Visual Studio Team Services, GitHub, BitBucket으로 [연속 통합 및 배포](../app-service-web/app-service-continous-deployment.md)를 설정합니다.

- **스테이징 및 테스트 환경** - [스테이징된 배포](../app-service-web/web-sites-staged-publishing.md)를 구현하여 프로덕션 환경과 동일한 프로덕션 전 환경에서 코드를 검증합니다. 준비되었으면 교환 작업을 수행하여 가동 중지 시간 0으로 새 버전의 앱을 릴리스합니다.

- **프로덕션에서 테스트** - 다음 수준으로 스테이징된 배포를 사용하고 [A/B 테스트를 수행](../app-service-web/app-service-web-test-in-production-get-start.md)하여 구성 가능한 일부 라이브 트래픽으로 새 코드를 검증합니다.

- **인증 및 권한 부여** - 코드를 변경하지 않고 API 앱을 인증되지 않은 액세스로부터 보호합니다. 기본 제공 인증 서비스는 사용자, 사용자를 나타내는 클라이언트 또는 서비스에서 액세스하는 경우 앱을 보호합니다. 지원되는 ID 공급자에는 Azure Active Directory, Facebook, Twitter, Google, Microsoft 계정 등이 있습니다. 자세한 내용은 [Azure 앱 서비스에서 인증 및 권한 부여](../app-service/app-service-authentication-overview.md)를 참조하세요.

- **모든 서비스에 연결** - 기본 제공 [커넥터](../connectors/apis-list.md)를 사용하여 몇 분 내에 엔터프라이즈 시스템 또는 SaaS(software-as-a-service)에 앱을 연결합니다. 엔터프라이즈 시스템(예: SAP, Siebel 및 Oracle), 인기 있는 엔터프라이즈 SaaS 서비스(예: Salesforce 및 Office 365), 인기 있는 인터넷 서비스(예: Facebook, Twitter 및 Dropbox) 등을 위한 50개 이상의 커넥터에서 선택합니다.

- **뛰어난 확장성** - 규모를 [확장](../app-service/app-service-scale.md)하거나 [축소](../azure-portal/insights-how-to-scale.md)하여 들어오는 고객 부하를 처리합니다. 수동으로 VM 수와 크기를 선택하거나 부하 또는 일정에 따라 자동 확장을 설정합니다. Microsoft의 글로벌 데이터 센터 인프라는 앱을 호스팅하고 데이터 및 호스팅 서비스를 여러 위치에서 간편하게 복제할 수 있도록 합니다.

- **엔터프라이즈급** - 앱 서비스는 중요 업무용 보안 응용 프로그램을 빌드 및 호스팅하도록 설계되었습니다. 안전하게 온-프레미스 리소스에 연결한 다음 [ISO, SOC 및 PCI 규격](https://www.microsoft.com/TrustCenter/)인 보안 클라우드 플랫폼에 호스트하는 Active Directory 통합 비즈니스 앱을 빌드합니다. 모두 엔터프라이즈 수준 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)입니다.

- **Azure 마켓플레이스** - 점점 증가하는 [응용 프로그램 템플릿 목록](https://azure.microsoft.com/marketplace/)에서 선택합니다. 한 번 클릭으로 WordPress, Joomla 및 Drupal과 같은 패키지를 설치하여 OSS 앱 커뮤니티를 최대한 활용합니다.

- **WebJobs** - 앱 서비스 VM에서 [프로그램이나 스크립트를 실행](../app-service-web/web-sites-create-web-jobs.md)합니다. 일정에 따라 작업을 계속 실행하거나 이벤트에 의해 트리거됩니다. Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md)는 작성하는 코드를 간소화하여 다른 Azure 및 타사 서비스와 통합합니다.

- **하이브리드 연결** - [하이브리드 연결](../biztalk-services/integration-hybrid-connection-overview.md) 및 [Azure 가상 네트워크](../app-service-web/web-sites-integrate-with-vnet.md)를 사용하여 온-프레미스 데이터에 액세스합니다.

- **Visual Studio 통합** - Visual Studio의 전용 도구는 웹앱, 모바일 앱 및 API 앱을 생성, 배포, 사용, 디버그 및 관리하는 작업을 간소화합니다.

또한 웹앱은 [API 앱](../app-service-api/app-service-api-apps-why-best-platform.md)(예: CORS 지원) 및 [모바일 앱](../app-service-mobile/app-service-mobile-value-prop.md)(예: 푸시 알림)에서 제공하는 기능을 활용할 수 있습니다. 이러한 세 가지 앱 유형(API, 웹, 모바일) 간의 유일한 차이는 해당 이름과 Azure 포털에서 사용되는 아이콘뿐입니다. 앱 서비스에서 앱 유형에 대한 자세한 내용은 [Azure 앱 서비스 개요](../app-service/app-service-value-prop-what-is.md)를 참조하세요.

Azure는 앱 서비스의 웹앱 뿐만 아니라 웹 사이트와 웹 응용 프로그램 호스팅에 사용할 수 있는 다른 서비스를 제공합니다. 대부분의 시나리오의 경우 웹앱을 사용하는 것이 좋습니다. 마이크로 서비스 아키텍처의 경우 [서비스 패브릭](https://azure.microsoft.com/documentation/services/service-fabric)을 사용하는 것이 좋으며 코드가 실행되는 VM을 보다 자세히 제어해야 하는 경우 [Azure 가상 컴퓨터](https://azure.microsoft.com/documentation/services/virtual-machines/)를 사용합니다. 이러한 Azure 서비스 중에서 하나를 선택하는 방법에 대한 자세한 내용은 [Azure 앱 서비스, 가상 컴퓨터, 서비스 패브릭 및 클라우드 서비스 비교](choose-web-site-cloud-service-vm.md)를 참조하세요.

## 시작

앱 서비스에서 새 웹앱으로 샘플 코드를 배포하여 시작하려면 [5분 내에 Azure에 첫 번째 웹앱 배포](app-service-web-get-started.md) 자습서를 따르세요. 무료 Azure 계정이 필요합니다.

Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

<!---HONumber=AcomDC_0601_2016-->