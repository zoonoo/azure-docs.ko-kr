<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

Azure 2014년 봄 릴리스 중요한 정보
==================================

이 문서에서는 Build 2014 회의에서 Azure .NET 개발자에게 프레젠테이션했으며 Azure SDK for .NET 2.3, Visual Studio 2013 Update 2 및 기타 봄 릴리스에서 사용할 수 있는 새로운 도구, 기능, 테마를 요약합니다.

Azure SDK for .NET 2.3에 대한 자세한 전체 내용은 [릴리스 정보를 참조](http://go.microsoft.com/fwlink/p/?LinkId=393548)(영문)하십시오.

4월 2-4일의 현황을 주문형 스트리밍으로 제공하는 [Build 비디오를 시청](http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409)(영문)하십시오.

목차
----

-   [웹 개발 및 게시](#webdeploy)
-   [진단 및 디버깅](#diagnostics)
-   [Visual Studio에서 Azure 서비스 관리](#service-management)
-   [PowerShell을 사용한 자동화](#automation)
-   [.NET을 사용한 모바일 개발](#mobile)
-   [리소스 관리자](#arm)

웹 개발 및 게시
---------------

Azure SDK 2.3에는 Azure를 사용한 웹 개발 및 게시를 간소화하는 몇 가지 업데이트가 포함되어 있습니다. 이제 새 웹 앱을 만들고 웹 배포를 사용하여 웹 사이트 또는 VM에 배포할 때 Azure 웹 사이트 또는 가상 컴퓨터를 프로비전하고 만들 수 있습니다. 새로운 기능을 활용하는 방법을 설명하는 자세한 내용 및 자습서는 다음 리소스를 참조하십시오.

-   [Visual Studio용 Azure 도구 사용(영문)](http://msdn.microsoft.com/en-us/library/azure/ff687127.aspx)
-   [Visual Studio 2013에서 ASP.NET 웹 프로젝트 만들기(영문)](http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio)
-   [Azure 및 ASP.NET 시작](http://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-get-started/)

진단 및 디버깅
--------------

가상 컴퓨터에 대한 새로운 원격 디버깅 및 새로운 네이티브 코드 디버깅을 사용하여 응용 프로그램 문제를 원격으로 진단합니다.

-   [Visual Studio에서 클라우드 서비스 또는 가상 컴퓨터 디버깅(영문)](http://msdn.microsoft.com/en-us/library/azure/ff683670.aspx)

Emulator Express는 클라우드 서비스에 사용되는 경량의 새로운 로컬 에뮬레이터입니다. 이를 사용하여 로컬 컴퓨터에서 클라우드 서비스를 테스트할 수 있는 방법을 알아보십시오.

-   [Emulator Express를 사용하여 클라우드 서비스 실행 및 디버그(영문)](http://msdn.microsoft.com/en-us/library/windowsazure/dn339018.aspx)

이제 Azure 웹 사이트에 배포된 앱을 Visual Studio에서 바로 원격으로 보고 디버그할 수 있습니다. 자세한 내용은 다음을 참조하십시오.

-   [Visual Studio에서 Azure 웹 사이트 문제 해결](http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio)

Visual Studio에서 Azure 서비스 관리
-----------------------------------

IDE에서 VM을 만드는 기능을 포함하여 Visual Studio의 향상된 가상 컴퓨터 관리를 활용하십시오.

-   [서버 탐색기에서 가상 컴퓨터 만들기(영문)](http://msdn.microsoft.com/en-us/library/windowsazure/dn569263.aspx)
-   [서버 탐색기에서 Azure 가상 컴퓨터 액세스(영문)](http://msdn.microsoft.com/en-us/library/windowsazure/jj131259.aspx)

다른 Azure 서비스를 서버 탐색기에서 효율적으로 관리할 수 있도록 돕는 몇 가지 고급 기능도 있습니다. 자세한 내용은 다음을 참조하십시오.

-   [Visual Studio 서버 탐색기로 서비스 버스 리소스 찾아보기(영문)](http://msdn.microsoft.com/en-us/library/windowsazure/jj149828.aspx)
-   [서버 탐색기로 저장소 리소스 찾아보기(영문)](http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx)

PowerShell을 사용한 자동화
--------------------------

Azure Powershell을 설치하여 웹 사이트, WebJobs 등에 새로운 cmdlet을 활용합니다. 자세한 내용은 다음을 참조하십시오.

-   [Azure PowerShell을 설치 및 구성하는 방법(영문)](http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell/)
-   [Azure PowerShell 설명서(영문)](http://msdn.microsoft.com/en-us/library/windowsazure/jj156055.aspx)

Visual Studio에서 바로 PowerShell 스크립트를 만들고 이를 사용하여 환경 만들기를 자동화합니다.

-   [Windows PowerShell 배포 스크립트를 사용하여 Azure에 웹 프로젝트 게시(영문)](http://msdn.microsoft.com/en-us/library/windowsazure/dn642480.aspx)

.NET을 사용한 모바일 개발
-------------------------

이제 Azure 모바일 서비스에는 Windows 스토어, Windows Phone, iOS, Android를 포함한 모바일 플랫폼을 대상으로 하는 모바일 앱에 .NET 기반 백 엔드를 사용하기 위한 옵션이 있습니다. 자세한 내용은 [모바일 개발자 센터](/en-us/develop/mobile/)를 참조하십시오.

리소스 관리자
-------------

리소스 관리자는 여러 리소스에 걸쳐 응용 프로그림을 배포하고 관리하는 새로운 프레임워크입니다. 새 JSON 편집자, PowerShell cmdlet 및 CLI 지원을 사용하여 리소스 관리자를 사용해 보십시오. 자세한 내용은 다음을 참조하십시오.

-   [리소스 관리자로 Azure PowerShell 사용(영문)](http://go.microsoft.com/fwlink/?LinkID=394767)
-   [리소스 관리자에서 Azure 플랫폼 간 명령줄 인터페이스 사용](/en-us/documentation/articles/xplat-cli-azure-resource-manager/)

