<properties 
	pageTitle="Azure 앱 서비스에서 웹 앱 관리" 
	description="Azure 앱 서비스에서 웹 앱 관리를 위한 리소스에 대한 링크입니다." 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="mwasson"/>

# Azure 앱 서비스에서 웹 앱 관리

이 항목에는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에서 웹 앱 관리를 위한 리소스의 링크가 포함되어 있습니다. 관리에는 웹 앱을 지속적으로 원활하게 실행하는 모든 작업이 포함됩니다.

웹 앱의 수명 주기 동안 초기 배포에서 일반 작업, 유지 관리 및 업데이트를 거치면서 여러 관리 작업을 수행합니다.

Azure 포털에서 다양한 웹 앱 관리 작업을 수행할 수 있습니다. 자세한 내용은 [Azure 포털을 사용하여 웹 앱 관리](web-sites-manage.md)를 참조하세요.

## 웹 앱을 프로덕션으로 배포하기 전에 수행할 작업

### 계층 선택

Azure 앱 서비스는 5개의 계층, 무료, 공유, 기본, 표준 및 프리미엄으로 제공됩니다. 각 계층의 기능 및 가격에 대한 자세한 내용은 [가격 세부 정보](/pricing/details/app-service/)를 참조하세요.

- [앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에서는 같은 계층에 여러 웹 앱을 그룹화할 수 있습니다.
- 웹 앱을 만든 후에 언제든지 [계층을 전환](web-sites-scale.md)할 수 있습니다.

### 구성

[Azure 미리 보기 포털](https://portal.azure.com/)을 사용하여 다양한 구성 옵션을 설정합니다. 자세한 내용은 [Azure 앱 서비스에서 웹 앱 구성](web-sites-configure.md)을 참조하세요. 아래에 간단한 검사 목록이 나와 있습니다.

- 필요에 따라 .NET, PHP, Java 또는 Python의 **런타임 버전**을 선택합니다.
- 웹 앱에서 WebSocket 프로토콜을 사용하는 경우 **WebSockets**을 사용하도록 설정합니다. 여기에는 [ASP.NET SignalR](http://www.asp.net/signalr) 또는 [socket.io](web-sites-nodejs-chat-app-socketio.md)를 사용하는 앱이 포함됩니다.
- 연속적인 웹 작업을 실행하는 경우 **무중단**을 사용하도록 설정합니다.
- index.html 등의 **기본 문서**를 설정합니다.

이러한 기본 구성 설정 외에 다음 항목도 구성할 수 있습니다.

- **SSL(Secure Socket Layer)** 암호화. 사용자 지정 도메인 이름과 SSL을 사용하려면 SSL 인증서를 가져와 웹 앱이 해당 인증서를 사용하도록 구성해야 합니다. [Azure 앱 서비스에서 웹 앱에 대한 HTTPS를 사용하도록 설정](web-sites-configure-ssl-certificate.md)을 참조하세요.
- **사용자 지정 도메인 이름.** 웹 앱에는 자동으로 azurewebsites.net 하위 도메인이 있습니다. Contoso.com 등의 사용자 지정 도메인 이름을 연결할 수 있습니다. [Azure 앱 서비스에서 사용자 지정 도메인 이름 구성](web-sites-custom-domain-name.md)을 참조하세요.

언어별 구성:

- **PHP**: [Azure 앱 서비스 웹 앱에서 PHP를 구성합니다.](web-sites-php-configure.md)
- **Python**: [Azure 앱 서비스 웹 앱에서 Python을 구성합니다.](web-sites-python-configure.md)


## 웹 앱을 실행하는 동안 수행할 작업

웹 앱이 실행되는 동안에는 웹 앱이 사용 가능하며 사용자 트래픽을 충족하도록 크기가 조정되는지 확인해야 합니다. 오류가 발생하는 경우 오류 문제도 해결해야 합니다.

### 모니터링

- Azure 미리 보기 포털을 통해 CPU 사용량, 클라이언트 요청 수 등의 [성능 메트릭을 추가](web-sites-monitor.md)할 수 있습니다.
- 보다 자세한 정보를 확인하려면 New Relic을 사용하여 성능을 모니터링 및 관리합니다. [New Relic 응용 프로그램 성능 관리를 사용하여 Azure 앱 서비스에서의 .NET 웹 앱](store-new-relic-web-sites-dotnet-application-performance-management.md)을 참조하세요.
- 트래픽에 맞게 [웹 앱 크기를 조정](web-sites-scale.md)합니다. 계층에 따라 VM의 수 및/또는 VM 인스턴스의 크기를 조정할 수 있습니다. 표준 및 프리미엄 계층에서는 자동 크기 조정도 설정할 수 있으므로, 고정된 일정 또는 부하에 따라 웹 앱 크기가 자동으로 조정됩니다.  
 
### 백업

- 웹 앱의 [자동 백업](web-sites-backup.md)을 설정합니다. 백업에 대해 자세히 알아보려면 [이 비디오](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)를 시청하세요.
- Azure SQL 데이터베이스의 [재해 복구](http://msdn.microsoft.com/library/azure/hh852669.aspx)용 옵션에 대해서도 알아볼 수 있습니다.

### 문제 해결

- 문제 발생 시에는 클라우드의 진단 로그 및 라이브 디버깅 기능을 사용하여 [Visual Studio에서 문제를 해결](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)할 수 있습니다. 
- Visual Studio 외에도 다양한 방식을 통해 진단 로그를 수집할 수 있습니다. [Azure 앱 서비스에서 웹 앱에 대한 진단 로깅 설정](web-sites-enable-diagnostic-log.md)을 참조하세요.
- Node.js 응용 프로그램의 경우, [Azure 앱 서비스에서 Node.js 웹 앱을 디버그하는 방법](web-sites-nodejs-debug.md)을 참조하세요.

### 데이터 복원

- 이전에 백업했던 웹 앱을 [복원](web-sites-restore.md)합니다.


## 웹 앱을 업데이트하는 시기

자동 백업을 사용하도록 설정하지 않은 경우 [수동 백업](web-sites-backup.md)을 만들 수 있습니다.

[단계적 배포](web-sites-staged-publishing.md)를 사용할 수 있습니다. 이 옵션을 사용하면 프로덕션 배포와 나란히 실행되는 스테이징 배포에 업데이트를 게시할 수 있습니다.

Visual Studio Online을 사용하는 경우 소스 제어에서 연속 배포를 설정할 수 있습니다.

- [TFVC(Team Foundation Version Control) 사용](../cloud-services-continuous-delivery-use-vso.md) 
- [Git 사용](../cloud-services-continuous-delivery-use-vso-git.md)
 
[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
<!-- Anchors. -->


[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website
<!--HONumber=54--> 