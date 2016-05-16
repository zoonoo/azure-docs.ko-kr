<properties
	pageTitle="Log Analytics에 Operations Manager 연결 | Microsoft Azure"
	description="System Center Operations Manager의 기존 투자를 유지 관리하고 Log Analytics로 확장된 기능을 사용하려면 OMS 작업 영역으로 Operations Manager를 통합할 수 있습니다."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Log Analytics에 Operations Manager 연결

System Center Operations Manager의 기존 투자를 유지 관리하고 Log Analytics로 확장된 기능을 사용하려면 OMS 작업 영역으로 Operations Manager를 통합할 수 있습니다. 이렇게 하면 Operations Manager를 계속해서 사용하는 동안 OMS의 기회를 활용하여 다음 작업을 할 수 있습니다.

- Operations Manager를 사용하여 IT 서비스의 상태 모니터링 계속
- 인시던트 및 문제 관리를 지원하는 ITSM 솔루션과 통합 유지 관리
- 온-프레미스 및 Operations Manager로 모니터링하는 공용 클라우드 IaaS 가상 컴퓨터에 배포된 에이전트의 수명 주기 관리

System Center Operations Manager와 통합하면 Operations Manager에서 데이터 수집, 저장 및 분석에 OMS의 속도 및 효율성을 활용하여 서비스 작업 전략에 값이 추가됩니다. OMS를 통해 기존 문제 관리 프로세스를 지원하기 위해 오류 문제를 식별하고 재발을 표시하도록 상호 연결하고 작업할 수 있습니다. 의미 있는 방식으로 이 데이터를 노출하는 다양한 대시보드 및 보고 기능으로 성능, 이벤트 및 경고 데이터를 검사하는 검색 엔진의 유연성은 Operations Manager를 제공하는 OMS의 강점을 보여 줍니다.

Operations Manager 관리 그룹에 대한 에이전트 보고는 Log Analytics 데이터 원본 및 OMS 구독에서 활성화한 솔루션에 기반한 서버에서 데이터를 수집합니다. 활성화한 솔루션에 따라 이러한 솔루션의 데이터는 Operations Manager 관리 서버에서 OMS 웹 서비스로 직접 전송되거나 에이전트 관리 시스템에서 수집된 데이터의 양으로 인해 에이전트에서 OMS 웹 서비스로 직접 전송됩니다. 관리 서버는 OMS 데이터를 OMS 웹 서비스에 직접 전달하며 OperationsManager 또는 OperationsManagerDW 데이터베이스에 작성되지 않습니다. 관리 서버는 OMS 웹 서비스와의 연결이 끊어지더라도 통신이 OMS와 다시 설정될 때까지 데이터를 로컬로 캐시합니다. 관리 서버가 계획된 유지 관리 또는 계획되지 않은 중단으로 인해 오프라인 상태인 경우 관리 그룹의 다른 관리 서버에서 OMS와의 연결을 다시 시작합니다.

다음 다이어그램은 System Center Operations Manager와 OMS 관계를 보여 줍니다.

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## 시스템 요구 사항
시작하기 전에 다음 세부 정보를 검토하여 필요한 필수 구성 요소를 충족하는지 확인합니다.

- OMS는 Operations Manager 2012 SP1 UR6 이상 및 Operations Manager 2012 R2 UR2 이상만을 지원합니다. 프록시 지원은 Operations Manager 2012 SP1 UR7 및 Operations Manager 2012 R2 UR3에 추가되었습니다.
- OMS 구독입니다. 자세한 내용은 [Log Analytics 시작](log-analytics-get-started.md)을 검토합니다.

## OMS에 Operations Manager 연결
Operations Manager 관리 그룹을 구성하도록 다음과 같은 일련의 단계를 수행하여 OMS 작업 영역 중 하나에 연결합니다.

1. Operations Manager 콘솔에서 **관리** 작업 영역을 선택합니다.
2. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
3. **Operations Management Suite에 등록** 링크를 클릭합니다.
4. **Operations Management Suite 등록 마법사: 인증** 페이지에서 전자 메일 주소 또는 전화 번호와 OMS 구독과 연결된 관리자 계정의 암호를 입력하고 **로그인**을 클릭합니다.
5. 성공적으로 인증된 후에 **Operations Management Suite 등록 마법사: 작업 영역 선택** 페이지에 OMS 작업 영역을 선택하라는 메시지가 나타납니다. 둘 이상의 작업 영역이 있는 경우 드롭다운 목록에서 Operations Manager 관리 그룹으로 등록하려는 작업 영역을 선택한 후 **다음**을 클릭합니다.
    >[AZURE.NOTE] Operations Manager는 한 번에 하나의 OMS 작업 영역을 지원합니다. 이전 작업 영역으로 OMS에 등록된 연결 및 컴퓨터는 OMS에서 제거됩니다.
6. **Operations Manager Suite 등록 마법사: 요약** 페이지에서 설정을 확인하고 올바른 경우 **만들기**를 클릭합니다.
7. **Operations Management Suite 등록 마법사: 마침** 페이지에서 **닫기**를 클릭합니다.

### 에이전트 관리 컴퓨터 추가
OMS 작업 영역과 통합을 구성한 후 OMS와의 연결을 설정하고 에이전트 보고에서 관리 그룹으로 데이터가 수집되지 않습니다. 이는 Log Analytics에 대한 데이터를 수집할 특정 에이전트 관리 컴퓨터를 구성할 때까지 일어나지 않습니다. 컴퓨터 개체를 개별적으로 선택하거나 Windows 컴퓨터 개체를 포함하는 그룹을 선택할 수 있습니다. 논리 디스크 또는 SQL 데이터베이스와 같은 다른 클래스의 인스턴스를 포함하는 그룹을 선택할 수 없습니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
3. 창 오른쪽 작업 제목 아래의 **컴퓨터/그룹 추가** 링크를 클릭합니다.
4. **컴퓨터 검색** 대화 상자에서, Operations Manager에서 모니터링하는 그룹 또는 컴퓨터를 검색할 수 있습니다. OMS에 등록할 컴퓨터 또는 그룹을 선택하고 **추가**를 클릭한 다음 **확인**을 클릭합니다.

운영 콘솔의 **관리** 작업 영역에서 Operations Management Suite 아래의 관리되는 컴퓨터 노드에서 데이터를 수집하도록 구성된 컴퓨터 및 그룹을 볼 수 있습니다. 여기에서 필요에 따라 컴퓨터 및 그룹을 추가하거나 제거할 수 있습니다.

### 운영 콘솔에서 OMS 프록시 설정 구성
내부 프록시 서버가 관리 그룹 및 OMS 웹 서비스 사이에 있는 경우 다음 단계를 수행합니다. 이러한 설정은 관리 그룹에서 중앙 집중식으로 관리되며 OMS에 대한 데이터를 수집하는 범위에 포함된 에이전트 관리 시스템에 배포됩니다. 이는 특정 솔루션이 관리 서버를 우회하고 데이터를 OMS 웹 서비스에 직접 보낼 때 효과적입니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2. Operations Management Suite를 확장한 다음 **연결**을 클릭합니다.
3. OMS 연결 보기에서 **프록시 서버 구성**을 클릭합니다.
4. **Operations Management Suite 마법사: 프록시 서버** 페이지에서 **프록시 서버를 사용하여 Operations Management Suite에 액세스**를 선택하고 포트 번호와 함께 URL을 입력(예: http://corpproxy:80)한 다음 **마침**을 클릭합니다.

프록시 서버에 인증이 필요한 경우 다음 단계를 수행하여 관리 그룹에서 OMS에 보고하는 관리되는 컴퓨터에 전파해야 하는 자격 증명 및 설정을 구성합니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2. **RunAs 구성**에서 **프로필**을 선택합니다.
3. **프로필 프록시로 시스템 센터 관리자 실행** 프로필을 엽니다.
4. 실행 프로필 마법사에서 추가를 클릭하여 실행 계정을 사용합니다. 새 [실행 계정](https://technet.microsoft.com/library/hh321655.aspx)을 만들거나 기존 계정을 사용할 수 있습니다. 이 계정에는 프록시 서버를 통과할 수 있는 권한이 있어야 합니다.
5. 관리할 계정을 설정하려면 **선택한 클래스, 그룹 또는 개체**를 선택하고 **선택...**을 클릭한 다음 **그룹...**을 클릭하여 **그룹 검색** 상자를 엽니다.
6. **Microsoft System Center Advisor 모니터링 서버 그룹**을 검색한 다음 선택합니다. 그룹을 선택한 후 **확인**을 클릭하여 **그룹 검색** 상자를 닫습니다.
7.	**확인**을 클릭하여 **계정으로 실행 추가** 상자를 닫습니다.
8.	**저장**을 클릭하여 마법사를 완료하고 변경 내용을 저장합니다.

연결이 만들어지고 OMS에 데이터가 수집 및 보고될 에이전트를 구성한 후 다음 구성이 관리 그룹 순서에 관계 없이 적용됩니다.

- 실행 계정 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**가 만들어집니다. 실행 프로필 **Microsoft System Center Advisor Run As Profile Blob**과 연결되고 두 개의 클래스 **수집 서버** 및 **Operations Manager 관리 그룹**을 대상으로 합니다.
- 두 개의 커넥터가 생성됩니다. 첫 번째는 **Microsoft.SystemCenter.Advisor.DataConnector**로 이름이 지정되고 관리 그룹에 있는 모든 클래스의 인스턴스에서 생성된 모든 경고를 OMS Log Analytics로 전달하는 구독으로 자동으로 구성됩니다. 두 번째 커넥터는 **Advisor Connector**이며 OMS 웹 서비스와 통신하고 데이터를 공유합니다.
- 관리 그룹에서 데이터를 수집하도록 선택한 에이전트 및 그룹은 **Microsoft System Center Advisor 모니터링 서버 그룹**에 추가됩니다.

## 관리 팩 업데이트
구성이 완료되면 Operations Manager 관리 그룹에서 OMS 서비스와의 연결을 설정합니다. 관리 서버는 웹 서비스와 동기화하고 Operations Manager와 통합하도록 활성화한 솔루션에 대한 관리 팩의 형태로 업데이트된 구성 정보를 수신합니다. Operations Manager는 이러한 관리 팩에 대한 업데이트를 확인하고 사용할 수 있을 때 자동으로 다운로드 하고 가져옵니다. 특히 이 동작을 제어하는 두 개의 규칙이 있습니다.

- **Microsoft.SystemCenter.Advisor.MPUpdate** - 기본 OMS 관리 팩을 업데이트합니다. 기본적으로 12시간마다 실행됩니다.
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 작업 영역에서 활성화된 솔루션 관리 팩을 업데이트합니다. 기본적으로 5분마다 실행됩니다.

비활성화하여 자동 다운로드를 방지하거나 관리 서버에서 새 관리 팩을 사용할 수 있고 다운로드해야 하는 경우를 결정하도록 OMS와 동기화하는 빈도를 수정하기 위해 이러한 두 규칙을 재정의할 수 있습니다. [규칙 또는 모니터를 재정의하는 방법](https://technet.microsoft.com/library/hh212869.aspx) 단계를 따라 초의 값으로 **빈도** 매개 변수를 수정하여 동기화 일정을 변경하거나 **활성화** 매개 변수를 수정하여 규칙을 비활성화합니다. Operations Manager 관리 그룹 클래스의 모든 개체에 대한 재정의를 대상으로 합니다.

프로덕션 관리 그룹에서 관리 팩 릴리스 제어를 위해 기존 변경 제어 프로세스를 계속 따르려는 경우 규칙을 비활성화하고 업데이트가 허용될 때 특정 시간 동안 활성화할 수 있습니다. 사용자 환경에 개발 또는 QA 관리 그룹이 있고 인터넷에 연결되어 있는 경우 OMS 작업 영역으로 해당 관리 그룹을 구성하여 이 시나리오를 지원할 수 있습니다. 이렇게 하면 프로덕션 관리 그룹으로 릴리스하기 전에 OMS 관리 팩의 반복적인 릴리스를 검토 및 평가할 수 있습니다.

## OMS와 Operations Manager 통합 유효성 검사
OMS에서 Operations Manager 통합이 성공적인지 확인할 수 있는 몇 가지 방법이 있습니다.

### OMS 포털에서 통합을 확인하려면

1.	OMS 포털에서 **설정** 타일을 클릭합니다.
2.	상단 메뉴에서 **연결된 원본**을 클릭합니다.
3.	System Center Operations Manager 섹션 아래에서 상태 **1 MGMT 그룹 연결됨**이 표시되고 아래 테이블에 데이터를 마지막으로 받았을 때 에이전트 및 상태 수로 나열된 관리 그룹의 이름이 표시됩니다.

### 운영 콘솔에서 통합을 확인하려면

1.	Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2.	**관리 팩** 노드를 클릭하고 **찾기:** 텍스트 상자에서 **관리자** 또는 **인텔리전스**를 입력합니다.
3.	활성화한 솔루션에 따라 검색 결과에 나열된 해당 관리 팩을 볼 수 있습니다. 예를 들어 경고 관리 솔루션을 활성화한 경우 관리 팩 Microsoft System Center Advisor 경고 관리가 목록에 나타납니다.

## 다음 단계

- [솔루션 갤러리에서 Log Analytics 솔루션을 추가](log-analytics-add-solutions.md)하여 기능을 추가하고 데이터를 수집합니다.
- 조직에서 프록시 서버 또는 방화벽을 사용하는 경우 에이전트가 Log Analytics 서비스와 통신할 수 있도록 [Log Analytics에서 프록시 및 방화벽 설정을 구성](log-analytics-proxy-firewall.md)합니다.

<!---HONumber=AcomDC_0504_2016-->