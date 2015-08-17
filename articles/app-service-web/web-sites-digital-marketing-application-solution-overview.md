<properties 
	pageTitle="Azure 앱 서비스 웹 앱에서 디지털 마케팅 캠페인 만들기" 
	description="이 가이드에서는 Azure 앱 서비스 웹 앱을 사용하여 디지털 마케팅 캠페인을 만드는 방법에 대한 기술적 개요를 제공합니다. 여기에는 배포, 소셜 미디어 통합, 확장 전략 및 모니터링이 포함됩니다." 
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
	ms.date="07/06/2015" 
	ms.author="cephalin"/>

# Azure 앱 서비스 웹 앱에서 디지털 마케팅 캠페인 만들기
[Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱은 디지털 마케팅 캠페인에 탁월한 선택입니다. 디지털 마케팅 캠페인은 일반적으로 수명이 짧고 단기적인 마케팅 목표를 달성하기 위한 것입니다. 두 가지 주요 시나리오를 고려해야 합니다. 첫 번째 시나리오에서는 타 마케팅 회사가 프로모션 기간에 고객을 위한 캠페인을 만들고 관리합니다. 두 번째 시나리오에서는 마케팅 회사가 디지털 마케팅 캠페인을 만든 후 해당 리소스의 소유권을 고객에게 전송합니다. 그러면 고객이 디지털 마케팅 캠페인을 스스로 운영 및 관리합니다. Azure 웹 사이트는 두 가지 시나리오에 모두 적합합니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

다음은 앱 서비스 웹 앱을 사용한 글로벌 다중 채널 디지털 마케팅 캠페인의 예입니다. 이 예에서는 최소한의 기술 투자로 앱 서비스 웹 앱을 다른 서비스와 함께 구성하는 것만으로 수행할 수 있는 작업을 보여 줍니다. **토폴로지의 요소를 클릭하여 자세한 정보를 확인할 수 있습니다.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]이 가이드에서는 Azure 앱 서비스 웹 앱에서 디지털 마케팅 캠페인을 실행하는 데 적합한 가장 일반적인 몇 가지 영역과 작업에 대해 설명합니다. 그러나 앱 서비스 웹 앱에서 구현할 수 있는 다른 일반적인 시나리오도 있습니다. 이러한 솔루션에 대해 알아보려면 [글로벌 웹 서비스](web-sites-global-web-presence-solution-overview.md) 및 [비즈니스 응용 프로그램](web-sites-business-application-solution-overview.md)에서 제공하는 다른 가이드를 참조하세요.

## 자산 새로 만들기 또는 기존 자산 가져오기

갤러리에 있는 일반적인 CMS에서 새 웹 앱을 신속하게 만들거나, 다양한 언어와 프레임워크의 기존 웹 자산을 앱 서비스 웹 앱으로 가져올 수 있습니다.

Azure 마켓플레이스에서는 [Orchard], [Umbraco], Drupal, [WordPress] 등 일반적인 웹 사이트 CMS(콘텐츠 관리 시스템)의 템플릿을 제공합니다. 선호하는 CMS를 사용하여 웹 앱을 만들 수 있습니다. [Azure SQL 데이터베이스] 및 [MySQL] 등 다양한 데이터베이스 백 엔드 중에서 선택하여 요구를 충족할 수 있습니다.

기존 웹 자산은 .NET, PHP, Java, Node.js 또는 Python이든 상관없이 웹 앱에서 실행할 수 있습니다. 친숙한 [FTP] 도구를 사용하여 웹앱으로 이동할 수 있습니다. 디지털 마케팅 캠페인을 자주 만드는 경우, 소스 제어 관리 시스템에 기존 웹 자산이 있을 수 있습니다. [Visual Studio], [Visual Studio Online] 및 [Git](로컬, GitHub, BitBucket, DropBox, Mercurial 등)와 같은 일반적인 소스 제어 옵션에서 웹앱에 직접 게시할 수 있습니다.

## 민첩성 유지

지속적으로 기존 소스 제어에서 직접 게시하여 민첩성을 유지하고 앱 서비스 웹 앱에서 A/B 테스트를 실행할 수 있습니다.

웹앱의 계획, 프로토타입 및 초기 개발 과정 중에 사용자와 고객은 웹앱의 [스테이징 슬롯에 배포]하여 라이브 전에 캠페인 앱의 실제 작동 버전을 살펴볼 수 있습니다. 소스 제어를 앱 서비스 웹앱과 통합하면 스테이징 슬롯에 [지속적으로 게시]하고 준비가 되면 작동 중지 시간 없이 프로덕션으로 전환할 수 있습니다.

또한 라이브 웹앱을 변경하려는 경우 프로덕션에서 테스트 기능을 사용하여 제안된 업데이트에서 간편하게 [A/B 테스트를 실행]하고, 실시간 사용자 동작을 분석하여 정보를 바탕으로 앱 디자인에 대한 의사를 결정할 수 있습니다.


## 소셜로 확장

앱 서비스 웹 앱의 디지털 마케팅 캠페인은 Facebook 및 Twitter와 같은 일반적인 공급자와의 인증을 통해 소셜 미디어에 통합될 수 있습니다. ASP.NET 응용 프로그램에서 이러한 접근 방식의 예를 보려면 [인증 및 SQL DB를 사용하여 ASP.NET MVC 앱을 만들고 Azure 앱 서비스에 배포]를 참조하세요.

또한 각 소셜 미디어 사이트에서는 일반적으로 .NET 및 기타 여러 프레임워크에서 통합하는 다양한 방법에 대한 정보를 제공합니다.

## 리치 미디어 사용 및 모든 장치 연결

다음과 같은 다른 Azure 서비스로 디지털 마케팅 캠페인을 보강할 수 있습니다.

-  [Azure 미디어 서비스]를 사용하여 전역적으로 비디오 업로드 및 스트리밍
-  [Azure Marketplace의 SendGrid 서비스]를 사용하여 사용자에게 전자 메일 보내기
-  [모바일 서비스]를 사용하여 Windows, iOS 및 Android 장치에서 서비스 설정
-  [알림 허브]를 사용하여 수백만 개의 장치로 푸시 알림 보내기

## 글로벌로 확장

Azure 트래픽 관리자를 사용하여 지역 사이트를 제공하고, Azure CDN으로 콘텐츠를 신속하게 배달하여 글로벌로 확장할 수 있습니다.

각 지역에서 글로벌 고객을 지원하려면 [Azure 트래픽 관리자]를 사용하여 사이트 방문자를 최상의 성능을 제공하는 지역 사이트로 라우팅합니다. 또는 여러 지역에서 호스트되는 웹 앱의 여러 복사본에 걸쳐 사이트 부하를 균등하게 분산시킬 수 있습니다.

[웹앱을 Azure CDN과 통합]하여 사용자에게 정적 콘텐츠를 신속하게 배달할 수 있습니다. Azure CDN은 사용자에게 가장 가까운 [CDN 노드]에 있는 정적 콘텐츠를 캐시하므로 웹앱 연결 및 지연 시간이 최소화됩니다.

## 최적화

자동 크기 조정으로 크기를 자동으로 조정하고, Azure Redis Cache로 캐시하고, WebJobs로 백그라운드 작업을 실행하고, Azure 트래픽 관리자로 고가용성을 유지하여 웹 앱을 최적화할 수 있습니다.

앱 서비스 웹앱의 [수평 및 수직 확장] 기능은 디지털 마케팅 캠페인과 같은 예측할 수 없는 작업에 적합합니다. [Azure Preview 포털](http://go.microsoft.com/fwlink/?LinkId=529715)을 통해 수동으로, [서비스 관리 API] 또는 [PowerShell 스크립팅]을 통해 프로그래밍 방식으로 또는 자동 크기 조정 기능을 통해 자동으로 웹앱을 확장할 수 있습니다. **표준** 계층에서는 자동 크기 조정을 사용하여 CPU 사용률에 따라 웹앱을 자동으로 확장할 수 있습니다. 이 기능을 사용하면 사용자 활동에 따라 필요한 경우에만 웹 앱을 확장하여 민첩성을 극대화하는 동시에 비용을 최소화할 수 있습니다. 모범 사례는 [Troy Hunt]의 [Azure를 사용하여 웹앱을 신속하게 확장하는 방법에 대해 배운 10가지]를 참조하세요.

[Azure Redis Cache]는 웹앱 응답 속도를 높여 줍니다. 이를 사용하여 백 엔드 데이터베이스와 그 밖에 [ASP.NET 세션 상태], [출력 캐시] 등에서 데이터를 캐시할 수 있습니다.

[Azure 트래픽 관리자]를 사용하여 웹앱의 고가용성을 유지합니다. 트래픽 관리자는 **장애 조치(Failover)** 방법을 사용하여 기본 사이트에서 문제가 발생한 경우 트래픽을 보조 사이트로 자동으로 라우팅합니다.

## 모니터링 및 분석

Azure 또는 타사 도구를 사용하여 웹 앱의 성능을 최신 상태로 유지할 수 있습니다. 중요한 웹 앱 이벤트에 대한 알림을 받습니다. Application Insight 또는 HDInsight의 웹 로그 분석을 통해 손쉽게 사용자 통찰력을 얻을 수 있으며,

[Azure Preview 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에서 웹앱 블레이드의 리소스 할당량 및 웹앱의 현재 성능 메트릭을 [한 눈에 볼 수] 있습니다. 가용성, 성능 및 사용량에 대한 응용프로그램의 360° 보기의 경우 [Azure Application Insights]를 사용하여 빠르고 강력한 문제 해결, 진단 및 사용 현황 정보를 얻을 수 있습니다. 또는 [New Relic]과 같은 타사 도구를 사용하여 웹앱에 대한 고급 모니터링 데이터를 제공할 수 있습니다.

**표준** 계층에서는 앱 응답성 모니터링을 통해 웹앱이 응답하지 않을 때마다 이메일 알림을 받을 수 있습니다. 자세한 내용은 [방법: Azure에서 경고 알림 받기 및 경고 규칙 관리]를 참조하세요.

## 추가 리소스

- [앱 서비스 웹앱 설명서](/services/app-service/web/)
- [Azure 앱 서비스 웹앱 학습 맵](websites-learning-map.md)
- [Azure 웹 블로그](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[WordPress]: web-sites-php-web-site-gallery.md
  
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL 데이터베이스]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[스테이징 슬롯에 배포]: web-sites-staged-publishing.md
[지속적으로 게시]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[A/B 테스트를 실행]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[인증 및 SQL DB를 사용하여 ASP.NET MVC 앱을 만들고 Azure 앱 서비스에 배포]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Azure 미디어 서비스]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace의 SendGrid 서비스]: sendgrid-dotnet-how-to-send-email.md
[모바일 서비스]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[알림 허브]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Azure 트래픽 관리자]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[웹앱을 Azure CDN과 통합]: cdn-websites-with-cdn.md
[CDN 노드]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[수평 및 수직 확장]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[서비스 관리 API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell 스크립팅]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[Azure를 사용하여 웹앱을 신속하게 확장하는 방법에 대해 배운 10가지]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET 세션 상태]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[출력 캐시]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[한 눈에 볼 수]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[방법: Azure에서 경고 알림 받기 및 경고 규칙 관리]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=August15_HO6-->