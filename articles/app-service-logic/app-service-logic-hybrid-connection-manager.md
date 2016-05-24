<properties 
	pageTitle="하이브리드 연결 관리자 사용 | Microsoft Azure 앱 서비스" 
	description="하이브리드 연결 관리자를 설치 및 구성하고 Azure 앱 서비스에서 온-프레미스 커넥터에 연결" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="mandia"/>

# 하이브리드 연결 관리자를 사용하여 Azure 앱 서비스에서 온-프레미스 커넥터에 연결

>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

온-프레미스 시스템을 사용하기 위해 Azure 앱 서비스가 하이브리드 연결 관리자를 사용합니다. 일부 커넥터는 SQL Server, SAP, SharePoint 등의 온-프레미스 시스템에 연결할 수 있습니다.

하이브리드 연결 관리자(HCM)는 방화벽 뒤의 네트워크 내에서 IIS 서버에 설치된 원클릭 설치 관리자입니다. Azure 서비스 버스 릴레이를 사용하여 HCM이 Azure의 커넥터로 온-프레미스 시스템을 인증합니다.

> [AZURE.NOTE] 방화벽 뒤의 온-프레미스 리소스에 연결하는 경우에만 하이브리드 연결 관리자가 필요합니다. 온-프레미스 시스템에 연결하지 않는 경우 하이브리드 연결 관리자는 필요하지 않습니다.

시작하려면 다음이 필요합니다.

- Azure 서비스 버스 릴레이 네임스페이스 SAS 연결 문자열. 릴레이를 포함하는 계층을 확인하려면 [서비스 버스 가격](https://azure.microsoft.com/pricing/details/service-bus/)를 참조하세요.
- 사용자 이름 및 암호를 포함하는 온-프레미스 시스템 로그인 정보. 예를 들어 온-프레미스 SQL Server에 연결하는 경우 SQL Server 로그인 계정 및 암호가 필요합니다.
- 포트 번호와 서버 이름을 포함하는 온-프레미스 서버 정보. 예를 들어 온-프레미스 SQL Server에 연결하는 경우 SQL Server 이름 및 TCP 포트 번호가 필요합니다.

## 서비스 버스 연결 문자열 얻기

Azure 포털에서 서비스 버스 루트 SAS 연결 문자열을 복사합니다. 이 연결 문자열은 Azure 커넥터를 온-프레미스 시스템에 연결합니다.

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에서 서비스 버스 네임스페이스를 선택하고 **연결 정보**를 선택합니다.

	![][SB_ConnectInfo]

2. SAS 연결 문자열을 복사합니다.

	![][SB_SAS]

## 하이브리드 연결 관리자 설치

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 만든 커넥터를 선택합니다. 커넥터를 **찾아보기**를 선택하고 **API 앱**을 선택한 다음 커넥터 또는 API 앱을 선택합니다. <br/><br/> **하이브리드 연결**에서 설치는 **완료되지 않음**으로 표시됩니다. <br/> ![][2] 

2. **하이브리드 연결**을 선택합니다. 이전에 입력한 서비스 버스 연결 문자열이 나열됩니다.
3. **기본 구성 문자열**을 복사합니다.<br/> ![][PrimaryConfigString]

4. **온-프레미스 하이브리드 연결 관리자**에서 하이브리드 연결 관리자를 다운로드하거나 포털에서 직접 설치할 수 있습니다. <br/><br/> 포털에서 직접 설치하려면 온-프레미스 IIS 서버로 이동하고 포털을 찾아 **다운로드 및 구성**을 선택합니다. <br/><br/> 하이브리드 연결 관리자를 다운로드하려면 온-프레미스 IIS 서버로 이동하고 **ClickOnce 응용 프로그램**으로 이동합니다. http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application) 설치가 자동으로 시작되므로 실행할 수 있습니다.

5. **수신기 설치** 창에서 3단계에서 붙여넣은 **기본 구성 문자열**을 입력하고 **설치**를 선택합니다.

설치가 완료된 경우, 다음이 표시됩니다. <br/> ![][3]

이제 커넥터를 다시 찾아보면 하이브리드 연결 상태가 **연결됨**인 것을 확인할 수 있습니다. 커넥터를 닫고 다시 열 수도 있습니다. <br/> ![][4]

> [AZURE.NOTE] 보조 연결 문자열을 전환하려면 하이브리드 연결 설치를 다시 실행하고 **보조 구성 문자열**을 입력합니다.


## TCP 포트 및 보안

하이브리드 연결을 만들 때 웹 사이트는 로컬 온-프레미스 IIS 서버에 만들어집니다. IIS 서버는 DMZ에 있을 수 있습니다. IIS 서버에서 TCP 포트 요구 사항은 다음과 같습니다.

TCP 포트 | 이유
--- | ---
 | 들어오는 TCP 포트가 없어야 합니다.
9350 - 9354 | 이러한 포트는 데이터 전송에 사용됩니다. 서비스 버스 릴레이 관리자는 포트 9350를 조사하여 TCP 연결을 사용할 수 있는지 확인합니다. 사용 가능한 경우 포트 9352도 사용 가능한 것으로 가정합니다. 데이터 트래픽이 포트 9352를 통해 이동합니다. <br/><br/>이러한 포트에 아웃 바운드 연결을 허용합니다.
5671 | 데이터 트래픽에 포트 9352를 사용하는 경우 포트 5671을 제어 채널로 사용합니다. <br/><br/>이 포트에 아웃 바운드 연결을 허용합니다. 
80, 443 | 포트 9352 및 5671를 사용할 수 없는 경우 *다음으로* 포트 80 및 443가 데이터 전송 및 제어 채널로 사용되는 대체(fallback) 포트입니다.<br/><br/>이러한 포트에 아웃 바운드 연결을 허용합니다.
온-프레미스 시스템 포트 | 온-프레미스 시스템에서 시스템이 사용하는 포트를 엽니다. 예를 들어 SQL Server는 일반적으로 포트 1433을 사용합니다. 이 TCP 포트를 엽니다.

[서비스 버스를 사용하는 방화벽 내 호스팅](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## 문제 해결

![][HCMFlow]

### 온-프레미스 문제 해결

1. IIS 서버에서 IIS 웹 역할이 설치되고 모든 IIS 서비스가 시작되었는지 확인합니다.
2. IIS 서버에서 하이브리드 연결 관리자가 설치되고 실행 중인지 확인합니다.
 - IIS 관리자(inetmgr)에서 ***MicrosoftAzureBizTalkHybridListener*** 웹 사이트가 나열되고 실행 중이어야 합니다. 
 - 이 웹사이트는 *NetworkService* 기본 제공 로컬 사용자 계정으로 실행되는 ***HybridListenerAppPool***을 사용합니다. 이 AppPool도 시작해야 합니다.
3. IIS 서버에서 커넥터가 설치되고 실행 중인지 확인합니다. 
 - 앱 서비스 커넥터용으로 웹 사이트가 생성됩니다. 예를 들어 SQL 커넥터를 만든 경우 ***MicrosoftSqlConnector\_nnn*** 웹 사이트가 생성됩니다. IIS 관리자(inetmgr)에서 이 웹 사이트가 나열되고 시작되었는지 확인합니다. 
 - 이 웹 사이트는 ***HybridAppPoolnnn***라는 자체 IIS 응용 프로그램 풀을 사용합니다. 이 AppPool은 *NetworkService* 기본 제공 로컬 사용자 계정으로 실행됩니다. 이 웹 사이트와 AppPool을 모두 시작해야 합니다. 
 - 로컬 커넥터를 찾습니다. 예를 들어 커넥터 웹 사이트가 포트 6569를 사용하는 경우 http://localhost:6569로 이동합니다. 기본 문서가 구성되지 않았으므로 `HTTP Error 403.14 - Forbidden error`이 예상됩니다.
4. 방화벽에서 이 항목에 나열된 TCP 포트가 열려있는지 확인합니다.
5. 원본 또는 대상 시스템을 살펴봅니다.
 - 일부 온-프레미스 시스템에는 추가 종속 파일이 필요합니다. 예를 들어 온-프레미스 SAP에 연결하는 경우 일부 추가 SAP 파일이 IIS 서버에 설치되어야 합니다.
 - 로그인 계정을 사용하여 시스템 연결을 확인합니다. 예를 들어 SQL Server용 포트 1433처럼 시스템에서 사용하는 TCP 포트가 열려야 합니다. Azure 포털에 입력한 로그인 계정은 시스템에 액세스할 수 있어야 합니다.
6. IIS 서버에서 모든 오류에 대한 이벤트 로그를 확인합니다. 
7. 하이브리드 연결 관리자 정리 및 다시 설치 
 - IIS에서 커넥터 웹 사이트 및 해당 응용 프로그램 풀을 수동으로 삭제합니다. 
 - 하이브리드 연결 관리자를 다시 실행하고 커넥터에 대한 올바른 **기본 구성 문자열**을 입력했는지 확인합니다.



### Azure 클래식 포털에서

1. 서비스 버스 네임스페이스가 **활성** 상태인지 확인합니다.
2. 커넥터를 만들 때 서비스 버스 SAS 연결 문자열을 입력합니다. ACS 연결 문자열은 입력하지 마십시오.


## FAQ

**질문**: 두 하이브리드 연결 관리자가 있습니다. 차이점은 무엇입니까?

**대답**: 웹 앱(이전의 웹 사이트) 및 모바일 앱(이전의 모바일 서비스)을 온-프레미스에 연결할 때 주로 사용한 [하이브리드 연결](../biztalk-services/integration-hybrid-connection-overview.md) 기술이 있습니다. 이 하이브리드 연결 관리자는 자체 [설치 관리자](../biztalk-services/integration-hybrid-connection-create-manage.md)가 있으며 백그라운드에서 Azure BizTalk 서비스를 사용합니다. 이는 TCP 및 HTTP 프로토콜만 지원합니다.

Azure 앱 서비스 커넥터를 사용할 경우 하이브리드 연결 관리자도 있습니다. 이 하이브리드 연결 관리자는 백그라운드에서 Azure BizTalk 서비스를 사용하지 *않고* TCP 및 HTTP 프로토콜보다 더 많은 것을 지원합니다. [커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)을 참조하세요.

둘 다 Azure 서비스 버스를 사용하여 온-프레미스 시스템에 연결합니다.

**질문**: 사용자 지정 API 앱을 만들 때 앱 서비스 하이브리드 연결 관리자를 사용하여 온-프레미스에 연결할 수 있습니까?

**대답**: 일반적인 경우 연결할 수 없습니다. 기본 제공 커넥터를 사용하여 앱 서비스 하이브리드 연결 관리자를 구성하고 온-프레미스 시스템에 연결할 수 있습니다. 그런 다음 논리 앱을 사용하여 사용자 지정 API 앱으로 이 커넥터를 사용합니다. 현재 사용자 고유의 하이브리드 API 앱(SQL 커넥터 또는 파일 커넥터 같은)을 개발하거나 만들 수는 없습니다.

사용자 지정 API에서 TCP 또는 HTTP 포트를 사용하는 경우 [하이브리드 연결](../biztalk-services/integration-hybrid-connection-overview.md) 및 하이브리드 연결 관리자를 사용할 수 있습니다. 이 시나리오에서는 Azure BizTalk 서비스를 사용합니다. [웹 앱에서 온-프레미스 SQL Server에 연결](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)이 도움이 될 것입니다.


## 자세히 알아보기

[논리 앱 모니터링](app-service-logic-monitor-your-logic-apps.md)<br/> [서비스 버스 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=AcomDC_0511_2016-->