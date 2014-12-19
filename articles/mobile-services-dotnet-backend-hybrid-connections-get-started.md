<properties urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결 - Azure 모바일 서비스" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="mahender" />
  
# 하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결 

기업이 클라우드로 전환할 때 보통 기술적 이유, 규정 준수상의 이유 또는 보안상의 이유로 일부 자산을 온-프레미스로 남겨야 합니다. 모바일 서비스를 통해 이러한 자산 위에 클라우드 호스티드 모바일 계층을 손쉽게 만들고 하이브리드 연결을 사용하여 온-프레미스에 다시 안전하게 연결할 수 있습니다. 지원되는 자산에는 Microsoft SQL Server, MySQL, HTTP 웹 API, 대부분의 사용자 지정 웹 서비스를 비롯하여 정적 TCP 포트에서 실행되는 모든 리소스가 포함됩니다. 

이 자습서에서는 서비스와 함께 프로비전된 기본 Azure SQL 데이터베이스가 아닌 로컬 온-프레미스 SQL Server 데이터베이스를 사용하도록 .NET 백 엔드 모바일 서비스를 수정하는 방법을 알아봅니다. 하이브리드 연결에서 JavaScript 백 엔드 모바일 서비스를 지원하지만 이 항목에서는 .NET 백 엔드 모바일 서비스만 다룹니다.

이 항목에서는 다음 기본 단계를 안내합니다.

1. [필수 조건](#Prerequisites)
2. [SQL Server Express 설치, TCP/IP 사용 및 SQL Server 데이터베이스 온-프레미스 만들기](#InstallSQL)
3. [하이브리드 연결 만들기](#CreateHC)
4. [온-프레미스 하이브리드 연결 관리자를 설치하여 연결 완료](#InstallHCM)
5. [연결을 사용하도록 모바일 서비스 수정](#CreateService)

<a name="Prerequisites"></a>
##필수 조건##
이 자습서를 완료하려면 다음 제품이 필요합니다. 모두 무료 버전으로 사용 가능할 수 있으므로, Azure용 개발을 완전히 무료로 시작할 수 있습니다.

- **Visual Studio 2013** - Visual Studio 2013의 무료 평가판 버전을 다운로드하려면 [Visual Studio 다운로드](http://www.visualstudio.com/downloads/download-visual-studio-vs)를 참조하세요. 계속하기 전에 이 제품을 설치하세요.

- **SQL Server 2014 Express with Tools** - [Microsoft 웹 플랫폼 데이터베이스 페이지](http://www.microsoft.com/web/platform/database.aspx)에서 Microsoft SQL Server Express를 무료로 다운로드하세요. **Express**(LocalDB가 아닌) 버전을 선택하세요. **Express with Tools** 버전에는 이 자습서에서 사용할 SQL Server Management Studio가 포함되어 있습니다.

또한 하이브리드 연결을 사용하여 Azure에 연결할 온-프레미스 컴퓨터가 필요합니다. 이 컴퓨터는 다음과 같은 조건을 충족해야 합니다.

- 포트 5671을 통해 Azure에 연결할 수 있습니다.
- 연결할 온-프레미스 리소스의 *호스트이름*:*포트번호*를 연결할 수 있습니다. 리소스는 동일한 컴퓨터에 호스트할 수도 있고, 호스트하지 않을 수도 있습니다. 

<a name="InstallSQL"></a>
## SQL Server Express 설치, TCP/IP 사용 및 SQL Server 데이터베이스 온-프레미스 만들기

하이브리드 연결을 사용하여 온-프레미스 SQL Server 또는 SQL Server Express 데이터베이스를 사용하려면 TCP/IP를 고정 포트에서 사용할 수 있어야 합니다. SQL Server의 기본 인스턴스에서는 고정 포트 1433을 사용하는 반면 명명된 인스턴스에서는 이 포트를 사용하지 않습니다. 

위에서 설명한 조건에 맞게 SQL Server를 구성하는 방법에 대한 자세한 내용은 [SQL Server Express 설치, TCP/IP 사용 및 SQL Server 데이터베이스 온-프레미스 만들기](/ko-kr/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL)를 참조하세요. 위에서 설명한 조건을 충족하는 구성 및 환경으로 SQL Server를 이미 설치한 경우 건너뛰어서 [SQL Server 데이터베이스 온-프레미스](/ko-kr/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB)를 시작할 수 있습니다. 

이 자습서에서는 데이터베이스 이름이 **OnPremisesDB**이고, 이 데이터베이스는 포트 **1433**에서 실행하며 컴퓨터의 호스트 이름은 **onPremisesServer**라고 가정합니다.

<a name="CreateHC"></a>
## 하이브리드 연결 만들기
1. 온-프레미스 컴퓨터에서 [Azure 관리 포털](http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409)에 로그온합니다.

2. 탐색 창 아래에서 **+새로 만들기**를 선택한 다음 **앱 서비스**, **BizTalk Service** 및 **사용자 지정 만들기**를 차례로 선택합니다.

	![Create BizTalk Service][CreateBTS]

3. **BizTalk 서비스 이름**을 입력하고 **버전**을 선택합니다. 

	![Configure new BizTalk Service][ConfigureBTS]

	이 자습서에서는 **mobile1**을 사용합니다. 새로운 BizTalk 서비스의 고유한 이름을 지정해야 합니다.

4. BizTalk 서비스를 만든 후에는 **하이브리드 연결** 탭을 선택한 다음 **추가**를 클릭합니다.

	![Add Hybrid Connection][AddHC]

	이제 새로운 하이브리드 연결이 생성됩니다.

5. 하이브리드 연결에 대한 **이름** 및 **호스트 이름**을 제공하고 **포트**를 `1433`으로 설정합니다. 
  
	![Configure Hybrid Connection][ConfigureHC]

	호스트 이름은 온-프레미스 서버의 이름입니다. 이제 포트 1433에서 실행 중인 SQL Server에 액세스하는 하이브리드 연결이 구성됩니다.

6. 생성된 새로운 연결은 다음 표에 나타납니다. 
 
	![Hybrid Connection successfully created][HCCreated]
	
	새로운 연결의 상태는 **On-premises setup incomplete**을 표시합니다.

이제 온-프레미스 컴퓨터에 하이브리드 연결 관리자를 설치해야 합니다.

<a name="InstallHCM"></a>
## 온-프레미스 하이브리드 연결 관리자를 설치하여 연결 완료

하이브리드 연결 관리자를 통해 온-프레미스 컴퓨터에서 Azure에 연결하여 TCP 트래픽을 중계할 수 있습니다. Azure에서 액세스하려는 온-프레미스 컴퓨터에 이 소프트웨어를 설치해야 합니다.

1. 방금 만든 연결을 선택하고 아래쪽 막대에서 **온-프레미스 설치**를 클릭합니다.

	![On-Premises Setup][DownloadHCM]

4. **설치 및 구성**을 클릭합니다.

	![Install Hybrid Connection Manager][InstallHCM]

	이제 연결 관리자의 사용자 지정된 인스턴스가 설치됩니다. 이 연결 관리자는 방금 만든 하이브리드 연결과 함께 작동하도록 미리 구성되어 있습니다.

3. 연결 관리자의 나머지 설치 단계를 완료합니다.

	![Hybrid Connection Manager setup][HCMSetup]

	설치가 완료되면 하이브리드 연결 상태가 **1개의 인스턴스 연결됨**으로 변경됩니다. 브라우저를 새로 고쳐서 몇 분 정도 기다려야 합니다. 온-프레미스 설치가 이제 완료됩니다.

	![Hybrid Connection connected][HCConnected]

<a name="CreateService"></a>
## 연결을 사용하도록 모바일 서비스 수정
### 하이브리드 연결과 서비스 연계
1. 포털의 **모바일 서비스** 탭에서 기존 모바일 서비스를 선택하거나 새로운 서비스를 만듭니다. 

	>[WACOM.NOTE].NET 백 엔드를 사용하여 생성된 서비스를 선택하거나 새 .NET 백 엔드 모바일 서비스를 만들어야 합니다. 새 .NET 백 엔드 모바일 서비스를 만드는 방법에 대한 자세한 내용은 [모바일 서비스 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) 를 참조하세요.

2. 모바일 서비스의 **구성** 탭에서 **하이브리드 연결** 섹션을 찾아 **하이브리드 연결 추가**를 선택합니다.

	![Associate Hybrid Connection][AssociateHC]

3. BizTalk 서비스 탭에서 방금 만든 하이브리드 연결을 선택하고 **확인**을 누릅니다. 

	![Pick associated Hybrid Connection][PickHC]

이제 모바일 서비스는 새로운 하이브리드 연결에 연계됩니다.

### 온-프레미스 연결 문자열을 사용하도록 서비스 업데이트
마지막으로, 연결 문자열의 값을 온-프레미스 SQL Server에 저장하는 앱 설정을 만들어야 합니다. 그런 다음 새 연결 문자열을 사용하도록 모바일 서비스를 수정해야 합니다. 

1. **연결 문자열**의 **구성** 탭에서 `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`와 같은 값을 사용하여 `OnPremisesDatabase`라는 새 연결 문자열을 추가합니다.

	![Connection string for on-premises database][ConnectionString]

	온-프레미스 데이터베이스의 보안 암호로 `{password}`를 바꿉니다.

2. **저장**을 눌러 하이브리드 연결과 방금 만든 연결 문자열을 저장합니다.

3. Visual Studio 2013에서 .NET 기반 모바일 서비스를 정의하는 프로젝트를 엽니다. 

	.NET 백 엔드 프로젝트를 다운로드하는 방법에 대해서는 [모바일 서비스 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/)를 참조하세요.
 
4. 솔루션 탐색기에서 **Models** 폴더를 확장하여 *Context.cs*로 끝나는 데이터 모델 파일을 엽니다.

6. 다음 코드 조각과 유사하게 **DbContext** 인스턴스 생성자를 수정합니다.

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDatabase")
            {
            }
        
            // snipped
        }

	이제 서비스는 Azure에 정의된 새 하이브리드 연결 문자열을 사용합니다.

5. 변경 내용을 게시하고 모바일 서비스에 연결된 클라이언트 앱을 사용하여 데이터베이스 변경 내용을 생성하는 몇 가지 작업을 호출합니다.

6. SQL Server가 실행되는 온-프레미스 컴퓨터에서 SQL Management Studio를 연 다음 개체 탐색기에서 **OnPremisesDB** 데이터베이스와 **테이블**을 확장합니다. 

9. **hybridService1.TodoItems** 테이블을 마우스 오른쪽 단추로 클릭하고 **상위 1000개의 행 선택**을 선택하여 결과를 봅니다.

	![SQL Management Studio][SMS]

앱에서 생성된 변경 내용이 모바일 서비스를 통해 온-프레미스 데이터베이스에 푸시됩니다.

##참고 항목##
 
+ [하이브리드 연결 웹 사이트](http://azure.microsoft.com/ko-kr/services/biztalk-services/)
+ [하이브리드 연결 개요](http://go.microsoft.com/fwlink/p/?LinkID=397274)
+ [BizTalk 서비스: 대시보드, 모니터, 확장, 구성 및 하이브리드 연결 탭](http://azure.microsoft.com/ko-kr/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

<!-- IMAGES -->
 
[CreateBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
[ConfigureBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
[AddHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
[ConfigureHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
[HCCreated]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
[InstallHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
[HCMSetup]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
[HCConnected]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
[AssociateHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
[PickHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
[ConnectionString]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
[SMS]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
[DownloadHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
