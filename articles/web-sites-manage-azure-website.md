<properties 
	pageTitle="Azure 웹 사이트 관리" 
	description="Microsoft Azure 웹 사이트 관리용 리소스의 링크입니다." 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2014" 
	ms.author="mwasson"/>

# Azure 웹 사이트 관리

이 항목에는 Azure 웹 사이트 관리를 위한 리소스의 링크가 포함되어 있습니다. 관리에는 웹 사이트를 지속적으로 원활하게 실행하는 모든 작업이 포함됩니다. 

사이트의 수명 주기 동안 초기 배포에서 일반 작업, 유지 관리 및 업데이트를 거치면서 여러 관리 작업을 수행합니다.

- [사이트를 프로덕션 환경으로 배포하기 전에 수행할 작업]
- [웹 사이트를 실행하는 동안 수행할 작업]
- [웹 사이트를 업데이트할 때 수행할 작업]

Azure 관리 포털에서 다양한 웹 사이트 관리 작업을 수행할 수 있습니다. 자세한 내용은 [Azure 관리 포털을 통해 웹 사이트 관리](http://azure.microsoft.com/documentation/articles/web-sites-manage/)를 참조하세요.

## 사이트를 프로덕션 환경으로 배포하기 전에 수행할 작업

### 계층 선택

Azure 웹 사이트는 무료, 공유, 기본, 표준의 네 가지 계층으로 제공됩니다. 각 계층의 기능 및 가격에 대한 자세한 내용은 [가격 세부 정보](http://azure.microsoft.com/pricing/details/websites/)를 참조하세요. 

- [웹 호스팅 계획](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview)에서는 같은 계층에 여러 웹 사이트를 그룹화할 수 있습니다.
- 웹 사이트를 만든 후에 언제든지 [계층을 전환](http://azure.microsoft.com/documentation/articles/web-sites-scale/)할 수 있습니다.

### 구성

[Azure 관리 포털](https://manage.windowsazure.com/)에서 다양한 구성 옵션을 설정합니다. 자세한 내용은 [웹 사이트를 구성하는 방법](http://azure.microsoft.com/documentation/articles/web-sites-configure/)을 참조하세요. 아래에 간단한 검사 목록이 나와 있습니다.

- 필요에 따라 .NET, PHP, Java 또는 Python의 **런타임 버전**을 선택합니다.
- 웹 사이트에서 WebSocket 프로토콜을 사용하는 경우 **WebSocket**을 사용하도록 설정합니다. 여기에는 [ASP.NET SignalR](http://www.asp.net/signalr) 또는 [socket.io](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-chat-app-socketio/)를 사용하는 앱이 포함됩니다.
- 연속적인 웹 작업을 실행하는 경우 **무중단**을 사용하도록 설정합니다.
- index.html 등의 **기본 문서**를 설정합니다.

이러한 기본 구성 설정 외에 다음 항목도 구성할 수 있습니다.

- **SSL(Secure Socket Layer)** 암호화. 사용자 지정 도메인 이름의 SSL을 사용하려면 SSL 인증서를 가져와 웹 사이트가 해당 인증서를 사용하도록 구성해야 합니다. [Azure 웹 사이트에 HTTPS 사용](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/)을 참조하세요.
- **사용자 지정 도메인 이름.** 웹 사이트에는 azurewebsites.net 아래의 하위 도메인이 자동으로 포함됩니다. contoso.com 등의 사용자 지정 도메인 이름을 연결할 수 있습니다. [사용자 지정 도메인 이름 구성](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/)을 참조하세요.

언어별 구성:

- **PHP**: [Azure 웹 사이트에서 PHP를 구성하는 방법](http://azure.microsoft.com/documentation/articles/web-sites-php-configure/)
- **Python**: [Azure 웹 사이트를 사용하여 Python 구성](http://azure.microsoft.com/documentation/articles/web-sites-python-configure/)


## 웹 사이트를 실행하는 동안 수행할 작업

사이트가 실행되는 동안에는 사이트가 사용 가능하며 사용자 트래픽을 충족하도록 크기가 조정되는지 확인해야 합니다. 오류가 발생하는 경우 오류 문제도 해결해야 합니다.

### 모니터링

- 관리 포털을 통해 CPU 사용량, 클라이언트 요청 수 등의 [성능 메트릭을 추가](http://azure.microsoft.com/documentation/articles/web-sites-monitor)할 수 있습니다.
- 보다 자세한 정보를 확인하려면 New Relic을 사용하여 성능을 모니터링 및 관리합니다. [Azure 웹 사이트에서 New Relic 응용 프로그램 성능 관리](http://azure.microsoft.com/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/)를 참조하세요.
- 트래픽에 맞게 [웹 사이트 크기를 조정](http://azure.microsoft.com/documentation/articles/web-sites-scale/)합니다. 계층에 따라 VM의 수 및/또는 VM 인스턴스의 크기를 조정할 수 있습니다. 표준 계획에서는 자동 크기 조정도 설정할 수 있으므로 고정된 일정으로 또는 부하에 따라 사이트 크기가 자동으로 조정됩니다.  
 
### 백업

- 웹 사이트의 [자동 백업](http://azure.microsoft.com/documentation/articles/web-sites-backup/)을 설정합니다. 백업에 대해 자세히 알아보려면 [이 비디오](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)를 시청하세요.
- Azure SQL 데이터베이스의 [재해 복구](http://msdn.microsoft.com/library/azure/hh852669.aspx) 옵션에 대해서도 알아보세요.

### 문제 해결

- 문제 발생 시에는 클라우드의 진단 로그 및 라이브 디버깅 기능을 사용하여 [Visual Studio에서 문제를 해결](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug)할 수 있습니다. 
- Visual Studio 외에도 다양한 방식을 통해 진단 로그를 수집할 수 있습니다. [Azure 웹 사이트에 진단 로그 사용](http://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)을 참조하세요.
- Node.js 응용 프로그램의 경우 [Azure 웹 사이트에서 Node.js 응용 프로그램 디버그 방법](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-debug/)을 참조하세요.

### 데이터 복원

- 이전에 백업한 웹 사이트를 [복원](http://azure.microsoft.com/documentation/articles/web-sites-restore/)할 수 있습니다.


## 웹 사이트를 업데이트할 때 수행할 작업

자동 백업을 사용하도록 설정하지 않은 경우 [수동 백업](http://azure.microsoft.com/documentation/articles/web-sites-backup/)을 만들 수 있습니다.

[준비된 배포](http://azure.microsoft.com/documentation/articles/web-sites-staged-publishing/)를 사용할 수 있습니다. 이 옵션을 사용하면 프로덕션 배포와 나란히 실행되는 스테이징 배포에 업데이트를 게시할 수 있습니다. 

Visual Studio Online을 사용하는 경우 소스 제어에서 연속 배포를 설정할 수 있습니다.

- [TFVC(Team Foundation Version Control) 사용](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [Git 사용](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[사이트를 프로덕션 환경으로 배포하기 전에 수행할 작업]: #before-you-deploy-your-site-to-production
[웹 사이트를 실행하는 동안 수행할 작업]: #while-your-website-is-running
[웹 사이트를 업데이트할 때 수행할 작업]: #when-you-update-your-website

 


<!--HONumber=42-->
