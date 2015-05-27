<properties 
	pageTitle="Azure WebJob 리소스" 
	description="Azure WebJobs 및 Azure WebJobs SDK를 사용하는 방법에 대해 설명하는 권장 리소스입니다." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# Azure WebJob 리소스

## 개요

이 항목은 Azure WebJob 및 Azure WebJobs SDK를 사용하는 방법에 대한 설명서 리소스에 연결됩니다. Azure WebJob은 쉽게 스크립트 또는 프로그램을 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에서 백그라운드 프로세스로 실행하는 편리한 방법을 제공합니다. cmd, bat, exe(.NET), ps1, sh, php, py, js 및 jar와 같은 실행 파일을 업로드하고 실행할 수 있습니다. 이러한 프로그램은 일정에 따라(cron) 또는 지속적으로 WebJob으로 실행됩니다.

WebJobs SDK를 사용하면 Azure 저장소를 보다 쉽게 사용할 수 있습니다. WebJobs SDK에는 Microsoft Azure 저장소 Blob, 큐 및 테이블 뿐만 아니라 서비스 버스 큐에서 작동하는 바인딩 및 트리거 시스템이 있습니다.

Visual Studio의 통합 도구를 사용하면 WebJob을 원활하게 만들고, 배포하고, 관리할 수 있습니다. 템플릿에서 WebJob을 만들고, 게시하고 관리(실행/중지/모니터링/디버깅)할 수 있습니다.

Azure 포털의 WebJob 대시보드는 WebJob 내의 개별 함수를 호출하는 기능을 비롯하여 WebJob의 실행을 완전히 제어할 수 있는 강력한 관리 기능을 제공합니다. 또한 대시보드에는 런타임 함수 및 로깅 출력도 표시됩니다.

##<a name="getstarted"></a>WebJobs 및 WebJobs SDK 시작

* [Azure WebJobs 소개](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs 기능](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [WebJobs SDK 정의](websites-dotnet-webjobs-sdk.md)
* [Microsoft Patterns and Practices에 따른 백그라운드 작업 지침](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [Microsoft Azure WebJobs SDK 1.0.0 RTM 발표](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Azure WebJobs SDK 시작](websites-dotnet-webjobs-sdk-get-started.md)
* [WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [WebJobs SDK를 사용하여 Azure Blob 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [WebJobs SDK를 사용하여 Azure 테이블 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [WebJobs SDK를 사용하여 Azure 서비스 버스로 작업하는 방법](websites-dotnet-webjobs-sdk-service-bus.md)
* [Azure WebJobs SDK 빠른 참조(PDF 다운로드)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* 비디오
	* [WebJobs 및 WebJobs SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Channel 9의 Azure WebJobs 비디오 시리즈](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [WebJobs Tooling for Visual Studio 소개](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs Tooling 및 원격 디버깅](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

[WebJobs 배포](#deploy) 및 [WebJobs 테스트 및 디버깅](#debug)에 대한 다음 섹션도 참조하세요.

##<a name="deploy"></a>WebJobs 배포

* [Visual Studio를 사용하여 Azure WebJobs를 배포하는 방법](websites-dotnet-deploy-webjobs.md)
* [Azure 관리 포털을 사용하여 WebJobs를 배포하는 방법](web-sites-create-web-jobs.md)
* [Azure WebJobs의 명령줄 또는 연속 배달 사용](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [WebJobs를 사용하여 Azure에 .NET 콘솔 앱을 배포하는 Git](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* 비디오
	* [WebJobs Tooling for Visual Studio 소개](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs Tooling 및 원격 디버깅](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>WebJobs 예약

* [Azure WebJob 추가 대화 상자](websites-dotnet-deploy-webjobs.md#configure)
* [Azure 관리 포털에서 예약된 WebJob 만들기](web-sites-create-web-jobs.md#CreateScheduled)

##<a name="debug"></a>WebJob 테스트 및 디버깅

* [Visual Studio의 Azure WebJobs에 대한 새로운 개발자 및 디버깅 기능](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [WebJobs 대시보드 보기](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [WebJobs SDK를 사용하여 로그를 작성하고 대시보드에서 보는 방법](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [원격 디버깅 WebJobs](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Blob 작성 주체](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [클라우드에서 대화형 코드 호스트](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [WebJobs SDK로 로컬 개발을 위한 대시보드 가져오기](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Azure WebJobs에 추적 추가](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Microsoft Azure 저장소 모니터링, 진단 및 문제 해결](../storage-monitoring-diagnosing-troubleshooting/)
* 비디오
	* [WebJobs Tooling 및 원격 디버깅](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>WebJobs 크기 조정

* [Azure 웹 사이트로 웹 응용 프로그램 크기 조정](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure 앱 서비스: 대규모 업무용 웹앱 보관](https://channel9.msdn.com/Events/Build/2014/3-626). WebJobs SDK를 비롯하여 WebJob을 사용한 웹앱 확장을 다룹니다.
* 비디오
	* [WebJobs 크기 조정](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>추가 WebJobs 리소스

* [Magnus Mårtensson의 Azure WebJobs GA 블로그 게시물](http://magnusmartensson.com/azure-webjobs-ga)
* [GitHub의 WebJobs 설정 설명서](https://github.com/projectkudu/kudu/wiki/Web-jobs)
* [Azure 앱 서비스에서 Powershell 웹 작업 실행](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Azure에서 트리거한 WebJobs 완료 시 알림 받기](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [WebJobs를 통한 간단한 웹앱 백업 보존 정책](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [최초 요청 시 느려지는 Azure 웹앱 및 클라우드 서비스](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). WebJobs를 사용하여 표준 가격 책정 계층에서만 사용할 수 있는 AlwaysOn 기능을 시뮬레이션하는 방법을 보여 줍니다.
* [WebJobs 정상 종료](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). WebJobs SDK 정상 종료에 대해서는 [정상 종료](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)를 참조하세요.
* [Azure WebJobs 및 AzCopy를 사용하여 백업 자동화](http://markjbrown.com/azure-webjobs-azcopy/)
* 비디오
	* [Magnus Mårtensson의 Azure WebJobs 비디오](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Channel 9의 Azure WebJobs 비디오 시리즈](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>추가 WebJobs SDK 리소스

* [WebJobs SDK 소스 코드](https://github.com/Azure/azure-webjobs-sdk)
* [AzureJobs의 Triggers, Bindings 및 Route 매개 변수](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Azure 저장소 바인딩
	* [Blob](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [큐](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [테이블](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [[BlobTrigger] 작동 방식](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Azure WebJobs SDK와의 고급 바인딩](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [WebJobs SDK를 사용하여 Azure 저장소에 FREB 파일을 업로드하기 위한 WebJob](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Azure 외부에 Azure WebJobs를 호스트하여 Azure 호스트 WebJob의 로깅 이점 얻기](http://bypassion.dk/?p=510)
* [Azure WebJobs를 사용하여 데이터 가져오기 도구 구축](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* 비디오
	* [Channel 9의 Azure WebJobs 비디오 시리즈](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>WebJob 응용 프로그램 예제

* [GitHub에 WebJobs 팀이 제공하는 응용 프로그램 예제](https://github.com/azure/azure-webjobs-sdk-samples)
* [WebJobs SDK를 사용한 WebJobs 백 엔드의 간단한 Azure 웹앱](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). 예약된 WebJob 및 이벤트 기반 WebJob의 사용 방식을 보여 줍니다. 블로그 게시물 [Azure WebJobs SDK를 사용하여 SiteMonitR 재구축](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)을 참조하세요.

##<a name="blogs"></a>블로그

* [Azure 블로그](/blog)
* [Amit Apple의 블로그](http://blog.amitapple.com/). SDK가 아닌 WebJob에 중점을 둡니다.
* [Magnus Mårtensson의 블로그](http://magnusmartensson.com/)

##<a name="gethelp"></a>WebJobs 관련 도움말 보기

* [WebJobs의 스택 오버플로](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [WebJobs SDK의 스택 오버플로](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure 및 ASP.NET 포럼](http://forums.asp.net/1247.aspx)
* [Azure 앱 서비스 웹앱 포럼](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure 웹앱 사용자 음성 사이트](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). 해시 태그 #AzureWebJobs를 사용합니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!--HONumber=54-->