<properties 
	pageTitle="Azure 앱 서비스에서 LOB(기간 업무) 웹 앱 만들기" 
	description="이 가이드에서는 Azure 앱 서비스 웹 앱을 사용하여 인트라넷 LOB(기간 업무) 응용 프로그램을 만드는 방법에 대한 기술적 개요를 제공합니다. 여기에는 인증 전략, 서비스 버스 릴레이 및 모니터링이 포함됩니다." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="cephalin"/>



# Azure 앱 서비스에서 LOB(기간 업무) 웹 앱 만들기

[Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱은 기간 업무 응용프로그램에 탁월한 선택입니다. 이러한 응용 프로그램은 내부 업무에 사용하기 위해 보안을 유지해야 하는 인트라넷 응용 프로그램입니다. 일반적으로 회사 디렉터리에 대한 인증과 온-프레미스 데이터 및 서비스의 일부 액세스 또는 통합에 대한 인증이 필요합니다.

기간 업무 응용 프로그램을 앱 서비스 웹 앱으로 이동할 경우에 얻을 수 있는 주요 이점은 다음과 같습니다.

-  연간 성과 검토를 처리하는 응용 프로그램과 같은 동적 작업에 따라 확장 및 축소할 수 있습니다. 대기업의 경우 검토 기간에는 트래픽이 급격히 상승합니다. Azure는 일 년 중 트래픽이 높은 검토 기간에는 로드 처리를 위해 확장하고 나머지 기간에는 다시 축소하여 비용을 절약할 수 있는 확장 옵션을 제공합니다. 
-  인프라 취득 및 관리보다 응용 프로그램 개발에 좀 더 중점을 둘 수 있습니다.
-  직원 및 파트너가 어디에서든 응용 프로그램을 사용하도록 향상된 지원을 제공할 수 있습니다. 사용자는 응용 프로그램을 사용하기 위해 회사 네트워크에 연결할 필요가 없으며, IT 그룹은 복잡한 역방향 프록시 솔루션을 마련할 필요가 없습니다. 회사 응용 프로그램에 대한 액세스를 보호하기 위한 몇 가지 인증 옵션이 있습니다.

다음은 앱 서비스 웹 앱에서 실행되는 기간 업무 응용 프로그램의 예입니다. 이 예에서는 최소한의 기술 투자로 웹 앱을 다른 서비스와 함께 구성하는 것만으로 수행할 수 있는 작업을 보여 줍니다. **토폴로지의 요소를 클릭하여 자세한 정보를 확인할 수 있습니다.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
이 가이드에서는 LOB(기간 업무) 응용 프로그램에 맞는 가장 일반적인 몇 가지 영역과 작업에 대해 설명합니다. 하지만 각자의 특정 구현에 사용할 수 있는 Azure 앱 서비스 웹 앱의 다른 기능도 있습니다. 이러한 기능에 대해 알아보려면 [글로벌 웹 서비스](web-sites-global-web-presence-solution-overview.md)(영문) 및 [디지털 마케팅 캠페인](web-sites-digital-marketing-application-solution-overview.md)(영문)에서 제공하는 다른 가이드도 참조하십시오.

## 기존 자산 가져오기

다양한 언어 및 프레임워크에서 앱 서비스 웹 앱에 기존 웹 자산을 가져옵니다.

기존 웹 자산은 .NET, PHP, Java, Node.js 또는 Python이든 상관 없이 앱 서비스 웹 앱에서 실행할 수 있습니다. 친숙한 [FTP] 도구 또는 소스 제어 관리 시스템을 사용하여 웹앱으로 이동할 수 있습니다. 웹앱에서는 [Visual Studio], [Visual Studio Team Services], [Git](로컬, GitHub, BitBucket, DropBox, Mercurial 등)과 같은 일반적인 소스 제어 옵션에서 직접 배포할 수 있습니다.

## 자산 보안 유지

암호화로 자산의 보안을 유지하고, 온사이트 또는 원격 회사 사용자를 인증하고, 자산 사용 권한을 부여할 수 있습니다.

[HTTPS]를 사용하여 내부 자산의 도용을 방지할 수 있습니다. ***.azurewebsites.net** 도메인 이름은 이미 SSL 인증서와 함께 제공되므로 사용자 지정 도메인을 사용하는 경우 해당 SSL 인증서를 앱 서비스 웹앱으로 가져올 수 있습니다. 각 SSL 인증서와 관련된 월별 요금(시간당 계산)이 부과됩니다. 자세한 내용은 [앱 서비스 가격 세부 정보]를 참조하세요.

회사 디렉터리에 대해 [사용자를 인증]할 수 있습니다. 앱 서비스 웹 앱에서는 AD FS(Active Directory Federation Services)와 같은 온-프레미스 ID 공급자 또는 회사 Active Directory 배포와 동기화된 Azure Active Directory 테넌트를 통해 사용자를 인증할 수 있습니다. 사용자는 온사이트에 있든, 현장에 있든 SSO(Single Sign-On)를 통해 웹 앱에서 웹 자산에 액세스할 수 있습니다. Office 365 또는 Microsoft Intune과 같은 기존 서비스에서는 이미 Azure Active Directory를 사용합니다. [간편한 인증]을 통해 웹앱의 동일한 Azure Active Directory 테넌트에 대한 인증을 매우 쉽게 설정할 수 있습니다.

사용자에게 웹 자산 [사용 권한]을 부여할 수 있습니다. 최소한의 추가 코드를 사용하여 예를 들어 `[Authorize]` 장식을 사용하여 동일한 온-프레미스 ASP.NET 코딩 패턴을 앱 서비스 웹앱으로 가져올 수 있습니다. 온-프레미스에서 유지 관리하는 응용 프로그램과 동일한 수준의 유연성으로 세분화된 액세스 제어를 유지할 수 있습니다.

## 온-프레미스 리소스 연결 ##

성능을 위해 클라우드에 있든, 규정 준수를 위해 온-프레미스에 있든 저장된 위치에 상관없이 웹 앱 데이터 또는 리소스에 연결할 수 있습니다. Azure에서 데이터를 유지하는 방법에 대한 자세한 내용은 [Azure 보안 센터]를 참조하세요.

[Azure SQL 데이터베이스] 및 [MySQL] 등 Azure의 다양한 데이터베이스 백 엔드 중에서 선택하여 웹앱에 대한 요구를 충족할 수 있습니다. Azure에서 데이터를 안전하게 유지하면 데이터가 웹 앱과 지리적으로 가까이 위치하고 해당 성능이 최적화됩니다.

그러나 회사에서 데이터를 온-프레미스에 유지해야 하는 경우도 있을 수 있습니다. 앱 서비스 웹앱에서는 데이터베이스 백 엔드와 같은 온-프레미스 리소스에 대한 [하이브리드 연결]을 쉽게 설정할 수 있습니다. 온-프레미스 연결에 대한 통합 관리가 필요한 경우 사이트 간 VPN이 있는 하나의 [Azure 가상 네트워크]에 여러 웹앱을 통합할 수 있습니다. 그런 다음 웹 앱이 온-프레미스에 있는 것처럼 온-프레미스 리소스에 액세스할 수 있습니다.

## 최적화

자동 크기 조정으로 크기를 자동으로 조정하고, Azure Redis Cache로 캐시하고, WebJobs로 백그라운드 작업을 실행하고, Azure 트래픽 관리자로 고가용성을 유지하여 LOB(기간 업무) 응용 프로그램을 최적화할 수 있습니다.

앱 서비스 웹앱의 [수평 및 수직 확장 기능]은 워크로드의 크기에 상관없이 기간 업무 응용프로그램의 요구를 충족합니다. [Azure 포털]을 통해 수동으로, [서비스 관리 API] 또는 [PowerShell 스크립팅]을 통해 프로그래밍 방식으로 또는 자동 크기 조정 기능을 통해 자동으로 웹앱을 확장할 수 있습니다. **표준** 계층에서는 자동 크기 조정을 사용하여 CPU 사용률에 따라 웹앱을 자동으로 확장할 수 있습니다. 모범 사례는 [Troy Hunt]의 [Azure를 사용하여 웹앱을 신속하게 확장하는 방법에 대해 배운 10가지]를 참조하세요.

[Azure Redis Cache]는 웹앱 응답 속도를 높여 줍니다. 이를 사용하여 백 엔드 데이터베이스와 그 밖에 [ASP.NET 세션 상태], [출력 캐시] 등에서 데이터를 캐시할 수 있습니다.

[Azure 트래픽 관리자]를 사용하여 웹앱의 고가용성을 유지합니다. 트래픽 관리자는 **장애 조치(Failover)** 방법을 사용하여 기본 사이트에서 문제가 발생한 경우 트래픽을 보조 사이트로 자동으로 라우팅합니다.

## 모니터링 및 분석

Azure 또는 타사 도구를 사용하여 웹 앱의 성능을 최신 상태로 유지할 수 있습니다. 중요한 웹 앱 이벤트에 대한 알림을 받습니다. Application Insight 또는 HDInsight의 웹 로그 분석을 통해 손쉽게 사용자 통찰력을 얻을 수 있으며,

[Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에서 웹앱 블레이드의 리소스 할당량 및 웹앱의 현재 성능 메트릭을 [한 눈에 볼 수] 있습니다. 가용성, 성능 및 사용량에 대한 응용프로그램의 360° 보기의 경우 [Azure Application Insights]를 사용하여 빠르고 강력한 문제 해결, 진단 및 사용 현황 정보를 얻을 수 있습니다. 또는 [New Relic]과 같은 타사 도구를 사용하여 웹앱에 대한 고급 모니터링 데이터를 제공할 수 있습니다.

**표준** 계층에서는 앱 응답성 모니터링을 통해 앱이 응답하지 않을 때마다 이메일 알림을 받을 수 있습니다. 자세한 내용은 [방법: Azure에서 경고 알림 받기 및 경고 규칙 관리]를 참조하세요.

## 추가 리소스

- [앱 서비스 웹앱 설명서](/services/app-service/web/)
- [Azure 앱 서비스 웹앱 학습 맵](/documentation/learning-paths/appservice-webapps/)
- [Azure 웹 블로그](/blog/topics/web/)

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]



[Azure App Service]: /services/app-service/web/

[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Team Services]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[HTTPS]: web-sites-configure-ssl-certificate.md
[앱 서비스 가격 세부 정보]: /pricing/details/app-service/#ssl-connections
[사용자를 인증]: web-sites-authentication-authorization.md
[간편한 인증]: /blog/2014/11/13/azure-websites-authentication-authorization/
[사용 권한]: web-sites-authentication-authorization.md

[Azure 보안 센터]: /support/trust-center/
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL 데이터베이스]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[하이브리드 연결]: web-sites-hybrid-connection-get-started.md
[Azure 가상 네트워크]: web-sites-integrate-with-vnet.md

[수평 및 수직 확장 기능]: web-sites-scale.md
[Azure 포털]: http://portal.azure.com/
[서비스 관리 API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell 스크립팅]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[Azure를 사용하여 웹앱을 신속하게 확장하는 방법에 대해 배운 10가지]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET 세션 상태]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[출력 캐시]: https://msdn.microsoft.com/library/azure/dn798898.aspx
[Azure 트래픽 관리자]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx

[한 눈에 볼 수]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[방법: Azure에서 경고 알림 받기 및 경고 규칙 관리]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

 

<!---HONumber=AcomDC_0302_2016-->