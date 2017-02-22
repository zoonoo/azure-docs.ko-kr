---
title: "Azure App Service에 앱 배포 | Microsoft Docs"
description: "Azure 앱 서비스에 앱을 배포하는 방법에 대해 알아봅니다."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: f1464f71-2624-400e-86a2-e687e385804f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin;dariac
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: 4b3b96e9c5d7a4ff99c803aa356dcb5ad6997978


---
# <a name="deploy-your-app-to-azure-app-service"></a>Azure 앱 서비스에 앱 배포
이 문서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에 웹앱용 파일, 모바일 앱 백엔드 또는 API 앱을 배포하는 최선의 방법을 결정하는 방법을 설명한 다음 선호하는 옵션에 해당하는 지침으로 적절한 리소스를 안내합니다.

## <a name="a-nameoverviewaazure-app-service-deployment-overview"></a><a name="overview"></a>Azure 앱 서비스 배포 개요
Azure 앱 서비스는 응용 프로그램 프레임워크를 유지합니다(ASP.NET, PHP, Node.js 등). 일부 프레임워크는 기본적으로 사용하도록 설정되어 있는 반면 Java와 Python과 같은 다른 프레임워크는 간단한 확인 표시를 구성해야 사용하도록 설정됩니다. 또한 런타임의 비트 수 또는 PHP 버전과 같이 응용 프로그램 프레임워크를 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure 앱 서비스에서 앱 구성](web-sites-configure.md)을 참조하세요.

웹 서버 또는 응용 프로그램 프레임워크에 대해 신경 쓰지 않아도 되므로 App Service에 앱을 배포할 때에는 코드, 이진 파일, 콘텐츠 파일 및 해당 디렉터리 구조를 Azure의 [**/site/wwwroot** 디렉터리](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(또는 WebJobs의 경우 **/site/wwwroot/App_Data/Jobs/** 디렉터리)에 배포하기만 하면 됩니다. App Service는 세 가지 배포 프로세스를 지원합니다. 이 문서의 모든 배포 방법은 다음 프로세스 중 하나를 사용합니다. 

* [FTP 또는 FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): FTP 또는 FTPS가 사용되도록 설정된 즐겨쓰는 도구를 사용하여 파일을 Azure로 이동합니다([FileZilla](https://filezilla-project.org)에서 [NetBeans](https://netbeans.org)와 같은 완전한 기능의 IDE로). 이는 엄격한 파일 업로드 프로세스입니다. 버전 제어, 파일 구조 관리 등과 같은 추가 서비스는 앱 서비스에서 제공되지 않습니다. 
* [Kudu(Git/Mercurial or OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): Kudu는 App Service에서 [배포 엔진](https://github.com/projectkudu/kudu/wiki)입니다. 코드를 리포지토리에서 직접 Kudu로 푸시합니다. 또한 Kudu는 코드가 푸시될 때마다 버전 제어, 패키지 복원, MSBuild 및 [웹 후크](https://github.com/projectkudu/kudu/wiki/Web-hooks) 를 비롯한 추가된 서비스를 지속적인 배포 및 다른 자동화 작업에 제공합니다. Kudu 배포 엔진은 3가지 유형의 배포 소스를 지원합니다.   
  
  * OneDrive 및 Dropbox의 콘텐츠 동기화   
  * GitHub, Bitbucket, Visual Studio Team Services의 자동 동기화를 이용한 리포지토리 기반 연속 배포  
  * 로컬 Git의 수동 동기화를 이용한 리포지토리 기반 배포  
* [웹 배포](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): IIS 서버에 대한 배포를 자동화하는 도구를 사용하여 Visual Studio와 같이 자주 사용하는 Microsoft 도구에서 App Service에 코드를 직접 배포합니다. 이 도구는 차이나는 부분만 배포, 데이터베이스 만들기, 연결 문자열 변환 등을 지원합니다. 웹 배포는 응용 프로그램 이진 파일이 Azure에 배포되기 전에 빌드된다는 점에서 Kudu와 다릅니다. FTP와 마찬가지로 앱 서비스에서 추가 서비스가 제공되지 않습니다.

널리 사용되는 웹 개발 도구는 이러한 배포 프로세스 중 하나 이상을 지원합니다. 선택하는 도구에 따라 활용할 수 있는 배포 프로세스가 결정되지만 실제 사용하는 DevOps 기능은 선택하는 배포 프로세스 및 특정 도구의 조합에 따라 달라집니다. 예를 들어 [Visual Studio(Azure SDK 포함)](#vspros)에서 웹 배포를 수행하면 Kudu에서 자동화를 이용할 수 없는 경우에도 Visual Studio에서 패키지 복원 및 MSBuild 자동화가 가능합니다. 

> [!NOTE]
> 이러한 배포 프로세스는 사용자 앱에 필요한 [Azure 리소스를 실제로 프로비전](../azure-resource-manager/resource-group-template-deploy-portal.md) 하지 않습니다. 그러나 연결된 대부분의 사용 방법 문서는 앱을 프로비전하고 종단 간으로 코드를 배포하는 방법을 보여 줍니다. 또한 [명령줄 도구를 사용하여 배포 자동화](#automate) 섹션의 Azure 리소스를 프로비전하는 방법에 대한 추가 옵션을 확인할 수 있습니다.
> 
> 

## <a name="a-nameftpadeploy-manually-by-uploading-files-with-ftp"></a><a name="ftp"></a>FTP를 사용하여 파일을 업로드하여 수동으로 배포
웹 콘텐츠를 웹 서버로 수동 복사하는 데 익숙한 경우 Windows 탐색기, [FileZilla](https://filezilla-project.org/)와 같은 [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 유틸리티를 사용하여 파일을 복사할 수 있습니다.

파일을 수동으로 복사할 때 장점:

* FTP 도구 사용에 익숙하며 복잡성이 최소화됨 
* 파일의 이동 위치를 정확히 인지
* FTPS를 사용한 보안 강화

파일을 수동으로 복사할 때 단점

* 앱 서비스에서 파일을 올바른 디렉터리에 배포하는 방법을 알아야 함 
* 오류가 발생할 때 롤백을 위한 버전 제어가 없음
* 배포 문제 해결을 위한 기본 배포 기록이 없음
* 많은 FTP 도구가 차이 나는 부분만 복사하는 것이 아니라 단순히 모든 파일을 복사하기 때문에 배포 시간이 길어질 수 있음  

### <a name="a-namehowtoftpahow-to-upload-files-with-ftp"></a><a name="howtoftp"></a>FTP를 사용하여 파일을 업로드하는 방법
[Azure Portal](https://portal.azure.com)에서는 FTP 또는 FTPS를 사용하여 앱의 디렉터리에 연결하기 위해 필요한 모든 정보를 제공합니다.

* [FTP를 사용하여 Azure App Service에 앱 배포](app-service-deploy-ftp.md)

## <a name="a-namedropboxadeploy-by-syncing-with-a-cloud-folder"></a><a name="dropbox"></a>클라우드 폴더와 동기화하여 배포
[파일을 수동으로 복사](#ftp) 하는 작업에 대한 좋은 대안은 OneDrive 및 Dropbox와 같은 클라우드 저장소 서비스에서 앱 서비스로 파일 및 폴더를 동기화하는 것입니다. 클라우드 폴더와 동기화하는 작업에서는 배포에 Kudu 프로세스를 활용합니다( [배포 프로세스 개요](#overview)참조).

클라우드 폴더를 동기화할 때의 장점:

* 배포의 간소화. OneDrive 및 Dropbox와 같은 서비스는 데스크톱 동기화 클라이언트를 제공하므로 로컬 작업 디렉터리도 배포 디렉터리입니다.
* 한 번의 클릭으로 배포
* Kudu 배포 엔진의 모든 기능을 사용할 수 있음(예: 패키지 복원, 자동화)

클라우드 폴더를 동기화할 때의 단점:

* 오류가 발생할 때 롤백을 위한 버전 제어가 없음
* 자동화된 배포가 지원되지 않으며 수동 동기화가 필요함

### <a name="a-namehowtodropboxahow-to-deploy-by-syncing-with-a-cloud-folder"></a><a name="howtodropbox"></a>클라우드 폴더와 동기화하여 배포하는 방법
[Azure 포털](https://portal.azure.com)에서 OneDrive 또는 Dropbox 클라우드 저장소에서 콘텐츠 동기화를 수행할 폴더를 지정하고 해당 폴더에서 앱 코드 및 콘텐츠 작업을 수행한 다음 단추를 클릭하여 앱 서비스를 동기화할 수 있습니다.

* [클라우드 폴더에서 Azure 앱 서비스로 콘텐츠 동기화](app-service-deploy-content-sync.md) 

## <a name="a-namecontinuousdeploymentadeploy-continuously-from-a-cloud-based-source-control-service"></a><a name="continuousdeployment"></a>클라우드 기반 소스 제어 서비스에서 연속 배포
배포 팀이 [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com), [BitBucket](https://bitbucket.org/)과 같은 클라우드 기반 SCM(소스 코드 관리)을 사용하는 경우 App Service를 구성하여 리포지토리와 통합하고 연속적으로 배포할 수 있습니다. 

클라우드 기반 소스 제어 서비스에서 배포할 때의 장점:

* 롤백을 실행하기 위한 버전 제어
* Git(해당할 경우 Mercurial) 리포지토리에 대한 연속 배포를 구성할 수 있음 
* 분기별 배포를 통해 다른 분기를 다른 [슬롯](web-sites-staged-publishing.md)에 배포할 수 있습니다.
* Kudu 배포 엔진의 모든 기능을 사용할 수 있음(예: 배포 버전 관리, 롤백, 패키지 복원, 자동화)

클라우드 기반 소스 제어 서비스에서 배포할 경우 단점:

* 관련 SCM 서비스에 대한 어느 정도의 지식이 필요합니다.

### <a name="a-namevstsahow-to-deploy-continuously-from-a-cloud-based-source-control-service"></a><a name="vsts"></a>클라우드 기반 소스 제어 서비스에서 연속 배포하는 방법
[Azure 포털](https://portal.azure.com)에서 GitHub, Bitbucket, Visual Studio Team Services에서 연속 배포를 구성할 수 있습니다.

* [Azure 앱 서비스에 연속 배포](app-service-continuous-deployment.md). 

Azure Portal에 의해 나열되지 않은 클라우드 저장소에서 수동으로 연속 배포를 구성하는 방법을 확인하려면(예: [GitLab](https://gitlab.com/)), [수동 단계를 사용하여 연속 배포 설정](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)을 참조하세요.

## <a name="a-namelocalgitdeploymentadeploy-from-local-git"></a><a name="localgitdeployment"></a>로컬 Git에서 배포
개발 팀이 Git를 기반으로 하는 온-프레미스 로컬 SCM(소스 코드 관리) 서비스를 사용할 경우 이 서비스를 앱 서비스에 대한 배포 소스로 구성할 수 있습니다. 

로컬 Git에서 배포할 경우 장점:

* 롤백을 실행하기 위한 버전 제어
* 분기별 배포를 통해 다른 분기를 다른 [슬롯](web-sites-staged-publishing.md)에 배포할 수 있습니다.
* Kudu 배포 엔진의 모든 기능을 사용할 수 있음(예: 배포 버전 관리, 롤백, 패키지 복원, 자동화)

로컬 Git에서 배포할 경우 단점:

* 관련 SCM 시스템에 대한 어느 정도의 지식이 필요합니다.
* 연속 배포를 위한 턴키 솔루션이 없음 

### <a name="a-namevstsahow-to-deploy-from-local-git"></a><a name="vsts"></a>로컬 Git에서 배포하는 방법
[Azure 포털](https://portal.azure.com)에서 로컬 Git 배포를 구성할 수 있습니다.

* [Azure 앱 서비스에 로컬 Git 배포](app-service-deploy-local-git.md) 
* [git/hg 리포지토리에서 웹 앱에 게시합니다.](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)  

## <a name="deploy-using-an-ide"></a>IDE를 사용하여 배포
[Azure SDK](https://azure.microsoft.com/downloads/) 또는 [Xcode](https://developer.apple.com/xcode/), [Eclipse](https://www.eclipse.org) 및 [IntelliJ IDEA](https://www.jetbrains.com/idea/)와 같은 기타 IDE 제품군이 포함된 [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)를 이미 사용하고 있는 경우 IDE 내에서 직접 Azure에 배포할 수 있습니다. 이 옵션은 개인 개발자에 이상적입니다.

Visual Studio가 기본 설정에 따라 모두 세 가지 배포 프로세스(FTP, Git, 웹 배포)를 지원하는 반면 FTP 또는 Git가 통합된 경우 다른 IDE를 사용하여 앱 서비스에 배포할 수 있습니다( [배포 프로세스 개요](#overview)참조).

IDE를 사용하여 배포할 때의 장점:

* 잠재적으로 종단 간 응용 프로그램 수명 주기에 사용할 도구 최소화 IDE 외부로 이동하지 않고 앱 개발, 디버그, 추적 및 Azure에 배포 

IDE를 사용하여 배포할 때의 단점:

* 도구 사용의 복잡성 증가
* 팀 프로젝트의 경우 여전히 소스 제어 시스템 필요

<a name="vspros"></a> Azure SDK가 있는 Visual Studio를 사용하여 배포할 때의 추가 장점:

* Azure SDK를 통해 Visual Studio에서 Azure 리소스를 최고의 리소스로 만들 수 있음. 앱 만들기, 삭제, 편집, 시작 및 중지, 백 엔드 SQL 데이터베이스 쿼리, Azure 앱 라이브 디버그 등의 수많은 작업이 가능합니다. 
* Azure에서 코드 파일의 라이브 편집
* Azure에서 앱의 라이브 디버깅
* 통합된 Azure 탐색기
* 차이나는 부분만 배포 

### <a name="a-namevsahow-to-deploy-from-visual-studio-directly"></a><a name="vs"></a>Visual Studio에서 직접 배포하는 방법
* [Azure 및 ASP.NET 시작](web-sites-dotnet-get-started.md). Visual Studio 및 웹 배포를 사용하여 간단한 ASP.NET MVC 웹 프로젝트를 만들고 배포하는 방법을 설명합니다.
* [Visual Studio를 사용하여 Azure WebJobs를 배포하는 방법](websites-dotnet-deploy-webjobs.md) WebJob으로 배포되도록 콘솔 응용 프로그램 프로젝트를 구성하는 방법입니다.  
* [멤버 자격, OAuth 및 SQL Database가 포함된 보안 ASP.NET MVC 5 앱을 Web Apps에 배포합니다](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Visual Studio, 웹 배포 및 Entity Framework Code First 마이그레이션을 사용하여 SQL 데이터베이스가 포함된 ASP.NET MVC 웹 프로젝트를 만들고 배포하는 방법을 설명합니다.
* [Visual Studio를 사용한 ASP.NET 웹 배포](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)(영문). 12개 부분으로 구성된 자습서 시리즈로, 이 목록의 다른 자료보다 더 폭넓은 배포 작업 범위를 다룹니다. 일부 Azure 배포 기능은 이 자습서가 작성되기 전에 추가되었지만 이후에 추가된 참고 사항에 누락된 내용이 설명되어 있습니다.
* [Visual Studio 2012를 사용하여 Git 리포지토리에서 바로 Azure에 ASP.NET 웹 사이트 배포](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)(영문). Git 플러그 인을 사용하여 코드를 Git에 커밋하고 Azure를 Git 리포지토리에 연결하여 Visual Studio에서 ASP.NET 웹 프로젝트를 배포하는 방법을 설명합니다. Visual Studio 2013부터 Git 지원이 기본 제공되므로 플러그 인을 설치할 필요가 없습니다.

### <a name="a-nameaztkahow-to-deploy-using-the-azure-toolkits-for-eclipse-and-intellij-idea"></a><a name="aztk"></a>Eclipse 및 IntelliJ IDEA용 Azure 도구 키트를 사용하여 배포하는 방법
Microsoft는 [Eclipse용 Azure 도구 키트](../azure-toolkit-for-eclipse.md) 및 [IntelliJ용 Azure 도구 키트](../azure-toolkit-for-intellij.md)를 통해 Eclipse 및 IntelliJ에서 Azure로 직접 Web Apps을 배포할 수 있도록 지원합니다. 다음 자습서에서는 이러한 IDE 중 하나를 사용하여 Azure에 간단한 "Hello" world 웹앱을 배포하는 단계를 설명합니다.

* [Eclipse에서 Azure용 Hello World Web Apps을 만듭니다](app-service-web-eclipse-create-hello-world-web-app.md). 이 자습서에서는 Eclipse용 Azure 도구 키트를 사용하여 Azure용 Hello World 웹앱을 만들고 배포하는 방법을 보여 줍니다.
* [IntelliJ에서 Azure용 Hello World Web Apps을 만듭니다](app-service-web-intellij-create-hello-world-web-app.md). 이 자습서에서는 IntelliJ용 Azure 도구 키트를 사용하여 Azure용 Hello World 웹앱을 만들고 배포하는 방법을 보여 줍니다.

## <a name="a-nameautomateaautomate-deployment-by-using-command-line-tools"></a><a name="automate"></a>명령줄 도구를 사용하여 배포 자동화
선택한 개발 환경으로 명령줄 터미널을 선호하는 경우에는 명령줄 도구를 사용하여 App Service에 대한 배포 작업을 스크립팅할 수 있습니다. 

명령줄 도구를 사용한 배포의 장점:

* 스크립트 배포 시나리오가 가능합니다.
* Azure 리소스와 코드 배포의 프로비저닝을 통합합니다.
* Azure 배포를 기존 연속 통합 스크립트에 통합합니다.

명령줄 도구를 사용한 배포의 단점:

* GUI를 선호하는 개발자에게는 적합하지 않습니다.

### <a name="a-nameautomatehowahow-to-automate-deployment-with-command-line-tools"></a><a name="automatehow"></a>명령줄 도구를 사용하여 배포를 자동화하는 방법

명령줄 도구 목록 및 자습서 링크는 [명령줄 도구를 사용하여 Azure 앱 배포 자동화](app-service-deploy-command-line.md)를 참조하세요. 

## <a name="a-namenextstepsanext-steps"></a><a name="nextsteps"></a>다음 단계
어떤 상황에서는 앱의 스테이징 버전과 프로덕션 버전 사이를 간단한 방법으로 자유롭게 전환할 수 있습니다. 자세한 내용은 [웹 앱에서 준비된 배포](web-sites-staged-publishing.md)를 참조하세요.

백업 및 복원 계획을 마련하는 일은 배포 워크플로의 중요한 부분입니다. 앱 서비스 백업 및 복원 기능에 대한 자세한 내용은 [웹앱 백업](web-sites-backup.md)을 참조하세요.  

Azure의 역할 기반 액세스 제어를 사용하여 앱 서비스 배포에 대한 액세스를 관리하는 방법은 [RBAC 및 웹앱 게시](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)를 참조하세요.




<!--HONumber=Jan17_HO1-->


