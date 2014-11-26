<properties linkid="manage-services-how-to-deploy-websites" pageTitle="How to deploy an Azure Website" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

# Azure 웹 사이트를 배포하는 방법

Azure 웹 사이트에 고유 콘텐츠를 배포하는 많은 옵션이 있습니다. 이 항목에서는 각 옵션에 대해 간략하게 설명하고 자세한 정보 링크를 제공합니다.

-   [클라우드에 호스트된 소스 제어 시스템에서 배포][클라우드에 호스트된 소스 제어 시스템에서 배포]

    -   VSO(Visual Studio Online)
    -   Git를 사용하는 리포지토리 웹 사이트
    -   Mercurial을 사용하는 리포지토리 웹 사이트
    -   Dropbox
-   [IDE에서 배포][IDE에서 배포]

    -   Visual Studio
    -   WebMatrix
-   [FTP 유틸리티를 사용하여 배포][FTP 유틸리티를 사용하여 배포]
-   [온-프레미스 소스 제어 시스템에서 배포][온-프레미스 소스 제어 시스템에서 배포]

    -   TFS(Team Foundation Server)
    -   온-프레미스 Git 또는 Mercurial 리포지토리
-   [명령줄 도구 및 Azure REST 관리 API 사용][명령줄 도구 및 Azure REST 관리 API 사용]

    -   MSBuild
    -   FTP 스크립트
    -   Windows PowerShell
    -   .NET 관리 API
    -   플랫폼 간 명령줄(xpat-cli)
    -   웹 배포 명령줄

## <a name="cloud"></a>클라우드에 호스트된 소스 제어 시스템에서 배포

웹 사이트를 배포하는 가장 좋은 방법은 [지속적인 전송 워크플로][지속적인 전송 워크플로](영문)를 [소스 제어 시스템][소스 제어 시스템](영문)에 통합하여 설정하는 것입니다. 자동화는 개발 프로세스를 더 효율적으로 만들 뿐만 아니라 백업 및 복원 프로세스의 관리 가능성과 안정성도 높일 수 있습니다.

아직 소스 제어를 설정하지 않은 경우 가장 쉽게 시작하는 방법은 클라우드에 호스트된 소스 제어 시스템을 사용하는 것입니다.

### <a name="vso"></a>VSO(Visual Studio Online)

[Visual Studio Online][Visual Studio Online](이전의 Team Foundation Service)은 소스 제어와 팀 공동 작업을 위한 Microsoft 클라우드 기반 솔루션입니다. 서비스는 최대 5명의 개발자로 이루어진 팀에게 무료입니다. Azure 웹 사이트에 지속적인 전송을 수행하도록 VSO를 설정할 수 있으며, 리포지토리는 [Git 또는 TFVC][Visual Studio Online](영문)를 사용할 수 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

-   [VSO 및 TFVC를 사용하여 지속적으로 Azure에 전송][VSO 및 TFVC를 사용하여 지속적으로 Azure에 전송](영문). TFVC를 사용하여 VSO에서 Azure 웹 사이트로의 지속적인 전송을 설정하는 방법을 보여 주는 간략한 단계별 자습서입니다. TFVC는 분산된 소스 제어 옵션인 Git과 대조되는 VSO의 중앙 집중식 소스 제어 옵션입니다.
-   [VSO 및 TFVC를 사용한 Azure로의 지속적인 전송][VSO 및 TFVC를 사용한 Azure로의 지속적인 전송](영문). 이전 자습서와 비슷하지만 이 자습서에는 VSO 계정을 등록하고 소스 제어에 프로젝트를 체크 인하는 단계도 수록되어 있습니다.
-   [Visual Studio Online 및 Git를 사용하여 Azure에 지속적으로 전송][Visual Studio Online 및 Git를 사용하여 Azure에 지속적으로 전송](영문). 이 자습서는 이전 자습서와 비슷하지만 TFVC 대신 Git를 사용합니다.

### <a name="git"></a>Git를 사용하는 리포지토리 웹 사이트

[Git][Visual Studio Online](영문)은 많이 사용되는 분산된 소스 제어 시스템입니다. Azure에는 [GitHub][GitHub], [CodePlex][CodePlex], [BitBucket][BitBucket]을 비롯하여 Git 리포지토리를 저장하는 애용되는 웹 기반 리포지토리 사이트에서 Azure 웹 사이트로 배포하는 작업을 쉽게 자동화할 수 있게 하는 기본 제공 기능이 있습니다. 배포에 Git을 사용하는 이점은 필요한 경우 이전 배포로 롤백하기가 비교적 쉽다는 점입니다.

자세한 내용은 다음 리소스를 참조하세요.

-   [소스 제어에서 Azure 웹 사이트로 게시][소스 제어에서 Azure 웹 사이트로 게시]. Git를 사용하여 로컬 컴퓨터에서 Azure 웹 사이트로 바로 게시하는 방법(Azure에서는 이 게시 방법을 Local Git라고 함)을 설명합니다. GitHub, CodePlex 또는 BitBucket에서 Git 리포지토리의 지속적인 배포를 가능하게 하는 방법도 보여 줍니다.
-   [Kudu를 사용하여 GitHub를 통해 웹 사이트에 배포][Kudu를 사용하여 GitHub를 통해 웹 사이트에 배포](영문). Scott Hanselman과 David Ebbo가 GitHub에서 바로 Azure 웹 사이트로 웹 사이트를 배포하는 방법을 소개하는 비디오입니다.
-   [Git, Mercurial 및 DropBox의 Azure 포럼][Git, Mercurial 및 DropBox의 Azure 포럼](영문).

### <a name="mercurial"></a>Mercurial을 사용하는 리포지토리 웹 사이트

소스 제어 시스템으로 [Mercurial][Mercurial]을 사용하며 [CodePlex][CodePlex] 또는 [BitBucket][BitBucket]에 리포지토리를 저장하는 경우 Azure 웹 사이트의 기본 제공 기능을 사용하여 콘텐츠를 자동 배포할 수 있습니다.

Mercurial을 사용하여 배포하는 방법에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [소스 제어에서 Azure 웹 사이트로 게시][소스 제어에서 Azure 웹 사이트로 게시]. 이 자습서에서는 Git 리포지토리를 게시하는 방법을 설명하지만, CodePlex 또는 BitBucket에서 호스트되는 Mercurial 리포지토리에 대한 프로세스도 비슷합니다.
-   [Git, Mercurial 및 Dropbox의 Azure 포럼][Git, Mercurial 및 DropBox의 Azure 포럼](영문).

### <a name="dropbox"></a>Dropbox

[Dropbox][Dropbox](영문)는 소스 제어 시스템이 아닙니다. 하지만 Dropbox에 소스 코드를 저장하는 경우 Dropbox 계정에서 배포를 자동화할 수 있습니다.

-   [Dropbox를 Windows Azure에 배포][Dropbox를 Windows Azure에 배포](영문). Azure 관리 포털을 사용하여 Dropbox 배포를 설정하는 방법을 설명입니다.
-   [Dropbox 및 Azure 웹 사이트][Dropbox 및 Azure 웹 사이트](영문). 이 비디오는 Dropbox 폴더를 Azure 웹 사이트에 연결하는 프로세스를 단계별로 안내하고 빠르게 웹 사이트를 실행하거나 간단히 끌어서 놓는 방식의 배포를 사용하여 유지 관리할 수 있는 방법을 보여 줍니다.
-   [Git, Mercurial 및 Dropbox의 Azure 포럼][Git, Mercurial 및 DropBox의 Azure 포럼](영문).

## <a name="ide"></a>IDE에서 배포

[Visual Studio][Visual Studio] 및 [WebMatrix][WebMatrix]는 웹 개발에 사용할 수 있는 Microsoft IDE(통합 개발 환경)입니다. 두 가지 모두 쉽게 Azure 웹 사이트에 배포할 수 있게 하는 기능을 기본으로 제공합니다. 두 가지 모두 [웹 배포][웹 배포](영문)를 사용하여 데이터베이스 배포 및 연결 문자열 변경과 같은 더 많은 배포 관련 작업을 자동화할 수 있습니다. 또한 [FTP 또는 FTPS][FTP 또는 FTPS](영문)를 사용하여 배포할 수도 있습니다.

WebMatrix는 신속하게 설치하고 쉽게 배울 수 있지만, Visual Studio는 Azure 웹 사이트 작업을 위한 더 많은 기능을 제공합니다. Visual Studio IDE 내에서 Azure 웹 사이트를 만들고, 중지하고, 시작하고 삭제할 수 있으며 실시간으로 생성되는 로그 보기, 원격 디버그 등을 수행할 수도 있습니다. Visual Studio는 또한 [Visual Studio Online][1], [Team Foundation Server][Team Foundation Server], [Git 리포지토리][Git 리포지토리]와 같은 소스 제어 시스템과 통합됩니다.

### <a name="vs"></a>Visual Studio

Visual Studio에서 Azure 웹 사이트에 배포하는 방법에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 및 ASP.NET 시작][Azure 및 ASP.NET 시작]. Visual Studio 및 웹 배포를 사용하여 간단한 ASP.NET MVC 웹 프로젝트를 만들고 배포하는 방법을 설명합니다.
-   [Azure 웹 사이트에 Azure WebJobs를 배포하는 방법][Azure 웹 사이트에 Azure WebJobs를 배포하는 방법]. WebJobs로 배포되도록 콘솔 응용 프로그램 프로젝트를 구성하는 방법입니다.
-   [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 ASP.NET MVC 5 앱 배포][Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 ASP.NET MVC 5 앱 배포]. Visual Studio, 웹 배포 및 Entity Framework Code First 마이그레이션을 사용하여 SQL 데이터베이스가 포함된 ASP.NET MVC 웹 프로젝트를 만들고 배포하는 방법을 설명합니다.
-   [Visual Studio 및 ASP.NET의 웹 배포 개요][Visual Studio 및 ASP.NET의 웹 배포 개요](영문). Visual Studio를 사용한 웹 배포에 대한 기본적인 소개입니다. 이전에 작성되기는 했지만 웹 응용 프로그램과 함께 데이터베이스를 배포하는 옵션의 개요, 직접 수행해야 하거나 Visual Studio에서 수행하도록 수동 구성해야 하는 추가 배포 작업 목록 등 여전히 관련 있는 정보를 포함하고 있습니다. 이 항목은 Azure 웹 사이트로의 배포에 국한된 내용이 아니라 일반적인 배포에 관한 내용입니다.
-   [Visual Studio를 사용한 ASP.NET 웹 배포][Visual Studio를 사용한 ASP.NET 웹 배포](영문). 12개 부분으로 구성된 자습서 시리즈로, 이 목록의 다른 자료보다 더 폭넓은 배포 작업 범위를 다룹니다.
-   [Visual Studio 2012를 사용하여 Git 리포지토리에서 바로 Azure에 ASP.NET 웹 사이트 배포][Visual Studio 2012를 사용하여 Git 리포지토리에서 바로 Azure에 ASP.NET 웹 사이트 배포](영문). Git 플러그 인을 사용하여 코드를 Git에 커밋하고 Azure를 Git 리포지토리에 연결하여 Visual Studio에서 ASP.NET 웹 프로젝트를 배포하는 방법을 설명합니다.

### <a name="webmatrix"></a>WebMatrix

WebMatrix에서 Azure 웹 사이트에 배포하는 방법에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Microsoft WebMatrix로 웹 사이트 개발 및 배포][Microsoft WebMatrix로 웹 사이트 개발 및 배포]. WebMatrix 템플릿을 사용하여 간단한 ASP.NET 웹 사이트를 만들고 WebMatrix 및 웹 배포를 사용하여 이 웹 사이트를 Azure 웹 사이트에 배포하는 방법을 설명합니다.
-   [WebMatrix를 사용하여 Node.js 웹 사이트 빌드 및 Azure에 배포][WebMatrix를 사용하여 Node.js 웹 사이트 빌드 및 Azure에 배포].
-   [WebMatrix를 사용하여 PHP-MySQL Azure 웹 사이트 만들기 및 배포][WebMatrix를 사용하여 PHP-MySQL Azure 웹 사이트 만들기 및 배포](영문).
-   [WebMatrix 3: 통합된 Git 및 Azure에 배포][WebMatrix 3: 통합된 Git 및 Azure에 배포](영문). WebMatrix를 사용하여 Git 소스 제어 리포지토리에서 배포하는 방법을 설명합니다.

## <a name="ftp"></a>FTP 유틸리티를 사용하여 배포

사용하는 IDE에 관계없이, [FTP][FTP 또는 FTPS](영문)를 사용해 파일을 복사하여 콘텐츠를 사이트에 배포할 수도 있습니다. Azure 웹 사이트용 FTP 자격 증명을 만들기가 쉬우며 Internet Explorer와 같은 브라우저 및 [FileZilla][FileZilla](영문)와 같은 모든 기능을 갖춘 무료 유틸리티를 포함하여 FTP와 작동하는 모든 응용 프로그램에서 이 자격 증명을 사용할 수 있습니다. Azure 웹 사이트는 또한 강화된 보안 FTPS 프로토콜을 지원합니다.

FTP 유틸리티를 사용하여 웹 사이트 파일을 Azure에 쉽게 복사할 수 있지만, 이 유틸리티는 데이터베이스 배포 또는 연결 문자열 변경과 같은 관련 배포 작업을 자동으로 처리하거나 조정하지 않습니다. 또한 많은 FTP 도구는 변경되지 않은 파일의 복사를 건너뛰기 위해 원본 파일과 대상 파일을 비교하는 과정을 거치지 않습니다. 대규모 사이트의 경우 항상 모든 파일을 복사한다면 사소한 업데이트 시에도 항상 모든 파일이 복사되기 때문에 배포 시간이 길어질 수 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

-   [PHP-MySQL Azure 웹 사이트 만들기 및 FTP를 사용하여 배포][PHP-MySQL Azure 웹 사이트 만들기 및 FTP를 사용하여 배포].
-   [웹 사이트를 관리하는 방법][웹 사이트를 관리하는 방법]. FTP 자격 증명 설정 방법을 설명하는 PHP 자습서에 포함되지 않은 추가 정보가 있습니다.

## <a name="onpremises"></a>온-프레미스 소스 제어 시스템에서 배포

온-프레미스(클라우드에 호스트되지 않음) 리포지토리에서 TFS, Git 또는 Mercurial을 사용 중인 경우 리포지토리에서 바로 Azure 웹 사이트에 배포할 수 있습니다.

### <a name="tfs"></a>TFS(Team Foundation Server)

Team Foundation Server는 소스 제어 및 팀 공동 작업용 Microsoft 온-프레미스 솔루션입니다. Azure 웹 사이트에 대한 지속적인 전송을 수행하도록 TFS를 설정할 수 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 클라우드 서비스의 지속적인 전송][Azure 클라우드 서비스의 지속적인 전송]. 이 문서는 Azure 클라우드 서비스에 대한 것이지만 일부 내용은 웹 사이트와도 관련이 있습니다.

### <a name="gitmercurial"></a>온-프레미스 Git 또는 Mercurial 리포지토리

Azure에서는 해당 위치에서 배포하기 위해 Git 또는 Mercurial을 사용하는 리포지토리의 URL을 입력할 수 있습니다. 로컬 Git 리포지토리에서 바로 Azure 웹 사이트로 푸시할 수도 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

-   [소스 제어에서 Azure 웹 사이트로 게시][소스 제어에서 Azure 웹 사이트로 게시]. Git를 사용하여 로컬 컴퓨터에서 Azure 웹 사이트로 바로 게시하는 방법(Azure에서는 이 게시 방법을 Local Git라고 함)을 설명합니다. GitHub, CodePlex 또는 BitBucket에서 Git 리포지토리의 지속적인 배포를 가능하게 하는 방법도 보여 줍니다.
-   [git/hg 리포지토리에서 Azure 웹 사이트에 게시][git/hg 리포지토리에서 Azure 웹 사이트에 게시](영문). Azure 웹 사이트의 "외부 리포지토리" 기능에 대해 설명하는 David Ebbo의 블로그입니다.
-   [Git, Mercurial 및 Dropbox의 Azure 포럼][Git, Mercurial 및 DropBox의 Azure 포럼](영문).
-   [하나의 Git 리포지토리에서 Azure에 두 웹 사이트 배포][하나의 Git 리포지토리에서 Azure에 두 웹 사이트 배포](영문). Scott Hanselman의 블로그 게시물입니다.

## <a name="commandline"></a> 명령줄 도구 및 Azure REST 관리 API 사용

개발 워크플로를 자동화하는 것이 항상 가장 좋습니다. 하지만 소스 제어 시스템에서 바로 자동화할 수 없는 경우에는 명령줄 도구를 사용하여 수동으로 설정할 수 있습니다. 이렇게 하려면 일반적으로 두 개 이상의 도구나 프레임워크를 사용해야 합니다. 배포에 콘텐츠 복사뿐만 아니라 사이트 관리 기능 수행도 필요한 경우가 많기 때문입니다.

REST 관리 API 및 API 작업을 더 수월하게 만드는 몇 가지 프레임워크가 제공되는 Azure는 배포를 위해 수행해야 할 사이트 관리 작업을 간소화합니다.

### <a name="msbuild"></a>MSBuild

개발하는 데 [Visual Studio IDE][Visual Studio IDE]를 사용하는 경우 [MSBuild][MSBuild]를 사용하면 IDE에서 할 수 있는 모든 작업을 자동화할 수 있습니다. 파일을 복사하는 데 [웹 배포][2] 또는 [FTP/FTPS][FTP 유틸리티를 사용하여 배포]를 사용하도록 MSBuild를 구성할 수 있습니다. 웹 배포는 데이터베이스 배포와 같은 다른 많은 배포 관련 작업을 자동화할 수도 있습니다.

MSBuild를 사용한 명령줄 배포에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포][Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포](영문). Visual Studio를 사용하여 Azure에 배포하는 방법에 대한 자습서 시리즈의 열 번째 문서입니다. Visual Studio에서 게시 프로필을 설정한 후 명령줄을 사용하여 배포하는 방법을 보여 줍니다.
-   [Microsoft Build Engine: Using MSBuild 및 Team Foundation Build 사용][MSBuild](영문). 배포에 MSBuild를 사용하는 방법에 대한 장이 포함된 하드 카피 책자입니다.

### <a name="ftp2"></a>FTP 스크립트

Azure 웹 사이트의 [FTP/FTPS][FTP 또는 FTPS] 자격 증명을 쉽게 만들 수 있으며 그런 다음, FTP 일괄 스크립트에 이 자격 증명을 사용할 수 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

-   [FTP 일괄 스크립트 사용][FTP 일괄 스크립트 사용].

### <a name="powershell"></a>Windows PowerShell

[Windows PowerShell][Windows PowerShell](영문)에서 MSBuild 또는 FTP 배포 기능을 수행할 수 있습니다. 이 기능을 수행하는 경우 Azure REST 관리 API를 쉽게 호출할 수 있는 Windows PowerShell cmdlet 컬렉션을 사용할 수도 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure에서 실제 클라우드 앱 빌드 - 자동화][Azure에서 실제 클라우드 앱 빌드 - 자동화](영문). 전자책에 나온 샘플 응용 프로그램이 Windows PowerShell 스크립트를 사용하여 Azure 테스트 환경을 만들고 이 환경에 배포하는 방법을 설명하는 전자책 장입니다. 추가 Azure PowerShell 설명서 링크를 보려면 [리소스][리소스](영문) 섹션을 참조하십시오.

### <a name="api"></a>.NET 관리 API

배포를 위해 MSBuild 또는 FTP 기능을 수행하도록 C# 코드를 작성할 수 있습니다. 이렇게 하면 Azure 관리 REST API에 액세스하여 사이트 관리 작업을 수행할 수 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 관리 라이브러리 및 .NET을 사용한 자동화][Azure 관리 라이브러리 및 .NET을 사용한 자동화](영문). .NET 관리 API를 소개하고 추가 설명서 링크를 제공합니다.

### <a name="cli"></a>플랫폼 간 명령줄(xpat-cli)

Mac 또는 Linux 컴퓨터에서 명령줄을 사용하여 FTP를 사용한 배포를 수행할 수 있습니다. 이 경우 Azure 플랫폼 간 명령줄 인터페이스(xpat-cli)를 사용하여 Azure REST 관리 API에 액세스할 수도 있습니다. xpat-cli는 Windows 컴퓨터에서도 사용할 수 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

-   [명령줄 도구][명령줄 도구]. WindowsAzure.com에서 명령줄 도구 정보를 제공하는 포털 페이지입니다.

### <a name="webdeploy"></a>웹 배포 명령줄

[웹 배포][웹 배포](영문)는 IIS에 배포하는 데 사용되는 Microsoft 소프트웨어로, 지능형 파일 동기화 기능을 제공할 뿐만 아니라 FTP 사용 시 자동화할 수 없는 다른 많은 배포 관련 작업을 수행하거나 조정할 수도 있습니다. 예를 들어 웹 배포는 웹 사이트와 함께 새 데이터베이스나 데이터베이스 업데이트를 배포할 수 있습니다. 웹 배포는 변경된 파일만 지능적으로 복사할 수 있으므로 기존 사이트를 업데이트하는 데 필요한 시간을 최소화할 수 있습니다. Microsoft WebMatrix, Visual Studio, Visual Studio Online 및 Team Foundation Server는 웹 배포를 기본적으로 지원합니다. 하지만 명령줄에서 바로 웹 배포를 사용하여 배포를 자동화할 수도 있습니다. 웹 배포 명령은 매우 강력하지만, 학습하는 데 오래 걸릴 수 있습니다.

자세한 내용은 다음 리소스를 참조하십시오.

-   [웹 배포 도구][웹 배포 도구](영문). Microsoft TechNet 사이트의 공식적인 설명서입니다. 이전에 작성되었지만 시작할 때 참조하기 좋습니다.
-   [웹 배포 사용][웹 배포 사용](영문). Microsoft IIS.NET 사이트의 공식적인 설명서입니다. 마찬가지로, 이전에 작성되었지만 시작할 때 참조하기 좋습니다.
-   [StackOverflow][StackOverflow](영문). 명령줄에서 웹 배포를 사용하는 방법에 대한 최신 정보를 원하는 경우 가장 적합한 자료입니다.
-   [Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포][Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포](영문). MSBuild는 Visual Studio에서 사용되는 빌드 엔진입니다. 명령줄에서 이 엔진을 사용하여 Azure 웹 사이트에 웹 응용 프로그램을 배포할 수도 있습니다. 이 자습서는 주로 Visual Studio 배포에 대해 다루는 시리즈의 일부입니다.

## <a name="nextsteps"></a>다음 단계

일부 상황에서는 웹 사이트의 스테이징 버전과 프로덕션 버전 간을 간단한 방법으로 자유롭게 전환할 수 있기를 원할 수도 있습니다. 이렇게 하려면 Azure 웹 사이트 준비된 배포 기능을 사용하면 됩니다. 자세한 내용은 [Microsoft Azure 웹 사이트의 준비된 배포][Microsoft Azure 웹 사이트의 준비된 배포]를 참조하십시오.

백업 및 복원 계획을 마련하는 일은 배포 워크플로의 중요한 부분입니다. Azure 웹 사이트의 백업 및 복원 기능에 대한 자세한 내용은 [Azure 웹 사이트 백업][Azure 웹 사이트 백업]을 참조하십시오.

다른 배포 항목에 대한 자세한 내용은 [Azure 웹 사이트 설명서][Azure 웹 사이트 설명서]의 배포 섹션을 참조하십시오.

  [클라우드에 호스트된 소스 제어 시스템에서 배포]: #cloud
  [IDE에서 배포]: #ide
  [FTP 유틸리티를 사용하여 배포]: #ftp
  [온-프레미스 소스 제어 시스템에서 배포]: #onpremises
  [명령줄 도구 및 Azure REST 관리 API 사용]: #commandline
  [지속적인 전송 워크플로]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [소스 제어 시스템]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Visual Studio Online]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs
  [VSO 및 TFVC를 사용하여 지속적으로 Azure에 전송]: http://www.visualstudio.com/ko-kr/learn/continuous-delivery-in-vs
  [VSO 및 TFVC를 사용한 Azure로의 지속적인 전송]: /ko-kr/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Visual Studio Online 및 Git를 사용하여 Azure에 지속적으로 전송]: http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
  [GitHub]: http://www.github.com
  [CodePlex]: http://www.codeplex.com/
  [BitBucket]: https://bitbucket.org/
  [소스 제어에서 Azure 웹 사이트로 게시]: /ko-kr/documentation/articles/web-sites-publish-source-control/
  [Kudu를 사용하여 GitHub를 통해 웹 사이트에 배포]: /ko-kr/documentation/videos/deploying-to-azure-from-github/
  [Git, Mercurial 및 DropBox의 Azure 포럼]: http://social.msdn.microsoft.com/Forums/windowsazure/ko-kr/home?forum=azuregit
  [Mercurial]: http://mercurial.selenic.com/
  [Dropbox]: https://www.dropbox.com/
  [Dropbox를 Windows Azure에 배포]: http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx
  [Dropbox 및 Azure 웹 사이트]: http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites
  [Visual Studio]: http://www.visualstudio.com/ko-kr/downloads/download-visual-studio-vs.aspx
  [WebMatrix]: http://www.microsoft.com/web/webmatrix/
  [웹 배포]: http://www.iis.net/downloads/microsoft/web-deploy
  [FTP 또는 FTPS]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [1]: #vso
  [Team Foundation Server]: #tfs
  [Git 리포지토리]: #git
  [Azure 및 ASP.NET 시작]: /ko-kr/develop/net/tutorials/get-started/
  [Azure 웹 사이트에 Azure WebJobs를 배포하는 방법]: /ko-kr/documentation/articles/websites-dotnet-deploy-webjobs/
  [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 ASP.NET MVC 5 앱 배포]: /ko-kr/develop/net/tutorials/web-site-with-sql-database/
  [Visual Studio 및 ASP.NET의 웹 배포 개요]: http://msdn.microsoft.com/ko-kr/library/dd394698.aspx
  [Visual Studio를 사용한 ASP.NET 웹 배포]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction
  [Visual Studio 2012를 사용하여 Git 리포지토리에서 바로 Azure에 ASP.NET 웹 사이트 배포]: http://www.dotnetcurry.com/ShowArticle.aspx?ID=881
  [Microsoft WebMatrix로 웹 사이트 개발 및 배포]: http://www.windowsazure.com/ko-kr/documentation/articles/web-sites-dotnet-using-webmatrix/
  [WebMatrix를 사용하여 Node.js 웹 사이트 빌드 및 Azure에 배포]: http://www.windowsazure.com/ko-kr/documentation/articles/web-sites-nodejs-use-webmatrix/
  [WebMatrix를 사용하여 PHP-MySQL Azure 웹 사이트 만들기 및 배포]: http://www.windowsazure.com/ko-kr/documentation/articles/web-sites-php-mysql-use-webmatrix/
  [WebMatrix 3: 통합된 Git 및 Azure에 배포]: http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD
  [FileZilla]: https://filezilla-project.org/
  [PHP-MySQL Azure 웹 사이트 만들기 및 FTP를 사용하여 배포]: /ko-kr/documentation/articles/web-sites-php-mysql-deploy-use-ftp/
  [웹 사이트를 관리하는 방법]: http://www.windowsazure.com/ko-kr/documentation/articles/web-sites-manage#ftp-credentials
  [Azure 클라우드 서비스의 지속적인 전송]: /ko-kr/develop/net/common-tasks/continuous-delivery/
  [git/hg 리포지토리에서 Azure 웹 사이트에 게시]: http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html
  [하나의 Git 리포지토리에서 Azure에 두 웹 사이트 배포]: http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx
  [Visual Studio IDE]: #vs
  [MSBuild]: http://msbuildbook.com/
  [2]: #webdeploy
  [Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment
  [FTP 일괄 스크립트 사용]: http://support.microsoft.com/kb/96269
  [Windows PowerShell]: http://msdn.microsoft.com/ko-kr/library/dd835506.aspx
  [Azure에서 실제 클라우드 앱 빌드 - 자동화]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [리소스]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources
  [Azure 관리 라이브러리 및 .NET을 사용한 자동화]: http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx
  [명령줄 도구]: /ko-kr/downloads/#cmd-line-tools
  [웹 배포 도구]: http://technet.microsoft.com/ko-kr/library/dd568996
  [웹 배포 사용]: http://www.iis.net/learn/publish/using-web-deploy
  [StackOverflow]: http://www.stackoverflow.com
  [Microsoft Azure 웹 사이트의 준비된 배포]: /ko-kr/documentation/articles/web-sites-staged-publishing/
  [Azure 웹 사이트 백업]: /ko-kr/documentation/articles/web-sites-backup/
  [Azure 웹 사이트 설명서]: /ko-kr/documentation/services/web-sites/
