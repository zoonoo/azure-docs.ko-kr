<properties
	pageTitle="Azure 앱 서비스에 앱 배포"
	description="Azure 앱 서비스에 앱을 배포하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Azure 앱 서비스에 앱 배포

이 문서를 참조하여 웹앱, 모바일 앱 백 엔드 또는 API 앱의 파일을 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 가장 적합한 옵션을 결정한 다음 원하는 옵션에 맞는 사용 방법 지침이 들어 있는 문서 및 블로그로 이동할 수 있습니다.

## <a name="overview"></a>배포 프로세스의 개요

Azure 앱 서비스에서 앱을 만들거나 프로비전하는 즉시, 이 앱에 어떤 코드를 배포하기 전에 Azure 앱 서비스는 응용 프로그램 프레임워크(ASP.NET, PHP, Node.js 등)를 유지 관리합니다. 일부 프레임워크는 기본적으로 사용하도록 설정되어 있는 반면 Java와 Python과 같은 다른 프레임워크는 간단한 확인 표시를 구성해야 사용하도록 설정됩니다. 또한 런타임의 비트 수 또는 PHP 버전과 같이 응용 프로그램 프레임워크를 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure 앱 서비스에서 앱 구성](web-sites-configure.md)을 참조하세요.

웹 서버 또는 응용 프로그램 프레임워크에 대해 염려할 필요가 없기 때문에 앱을 앱 서비스에 배포하는 작업은 코드, 이진 파일, 콘텐츠 파일 및 개별 디렉터리 구조를 [Azure의 **/site/wwwroot** 디렉터리](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(WebJobs의 경우 **/Data/Jobs** 디렉터리)에 배포하기만 하면 됩니다. 앱 서비스는 다음과 같은 세 가지 배포 프로세스를 지원합니다.

- [FTP 또는 FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): FTP 또는 FTPS가 사용하도록 설정된 즐겨쓰는 도구를 사용하여 파일을 Azure로 이동합니다([FileZilla](https://filezilla-project.org)에서 [NetBeans](https://netbeans.org)와 같은 완전한 기능의 IDE로). 이는 엄격한 파일 업로드 프로세스입니다. 버전 제어, 파일 구조 관리 등과 같은 추가 서비스는 앱 서비스에서 제공되지 않습니다. 
- [Kudu(Git/Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment): 앱 서비스의 [배포 엔진](https://github.com/projectkudu/kudu/wiki)입니다. 코드를 리포지토리에서 직접 Kudu로 푸시합니다. 또한 Kudu는 코드가 푸시될 때마다 버전 제어, 패키지 복원, MSBuild 및 [웹 후크](https://github.com/projectkudu/kudu/wiki/Web-hooks)를 비롯한 추가된 서비스를 지속적인 배포 및 다른 자동화 작업에 제공합니다. 이러한 모든 서비스는 사용자 지정 가능하며 다음과 같은 경우에 트리거됩니다. 
    - 구성된 Git 리포지토리에서 **git push**가 실행될 때마다
	- 구성된 Mercurial 리포지토리에서 **hg push**가 실행될 때마다 
    - Dropbox 또는 OneDrive와 같은 연결된 클라우드 저장소가 앱 서비스와 동기화될 때마다 
- [웹 배포](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): IIS 서버에 대한 배포를 자동화하는 동일한 도구입니다. Visuall Studio, WebMatrix 및 Visual Studio Team Services와 같은 즐겨쓰는 Microsoft 도구에서 직접 앱 서비스로 코드를 배포합니다. 이 도구는 차이나는 부분만 배포, 데이터베이스 만들기, 연결 문자열 변환 등을 지원합니다. 웹 배포는 응용 프로그램 이진 파일이 Azure에 배포되기 전에 빌드된다는 점에서 Kudu와 다릅니다. FTP와 마찬가지로 앱 서비스에서 추가 서비스가 제공되지 않습니다.

널리 사용되는 웹 개발 도구는 이러한 배포 프로세스 중 하나 이상을 지원합니다. 선택하는 도구에 따라 활용할 수 있는 배포 프로세스가 결정되지만 실제 사용하는 DevOps 기능은 선택하는 배포 프로세스 및 특정 도구의 조합에 따라 달라집니다. 예를 들어 [Azure SDK가 있는 Visual Studio](#vspros)에서 웹 배포를 수행하는 경우 Kudu에서 자동화 기능을 가져오지 않아도 Visual Studio에서 패키지 복원 및 MSBuild 자동화 기능을 가져옵니다. 또한 Azure SDK는 Visual Studio 인터페이스에서 직접 필요한 Azure 리소스를 만들 수 있도록 사용하기 쉬운 마법사를 제공합니다.

>[AZURE.NOTE]이러한 배포 프로세스는 앱 서비스 계획, 앱 서비스 앱 및 SQL 데이터베이스와 같이 앱에서 필요할 수 있는 [Azure 리소스를 실제로 프로비전](resource-group-portal)하지 않습니다. 그러나 연결된 대부분의 사용 방법 문서는 앱을 프로비전하고 종단 간으로 코드를 배포하는 방법을 보여 줍니다. Azure 리소스를 프로비전하기 위한 추가 옵션은 [명령줄 도구를 사용하여 배포 자동화](#automate) 섹션에도 나와 있습니다.

## <a name="ftp"></a>Azure에 파일을 수동으로 복사하여 배포
웹 콘텐츠를 웹 호스터에 수동으로 복사(PHP 개발자를 위한 일반 워크플로)하는 것이 익숙한 경우 Windows 탐색기 또는 [FileZilla](https://filezilla-project.org/)와 같은 [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 유틸리티를 사용하여 파일을 복사할 수 있습니다.

파일을 수동으로 복사하는 작업에서는 배포에 FTP 프로토콜을 활용합니다([배포 프로세스 개요](#overview) 참조).

파일을 수동으로 복사할 때 장점:

- FTP 도구 사용에 익숙함 
- 파일의 이동 위치를 정확히 인지
- FTPS를 사용한 보안 강화
- 웹 개발에 최소한의 도구를 사용하려는 경우(예: 메모장을 사용하여 웹앱 개발) 좋은 배포 솔루션

파일을 수동으로 복사할 때 단점

- 앱 서비스에서 파일을 올바른 디렉터리에 배포하는 책임이 사용자에게 있음
- 오류가 발생할 때 롤백을 위한 버전 제어가 없음
- 많은 FTP 도구가 차이나는 부분만 복사하는 것이 아니라 단순히 모든 파일을 복사함 대규모 앱의 경우 사소한 업데이트에도 배포 시간이 길어짐

### <a name="howtoftp"></a>수동으로 파일을 Azure에 복사하여 배포하는 방법
Azure에 파일을 복사하는 작업에는 몇 가지 간단한 단계가 포함됩니다.

1. [Azure 포털](https://portal.azure.com)에서 앱에 대한 배포 자격 증명을 만듭니다. 이를 위해서는 앱 블레이드에서 **설정** > **배포 자격 증명**을 클릭합니다.
2. 배포 자격 증명을 구성한 후 **설정** > **속성**으로 이동한 다음 **FTP/개발 사용자**, **FTP 호스트 이름** 및 **FTPS 호스트 이름**에 대한 값을 복사하여 FTP 연결 정보를 가져옵니다.
3. FTP 클라이언트에서 수집한 연결 정보를 사용하여 앱에 연결합니다.
4. 파일 및 해당하는 개별 디렉터리 구조를 [Azure의 **/site/wwwroot** 디렉터리](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(또는 WebJobs의 경우 **/Data/Jobs** 디렉터리)에 복사합니다.
5. 앱의 URL을 찾아 앱이 제대로 실행하는지 확인합니다. 

자세한 내용은 다음 리소스를 참조하세요.

* [PHP-MySQL 웹 앱을 만들고 FTP를 사용하여 배포합니다.](web-sites-php-mysql-deploy-use-ftp.md)
* [FTP 일괄 스크립트 사용](http://support.microsoft.com/kb/96269).

## <a name="dropbox"></a>클라우드 폴더와 동기화하여 배포
[파일을 수동으로 복사](#ftp)하는 작업에 대한 좋은 대안은 OneDrive 및 Dropbox와 같은 클라우드 저장소 서비스에서 앱 서비스로 파일 및 폴더를 동기화하는 것입니다. [Azure 포털](https://portal.azure.com)의 클라우드 저장소에서 특별한 폴더를 구성하고 해당 폴더에서 앱 코드 및 콘텐츠 작업을 수행한 다음 단추를 클릭하여 앱 서비스를 동기화할 수 있습니다.

클라우드 폴더와 동기화하는 작업에서는 배포에 Kudu 프로세스를 활용합니다([배포 프로세스 개요](#overview) 참조).

클라우드 폴더를 동기화할 때의 장점:

- 배포의 간소화. OneDrive 및 Dropbox와 같은 서비스는 데스크톱 동기화 클라이언트를 제공하므로 로컬 작업 디렉터리도 배포 디렉터리입니다.
- 한 번의 클릭으로 배포
- Kudu의 모든 기능을 사용할 수 있음(예: 배포 버전 관리, 롤백, 패키지 복원, 자동화)
- 웹 개발에 최소한의 도구를 사용하려는 경우 좋은 배포 솔루션

클라우드 폴더를 동기화할 때의 단점:

- 팀 프로젝트의 경우 좋은 솔루션이 아님

### <a name="howtodropbox"></a>클라우드 폴더와 동기화하여 배포하는 방법
 
* [드롭 상자에서 웹 앱으로 배포합니다.](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx) [Azure 포털](https://portal.azure.com)을 사용하여 드롭 상자 배포를 설정하는 방법입니다.
* [웹 앱에 드롭 상자를 배포합니다.](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites) 이 비디오는 Dropbox 폴더를 웹 앱에 연결하는 프로세스를 단계별로 안내하고 빠르게 웹 앱을 실행하거나 간단히 끌어서 놓는 방식의 배포를 사용하여 유지 관리할 수 있는 방법을 보여 줍니다.
* [Git, Mercurial 및 Dropbox의 Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)(영문).

## IDE를 사용하여 배포
[Azure SDK](https://azure.microsoft.com/downloads/) 또는 WebMatrix나 [Xcode](https://developer.apple.com/xcode/) 및 [Eclipse](https://www.eclipse.org)와 같은 기타 IDE 제품군이 포함된 [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)를 이미 사용하고 있는 경우 IDE 내에서 직접 Azure에 배포할 수 있습니다. 이 옵션은 개인 개발자에 이상적입니다.

Visual Studio가 기본 설정에 따라 모두 세 가지 배포 프로세스(FTP, Git, 웹 배포)를 지원하는 반면 FTP 또는 Git가 통합된 경우 다른 IDE를 사용하여 앱 서비스에 배포할 수 있습니다([배포 프로세스 개요](#overview) 참조).

IDE를 사용하여 배포할 때의 장점:

- 잠재적으로 종단 간 응용 프로그램 수명 주기에 사용할 도구 최소화 IDE 외부로 이동하지 않고 앱 개발, 디버그, 추적 및 Azure에 배포 

IDE를 사용하여 배포할 때의 단점:

- 도구 사용의 복잡성 증가
- 팀 프로젝트의 경우 여전히 소스 제어 시스템 필요

<a name="vspros"></a> Azure SDK가 있는 Visual Studio를 사용하여 배포할 때의 추가 장점:

- Azure SDK를 통해 Visual Studio에서 Azure 리소스를 최고의 리소스로 만들 수 있음. 앱 만들기, 삭제, 편집, 시작 및 중지, 백 엔드 SQL 데이터베이스 쿼리, Azure 앱 라이브 디버그 등의 수많은 작업이 가능합니다. 
- Azure에서 코드 파일의 라이브 편집
- Azure에서 앱의 라이브 디버깅
- 통합된 Azure 탐색기
- 차이나는 부분만 배포 

###<a name="vs"></a>Visual Studio에서 직접 배포하는 방법

* [Azure 및 ASP.NET 시작](web-sites-dotnet-get-started.md). Visual Studio 및 웹 배포를 사용하여 간단한 ASP.NET MVC 웹 프로젝트를 만들고 배포하는 방법을 설명합니다.
* [Visual Studio를 사용하여 Azure 웹 작업을 배포하는 방법.](websites-dotnet-deploy-webjobs.md) WebJob으로 배포되도록 콘솔 응용 프로그램 프로젝트를 구성하는 방법입니다.  
* [멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 Asp.Net.MVC 5 앱을 웹 앱에 배포합니다.](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) Visual Studio, 웹 배포 및 Entity Framework Code First 마이그레이션을 사용하여 SQL 데이터베이스가 포함된 ASP.NET MVC 웹 프로젝트를 만들고 배포하는 방법을 설명합니다.
* [Visual Studio 및 ASP.NET의 웹 배포 개요](http://msdn.microsoft.com/library/dd394698.aspx)(영문). Visual Studio를 사용한 웹 배포에 대한 기본적인 소개입니다. 이전에 작성되기는 했지만 웹 응용 프로그램과 함께 데이터베이스를 배포하는 옵션의 개요, 직접 수행해야 하거나 Visual Studio에서 수행하도록 수동 구성해야 하는 추가 배포 작업 목록 등 여전히 관련 있는 정보를 포함하고 있습니다. 이 주제는 웹 앱으로의 배포에 국한된 내용이 아니라 일반적인 배포에 관한 내용입니다.
* [Visual Studio를 사용한 ASP.NET 웹 배포](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)(영문). 12개 부분으로 구성된 자습서 시리즈로, 이 목록의 다른 자료보다 더 폭넓은 배포 작업 범위를 다룹니다. 일부 Azure 배포 기능은 이 자습서가 작성되기 전에 추가되었지만 이후에 추가된 참고 사항에 누락된 내용이 설명되어 있습니다.
* [Visual Studio 2012를 사용하여 Git 리포지토리에서 바로 Azure에 ASP.NET 웹 사이트 배포](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)(영문). Git 플러그 인을 사용하여 코드를 Git에 커밋하고 Azure를 Git 리포지토리에 연결하여 Visual Studio에서 ASP.NET 웹 프로젝트를 배포하는 방법을 설명합니다. Visual Studio 2013부터 Git 지원이 기본적으로 제공되므로 플러그 인을 설치할 필요가 없습니다.

###<a name="webmatrix"></a>WebMatrix에서 직접 배포하는 방법

* [WebMatrix를 사용하여 Node.js 웹 사이트 빌드 및 Azure에 배포](web-sites-nodejs-use-webmatrix.md).
* [WebMatrix를 사용하여 PHP-MySQL 웹 앱을 만들고 배포합니다.](web-sites-php-mysql-use-webmatrix.md)
* [WebMatrix 3:통합된 Git 및 Azure에 배포합니다](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD)(영문). WebMatrix를 사용하여 Git 소스 제어 리포지토리에서 배포하는 방법을 설명합니다.

## <a name="onprem"></a>온-프레미스 소스 제어 시스템에서 배포

규모와 상관없이 개발 팀에서 작업하고 TFS([Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx)), [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) 또는 [Mercurial](http://mercurial.selenic.com/)과 같은 온-프레미스 SCM(소스 코드 관리)을 사용하는 경우 소스 제어 워크플로에서 리포지토리와 통합하고 직접 앱 서비스에 배포하도록 앱 서비스를 구성할 수 있습니다. TFS를 사용하는 경우 앱 서비스에 지속적으로 배포하도록 구성할 수도 있습니다.

TFS는 웹 배포를 사용하여 앱 서비스에 배포하는 반면 Git/Mercurial 리포지토리에서 배포하는 것은 Kudu를 사용합니다([배포 프로세스 개요](#overview) 참조).

온-프레미스 소스 제어 시스템에서 배포의 장점:

- [Xcode](https://developer.apple.com/xcode/) 및 [Eclipse](https://www.eclipse.org)를 포함하여 프레임워크(모든 언어), Git 또는 Mercurial 클라이언트에서의 배포 지원
- 분기별 배포를 통해 다른 버전을 별도의 [슬롯](web-sites-staged-publishing)에 배포할 수 있음
- 모든 규모의 개발 팀에 적합

온-프레미스 소스 제어 시스템에서 배포의 단점:

- 선택한 SCM 시스템에 대한 지식이 필요
- 필요 이상의 기능을 제공할 수 있음
- Git 및 Mercurial 클라이언트 도구에서 지속적인 배포 및 분기별 배포를 위한 턴키 솔루션 부족 

TFS를 사용하여 배포할 때의 추가 장점:

- 빌드, 테스트 및 배포를 위한 CI(지속적인 통합)
- 기존 IDE 또는 편집기와 작동하는 기본 제공 공동 작업 도구
- 분산 버전 제어의 경우 Git를 지원하거나 중앙화된 버전 제어의 경우 TFVC(Team Foundation 버전 제어)를 지원 
- Agile 배포를 위한 풍부한 도구
- [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Azure 서비스 버스](/services/service-bus/) 등에 대해 미리 만들어진 통합 
- [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259)가 최대 5명의 개발자로 이루어진 팀에게 무료로 제공됨

###<a name="tfs"></a>TFS를 사용하여 지속적으로 배포하는 방법

* [Azure 클라우드 서비스의 지속적인 전송](../cloud-services-dotnet-continuous-delivery.md). 이 문서는 Azure 클라우드 서비스에 대한 것이지만 일부 내용은 웹 앱과도 관련이 있습니다.

###<a name="gitmercurial"></a>온-프레미스 Git 또는 Mercurial 리포지토리에서 배포하는 방법

* [Git를 사용하여 소스 제어에서 웹 앱으로 게시합니다.](web-sites-publish-source-control.md) Git를 사용하여 로컬 컴퓨터에서 웹 앱으로 바로 게시하는 방법(Azure에서는 이 게시 방법을 Local Git라고 함)을 설명합니다. GitHub, CodePlex 또는 BitBucket에서 Git 리포지토리의 지속적인 배포를 가능하게 하는 방법도 보여 줍니다.
* [git/hg 리포지토리에서 웹 앱에 게시합니다.](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html) 웹 앱의 "외부 리포지토리" 기능에 대해 설명하는 블로그입니다.
* [Git, Mercurial 및 Dropbox의 Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)(영문).
* [하나의 Git 리포지토리에서 Azure에 두 웹 사이트 배포](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx)(영문). Scott Hanselman의 블로그 게시물입니다.

## 클라우드 기반 소스 제어 서비스에서 배포
규모와 상관없이 개발 팀에서 작업하고 [Visual Studio Team Services](http://www.visualstudio.com/)(이전의 Visual Studio Online), [GitHub](https://www.github.com), [GitLab](https://gitlab.com), [BitBucket](https://bitbucket.org/), [CodePlex](https://www.codeplex.com/), [Codebase](https://www.codebasehq.com) 및 [Kiln](https://www.fogcreek.com/kiln/)과 같은 클라우드 기반 SCM(소스 코드 관리)을 사용하는 경우 리포지토리와 통합하고 지속적으로 배포하도록 앱 서비스를 구성할 수 있습니다.

Visual Studio Team Services는 웹 배포를 사용하여 앱 서비스에 배포하는 반면 온라인 리포지토리에서 배포는 Kudu를 사용합니다([배포 프로세스 개요](#overview) 참조).

클라우드 기반 소스 제어 서비스에서 배포할 때의 장점:

- 모든 언어 프레임워크 지원
- Git 및 Mercurial 리포지토리에 대해 구성이 필요 없는 지속적인 배포 
- 분기별 배포를 통해 다른 분기를 다른 [슬롯](web-sites-staged-publishing)에 배포할 수 있습니다.
- 모든 규모의 개발 팀에 적합

클라우드 기반 소스 제어 서비스에서 배포할 때의 단점:

- 선택한 SCM 서비스에 대한 지식 필요
- 필요 이상의 기능을 제공할 수 있음

Visual Studio Team Services를 사용하여 배포할 때의 추가 장점:

- 최대 5명의 개발자로 이루어진 팀의 경우 무료로 제공
- 빌드, 테스트 및 배포를 위한 CI(지속적인 통합)
- 기존 IDE 또는 편집기와 작동하는 기본 제공 공동 작업 도구
- 분산 버전 제어의 경우 Git를 지원하거나 중앙화된 버전 제어의 경우 TFVC(Team Foundation 버전 제어)를 지원 
- Agile 배포를 위한 풍부한 도구
- [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Azure 서비스 버스](/services/service-bus/) 등에 대해 미리 만들어진 통합 
- [Power BI에 연결](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs)하여 쉬운 보고를 위한 다양한 대시보드

###<a name="vsts"></a>Visual Studio Team Services를 사용하여 지속적으로 배포하는 방법

- [Visual Studio Team Services 및 TFVC를 사용하여 Azure에 대한 지속적인 업데이트](../cloud-services-continuous-delivery-use-vso.md) TFVC를 사용하여 Visual Studio Team Services에서 웹앱으로의 지속적인 업데이트를 설정하는 방법을 보여 주는 단계별 자습서입니다. 
- [Visual Studio Team Services 및 Git을 사용하여 Azure에 대한 지속적인 업데이트](../cloud-services-continuous-delivery-use-vso-git.md) 이 자습서는 이전 자습서와 비슷하지만 TFVC 대신 Git를 사용합니다.

###<a name="cloudgitmercurial"></a>클라우드 기반 Git 또는 Mercurial 리포지토리에서 배포하는 방법

- [Git를 사용하여 소스 제어에서 웹 앱으로 게시합니다.](web-sites-publish-source-control.md) GitHub, CodePlex 또는 BitBucket에서 리포지토리의 지속적인 배포를 가능하게 하는 방법도 보여 줍니다. 이 자습서에서는 Git 리포지토리를 게시하는 방법을 설명하지만, CodePlex 또는 BitBucket에서 호스트되는 Mercurial 리포지토리에 대한 프로세스도 비슷합니다.
- [Kudu를 사용하는 GitHub를 통해 웹 앱에 배포합니다.](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/) GitHub에서 앱 서비스에 직접 웹앱을 배포하는 방법을 보여 주는 Scott Hanselman과 David Ebbo의 동영상.
- [웹 앱에 대한 Azure 단추에 배포합니다.](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) Git 리포지토리에서 배포를 트리거하는 방법에 대한 블로그입니다.
- [Git, Mercurial 및 Dropbox의 Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)(영문).

자세한 내용은 다음 리소스를 참조하십시오.

- [Git를 사용하여 소스 제어에서 웹 앱으로 게시합니다.](web-sites-publish-source-control.md) Git를 사용하여 로컬 컴퓨터에서 웹 앱으로 바로 게시하는 방법(Azure에서는 이 게시 방법을 Local Git라고 함)을 설명합니다. 

## <a name="automate"></a>명령줄 도구를 사용하여 배포 자동화

* [MSBuild를 사용하여 배포 자동화](#msbuild)
* [FTP 도구 및 스크립트를 사용하여 파일 복사](#ftp)
* [Windows PowerShell을 사용하여 배포 자동화](#powershell)
* [.NET 관리 API를 사용하여 배포 자동화](#api)
* [Azure CLI(Azure 명령줄 인터페이스)에서 배포](#cli)
* [웹 배포 명령줄에서 배포](#webdeploy)
* [FTP 일괄 스크립트 사용](http://support.microsoft.com/kb/96269).
 
다른 배포 옵션은 [Octopus 배포](http://en.wikipedia.org/wiki/Octopus_Deploy) 같은 클라우드 기반 서비스를 사용하는 것입니다. 자세한 내용은 참조 [Azure 웹사이트에 ASP.NET 응용 프로그램 배포](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)를 참조하세요.

###<a name="msbuild"></a>MSBuild를 사용하여 배포 자동화

개발하는 데 [Visual Studio IDE](#vs)를 사용하는 경우 [MSBuild](http://msbuildbook.com/)를 사용하면 IDE에서 할 수 있는 모든 작업을 자동화할 수 있습니다. 파일을 복사하는 데 [웹 배포](#webdeploy) 또는 [FTP/FTPS](#ftp)를 사용하도록 MSBuild를 구성할 수 있습니다. 웹 배포는 데이터베이스 배포와 같은 다른 많은 배포 관련 작업을 자동화할 수도 있습니다.

MSBuild를 사용한 명령줄 배포에 대한 자세한 내용은 다음 리소스를 참조하십시오.

* [Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment) Visual Studio를 사용하여 Azure에 배포하는 방법에 대한 자습서 시리즈의 열 번째 문서입니다. Visual Studio에서 게시 프로필을 설정한 후 명령줄을 사용하여 배포하는 방법을 보여 줍니다.
* [Microsoft 빌드 엔진 내: MSBuild 및 Team Foundation Build 사용](http://msbuildbook.com/). 배포에 MSBuild를 사용하는 방법에 대한 장이 포함된 하드 카피 책자입니다.

###<a name="powershell"></a>Windows PowerShell을 사용하여 배포 자동화

[Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)(영문)에서 MSBuild 또는 FTP 배포 기능을 수행할 수 있습니다. 이 기능을 수행하는 경우 Azure REST 관리 API를 쉽게 호출할 수 있는 Windows PowerShell cmdlet 컬렉션을 사용할 수도 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

* [GitHub 리포지토리에 연결된 웹앱 배포](app-service-web-arm-from-github-provision.md)
* [SQL 데이터베이스를 사용하는 웹앱을 프로비전](app-service-web-arm-with-sql-database-provision.md)
* [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](app-service-deploy-complex-application-predictably.md)
* [Azure에서 실제 클라우드 앱 빌드 - 자동화](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)(영문). 전자책에 나온 샘플 응용 프로그램이 Windows PowerShell 스크립트를 사용하여 Azure 테스트 환경을 만들고 이 환경에 배포하는 방법을 설명하는 전자책 장입니다. 추가 Azure PowerShell 설명서 링크를 보려면 [리소스](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources)(영문) 섹션을 참조하십시오.
* [Windows PowerShell 스크립트를 사용하여 개발 및 테스트 환경에 게시합니다.](http://msdn.microsoft.com/library/dn642480.aspx) Visual Studio에서 생성된 Windows PowerShell 배포 스크립트를 사용하는 방법.

###<a name="api"></a>.NET 관리 API를 사용하여 배포 자동화

배포를 위해 MSBuild 또는 FTP 기능을 수행하도록 C# 코드를 작성할 수 있습니다. 이렇게 하면 Azure 관리 REST API에 액세스하여 사이트 관리 작업을 수행할 수 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

* [Azure 관리 라이브러리 및 .NET을 사용한 자동화](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)(영문). .NET 관리 API를 소개하고 추가 설명서 링크를 제공합니다.

###<a name="cli"></a>Azure CLI(Azure 명령줄 인터페이스)에서 배포

Windows, Mac 또는 Linux 컴퓨터에서 명령줄을 사용하여 FTP를 사용한 배포를 수행할 수 있습니다. 이 경우 Azure CLI를 사용하여 Azure REST 관리 API에 액세스할 수도 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

* [Azure 명령줄 도구](/downloads/#cmd-line-tools). Azure.com에서 명령줄 도구 정보를 제공하는 포털 페이지입니다.

###<a name="webdeploy"></a>웹 배포 명령줄에서 배포

[웹 배포](http://www.iis.net/downloads/microsoft/web-deploy)(영문)는 IIS에 배포하는 데 사용되는 Microsoft 소프트웨어로, 지능형 파일 동기화 기능을 제공할 뿐만 아니라 FTP 사용 시 자동화할 수 없는 다른 많은 배포 관련 작업을 수행하거나 조정할 수도 있습니다. 예를 들어 웹 배포는 웹 앱과 함께 새 데이터베이스나 데이터베이스 업데이트를 배포할 수 있습니다. 웹 배포는 변경된 파일만 지능적으로 복사할 수 있으므로 기존 사이트를 업데이트하는 데 필요한 시간을 최소화할 수 있습니다. Microsoft WebMatrix, Visual Studio, Visual Studio Team Services 및 Team Foundation Server는 웹 배포를 기본적으로 지원합니다. 하지만 명령줄에서 바로 웹 배포를 사용하여 배포를 자동화할 수도 있습니다. 웹 배포 명령은 매우 강력하지만, 학습하는 데 오래 걸릴 수 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

* [간단한 웹 앱: 배포](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). 웹 배포를 보다 쉽게 사용할 수 있도록 하기 위해 자신이 작성한 도구에 대해 소개하는 David Ebbo의 블로그입니다.
* [웹 배포 도구](http://technet.microsoft.com/library/dd568996)(영문). Microsoft TechNet 사이트의 공식적인 설명서입니다. 이전에 작성되었지만 시작할 때 참조하기 좋습니다.
* [웹 배포 사용](http://www.iis.net/learn/publish/using-web-deploy)(영문). Microsoft IIS.NET 사이트의 공식적인 설명서입니다. 마찬가지로, 이전에 작성되었지만 시작할 때 참조하기 좋습니다.
* [StackOverflow](http://www.stackoverflow.com)(영문). 명령줄에서 웹 배포를 사용하는 방법에 대한 최신 정보를 원하는 경우 가장 적합한 자료입니다.
* [Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment) MSBuild는 Visual Studio에서 사용되는 빌드 엔진입니다. 명령줄에서 이 엔진을 사용하여 웹 앱에 웹 응용 프로그램을 배포할 수도 있습니다. 이 자습서는 주로 Visual Studio 배포에 대해 다루는 시리즈의 일부입니다.

##<a name="nextsteps"></a>다음 단계

일부 상황에서는 웹 앱의 스테이징 버전과 프로덕션 버전 간을 간단한 방법으로 자유롭게 전환할 수 있습니다. 자세한 내용은 [웹 앱에서 준비된 배포](web-sites-staged-publishing.md)를 참조하세요.

백업 및 복원 계획을 마련하는 일은 배포 워크플로의 중요한 부분입니다. 웹 앱 백업 및 복원 기능에 대한 자세한 내용은 [웹 앱 백업](web-sites-backup.md)을 참조하세요.

Azure의 역할 기반 액세스 제어를 사용하여 웹 앱 배포에 대한 액세스를 관리하는 방법은 [RBAC 및 웹 앱 게시](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing)를 참조하세요.

다른 배포 항목에 대한 자세한 내용은 [웹 앱 설명서](/documentation/services/web-sites/)의 배포 섹션을 참조하세요.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
 

<!---HONumber=AcomDC_0107_2016-->