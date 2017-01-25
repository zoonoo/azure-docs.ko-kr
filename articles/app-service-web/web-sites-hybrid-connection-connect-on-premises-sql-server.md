---
title: "하이브리드 연결을 사용하여 Azure 앱 서비스의 웹 앱에서 온-프레미스 SQL Server에 연결"
description: "Microsoft Azure에서 웹 앱을 만들어 온-프레미스 SQL Server 데이터베이스에 연결"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: mollybos
ms.assetid: 2b4e0539-1a0b-4aa1-8a69-b4b053c3b2e5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7e3c1440cc2b669c574c2c0160a0d282b5f27bca


---
# <a name="connect-to-on-premises-sql-server-from-a-web-app-in-azure-app-service-using-hybrid-connections"></a>하이브리드 연결을 사용하여 Azure 앱 서비스의 웹 앱에서 온-프레미스 SQL Server에 연결
하이브리드 연결은 정적 TCP 포트를 사용하는 온-프레미스 리소스에 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹 앱을 연결할 수 있습니다. 지원되는 리소스로는 Microsoft SQL Server, MySQL, HTTP 웹 API, App Service, 대부분의 사용자 지정 웹 서비스가 있습니다.

이 자습서에서 [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에서 앱 서비스 웹앱을 만들고, 새 하이브리드 연결 기능을 사용하여 로컬 온-프레미스 SQL Server 데이터베이스에 웹앱을 연결하고, 하이브리드 연결을 사용하는 단순한 ASP.NET 응용 프로그램을 만들고, 앱 서비스 웹앱에 응용 프로그램을 배포하는 방법에 대해 알아봅니다. Azure의 완전한 웹 앱은 온-프레미스인 멤버 자격 데이터베이스에서 사용자 자격 증명을 저장합니다. 이 자습서는 이전에 Azure 또는 ASP.NET을 사용해 본 경험이 있다고 가정합니다.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](http://go.microsoft.com/fwlink/?LinkId=523751)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 하이브리드 연결 기능의 웹 앱 부분은 [Azure 포털](https://portal.azure.com)에서만 사용할 수 있습니다. BizTalk 서비스에서 연결을 만들려면 [하이브리드 연결](http://go.microsoft.com/fwlink/p/?LinkID=397274)(영문)을 참조하세요.  
> 
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 제품이 필요합니다. 모두 무료 버전으로 사용 가능할 수 있으므로, Azure용 개발을 완전히 무료로 시작할 수 있습니다.

* **Azure 구독** - 무료 구독에 대해서는 [Azure 무료 평가](/pricing/free-trial/)를 참조하세요.
* **Visual Studio 2013** - Visual Studio 2013의 무료 평가판 버전을 다운로드하려면 [Visual Studio 다운로드](http://www.visualstudio.com/downloads/download-visual-studio-vs)를 참조하세요. 계속하기 전에 이 제품을 설치하세요.
* **Microsoft .NET Framework 3.5 서비스 팩 1** - 운영 체제가 Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 또는 Windows Server 2008 R2인 경우 제어판 > 프로그램 및 기능 > Windows 기능 사용/사용 안 함에서 이 제품을 사용하도록 설정할 수 있습니다. 그렇지 않은 경우 [Microsoft 다운로드 센터](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22)에서 다운로드할 수 있습니다.
* **SQL Server 2014 Express with Tools** - [Microsoft 웹 플랫폼 데이터베이스 페이지](http://www.microsoft.com/web/platform/database.aspx)에서 Microsoft SQL Server Express를 무료로 다운로드하세요. **Express** (LocalDB가 아닌) 버전을 선택하세요. **Express with Tools** 버전에는 이 자습서에서 사용할 SQL Server Management Studio가 포함되어 있습니다.
* **SQL Server Management Studio Express** - 이 제품은 위에서 언급한 SQL Server 2014 Express with Tools 다운로드와 함께 포함되지만 별도로 설치해야 하는 경우 [SQL Server Express 다운로드 페이지](http://www.microsoft.com/web/platform/database.aspx)에서 이 제품을 다운로드하여 설치할 수 있습니다.

이 자습서에서는 사용자가 Azure 구독을 사용하며, Visual Studio 2013을 설치했고, .NET Framework 3.5을 설치했거나 사용하도록 설정했다고 가정합니다. 이 자습서에서는 Azure 하이브리드 연결 기능(정적 TCP 포트를 사용하는 기본 인스턴스)에서 적절히 작동하는 구성에 SQL Server 2014 Express를 설치하는 방법을 보여 줍니다. 자습서를 시작하기 전에 SQL Server를 설치하지 않은 경우 위에서 언급한 SQL Server 2014 Express with Tools를 다운로드하세요.

### <a name="notes"></a>참고 사항
하이브리드 연결을 사용하여 온-프레미스 SQL Server 또는 SQL Server Express 데이터베이스를 사용하려면 TCP/IP를 고정 포트에서 사용할 수 있어야 합니다. SQL Server의 기본 인스턴스에서는 고정 포트 1433을 사용하는 반면 명명된 인스턴스에서는 이 포트를 사용하지 않습니다.

온-프레미스 하이브리드 연결 관리자 에이전트를 설치하는 컴퓨터는 다음 조건을 충족해야 합니다.

* 다음 포트를 통해 Azure와의 아웃바운드 연결이 설정되어야 합니다.

| 포트 | 이유 |
| --- | --- |
| 80 |**필요하며** 선택적으로 데이터 연결에 필요합니다. |
| 443 |**선택사항** 입니다. 443에 대한 아웃바운드 연결을 사용할 수 없으면 TCP 포트 80이 사용됩니다. |
| 5671 및 9352 |**권장** 하지만 선택사항입니다. 이 모드를 사용하면 일반적으로 더 높은 처리량을 얻을 수 있습니다. 이러한 포트에 대한 아웃바운드 연결을 사용할 수 없으면 TCP 포트 443이 사용됩니다. |

* 온-프레미스 리소스의 *ostname*에서 다음을 수행합니다.*포트번호* 에 연결할 수 있어야 합니다.

이 자습서의 단계에서는 온-프레미스 하이브리드 연결 에이전트를 호스트하는 컴퓨터에서 브라우저를 사용하고 있다고 가정합니다.

위에서 설명한 조건을 충족하는 구성 및 환경으로 SQL Server를 이미 설치한 경우 건너뛰어서 [SQL Server 데이터베이스 온-프레미스](#CreateSQLDB)를 시작할 수 있습니다.

<a name="InstallSQL"></a>

## <a name="a-install-sql-server-express-enable-tcpip-and-create-a-sql-server-database-on-premises"></a>A. SQL Server Express 설치, TCP/IP 사용 및 SQL Server 데이터베이스 온-프레미스 만들기
이 섹션에서는 웹 응용 프로그램이 Azure 포털에서 작동하도록 SQL Server Express를 설치하고, TCP/IP를 사용하도록 설정하고, 데이터베이스를 만드는 방법을 보여 줍니다.

### <a name="install-sql-server-express"></a>SQL Server Express 설치
1. SQL Server Express를 설치하기 위해 다운로드한 **SQLEXPRWT_x64_ENU.exe** 또는 **SQLEXPR_x86_ENU.exe** 파일을 실행합니다. SQL Server 설치 센터 마법사가 나타납니다.
   
    ![SQL Server 설치][SQLServerInstall]
2. **새 SQL Server 독립 실행형 설치 또는 기존 설치에 기능 추가**을 선택합니다. **인스턴스 구성** 페이지가 나타날 때까지 지침을 따라, 기본 선택 사항 및 설정을 그대로 사용합니다.
3. **인스턴스 구성** 페이지에서 **기본 인스턴스**를 선택합니다.
   
    ![기본 인스턴스 선택][ChooseDefaultInstance]
   
    기본적으로, SQL Server의 기본 인스턴스는 하이브리드 연결 기능이 요구하는 고정 포트 1433을 통해 SQL Server 클라이언트의 요청을 수신 대기합니다. 명명된 인스턴스는 하이브리드 연결에서 지원하지 않는 동적 포트 및 UDP를 사용합니다.
4. **서버 구성** 페이지에서 기본값을 그대로 사용합니다.
5. **데이터베이스 엔진 구성** 페이지의 **인증 모드**에서 **혼합 모드(SQL Server 인증 및 Windows 인증)**를 선택하고 암호를 입력합니다.
   
    ![혼합 모드 선택][ChooseMixedMode]
   
    이 자습서에서는 SQL Server 인증을 사용합니다. 나중에 필요하므로, 입력하는 암호는 기억해 두어야 합니다.
6. 마법사의 나머지 단계를 진행하여 설치를 완료합니다.

### <a name="enable-tcpip"></a>TCP/IP 사용
TCP/IP를 사용하도록 설정하려면 SQL Server Express를 설치할 때 설치된 SQL Server 구성 관리자를 사용합니다. 계속하기 전에 [SQL Server에 대한 TCP/IP 네트워크 프로토콜 사용](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) 의 단계를 따르세요.

<a name="CreateSQLDB"></a>

### <a name="create-a-sql-server-database-on-premises"></a>SQL Server 데이터베이스 온-프레미스
Visual Studio 웹 응용 프로그램을 사용하려면 Azure에서 액세스할 수 있는 멤버 자격 데이터베이스가 필요합니다. 여기에는 SQL Server 또는 SQL Server Express 데이터베이스(기본적으로 MVC 템플릿에서 사용하는 LocalDB 데이터베이스 아님)가 필요하므로, 이제 멤버 자격 데이터베이스를 만듭니다.

1. SQL Server Management Studio에서 방금 설치한 SQL Server에 연결합니다. (**서버에 연결** 대화 상자가 자동으로 나타나지 않으면 왼쪽 창의 **개체 탐색기**로 이동하여 **연결**을 클릭한 후 **데이터베이스 엔진**을 클릭합니다.)  ![서버에 연결][SSMSConnectToServer]
   
    **서버 유형**으로 **데이터베이스 엔진**을 선택합니다. **서버 이름**으로 **localhost** 또는 사용 중인 컴퓨터의 이름을 사용할 수 있습니다. **SQL Server 인증**을 선택한 다음 앞서 만든 사용자 이름 및 암호로 로그인합니다.
2. SQL Server Management Studio를 사용하여 새 데이터베이스를 만들려면 개체 탐색기에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭한 다음 **새 데이터베이스**를 클릭합니다.
   
    ![새 데이터베이스 만들기][SSMScreateNewDB]
3. **새 데이터베이스** 대화 상자에 데이터베이스 이름으로 MembershipDB를 입력한 다음 **확인**을 클릭합니다.
   
    ![데이터베이스 이름 입력][SSMSprovideDBname]
   
    여기서는 데이터베이스를 변경하지 않습니다. 나중에 웹 응용 프로그램을 실행할 때 멤버 자격 정보가 자동으로 추가됩니다.
4. 개체 탐색기에서 **데이터베이스**를 확장하면 멤버 자격 데이터베이스가 생성됩니다.
   
    ![MembershipDB 생성됨][SSMSMembershipDBCreated]

<a name="CreateSite"></a>

## <a name="b-create-a-web-app-in-the-azure-portal"></a>B. Azure 포털에서 웹 앱 만들기
> [!NOTE]
> 이 자습서에서 사용하고자 하는 Azure 포털에서 웹앱을 이미 만들었다면, [하이브리드 연결 및 BizTalk 서비스 만들기](#CreateHC) 를 건너뛰고 계속 진행합니다.
> 
> 

1. [Azure Portal](https://portal.azure.com)에서 **새로 만들기** > **웹 + 모바일** > **웹앱**을 클릭합니다.
   
    ![새 단추][New]
2. 웹 앱을 구성하고 **만들기**를 클릭합니다.
   
    ![웹 사이트 이름][WebsiteCreationBlade]
3. 잠시 후 웹 앱이 생성되고 웹 앱 블레이드가 나타납니다. 블레이드는 세로로 스크롤 가능한 대시보드이며 여기서 웹 앱을 관리할 수 있습니다.
   
    ![실행 중인 웹 사이트][WebSiteRunningBlade]
   
    웹 앱이 라이브인지 확인하려면 **찾아보기** 아이콘을 클릭하여 기본 페이지를 표시합니다.

이제 웹 앱용 하이브리드 연결 및 BizTalk 서비스를 만듭니다.

<a name="CreateHC"></a>

## <a name="c-create-a-hybrid-connection-and-a-biztalk-service"></a>C. 하이브리드 연결 및 BizTalk 서비스 만들기
1. 포털로 돌아와서 설정으로 이동하고 **네트워킹** > **하이브리드 연결 끝점 구성**을 클릭합니다.
   
    ![하이브리드 연결][CreateHCHCIcon]
2. 하이브리드 연결 블레이드에서 **추가** > **새 하이브리드 연결**을 클릭합니다.
3. **하이브리드 연결 블레이드 만들기** 에서 다음을 수행합니다.
   
   * **이름**에서 연결 이름을 입력합니다.
   * **호스트 이름**에 SQL Server 호스트 컴퓨터의 이름을 입력합니다.
   * **포트**에, 1433(SQL Server의 기본 포트)을 입력합니다.
   * **BizTalk 서비스** > **새 BizTalk 서비스**를 클릭하고 BizTalk 서비스의 이름을 입력합니다.
     
     ![하이브리드 연결 만들기][TwinCreateHCBlades]
4. **확인** 을 두 번 클릭합니다.
   
    프로세스가 완료되면 **알림** 영역은 녹색 **성공**으로 깜박이며 **하이브리드 연결** 블레이드는 **연결 되지 않음** 상태와 함께 새 하이브리드 연결을 표시합니다 .
   
    ![1개의 하이브리드 연결 생성됨][CreateHCOneConnectionCreated]

여기서 클라우드 하이브리드 연결 인프라의 중요한 부분을 완료했습니다. 이제 해당하는 온-프레미스 부분을 만듭니다.

<a name="InstallHCM"></a>

## <a name="d-install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>D. 온-프레미스 하이브리드 연결 관리자를 설치하여 연결 완료
[!INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

하이브리드 연결 인프라를 완성했으므로 이 인프라를 사용하는 웹 응용 프로그램을 만듭니다.

<a name="CreateASPNET"></a>

## <a name="e-create-a-basic-aspnet-web-project-edit-the-database-connection-string-and-run-the-project-locally"></a>E. 기본 ASP.NET 웹 프로젝트 만들기, 데이터베이스 연결 문자열 편집 및 로컬로 프로젝트 실행
### <a name="create-a-basic-aspnet-project"></a>기본 ASP.NET 프로젝트 만들기
1. Visual Studio의 **파일** 메뉴에서 새 프로젝트를 만듭니다.
   
    ![새 Visual Studio 프로젝트][HCVSNewProject]
2. **새 프로젝트** 대화 상자의 **템플릿** 섹션에서 **웹**을 선택하고 **ASP.NET 웹 응용 프로그램**을 선택한 다음 **확인**을 클릭합니다.
   
    ![ASP.NET 웹 응용 프로그램 선택][HCVSChooseASPNET]
3. **새 ASP.NET 프로젝트** 대화 상자에서 **MVC**를 선택한 후 **확인**을 클릭합니다.
   
    ![MVC 선택][HCVSChooseMVC]
4. 프로젝트를 만들 때 응용 프로그램 추가 정보 페이지가 나타납니다. 아직 웹 프로젝트를 실행하지 마세요.
   
    ![추가 정보 페이지][HCVSReadmePage]

### <a name="edit-the-database-connection-string-for-the-application"></a>응용 프로그램의 데이터베이스 연결 문자열 편집
이 단계에서, 응용 프로그램에 로컬 SQL Server Express 데이터베이스를 찾을 수 있는 위치를 알려 주는 연결 문자열을 편집합니다. 연결 문자열은 응용 프로그램에 대한 구성 정보를 포함하는 응용 프로그램의 Web.config 파일에 있습니다.

> [!NOTE]
> 응용 프로그램이 Visual Studio의 기본 LocalDB에 있는 데이터베이스가 아닌 SQL Server Express에서 만든 데이터베이스를 사용하도록 하려면 프로젝트를 실행하기 전에 이 단계를 완료하는 것이 중요합니다.
> 
> 

1. 솔루션 탐색기에서 Web.config 파일을 두 번 클릭합니다.
   
    ![Web.config][HCVSChooseWebConfig]
2. 다음 예의 다음 구문과 같이 로컬 컴퓨터에서 SQL Server 데이터베이스를 가리키도록 **connectionStrings** 섹션을 편집합니다.
   
    ![연결 문자열][HCVSConnectionString]
   
    연결 문자열을 작성할 때 다음 사항을 기억하세요.
   
   * 기본 인스턴스(예: YourServer\SQLEXPRESS)가 아닌 명명된 인스턴스에 연결하는 경우 정적 포트를 사용하도록 SQL Server를 구성해야 합니다. 정적 포트를 구성하는 방법에 대한 자세한 내용은 [특정 포트에서 수신하도록 SQL Server를 구성하는 방법](http://support.microsoft.com/kb/823938)을 참조하세요. 기본적으로, 명명된 인스턴는 하이브리드 연결에서 지원되지 않는 UDP 및 동적 포트를 사용합니다.
   * 로컬 SQL Server가 TCP를 사용하도록 설정하고 올바른 포트를 사용하고 있는지 확인할 수 있도록 연결 문자열에서 포트(예와 같이 기본적으로는 1433)를 지정하는 것이 좋습니다.
   * 연결 문자열에 사용자 ID 및 암호를 지정하면서 SQL Server 인증을 사용하세요.
3. Visual Studio에서 **저장** 을 클릭하여 Web.config 파일을 저장합니다.

### <a name="run-the-project-locally-and-register-a-new-user"></a>로컬로 프로젝트 실행 및 새 사용자 등록
1. 이제, 디버그 아래에 있는 찾아보기 단추를 클릭하여 새 웹 프로젝트를 로컬로 실행합니다. 이 예에서는 Internet Explorer를 사용합니다.
   
    ![프로젝트 실행][HCVSRunProject]
2. 기본 웹 페이지의 오른쪽 위에서 **등록** 을 선택하여 새 계정을 등록합니다.
   
    ![새 계정 등록][HCVSRegisterLocally]
3. 사용자 이름 및 암호를 입력합니다.
   
    ![사용자 이름 및 암호를 입력합니다.][HCVSCreateNewAccount]
   
    그러면 로컬 SQL Server에 응용 프로그램의 멤버 자격 정보를 저장하는 데이터베이스가 자동으로 생성됩니다. 테이블(**dbo.AspNetUsers**) 중 하나는 방금 입력한 것과 같은 웹앱 사용자 자격 증명을 저장합니다. 이 테이블은 자습서의 뒷부분에 나옵니다.
4. 기본 웹 페이지의 브라우저 창을 닫습니다. 그러면 Visual Studio에서 응용 프로그램이 중지됩니다.

이제 Azure에 응용 프로그램을 게시하고 테스트하는 다음 단계를 수행할 수 있습니다.

<a name="PubNTest"></a>

## <a name="f-publish-the-web-application-to-azure-and-test-it"></a>F. 웹 응용 프로그램을 Azure에 게시하고 테스트
이제, 앱 서비스 웹 앱에 응용 프로그램을 게시한 다음 테스트하여 로컬 컴퓨터의 데이터베이스에 웹 앱을 연결하는 데 앞서 구성한 하이브리드 연결을 사용하는 방법을 살펴봅니다.

### <a name="publish-the-web-application"></a>웹 응용 프로그램 게시
1. Azure 포털에서 앱 서비스 웹앱에 대한 게시 프로필을 다운로드할 수 있습니다. 웹 앱의 블레이드에서 **게시 프로필 얻기**를 선택한 다음, 컴퓨터에 파일을 저장합니다.
   
    ![게시 프로필 다운로드][PortalDownloadPublishProfile]
   
    이제 이 파일을 Visual Studio 웹 응용 프로그램으로 가져옵니다.
2. Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
   
    ![게시 선택][HCVSRightClickProjectSelectPublish]
3. **웹 게시** 대화 상자의 **프로필** 탭에서 **가져오기**를 선택합니다.
   
    ![가져오기][HCVSPublishWebDialogImport]
4. 다운로드한 게시 프로필로 이동하여, 선택한 다음 **확인**을 클릭합니다.
   
    ![프로필로 이동][HCVSBrowseToImportPubProfile]
5. 게시 정보를 가져와 대화 상자의 **연결** 탭에 표시합니다.
   
    ![게시 클릭][HCVSClickPublish]
   
    **게시**를 클릭합니다.
   
    게시가 완료되면 브라우저가 시작되고 현재 Azure 클라우드에서 라이브라는 점을 제외하고는 이제 친숙한 ASP.NET 응용 프로그램이 표시됩니다!

이제 라이브 웹 응용 프로그램을 사용하여 작동 중인 하이브리드 연결을 알아봅니다.

### <a name="test-the-completed-web-application-on-azure"></a>Azure에서 완료된 웹 응용 프로그램 테스트
1. Azure 웹 페이지의 맨 위 오른쪽에서 **로그인**을 선택합니다.
   
    ![로그인 테스트][HCTestLogIn]
2. 이제 앱 서비스 웹 앱이 로컬 컴퓨터에 있는 웹 응용 프로그램의 멤버 자격 데이터베이스에 연결됩니다. 이를 확인하기 위해 앞서 로컬 데이터베이스에 입력한 자격 증명으로 로그인합니다.
   
    ![Hello 인사][HCTestHelloContoso]
3. 새 하이브리드 연결을 더 테스트하려면 Azure 웹 응용 프로그램을 로그오프하고 다른 사용자로 등록합니다. 새 사용자 이름 및 암호를 입력한 다음 **등록**을 클릭합니다.
   
    ![다른 사용자 테스트 등록][HCTestRegisterRelecloud]
4. 하이브리드 연결을 통해 새 사용자의 자격 증명이 로컬 데이터베이스에 저장되었는지 확인하려면 로컬 컴퓨터에 SQL Management Studio를 엽니다. 개체 탐색기에서 **MembershipDB** 데이터베이스를 확장한 다음 **테이블**을 확장합니다. **dbo.AspNetUsers** 멤버 자격 테이블을 마우스 오른쪽 단추로 클릭하고 **상위 1000개의 행 선택**을 선택하여 결과를 봅니다.
   
    ![결과 보기][HCTestSSMSTree]
5. 이제 로컬 멤버 자격 테이블이 로컬로 만든 계정과 Azure 클라우드에서 만든 계정을 모두 표시합니다. 클라우드에서 만든 계정이 Azure의 하이브리드 연결 기능을 통해 온-프레미스 데이터베이스에 저장됩니다.
   
    ![온-프레미스 데이터베이스에 등록된 사용자][HCTestShowMemberDb]

이제 Azure 클라우드의 웹 앱과 온-프레미스 SQL Server 간의 하이브리드 연결을 사용하는 ASP.NET 웹 응용 프로그램을 만들어 배포했습니다. 축하합니다.

## <a name="see-also"></a>참고 항목
[하이브리드 연결 개요](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist가 소개하는 하이브리드 연결(채널 9 비디오)(영문)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[하이브리드 연결 개요](/services/biztalk-services/)

[BizTalk 서비스: 대시보드, 모니터, 확장, 구성 및 하이브리드 연결 탭](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[원활한 응용 프로그램 이식성으로 실시간 하이브리드 연결 클라우드 구축(채널 9 비디오)(영문)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Azure App Service에서 하이브리드 연결을 사용하여 온-프레미스 리소스에 액세스](web-sites-hybrid-connection-get-started.md)

[ASP.NET ID 개요(영문)](http://www.asp.net/identity)

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png



<!--HONumber=Nov16_HO3-->


