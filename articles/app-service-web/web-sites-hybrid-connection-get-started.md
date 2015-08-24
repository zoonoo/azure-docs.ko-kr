<properties 
	pageTitle="Azure 앱 서비스에서 하이브리드 연결을 사용하여 온-프레미스 리소스에 액세스" 
	description="Azure 앱 서비스의 웹 앱과 정적 TCP 포트를 사용하는 온-프레미스 리소스 간의 연결 만들기" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="cephalin"/>

#Azure 앱 서비스에서 하이브리드 연결을 사용하여 온-프레미스 리소스에 액세스

Azure 앱 서비스의 웹 앱을 정적 TCP 포트를 사용하는 온-프레미스 리소스(예: SQL Server, MySQL, HTTP 웹 API, 모바일 서비스 및 대부분의 사용자 지정 웹 서비스)에 연결할 수 있습니다. 이 문서에서는 앱 서비스의 웹 앱과 온-프레미스 SQL Server 데이터베이스 간 하이브리드 연결을 만드는 방법을 보여 줍니다.

> [AZURE.NOTE]하이브리드 연결 기능의 웹 앱 부분은 [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에서만 사용할 수 있습니다. BizTalk 서비스에서 연결을 만들려면 [하이브리드 연결](http://go.microsoft.com/fwlink/p/?LinkID=397274)(영문)을 참조하세요.

## 필수 조건
- Azure 구독. 무료 구독에 대해서는 [Azure 1개월 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 

- 하이브리드 연결을 사용하여 온-프레미스 SQL Server 또는 SQL Server Express 데이터베이스를 사용하려면 TCP/IP를 고정 포트에서 사용할 수 있어야 합니다. SQL Server의 기본 인스턴스는 고정 포트 1433을 사용하므로 권장됩니다. 하이브리드 연결에 사용하기 위해 SQL Server Express를 설치 및 구성하는 방법에 대한 자세한 내용은 [하이브리드 연결을 사용하여 Azure 웹 사이트에서 온-프레미스 SQL Server에 연결](http://go.microsoft.com/fwlink/?LinkID=397979)을 참조하세요.

- 이 문서의 뒷부분에서 설명하는 온-프레미스 하이브리드 연결 관리자 에이전트를 설치하는 컴퓨터는 다음 조건을 충족해야 합니다.

	- 포트 5671을 통해 Azure에 연결할 수 있어야 합니다.
	- 온-프레미스 리소스의 *호스트이름*:*포트번호*에 연결할 수 있어야 합니다. 

> [AZURE.NOTE]이 자습서의 단계에서는 온-프레미스 하이브리드 연결 에이전트를 호스트하는 컴퓨터에서 브라우저를 사용하고 있다고 가정합니다.


## Azure 포털에서 웹 앱 만들기 ##

> [AZURE.NOTE]이 자습서에 사용하려는 Azure 포털에서 웹 앱을 이미 만들었으면 앞의 [하이브리드 연결 및 BizTalk 서비스 만들기](#CreateHC)로 건너뛰어 거기에서 시작합니다.

1. [Azure 포털](https://portal.azure.com)의 왼쪽 아래 모서리에서 **새로 만들기** > **웹 + 모바일** > **웹 사이트**를 클릭합니다.
	
	![새 단추][New]
	
	![새 웹 앱][NewWebsite]
	
2. **웹 앱** 블레이드에서 URL > **만들기**를 지정합니다.
	
	![웹 사이트 이름][WebsiteCreationBlade]
	
3. 잠시 후 웹 앱이 생성되고 웹 앱 블레이드가 나타납니다. 블레이드는 세로로 스크롤 가능한 대시보드이며 여기서 사이트를 관리할 수 있습니다.
	
	![실행 중인 웹 사이트][WebSiteRunningBlade]
	
4. 사이트가 라이브인지 확인하려면 **찾아보기** 아이콘을 클릭하여 기본 페이지를 표시합니다.
	
	![찾아보기를 클릭하여 웹 앱 표시][Browse]
	
	![기본 웹 앱 페이지][DefaultWebSitePage]
	
이제 웹 앱용 하이브리드 연결 및 BizTalk 서비스를 만듭니다.

<a name="CreateHC"></a>
## 하이브리드 연결 및 BizTalk 서비스 만들기 ##

1. 웹 앱에 대한 블레이드까지 아래로 스크롤하고 **하이브리드 연결**을 선택합니다.
	
	![하이브리드 연결][CreateHCHCIcon]
	
2. 하이브리드 연결 블레이드에서 **추가**를 클릭합니다.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. **하이브리드 연결 추가** 블레이드가 열립니다. 첫 번째 하이브리드 연결이므로, **새 하이브리드 연결** 옵션이 미리 선택되어 있으며 **하이브리드 연결 만들기** 블레이드가 자동으로 열립니다.
	
	![하이브리드 연결 만들기][TwinCreateHCBlades]
	
	**하이브리드 연결 블레이드 만들기**에서 **이름**에 연결 이름을 입력하고 **호스트 이름**에는 리소스를 호스트하는 온-프레미스 컴퓨터의 이름을 입력합니다. **포트**에는 온-프레미스 리소스가 사용하는 포트 번호를 입력합니다(SQL Server 기본 인스턴스의 경우 1433). **Biz Talk 서비스**를 클릭합니다.


4. **BizTalk 서비스 만들기** 블레이드가 열립니다. BizTalk 서비스의 이름을 입력한 다음 **확인**을 클릭합니다.
	
	![BizTalk 서비스 만들기][CreateHCCreateBTS]
	
	**BizTalk 서비스 만들기** 블레이드가 닫히고 **하이브리드 연결 만들기** 블레이드로 돌아갑니다.
	
5. 하이브리드 연결 만들기 블레이드에서 **확인**을 클릭합니다.
	
	![확인 클릭][CreateBTScomplete]
	
6. 프로세스가 완료되면 포털의 알림 영역에서 연결이 설정되었다는 메시지를 표시합니다.
	<!-- TODO

Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the old portal
(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
Create hybrid conn step, you get the following error
Failed to create hybrid connection RelecIoudHC. The 
resource type could not be found in the namespace 
'Microsoft.BizTaIkServices for api version 2014-06-01'.

The error indicates it couldn't find the type, not the instance.
![Success notification][CreateHCSuccessNotification]
-->
7. 웹 앱 블레이드에서 이제 **하이브리드 연결** 아이콘이 1개의 하이브리드 연결이 설정되었음을 보여 줍니다.
	
	![1개의 하이브리드 연결 생성됨][CreateHCOneConnectionCreated]
	
여기서 클라우드 하이브리드 연결 인프라의 중요한 부분을 완료했습니다. 이제 해당하는 온-프레미스 부분을 만듭니다.

<a name="InstallHCM"></a>
## 온-프레미스 하이브리드 연결 관리자를 설치하여 연결 완료 ##

1. 웹 앱 블레이드에서 하이브리드 연결 아이콘을 클릭합니다. 
	
	![하이브리드 연결 아이콘][HCIcon]
	
2. **하이브리드 연결** 블레이드에서 최근에 추가된 끝점의 **상태** 열이 **연결되지 않음**을 표시합니다. 구성할 연결을 클릭합니다.
	
	![연결되지 않음][NotConnected]
	
	하이브리드 연결 블레이드가 열립니다.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. 블레이드에서 **수신기 설정**을 클릭합니다.
	
	![수신기 설정 클릭][ClickListenerSetup]
	
4. **하이브리드 연결 속성** 블레이드가 열립니다. **온-프레미스 하이브리드 연결 관리자**에서 **설치하려면 여기를 클릭하세요.**를 선택합니다.
	
	![설치하려면 여기를 클릭][ClickToInstallHCM]
	
5. 응용 프로그램 실행 보안 경고 대화 상자에서 **실행**을 선택하여 계속합니다.
	
	![실행을 선택하여 계속합니다.][ApplicationRunWarning]
	
6.	**사용자 계정 컨트롤** 대화 상자에서 **예**를 선택합니다.
	
	![예 선택][UAC]
	
7. 하이브리드 연결 관리자가 자동으로 다운로드되어 설치됩니다.
	
	![설치][HCMInstalling]
	
8. 설치가 완료되면 **닫기**를 클릭합니다.
	
	![닫기 클릭][HCMInstallComplete]
	
	**하이브리드 연결** 블레이드에서 이제 **상태** 열은 **연결됨**을 표시합니다.
	
	![연결됨 상태][HCStatusConnected]

하이브리드 연결 인프라를 완성했으므로 이 인프라를 사용하는 하이브리드 응용 프로그램을 만듭니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

<a name="NextSteps"></a>
## 다음 단계 ##

- 하이브리드 연결을 사용하는 ASP.NET 웹 응용 프로그램 만들기에 대한 자세한 내용은 [하이브리드 연결을 사용하여 Azure 웹 사이트에서 온-프레미스 SQL Server에 연결](http://go.microsoft.com/fwlink/?LinkID=397979)(영문)을 참조하세요.

- 모바일 서비스에 대한 하이브리드 연결 사용에 대한 자세한 내용은 [하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)(영문)을 참조하세요.

### 추가 리소스

[하이브리드 연결 개요](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist가 소개하는 하이브리드 연결(채널 9 비디오)(영문)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[하이브리드 연결 웹 사이트](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk 서비스: 대시보드, 모니터, 확장, 구성 및 하이브리드 연결 탭](../biztalk-dashboard-monitor-scale-tabs/)

[원활한 응용 프로그램 이식성으로 실시간 하이브리드 연결 클라우드 구축(채널 9 비디오)(영문)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결(채널 9 비디오)(영문)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [Azure 앱 서비스에서 웹 사이트 및 웹 앱에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 

<!---HONumber=August15_HO7-->