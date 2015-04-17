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
	ms.date="03/06/2015" 
	ms.author="tdykstra"/>

# API 앱 정의

## 개요

Azure 앱 서비스는 다양한 기능을 웹, 모바일 및 통합 시나리오에 적용하는 전문 개발자를 위한 완전히 관리되는 계산 플랫폼입니다. API 앱은 앱 서비스 모음의 일부이며 이를 통해 기술을 잘 아는 사용자나 개발자는 기능이 풍부하고 확장 가능하며 전 세계에서 사용 가능한 최신 클라우드 플랫폼에서 API 및 SaaS 커넥터를 검색, 호스트, 관리 및 수익화할 수 있습니다.

## API 앱을 사용하는 이유

Azure 앱 서비스의 API 앱은 API를 쉽게 개발, 게시, 관리 및 수익화할 수 있게 해줍니다.

- **API를 있는 그대로 사용** - API에 ASP.NET, Java, PHP, Node.js 또는 Python을 사용할 수 있습니다. API는 Azure 앱 서비스의 기능을 변경 없이 활용할 수 있습니다.

- **널리 사용되는 SaaS 플랫폼에 앱 연결** - Azure 앱 서비스를 사용하면 Salesforce, Office 365, Twitter, Facebook, Dropbox 등 널리 사용되는 SaaS 플랫폼에 쉽게 연결할 수 있습니다.

- **간단한 액세스 제어** - API를 Azure 앱 서비스 내의 다른 앱이나 일반에 노출할 수 있으며 Azure Active Directory 또는 타사 서비스에 의한 인증을 변경 없이 코드에 추가할 수 있습니다.

- **공용 및 조직 API 갤러리** - 전용 API 조직 갤러리를 사용하여 조직 내의 다른 팀과 쉽게 API를 공유할 수 있습니다. 타사 개발자가 사용할 수 있도록 API를 공개적으로 공유할 수도 있습니다.

- **자동 SDK 생성** - Azure 앱 서비스에서는 C#, Java 및 JavaScript를 비롯한 다양한 언어용 SDK를 자동으로 빌드하여 API를 많은 플랫폼에 제공되도록 할 수 있습니다.

- **시중 최상의 IDE 사용** - [Visual
Studio](/campaigns/visual-studio-2013/) 통합에서는 API 만들기, 디버깅, 패키징 및 게시를 간소화하며 전체 수명 주기 앱 관리가 가능하도록 합니다.

<!--이 섹션의 크기를 줄이라는 지시에 따라 제거
- **연산자 없음** - 자동 패치를 통해 고가용성 환경에서 API 앱을 실행할 수 있습니다. Azure 앱 서비스를 통해 배포되는 API 앱은 응용 프로그램 전용 VM으로 격리 및 호스트되어 예측 가능한 성능 및 보안 격리를 보장합니다.

- **자동 확장** - Azure 앱 서비스를 사용하면 들어오는 고객 부하를 처리하기 위해 빠르게 강화 또는 규모 확장할 수 있습니다. 수동으로 VM 수와 크기를 선택하거나
[자동 확장](/documentation/videos/auto-scaling-azure-web-sites/)
을 설정하여 부하 또는 일정에 따라 서버를 확장할 수 있습니다.
-->

- **온-프레미스 데이터 액세스** - API 앱은 [하이브리드 연결](integration-hybrid-connection-overview.md) 및 [VNET](web-sites-integrate-with-vnet.md)을 사용하여 자체 데이터 센터의 데이터를 사용할 수 있습니다.

- **원활한 배포** - Visual Studio Online, GitHub, TeamCity, Hudson 또는 BitBucket을 통해 연속 통합 및 배포 워크플로를 설정하여 코드 체크 인 또는 통합 테스트에 성공할 때마다 API 앱을 자동으로 빌드, 테스트 및 배포할 수 있습니다.

## API 앱 개념 ##

- **API 앱 갤러리** - 계속 증가하는 기존 API 앱 템플릿 목록에서 선택할 수 있습니다.
- **커넥터** - Salesforce 및 Office 365와 같은 SaaS 플랫폼에 쉽게 연결할 수 있게 해주는 API 앱의 한 유형입니다.
- **게이트웨이** - 리소스 그룹의 모든 API 앱에 대한 인증과 같은 API 관리 기능을 처리하는 웹앱입니다. 
- **자동 확장** - API 앱은 들어오는 고객 부하를 처리하기 위해 자동으로 강화 또는 규모 확장할 수 있습니다. 수동으로 VM 수와 크기를 선택하거나 자동 확장을 설정하여 부하 또는 일정에 따라 서버를 확장할 수 있습니다.
- **연속 통합** - VSO, GitHub, TeamCity, Hudson 또는 BitBucket을 통해 연속 통합 및 배포 워크플로를 설정하여 코드 체크 인 또는 통합 테스트에 성공할 때마다 웹앱을 자동으로 빌드, 테스트 및 배포할 수 있습니다.

## 시작

API 앱을 시작하려면 [API 앱 만들기 자습서](app-service-dotnet-create-api-app.md)를 따르세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스](app-service-value-prop-what-is.md)를 참조하세요.

<!--HONumber=49-->