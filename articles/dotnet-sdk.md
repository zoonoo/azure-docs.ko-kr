<properties 
	pageTitle="Azure .NET SDK 정의" 
	description="Azure .NET SDK에 포함된 항목에 대해 알아봅니다." 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/11/2015" 
	ms.author="tdykstra"/>

# Azure SDK for .NET 정의

## 개요

Azure SDK for .NET은 Azure에서 실행되는 앱을 개발, 테스트 및 배포하는 데 도움이 되는 Visual Studio 도구, 명령줄 도구, 런타임 라이브러리 및 클라이언트 라이브러리 집합입니다. 이 문서에서는 SDK를 설치할 경우 제공되는 항목에 대해 자세히 설명합니다. [Azure 다운로드 페이지](/downloads/)에서 SDK를 다운로드할 수 있습니다.

Azure SDK for .NET은 또한 [Azure 서비스를 사용하기 위한 클라이언트 라이브러리](http://go.microsoft.com/fwlink/?LinkId=510472)로 구성됩니다. 해당 라이브러리는 NuGet을 사용하여 별도로 설치됩니다.

##<a id="included"></a>Azure SDK for .NET에 포함된 항목

Azure SDK for .NET은 다음 제품을 설치합니다.

- [Visual Studio Express for Web](#vwd)도 설치합니다.
- [Visual Studio용 Microsoft ASP.NET 및 웹 도구](#wte)
- [Microsoft Visual Studio용 Microsoft Azure 도구](#tools)
- [Microsoft Azure 작성 도구](#auth)
- [Microsoft Azure 에뮬레이터](#emulator)
- [Microsoft Azure 저장소 에뮬레이터](#stgemulator)
- [Microsoft Azure 저장소 도구](#stgtools)
- [.NET용 Microsoft Azure 라이브러리](#libraries)
- [HDInsight Tools for Visual Studio](#hdinsight) 및 [Microsoft Hive ODBC 드라이버](#hdinsight)
- [Microsoft Azure Mobile App SDK V1.0](#mobile)
- [Microsoft Azure PowerShell](#ps)

###<a id="vwd"></a>Visual Studio Express for Web

컴퓨터에 Visual Studio가 없는 경우 SDK에서 [Visual Studio Express for Web](http://www.visualstudio.com/products/visual-studio-express-vs.aspx)을 설치합니다.
 
###<a id="wte"></a>Visual Studio용 Microsoft ASP.NET 및 웹 도구

이 제품을 사용하면 Azure 웹 사이트에서 다음 작업을 수행할 수 있습니다.

* [Azure 웹 사이트에 웹 프로젝트 게시](web-sites-dotnet-get-started.md)
* [Azure WebJobs에 콘솔 응용 프로그램 프로젝트 게시](websites-dotnet-deploy-webjobs.md)
* [새 웹 프로젝트를 만드는 동안 또는 웹 프로젝트를 게시하는 동안 Azure 웹 사이트 및 SQL 데이터베이스 리소스 만들기](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* [새 웹 사이트를 만드는 동안 PowerShell 배포 스크립트 만들기](http://msdn.microsoft.com/library/dn642480.aspx)
* [서버 탐색기에서 Azure 웹 사이트 관리 및 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement)
* [웹 사이트 및 WebJobs에 대해 원격으로 디버그 모드에서 실행](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) 

>[AZURE.NOTE]이러한 기능을 사용하기 위해 Azure SDK for .NET을 설치할 필요는 없습니다. Visual Studio 업데이트에도 포함되어 있습니다.

###<a id="tools"></a>Microsoft Visual Studio용 Microsoft Azure 도구

이 제품을 사용하면 Azure 리소스, 주로 클라우드 서비스 및 가상 컴퓨터에서 다음 작업을 수행할 수 있습니다.

* [클라우드 서비스 프로젝트 만들기, 열기 및 게시](cloud-services-dotnet-get-started.md)
* [클라우드 서비스 프로젝트에 대한 배포 패키지 만들기](http://msdn.microsoft.com/library/ff683672.aspx)
* [새 웹 프로젝트를 만드는 동안 Azure 가상 컴퓨터 만들기](virtual-machines-dotnet-create-visual-studio-powershell.md)
* [새 가상 컴퓨터를 만드는 동안 PowerShell 스크립트 만들기](http://msdn.microsoft.com/library/dn642480.aspx)
* [Visual Studio 프로젝트 속성 창에서 클라우드 서비스 프로젝트 설정 보기 및 관리](http://msdn.microsoft.com/library/ee405486.aspx)
* 서버 탐색기에서 [클라우드 서비스](http://msdn.microsoft.com/library/ff683675.aspx), [가상 컴퓨터](http://msdn.microsoft.com/library/jj131259.aspx) 및 [서비스 버스](http://msdn.microsoft.com/library/jj149828.aspx) 보기 및 관리 
* [클라우드 서비스 및 가상 컴퓨터에 대해 원격으로 디버그 모드에서 실행](http://msdn.microsoft.com/library/ff683670.aspx)
* [Azure 리소스 그룹 배포 프로젝트를 사용하여 리소스 프로비저닝 자동화](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="auth"></a>Microsoft Azure 작성 도구

이 제품에는 다음이 포함됩니다.

* 배포 패키지를 만들기 위한 [CSPack 명령줄 도구](http://msdn.microsoft.com/library/gg432988.aspx)
* 원격 데스크톱 연결을 통해 클라우드 서비스 역할 인스턴스에 액세스하는 데 사용되는 암호를 암호화하기 위한 [CSEncrypt 명령줄 도구](http://msdn.microsoft.com/library/hh404001.aspx)
* 클라우드 서비스 프로젝트가 런타임 환경과 통신하고 진단하는 데 필요한 런타임 이진. NuGet 패키지에서는 해당 이진을 사용할 수 없습니다.

###<a id="emulator"></a>Microsoft Azure 에뮬레이터

[Azure 에뮬레이터](http://msdn.microsoft.com/library/dn339018.aspx)는 Azure에 배포하기 전에 사용자 컴퓨터에서 클라우드 서비스 프로젝트를 로컬로 테스트할 수 있도록 클라우드 서비스 환경을 시뮬레이트합니다.

###<a id="stgemulator"></a>Microsoft Azure 저장소 에뮬레이터

[Azure 저장소 에뮬레이터](http://msdn.microsoft.com/library/hh403989.aspx)는 로컬에서 테스트할 수 있도록 SQL Server 인스턴스 및 로컬 파일 시스템을 사용하여 Azure 저장소(큐, 테이블, blob)를 시뮬레이트합니다.

###<a id="stgtools"></a>Microsoft Azure 저장소 도구

이 제품은 Azure 저장소 계정으로 데이터를 전송하거나 계정에서 데이터를 전송하는 데 사용할 수 있는 명령줄 도구인 [AzCopy](http://aka.ms/AzCopy)를 설치합니다.

###<a id="libraries"></a>.NET용 Microsoft Azure 라이브러리

이 제품에는 다음이 포함됩니다.

* Visual Studio가 오프라인 상태에서 새 클라우드 서비스 프로젝트를 만들 수 있도록 사용자 컴퓨터에 저장되는 Azure 저장소, 서비스 버스 및 캐싱용 NuGet 패키지입니다.
* [In-Role Cache](http://msdn.microsoft.com/library/dn386103.aspx) 프로젝트가 Visual Studio에서 로컬로 실행될 수 있도록 하는 Visual Studio 플러그 인입니다. 

###<a id="hdinsight"></a>HDInsight Tools for Visual Studio 및 Microsoft Hive ODBC 드라이버

서버 탐색기의 HDInsight 도구를 사용하여 Hive 데이터베이스와 HDInsight 클러스터에 대해 연결된 저장소 계정을 탐색하고 테이블을 만들고 Hive 쿼리를 생성 및 전송할 수 있습니다. 자세한 내용은 [Visual Studio용 HDInsight Hadoop 도구 사용 시작](hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

###<a id="mobile">Microsoft Azure Mobile App SDK

[Azure 앱 서비스 모바일 앱](app-service-mobile-value-prop-preview.md)으로 작업하기 위한 도구

###<a id="ps"></a>Microsoft Azure PowerShell

Azure PowerShell을 사용하면 [Azure 환경 만들기 및 배포를 자동화](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)할 수 있습니다.

##<a id="notincluded"></a>Azure SDK for .NET을 설치할 때 포함되지 않는 항목

SDK를 설치할 때 포함되지 않지만 Azure 개발에 유용할 수 있는 몇 가지 항목이 있습니다. 가장 중요한 항목은 다음과 같습니다.

* [클라이언트 라이브러리](http://go.microsoft.com/fwlink/?LinkId=510472) 

	Azure SDK에는 Azure 서비스를 사용하기 위한 클라이언트 라이브러리가 포함되어 있지만 SDK를 설치할 때 모두 설치되는 것은 아닙니다. SDK에서 설치하지 않는 클라이언트 라이브러리가 응용 프로그램에 필요한 경우 [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472)에서 다운로드할 수 있습니다. 응용 프로그램이 SDK에서 설치하는 클라이언트 라이브러리를 사용하는 경우 NuGet.org의 최신 버전으로 업데이트하는 것이 좋습니다.

  	**클라이언트 라이브러리의 로컬 복사본.** Azure SDK for .NET은 저장소, 서비스 버스, 캐싱 등 일부 Azure 클라이언트 라이브러리에 대한 NuGet 패키지를 사용자 컴퓨터에 복사합니다. 이러한 클라이언트 라이브러리는 새 클라우드 서비스 프로젝트에 자동으로 포함되므로 로컬 NuGet 패키지를 사용하면 인터넷에 연결되지 않은 경우에도 Visual Studio에서 프로젝트를 만들 수 있습니다. 클라이언트 라이브러리는 일반적으로 새 SDK 버전 릴리스보다 자주 업데이트되므로 NuGet.org의 클라이언트 라이브러리가 SDK와 함께 설치되는 버전보다 최신인 경우가 많습니다.

	**클라이언트 라이브러리가 포함된 프로젝트 템플릿.** [Azure 클라우드 서비스](cloud-services-dotnet-get-started.md) 및 [Azure 모바일 서비스](mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md) 프로젝트 템플릿에는 일부 클라이언트 라이브러리가 자동으로 포함됩니다. 다른 라이브러리 또는 다른 템플릿을 사용하려면 필요한 [클라이언트 라이브러리 NuGet 패키지](http://go.microsoft.com/fwlink/?LinkId=510472)를 설치합니다.

* [Azure 모바일 서비스 프로젝트 템플릿](mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md)

	모바일 서비스 템플릿은 Visual Studio 2013 업데이트 2 이상에서만 사용할 수 있습니다. Visual Studio 2012 이전 버전과 Visual Studio 2013 업데이트 1 이전에서는 Azure SDK for .NET을 설치한 경우에도 사용할 수 없습니다.

##<a id="faq"></a>질문과 대답

- [많은 Azure 기능은 Visual Studio에 이미 있습니다. Azure SDK for .NET을 설치해야 하나요?](#azinvs)
- [클라이언트 라이브러리를 사용하려고 합니다. 클라이언트 라이브러리를 사용하려면 Azure SDK for .NET을 설치해야 하나요?](#clientlib)
- [이전 버전의 Azure SDK for .NET은 어디서 찾을 수 있나요?](#olderversions)
- [Azure SDK for .NET 버전에 대한 수명 주기 정책은 무엇인가요?](#lifecycle)
- [Azure SDK for .NET과 호환되는 게스트 OS 버전은 무엇인가요?](#guestos)
- [Azure SDK for .NET은 어떻게 제거해야 하나요?](#uninstall)

###<a id="azinvs"></a>많은 Azure 기능은 Visual Studio에 이미 있습니다. Azure SDK for .NET을 설치해야 하나요?

최신 도구를 사용하여 Azure에 대해 개발하려면 SDK를 설치하는 것이 좋습니다. SDK를 설치하지 않으려는 경우 다음 조건이 true이면 설치하지 않아도 됩니다.

* 최신 [Visual Studio 업데이트](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5)를 설치했습니다.
* Azure 웹 사이트 또는 모바일 서비스에 대해서만 개발하고 클라우드 서비스 또는 가상 컴퓨터에 대해 개발하지 않습니다.
* 응용 프로그램이 저장소를 사용하지 않거나 저장소를 사용하지만 저장소 에뮬레이터 또는 AzCopy 도구가 필요 없습니다.

###<a id="clientlib"></a>클라이언트 라이브러리를 사용하려고 합니다. 클라이언트 라이브러리를 사용하려면 Azure SDK for .NET을 설치해야 하나요?

SDK는 인터넷에 연결되지 않은 경우에도 클라우드 서비스 프로젝트를 만들 수 있도록 하기 위해서만 클라이언트 라이브러리를 설치합니다. [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472)의 NuGet 패키지에서 최신 클라이언트 라이브러리를 사용할 수 있습니다. 자세한 내용은 이 문서의 앞부분에 있는 [Azure SDK for .NET을 설치할 때 포함되지 않는 항목](#notincluded)을 참조하세요.

###<a id="olderversions"></a>이전 버전의 Azure SDK for .NET은 어디서 찾을 수 있나요?

이전 버전의 경우 [Azure SDK for .NET](/downloads/archive-net-downloads/) 다운로드 페이지를 참조하세요.

###<a id="lifecycle"></a>Azure SDK for .NET 버전에 대한 수명 주기 정책은 무엇인가요?

[Microsoft Azure 클라우드 서비스 지원 수명 주기 정책](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)을 참조하세요.

###<a id="guestos"></a>Azure SDK for .NET과 호환되는 게스트 OS 버전은 무엇인가요?

[Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](http://msdn.microsoft.com/library/ee924680.aspx)를 참조하세요.

###<a id="uninstall"></a>Azure SDK for .NET은 어떻게 제거하나요?

이 문서의 [Azure SDK for .NET에 포함된 항목](#included) 아래에 있는 각 항목은 Windows 제어판 **프로그램 및 기능**에 설치되어 있는 프로그램 목록에 있는 별도의 프로그램입니다. 이들을 그룹으로 제거할 방법은 없습니다. 각 프로그램을 개별적으로 제거해야 합니다.

이미 Azure SDK for .NET이 설치되어 있고 새로운 버전을 설치하는 경우에는 일반적으로 기존 버전을 제거하지 않아도 됩니다. 대부분의 경우 SDK를 설치하면 새로운 프로그램을 추가하고 기존의 프로그램을 없애는 것이 아니라 기존 프로그램을 업데이트합니다.

그러나 기존 버전에서 더 이상 필요 없는 나머지 부분들을 제거하고자 한다면 기존 버전 번호를 지정하는 프로그램만 제거하고 새로운 버전의 동일한 프로그램이 있는 경우에만 제거하십시오. 예를 들어, 2.5에서 2.6으로 업데이트한 다음 "Microsoft Azure Tools for Microsoft Visual Studio 2013"의 2.5 및 2.6버전이 모두 표시될 수도 있는데, 이 때 2.5버전을 제거할 수 있습니다. 하지만 2.5버전의 "Microsoft Azure Authoring Tools"만 표시될 수도 있는데, 이 경우에는 제거하지 않는 것이 안전합니다.

**프로그램 및 기능**에 표시된 프로그램 제목 내의 버전 번호는 잘못된 것일 수도 있습니다. 예를 들어, SDK for Visual Studio 2013을 설치하는 경우 SDK 버전 2.6에 "Microsoft Azure Mobile App SDK V1.0"이 포함되며, Visual Studio 2015의 경우에는 "Microsoft Azure Mobile App SDK V2.0"이 포함됩니다. 이 경우 버전은 SDK 버전이 아니지만 해당 프로그램이 적용되는 Visual Studio 버전을 나타냅니다.

이 문서는 Azure SDK에 포함된 모든 기존 버전이 포함된 모든 프로그램을 나열하지는 않으며, 기존 SDK 버전에서 제거할 수 있는 다른 프로그램들이 있습니다. 기존 SDK 버전에는 이후 버전에서 생략된 프로그램이 포함된 경우도 있기 때문입니다. 예를 들어, 버전 2.5는 "Azure Resource Manager Tools for Visual Studio"를 설치하지만 더 이상 **프로그램 및 기능** 내에서 별도의 프로그램으로 표시되지 않기 때문에 이 문서의 목록에는 없습니다. 이 문서는 Azure SDK for .NET 버전 2.6에 포함된 프로그램만 나열합니다.

> **참고:** SDK에 포함된 일부 프로그램은 다른 컨텍스트에서 별도로 설치될 수도 있으며, 전체 SDK가 필요 없어도 이러한 프로그램이 필요할 수도 있습니다. 기존 SDK 버전에 의해 설치되었지만 이후 SDK 버전에서 생략된 프로그램도 마찬가지일 수 있습니다. 프로그램을 제거할 때는 아직 컴퓨터에서 필요한 항목을 제거하지 않도록 유의하십시오.



##<a id="versions"></a>버전

어떤 버전이 최신인지 확인하고 기존 버전을 다운로드하려면 [Azure SDK for .NET 버전 기록](/downloads/archive-net-downloads/) 페이지를 참조하십시오.

##<a id="resources"></a>리소스

Azure SDK for .NET 또는 클라이언트 라이브러리를 다운로드하려면 [Azure 다운로드 페이지](/downloads/)를 참조하세요.

클라이언트 라이브러리를 비롯한 Azure SDK for .NET 소스 코드는 [GitHub.com/Azure](https://github.com/azure/)를 참조하세요.

Azure 클라이언트 라이브러리 참조 설명서는 [Azure .NET 참조](/documentation/api/)를 참조하세요.

<!---HONumber=62-->