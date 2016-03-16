<properties 
	pageTitle="하이브리드 연결 만들기 및 관리 | Microsoft Azure" 
	description="하이브리드 연결을 만들고, 연결을 관리하며, 하이브리드 연결 관리자를 설치하는 방법에 대해 알아봅니다. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="mandia"/>


# 하이브리드 연결 만들기 및 관리


## 단계의 개요
1. 개인 네트워크에 있는 온-프레미스 리소스의 호스트 이름 또는 IP 주소를 입력하여 하이브리드 연결을 만듭니다.
2. 하이브리드 연결에 Azure 웹앱 또는 Azure 모바일 앱을 연결합니다.
3. 온-프레미스 리소스에 하이브리드 연결 관리자를 설치하고 특정 하이브리드 연결에 연결합니다. Azure 포털은 한 번 클릭으로 설치하고 연결할 수 있도록 합니다.
4. 하이브리드 연결을 관리하고 연결 키를 관리합니다.

이 항목에서는 이러한 단계를 나열합니다.


## <a name="CreateHybridConnection"></a>하이브리드 연결 만들기

하이브리드 연결은 웹앱**또는** BizTalk 서비스를 사용하여 Azure 포털에서 만들 수 있습니다.

**웹앱을 사용하여 하이브리드 연결을 만들려면** [Azure 웹앱을 온-프레미스 리소스에 연결](../app-service-web/web-sites-hybrid-connection-get-started.md)을 참조하세요.

**BizTalk 서비스에서 하이브리드 연결을 만들려면**

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **BizTalk 서비스**를 선택하고 BizTalk 서비스를 선택합니다. 

	기존 BizTalk 서비스가 없는 경우 [BizTalk 서비스를 만들 수](biztalk-provision-services.md) 있습니다.
3. **하이브리드 연결** 탭을 선택합니다. ![하이브리드 연결 탭][HybridConnectionTab]

4. **하이브리드 연결 만들기**를 선택하거나 작업 표시줄의 **추가** 단추를 선택합니다. 다음을 입력합니다.

	속성 | 설명
--- | ---
이름 | 하이브리드 연결 이름은 고유해야 하며 BizTalk 서비스와 같은 이름일 수 없습니다. 어떤 이름을 입력해도 괜찮지만 용도에 맞는 구체적인 이름을 입력하는 것이 좋습니다. 예:<br/><br/>급여*SQLServer*<br/>SupplyList*SharepointServer*<br/>고객*OracleServer*
호스트 이름 | 온-프레미스 리소스의 정규화된 호스트 이름, 호스트 이름만 또는 IPv4 주소를 입력합니다. 예는 다음을 포함합니다.<br/><br/>mySQLServer<br/>* mySQLServer *.*도메인*. corp.*yourCompany*.com<br/>* myHTTPSharePointServer *<br/>* myHTTPSharePointServer *.*yourCompany*.com<br/>10.100.10.10
포트 | 온-프레미스 리소스의 포트 번호를 입력합니다. 예를들어, 웹앱을 사용하는 경우 포트 80 또는 443을 입력합니다. SQL Server를 사용하는 경우 포트 1433을 입력합니다.

5. 설정을 완료하려면 확인 표시를 선택합니다.

#### 추가 항목

- 하이브리드 연결을 여러 개 만들 수 있습니다. 허용되는 연결 수는 [BizTalk 서비스: Editions 차트](biztalk-editions-feature-chart.md)를 참조하세요. 
- 각 하이브리드 연결은 송신하는 응용 프로그램 키와 수신하는 온-프레미스 키, 연결 문자열 쌍으로 생성됩니다. 각 쌍에는 기본 및 보조 키가 있습니다. 


## <a name="LinkWebSite"></a>Azure 웹앱 또는 Azure 모바일 앱 연결

Azure 웹앱을 기존 하이브리드 연결에 연결하려면 하이브리드 연결 블레이드에서 **기존 하이브리드 연결 사용**을 선택합니다. [Azure 웹앱을 온-프레미스 리소스](../app-service-web/web-sites-hybrid-connection-get-started.md)에 연결을 참조하세요.

Azure 모바일 앱을 기존 하이브리드 연결에 연결하려면 모바일 서비스를 변경하거나 만들 때 **하이브리드 연결 추가**를 선택합니다. [Azure 모바일 서비스 및 하이브리드 연결](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md)(영문)을 참조하세요.


## <a name="InstallHCM"></a>온-프레미스에 하이브리드 연결 관리자 설치

하이브리드 연결이 만들어진 후에는 온-프레미스 리소스에 하이브리드 연결 관리자를 설치합니다. 이 관리자는 Azure 웹앱이나 BizTalk 서비스에서 다운로드할 수 있습니다. BizTalk 서비스 단계는 다음과 같습니다.

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **BizTalk 서비스**를 선택하고 BizTalk 서비스를 선택합니다. 
3. **하이브리드 연결** 탭을 선택합니다. ![하이브리드 연결 탭][HybridConnectionTab]
4. 작업 표시줄에서 **온-프레미스 설치**를 선택합니다. ![온-프레미스 설치][HCOnPremSetup]
5. **설치 및 구성**을 선택하여 온-프레미스 시스템에서 하이브리드 연결 관리자를 실행하거나 다운로드합니다. 
6. 설치를 시작하려면 확인 표시를 선택합니다. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### 추가 항목
- 하이브리드 연결은 다음 운영 체제에 설치된 온-프레미스 리소스를 지원합니다.

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- 하이브리드 연결 관리자를 설치한 후에 다음이 진행됩니다.

	- Azure에 호스트된 하이브리드 연결은 기본 응용 프로그램 연결 문자열을 사용하도록 자동으로 구성됩니다. 
	- 온-프레미스 리소스는 기본 온-프레미스 연결 문자열을 사용하도록 자동으로 구성됩니다.

- 하이브리드 연결 관리자는 권한 부여를 위해 유효한 온-프레미스 연결 문자열을 사용해야 합니다. Azure 웹앱 또는 모바일 앱은 권한 부여를 위해 유효한 응용 프로그램 연결 문자열을 사용해야 합니다.
- 다른 서버에 하이브리드 연결 관리자의 다른 인스턴스를 설치하여 하이브리드 연결을 확장할 수 있습니다. 첫 번째 온-프레미스 수신기와 동일한 주소를 사용하도록 온-프레미스 수신기를 구성합니다. 이 상황에서 트래픽이 활성 온-프레미스 수신기 사이에서 임의로 분산됩니다(라운드 로빈). 


## <a name="ManageHybridConnection"></a>하이브리드 연결 관리
하이브리드 연결을 관리하기 위해 다음을 할 수 있습니다.

- Azure 포털을 사용하고 BizTalk 서비스로 이동합니다. 
- [REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)를 사용합니다.

#### 하이브리드 연결 문자열 복사/다시 생성

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **BizTalk 서비스**를 선택하고 BizTalk 서비스를 선택합니다. 
3. **하이브리드 연결** 탭을 선택합니다. ![하이브리드 연결 탭][HybridConnectionTab]
4. 하이브리드 연결을 선택합니다. 작업 표시줄에서 **연결 관리**를 선택합니다. ![옵션 관리][HCManageConnection]

	**연결 관리**에는 응용 프로그램 및 온-프레미스 연결 문자열이 나열됩니다. 연결 문자열을 복사하거나 연결 문자열에 사용된 액세스 키를 다시 생성할 수 있습니다.

	**다시 생성을 선택하면** 연결 문자열에 사용된 공유 선택키가 변경됩니다. 다음을 수행합니다.
	- Azure 클래식 포털의 Azure 응용 프로그램에서 **키 동기화**를 선택합니다.
	- **온-프레미스 설치**를 다시 실행합니다. 온-프레미스 설치를 다시 실행하면 온-프레미스 리소스가 업데이트된 기본 연결 문자열을 사용하도록 자동으로 구성됩니다.


#### 그룹 정책을 사용하여 하이브리드 연결에 사용되는 온-프레미스 리소스를 제어합니다.

1. [하이브리드 연결 관리자 관리 템플릿](http://www.microsoft.com/download/details.aspx?id=42963)을 다운로드합니다.
2. 파일의 압축을 풉니다.
3. 그룹 정책을 수정하는 컴퓨터에서 다음을 수행합니다.  

	- .ADMX 파일을 *%WINROOT%\\PolicyDefinitions* 폴더에 복사합니다.
	- .ADML 파일을 *%WINROOT%\\PolicyDefinitions\\ko-KR* 폴더에 복사합니다.

복사되면 그룹 정책 편집기를 사용하여 정책을 변경할 수 있습니다.




## 다음

[Azure 웹앱을 온-프레미스 리소스에 연결](../app-service-web/web-sites-hybrid-connection-get-started.md) [Azure 웹앱에서 온-프레미스 SQL Server에 연결](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) [Azure 모바일 서비스 및 하이브리드 연결](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md) [하이브리드 연결 개요](integration-hybrid-connection-overview.md)


## 참고 항목

[Microsoft Azure에서 BizTalk 서비스 관리를 위한 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx) [BizTalk 서비스: Editions 차트](biztalk-editions-feature-chart.md) [Azure 클래식 포털을 사용하여 BizTalk 서비스 만들기](biztalk-provision-services.md) [BizTalk 서비스: 대시보드, 모니터 및 배율 탭](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=AcomDC_0302_2016-->