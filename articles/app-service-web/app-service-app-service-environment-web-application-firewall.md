<properties 
	pageTitle="앱 서비스 환경에 대한 웹 응용 프로그램 방화벽(WAF) 구성" 
	description="앱 서비스 환경의 앞에 웹 응용 프로그램 방화벽을 구성하는 방법에 알아봅니다." 
	services="app-service\web" 
	documentationCenter="" 
	authors="naziml" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="naziml"/>

# 앱 서비스 환경에 대한 웹 응용 프로그램 방화벽(WAF) 구성

## 개요 ##
[Azure Marketplace](http://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/)에서 사용 가능한 [Azure용 Barracuda WAF](https://www.barracuda.com/programs/azure)와 같은 웹 응용 프로그램 방화벽은 SQL 주입, 교차 사이트 스크립팅, 맬웨어 업로드와 DDos 응용 프로그램 및 다른 공격을 막는 인바운드 웹트래 픽을 검사하여 웹 응용 프로그램 보안을 도와줍니다. 데이터 손실 방지 DLP (Data Loss Prevention)에 대한 백엔드 웹 서버로부터의 응답도 검사합니다. 앱 서비스 환경은 격리와 추가 확장의 조합을 제공합니다. 이 조합은 악의적인 요청과 고용량 트래픽을 견뎌야 하는 호스트 비즈니스 중요한 웹 응용 프로그램에 이상적인 환경을 제공합니다.

\+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 설정 ##
이 문서에서는 Barracuda WAF의 다중 부하 분산 인스턴스 뒤의 앱 서비스 환경을 구성하여 WAF의 트래픽만이 앱 서비스 환경에 도달할 수 있게 하고 DMZ로부터는 접근할 수 없습니다. Azure 트래픽 관리자를 Azure 데이터 센터와 지역 간의 작업 부하를 위해 Barracuda WAF 앞에 놓겠습니다. 설치 프로그램의 높은 수준의 다이어그램은 아래와 비슷합니다.

![아키텍처][Architecture]

## 앱 서비스 환경 구성 ##
앱 서비스 환경을 구성하려면 해당 제목의 [설명서](app-service-web-how-to-create-an-app-service-environment.md)를 참조하세요. 앱 서비스 환경을 만들면 [웹앱](app-service-web-overview.md), [API 앱](app-service-api-apps-why-best-platform.md) 및 [모바일 앱](app-service-mobile-value-prop-preview.md)을 다음에 섹션에서 구성할 WAF로 모두 보호 받는 환경에서 만들 수 있습니다.

## Barracuda WAF 클라우드 서비스를 구성합니다. ##
Barracuda에는 Azure의 가상 컴퓨터에 WAF를 배포하는 방법에 대한 [자세한 문서](https://techlib.barracuda.com/WAF/AzureDeploy)가 있습니다. 이 설명서를 따라할 때 중복성을 원하고 단일 실패 지점을 도입하지 않는 것뿐만 아니라, 동일한 클라우드 서비스 안에 최소 2개의 WAF 인스턴스 VM을 배포하는 것을 원합니다.

### 클라우드 서비스에 끝점 추가 ###
클라우드 서비스 내에 2개나 그 이상의 WAF VM이 있다면, [Azure 관리 포털](https://portal.azure.com)을 사용하여 아래의 그림처럼 응용 프로그램에서 사용하는 HTTP와 HTTPS 끝점을 추가할 수 있습니다.

![끝점 구성][ConfigureEndpoint]

응용 프로그램에서 다른 끝점을 사용하는 경우 이 목록에 추가해야 합니다.

### 해당 관리 포털을 통해 WAF Barracuda 구성 ###
해당 관리 포털을 통해 Barracuda WAF를 사용하여 TCP Port 8000에 대해 구성합니다. WAF VM의 여러 인스턴스가 있기 때문에 각 VM에 이 단계를 반복해서 수행해야 합니다.


> 참고: WAF 구성이 끝났다면, WAF 보안을 유지하기 위해 모든 WAF VM에서 TCP/8000 끝점을 제거합니다.

아래의 그림처럼 관리 끝점을 추가하여 Barracuda WAF를 구성합니다.

![관리 끝점 추가][AddManagementEndpoint]
 
브라우저를 사용하여 클라우드 서비스에서 관리 끝점으로 이동 합니다. 클라우드 서비스 이름이 test.cloudapp.net 이라면 http://test.cloudapp.net:8000로 이동하여 이 끝점에 액세스합니다. 로그인 페이지를 참조해야 아래와 같은 WAF VM 설치 단계에서 지정한 자격 증명을 사용하여 로그인할 수 있습니다.

![관리 로그인 페이지][ManagementLoginPage]

로그인 하면 아래 그림 안의 1처럼 대시보드를 참조해야 WAF 보호에 관한 기본 통계를 표시합니다.

![관리 대시보드][ManagementDashboard]

서비스 탭을 클릭하여 WAF가 보호하는 서비스에 대해 구성할 수 있습니다. Barracuda WAF를 구성하는 방법에 대한 자세한 내용은 [해당 설명서](https://techlib.barracuda.com/waf/getstarted1)를 참조하세요. 아래 예제는 Azure 웹앱이 구성된 HTTP 및 HTTPS로 트래픽을 제공합니다.

![관리 서비스를 추가 합니다.][ManagementAddServices]

> 참고: 응용 프로그램이 어떻게 구성되었는지, 앱 서비스 환경에서 어떤 기능들이 쓰이고 있는지에 따라서 트래픽을 80 및 443이 아닌 TCP 포트로 전달해야 합니다(예: 웹앱에 대한 IP SSL을 설치한 경우). 앱 서비스 환경에서 사용되는 네트워크 포트의 목록은 [인바운드 트래픽을 제어 설명서](app-service-app-service-environment-control-inbound-traffic.md)의 네트워크 포트 섹션을 참조하세요.

## Microsoft Azure 트래픽 관리자를 구성합니다. (선택 사항) ##
응용 프로그램이 여러 지역에서 사용할 수 있는 경우, [Azure 트래픽 관리자](traffic-manager.md)를 사용하여 부하 분산하고자 합니다. 이 작업을 수행하려면 아래의 그림처럼 트래픽 관리자 프로필의 WAF 클라우드 서비스 이름을 이용하여 [Azure 관리 포털](https://manage.azure.com)에서 끝점을 추가합니다.

![트래픽 관리자 끝점][TrafficManagerEndpoint]

응용 프로그램에 대해 인증이 필요한 경우, 응용 프로그램의 가용성에 대해 ping하는 트리팩 관리자에 대한 어떤 자격 증명도 필요하지 않는 리소스가 남아 있는지 확인합니다. 아래 그림처럼 [Azure 관리 포털](https://manage.azure.com)의 구성 섹션에서 URL을 구성할 수 있습니다.

![트래픽 관리자를 구성하는 방법][ConfigureTrafficManager]

아래의 예가 보여주는 것처럼 WAF에서 응용 프로그램으로 트래픽 관리자 Ping을 전송하려면, 웹 사이트 번역을 Barracuda WAF에 설치하여 트래픽을 응용 프로그램으로 전송하게 합니다.

![웹사이트 번역][WebsiteTranslations]

## 네트워크 리소스 그룹을 사용하여 앱 서비스 환경에 대한 트래픽 보안##
클라우드 서비스의 VIP 주소만 사용하여 WAF에서 앱 서비스 환경으로 들어오는 트래픽을 제한하는 방법에 대한 자세한 내용은 [인바운드 트래픽 제어 설명서](app-service-app-service-environment-control-inbound-traffic.md)를 참조하세요. TCP 포트 80에 대한 작업을 수행 하기 위한 샘플 Powershell 명령은 다음과 같습니다.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

프로그램 WAF 클라우드 서비스의 가상 IP 주소 (VIP)는 SourceAddres Prefix를 바꿉니다.

> 참고: 클라우드 서비스의 VIP는 클라우드 서비스를 삭제하고 다시 만들 때 변경 됩니다. 이렇게 하면 네트워크 리소스 그룹의 IP 주소를 업데이트해야 합니다.
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png

<!---HONumber=Oct15_HO3-->