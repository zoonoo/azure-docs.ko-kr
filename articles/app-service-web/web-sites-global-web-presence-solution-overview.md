<properties 
	pageTitle="Azure 앱 서비스 웹 앱에서 글로벌 웹 서비스 만들기" 
	description="이 가이드에서는 Azure 앱 서비스 웹 앱에서 조직의 사이트(.COM)를 호스팅하는 방법에 대한 기술적 개요를 제공합니다. 여기에는 배포, 사용자 지정 도메인, SSL 및 모니터링이 포함됩니다." 
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
	ms.date="04/08/2015" 
	ms.author="cephalin"/>


# Azure 앱 서비스 웹 앱에서 글로벌 웹 서비스 만들기

[Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹 앱에는 .COM 사이트에 대한 글로벌 웹 서비스를 설정하는 데 필요한 모든 기능이 있습니다. 비즈니스, 브랜드 인지도 및 고객 통신을 구축하려면 조직의 규모에 상관없이 강력하고 안전하며 확장 가능한 플랫폼이 필요합니다. 앱 서비스 웹 앱에서는 Microsoft에서 지원하는 비즈니스 연속성을 통해 기업 브랜드와 독자성을 유지 관리할 수 있습니다.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 응용 프로그램을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

다음은 앱 서비스 웹 앱에서 실행되는 .COM 웹 사이트의 예입니다. 이 예에서는 최소한의 기술 투자로 웹 앱을 다른 서비스와 함께 구성하는 것만으로 수행할 수 있는 작업을 보여 줍니다. **토폴로지의 요소를 클릭하여 자세한 정보를 확인할 수 있습니다.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> 이 가이드에서는 Azure 앱 서비스 웹 앱에서 공용 .COM 사이트를 실행하는 데 적합한 가장 일반적인 몇 가지 영역과 작업에 대해 설명합니다. 그러나 Azure 앱 서비스 웹 앱에서 구현할 수 있는 다른 일반적인 시나리오도 있습니다. 이러한 솔루션을 검토하려면 [디지털 마케팅 캠페인](web-sites-digital-marketing-application-solution-overview.md) 및 [비즈니스 응용 프로그램](web-sites-business-application-solution-overview.md)에서 기타 가이드를 참조하세요.

## 자산 새로 만들기 또는 기존 자산 가져오기

갤러리에 있는 일반적인 CMS에서 새 웹 사이트를 신속하게 만들거나, 다양한 언어와 프레임워크의 기존 웹 자산을 앱 서비스 웹 앱으로 가져올 수 있습니다.

Azure 마켓플레이스에서는 [Orchard], [Umbraco], [Drupal], [WordPress] 등 일반적인 웹 사이트 CMS(콘텐츠 관리 시스템)의 템플릿을 제공합니다. 선호하는 CMS를 사용하여 웹 앱을 만들 수 있습니다. [Azure SQL 데이터베이스] 및 [MySQL] 등 다양한 데이터베이스 백 엔드 중에서 선택하여 요구를 충족할 수 있습니다.

기존 웹 자산은 .NET, PHP, Java, Node.js 또는 Python이든 상관 없이 앱 서비스 웹 앱에서 실행할 수 있습니다. 친숙한 [FTP] 도구 또는 소스 제어 관리 시스템을 사용하여 웹 앱으로 이동할 수 있습니다. 웹 앱에서는 [Visual Studio], [Visual Studio Online] 및 [Git](로컬, GitHub, BitBucket, DropBox, Mercurial 등)와 같은 일반적인 소스 제어 옵션에서 직접 게시할 수 있습니다.

## 안정적으로 게시

기존 소스 제어 시스템에서 직접 게시하고 콘텐츠를 실시간으로 테스트하여 안정적으로 웹 사이트를 게시할 수 있습니다. 

사이트의 계획, 프로토타입 및 초기 개발 과정 중에 앱 서비스 웹 앱의 [스테이징 슬롯에 배포]하여 라이브 전에 웹 사이트의 실제 작동 버전을 살펴볼 수 있습니다. 소스 제어를 웹 앱과 통합하면 스테이징 슬롯에 [지속적으로 게시]하고 준비가 되면 작동 중지 시간 없이 프로덕션으로 전환할 수 있습니다. 프로덕션 사이트에서 문제가 발생한 경우 이전 버전의 사이트로 즉시 전환할 수도 있습니다. 

또한 라이브 웹 사이트를 변경하려는 경우 프로덕션에서 테스트 기능을 사용하여 제안된 업데이트에서 간편하게 [A/B 테스트를 실행]하고, 실시간 사용자 동작을 분석하여 정보를 바탕으로 사이트 디자인에 대한 의사를 결정할 수 있습니다.

## 브랜드 및 보안

앱 서비스 웹 앱 도메인을 무료로 사용하거나 등록된 도메인 이름에 매핑한 다음 CA 서명된 SSL 인증서로 브랜드의 보안을 유지할 수 있습니다.

***.azurewebsites.net** 도메인은 웹 앱에서 웹 사이트를 실행할 경우 무료로 제공됩니다. 또는 GoDaddy와 같은 DNS 레지스트리에서 가져온 [사용자 지정 도메인](예: contoso.com)에 웹 사이트를 매핑할 수 있습니다.

사용자 정보를 수집하거나, 전자 상거래를 수행하거나, 다른 중요한 데이터를 관리하는 경우 [HTTPS]를 사용하여 브랜드 평판 및 고객을 보호할 수 있습니다. ***.azurewebsites.net** 도메인 이름은 이미 SSL 인증서와 함께 제공되므로 사용자 지정 도메인을 사용하는 경우 해당 SSL 인증서를 웹 앱으로 가져올 수 있습니다. 각 SSL 인증서와 관련된 월별 요금(시간당 계산)이 부과됩니다. 자세한 내용은 [앱 서비스 가격 세부 정보]를 참조하세요.

## 글로벌로 확장

Azure 트래픽 관리자를 사용하여 지역 사이트를 제공하고, Azure CDN으로 콘텐츠를 신속하게 배달하여 글로벌로 확장할 수 있습니다.

각 지역에서 글로벌 고객을 지원하려면 [Azure 트래픽 관리자]를 사용하여 사이트 방문자를 최상의 성능을 제공하는 지역 사이트로 라우팅합니다. 또는 여러 지역에서 호스트되는 웹 사이트의 여러 복사본에 걸쳐 사이트 부하를 균등하게 분산시킬 수 있습니다.

[웹 앱을 Azure CDN과 통합]하여 사용자에게 정적 콘텐츠를 신속하게 배달할 수 있습니다. Azure CDN은 사용자에게 가장 가까운 [CDN 노드]에 있는 정적 콘텐츠를 캐시하므로 웹 사이트 연결 및 지연 시간이 최소화됩니다.

## 최적화

자동 크기 조정으로 크기를 자동으로 조정하고, Azure Redis Cache로 캐시하고, WebJobs로 백그라운드 작업을 실행하고, Azure 트래픽 관리자로 고가용성을 유지하여 .COM 사이트를 최적화할 수 있습니다.

앱 서비스 웹 앱의 [수평 및 수직 확장] 기능은 작업의 크기에 상관없이 .COM 사이트의 요구를 충족합니다. [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)을 통해 수동으로, [서비스 관리 API] 또는 [PowerShell 스크립팅]을 통해 프로그래밍 방식으로 또는 자동 크기 조정 기능을 통해 자동으로 웹 사이트를 확장할 수 있습니다. **표준** 호스팅 계획에서는 자동 크기 조정을 사용하여 CPU 사용률에 따라 웹 사이트를 자동으로 확장할 수 있습니다. 모범 사례는 [Troy Hunt]의 [Azure를 사용하여 웹 앱을 신속하게 확장하는 방법에 대해 배운 10가지]를 참조하세요.

[Azure Redis Cache]는 웹 사이트 응답 속도를 높여 줍니다. 이를 사용하여 백 엔드 데이터베이스와 그 밖에 [ASP.NET 세션 상태], [출력 캐시] 등에서 데이터를 캐시할 수 있습니다.

[Azure 트래픽 관리자]는 웹 사이트의 고가용성을 유지하는 데 사용됩니다. 트래픽 관리자는 **장애 조치(Failover)** 방법을 사용하여 기본 사이트에서 문제가 발생한 경우 트래픽을 보조 사이트로 자동으로 라우팅합니다.

## 모니터링 및 분석

Azure 또는 타사 도구를 사용하여 웹 사이트의 성능을 최신 상태로 유지할 수 있습니다. 중요한 웹 사이트 이벤트에 대한 알림을 받고, Application Insight 또는 HDInsight의 웹 로그 분석을 통해 손쉽게 사용자 통찰력을 얻을 수 있으며, 

[Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에서 웹 앱 블레이드의 리소스 할당량 및 웹 사이트의 현재 성능 메트릭을 [한 눈에 볼 수] 있습니다. 가용성, 성능 및 사용량에 대한 응용 프로그램의 360° 보기의 경우 [Azure Application Insights]를 사용하여 빠르고 강력한 문제 해결, 진단 및 사용 현황 정보를 얻을 수 있습니다. 또는 [New Relic]과 같은 타사 도구를 사용하여 웹 사이트에 대한 고급 모니터링 데이터를 제공할 수 있습니다.

**표준** 호스팅 계획에서는 사이트 응답성 모니터링을 통해 사이트가 응답하지 않을 때마다 전자 메일 알림을 받을 수 있습니다. 자세한 내용은 [방법: Azure에서 경고 알림 받기 및 경고 규칙 관리]를 참조하세요.

## 리치 미디어 사용 및 모든 장치 연결

다음과 같은 리치 미디어로 .COM 사이트의 매력적으로 만들 수 있습니다.

-  [Azure 미디어 서비스]를 사용하여 전역적으로 비디오 업로드 및 스트리밍
-  [Azure Marketplace의 SendGrid 서비스]를 사용하여 사용자에게 전자 메일 보내기

## 추가 리소스

- [앱 서비스 웹 앱 설명서](/services/app-service/web/)
- [Azure 앱 서비스 웹 앱 학습 맵](websites-learning-map.md)
- [Azure 웹 블로그](/blog/topics/web/)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 다음을 참조하세요. [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)
* 이전 포털에서 새 포털로의 변경에 대한 지침은 다음을 참조하세요. [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)

[Azure 앱 서비스]: /services/app-service/web/

[Orchard]:web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]:web-sites-gallery-umbraco.md
[Drupal]:web-sites-php-migrate-drupal.md
[WordPress]:web-sites-php-web-site-gallery.md
[MySQL]:web-sites-php-mysql-deploy-use-git.md
[Azure SQL 데이터베이스]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Online]:../cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[스테이징 슬롯에 배포]:web-sites-staged-publishing.md 
[지속적으로 게시]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[A/B 테스트를 실행]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[사용자 지정 도메인]:web-sites-custom-domain-name.md
[HTTPS]:web-sites-configure-ssl-certificate.md
[앱 서비스 가격 세부 정보]: /pricing/details/app-service/#ssl-connections

[Azure 트래픽 관리자]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[웹 앱을 Azure CDN과 통합]:cdn-websites-with-cdn.md 
[CDN 노드]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[수평 및 수직 확장]:web-sites-scale.md
[Azure 관리 포털]:http://manage.windowsazure.com/
[서비스 관리 API]:https://msdn.microsoft.com/library/azure/ee460799.aspx
[PowerShell 스크립팅]:https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[Azure를 사용하여 웹 앱을 신속하게 확장하는 방법에 대해 배운 10가지]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET 세션 상태]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[출력 캐시]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[한 눈에 볼 수]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:../store-new-relic-cloud-services-dotnet-application-performance-management.md
[방법: Azure에서 경고 알림 받기 및 경고 규칙 관리]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure 미디어 서비스]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace의 SendGrid 서비스]:sendgrid-dotnet-how-to-send-email.md



<!--HONumber=52--> 