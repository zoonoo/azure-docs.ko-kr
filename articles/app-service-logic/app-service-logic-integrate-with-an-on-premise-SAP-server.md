<properties 
	pageTitle="Microsoft Azure 앱 서비스에서 온-프레미스 SAP 서버와 통합"
	description="온-프레미스 SAP 서버와 통합하는 방법을 알아봅니다."
	authors="rajeshramabathiran" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="harish"/>


# 온-프레미스 SAP 서버와 통합
SAP 커넥터를 사용하여 Azure 앱 서비스 웹, 모바일 및 논리 앱을 기존 SAP 서버에 연결할 수 있습니다. SAP 서버로 IDOC를 보낼 수 있을 뿐만 아니라 RFC, BAPI, tRFC도 호출할 수 있습니다.
	
SAP 서버는 방화벽 온-프레미스 뒤에 있을 수도 있습니다. 온-프레미스 서버의 경우 아래 그림과 같이 하이브리드 수신기를 통해 연결이 설정됩니다.

![하이브리드 연결 흐름][1]

클라우드의 SAP 커넥터가 방화벽 뒤에 있는 SAP 서버에 직접 연결할 수는 없습니다. 하이브리드 수신기가 커넥터를 통해 SAP 서버에 대한 연결을 안전하게 설정하도록 허용하는 릴레이 끝점을 호스트하여 빈 간격을 연결합니다.


## SAP와 통합하는 여러 가지 방법
다음과 같은 작업이 지원됩니다.

- RFC 호출
- TRFC 호출
- BAPI 호출
- IDoc 보내기

## 필수 조건
하이브리드 수신기가 설치되어 실행 중인 클라이언트 컴퓨터에 SAP용 클라이언트 라이브러리가 필요합니다. 자세한 내용은 [여기][9]의 **SAP 어댑터용** 섹션을 참조하세요.


## 새 SAP 어댑터 만들기
1. Microsoft Azure 관리 포털에 로그인합니다. 
2. **새로 만들기**를 선택합니다.
3. 만들기 블레이드에서 **계산** > **Azure 마켓플레이스**를 선택합니다.
4. 마켓플레이스 블레이드에서 **API 앱**을 선택하고 검색 창에서 SAP를 검색합니다.
	
	![SAP 커넥터 API 앱][2]	
5. Microsoft에서 게시한 **SAP 커넥터**를 선택합니다.
6. SAP 커넥터 블레이드에서 **만들기**를 선택합니다.
7. 열리는 새 블레이드에서 다음을 입력합니다.
	1. **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	2. **구독** - 이 커넥터를 만들 구독을 선택합니다.
	3. **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	4. **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
	5. **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	6. **이름** - SAP 커넥터의 이름을 입력합니다.
	7. **패키지 설정**
		- **서버 이름** - SAP 서버 이름을 입력합니다. 예: "SAPserver" 또는 "SAPserver.mydomain.com"
		- **사용자 이름** - SAP 서버에 연결하는 데 사용할 유효한 사용자 이름을 입력합니다.
		- **암호** - SAP 서버에 연결하는 데 사용할 유효한 암호를 입력합니다.
		- **시스템 번호** - SAP 응용 프로그램 서버의 시스템 번호를 입력합니다.
		- **언어** - "EN"과 같은 로그온 언어를 입력합니다. 입력된 값이 없는 경우 "EN"인 것으로 간주됩니다.
		- **온-프레미스** - SAP 서버가 방화벽 뒤의 온-프레미스인지 여부를 입력합니다. TRUE로 설정한 경우 SAP 서버에 액세스할 수 있는 서버에 수신기 에이전트를 설치해야 합니다. API 앱 통계 페이지로 이동하고 '하이브리드 연결’을 선택하여 에이전트를 설치할 수 있습니다.
		- **서비스 버스 연결 문자열** - SAP 서버가 온-프레미스인 경우 이 매개 변수를 입력합니다. 이 문자열은 유효한 서비스 버스 네임스페이스 연결 문자열이어야 합니다.
		- **RFC** - 커넥터에서 호출할 수 있는 SAP의 RFC를 입력합니다.
		- **TRFC** - 커넥터에서 호출할 수 있는 SAP의 TRFC를 입력합니다.
		- **BAPI** - 커넥터에서 호출할 수 있는 SAP의 BAPI를 입력합니다.
		- **IDOC** - 커넥터를 통해 보낼 수 있는 SAP의 IDOC를 입력합니다.
	8. 선택을 선택합니다. 몇 분 내에 SAP 커넥터가 만들어집니다.


## 하이브리드 수신기 설치
**찾아보기** > **API 앱** > *커넥터의 이름*을 통해 만든 SAP 커넥터로 이동합니다.

커넥터 블레이드에서 하이브리드 연결 상태가 보류 중인 것을 확인할 수 있습니다. 하이브리드 연결을 선택합니다. 하이브리드 연결 블레이드가 열립니다.

![하이브리드 연결 블레이드][3]

기본 게이트웨이 구성 문자열을 복사합니다. 나중에 하이브리드 수신기 설치의 일부로 사용할 수 있습니다.

**다운로드 및 구성** 링크를 선택하고 설치 관리자 한 번 클릭을 실행합니다.

![하이브리드 연결 한 번 클릭 설치 관리자][4]

**설치**를 선택한 다음 앞서 복사한 게이트웨이 구성 설정을 입력합니다.

![릴레이 수신 대기 연결 문자열][5]

**설치**를 선택하여 하이브리드 연결 관리자 설치를 완료합니다.

![하이브리드 연결 관리자 설치 진행 중][6]

![하이브리드 연결 관리자 설치 완료][7]

## 하이브리드 연결의 유효성 검사
**찾아보기** > **API 앱** > *커넥터의 이름*을 통해 만든 SAP 커넥터로 이동합니다.

커넥터 블레이드에서 하이브리드 연결 상태가 *연결됨*인 것을 확인할 수 있습니다.

![하이브리드 연결 상태 - 연결됨][8]


## 논리 앱에서 SAP 커넥터 사용
SAP 커넥터가 생성되면 논리 앱 워크플로에서 해당 커넥터를 사용할 수 있습니다.

**새로 만들기** > **논리 앱** > **만들기**를 통해 새 논리 앱을 만듭니다. 리소스 그룹을 비롯하여 논리 앱에 대한 메타데이터를 입력합니다.

**트리거 및 동작**을 선택합니다. 논리 앱 워크플로 디자이너가 열립니다.

오른쪽 창에서 SAP 커넥터를 선택하고 동작 탭에서 동작을 선택합니다.

> [AZURE.NOTE]동작 목록은 SAP 커넥터를 만들 때 입력한 구성을 기반으로 합니다.

선택한 동작에 대한 입력 및 출력 매개 변수가 표시됩니다. 동작에 대한 입력을 입력하고 다른 API 앱에서 현재 동작의 출력을 사용하여, 워크플로의 추가 의사 결정에 사용할 수 습니다.

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://download.microsoft.com/download/2/D/7/2D7CE8DF-A6C5-45F0-8319-14C3F1F9A0C7/InstallationGuide.htm



 

<!---HONumber=62-->