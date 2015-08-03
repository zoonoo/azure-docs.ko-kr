<properties
	pageTitle="Operational Insights에 컴퓨터를 직접 연결"
	description="등록하려는 각 컴퓨터에 Operational Insights 에이전트를 설치하여 컴퓨터를 Operational Insights에 직접 연결할 수 있습니다."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015" 
	ms.author="banders"/>
# Operational Insights에 컴퓨터를 직접 연결

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

등록하려는 각 컴퓨터에 Operational Insights 에이전트를 설치하여 컴퓨터를 Operational Insights에 직접 연결할 수 있습니다.

> [AZURE.TIP]Azure에서 실행 중인 가상 컴퓨터의 경우 [Microsoft Azure의 서버에서 데이터 분석](operational-insights-analyze-data-azure.md)의 단계를 수행하여 에이전트를 설치합니다.

## 에이전트 다운로드 및 설치
다음 절차를 사용하여 Operational Insights 에이전트를 다운로드하고 설치합니다.

### 에이전트 설치 파일을 다운로드하려면
1. Operations Insights 포털의 **개요** 페이지에서 **설정** 타일을 클릭합니다. 위쪽에서 **연결 원본** 탭을 클릭합니다.![설정 페이지](./media/operational-insights-direct-agent/direct-agent01.png)
2. **서버 직접 연결(64 비트)** 아래에서 에이전트 다운로드 단추를 클릭하여 설치 파일을 다운로드합니다.
3. **작업 영역 ID**오른쪽에서 복사 아이콘을 클릭하고 ID를 메모장에 붙여넣습니다.
4. **기본 키** 오른쪽에서 복사 아이콘을 클릭하고 ID를 메모장에 붙여넣습니다.![설정 페이지](./media/operational-insights-direct-agent/direct-agent02.png)

### 설치 프로그램을 사용하여 에이전트를 설치하려면
1. 설치 프로그램을 실행하여 관리하려는 컴퓨터에 에이전트를 설치합니다.
2. **Microsoft Azure Operational Insights에 에이전트 연결**을 선택한 다음 **다음**을 클릭합니다.
3. 메시지가 표시되면 이전 절차에서 메모장에 복사한 **작업 영역 ID** 및 **기본 키**를 입력합니다.

4. **다음**을 클릭합니다. 에이전트가 Operational Insights에 연결할 수 있는지 확인합니다.
5. 완료되면 **Microsoft 관리 에이전트**는 **제어판**에 나타납니다.

### 명령줄을 사용하여 에이전트를 설치하려면
- 명령줄을 사용하여 에이전트를 설치하려면 다음 예제를 수정한 다음 사용합니다.```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Microsoft 모니터링 에이전트 구성(선택 사항)
다음 정보를 사용하여 Microsoft Azure Operational Insights 서비스와 직접 통신하는 에이전트를 사용하도록 합니다. 에이전트를 구성한 후, 에이전트 서비스로 등록하고 필요한 구성 정보와 솔루션 정보를 포함하는 관리 팩을 가져옵니다.

에이전트에서 모니터링된 컴퓨터에서 데이터를 수집한 후, 모니터링되는 컴퓨터 수가 **서버 직접 연결(64비트)** 아래 **설정**의 **연결된 소스**탭에 있는 Operational Insights 포털에 표시됩니다. 데이터를 전송하는 모든 컴퓨터에 대해, Operational Insights 포털에서 해당 데이터 및 평가 정보를 볼 수 있습니다.

필요한 경우 에이전트를 비활성화하거나 명령줄 또는 스크립트를 사용하여 활성화할 수도 있습니다.

### 에이전트를 구성하려면
1. **Microsoft 모니터링 에이전트**를 설치한 후 **제어판**을 엽니다.
2. Microsoft 모니터링 에이전트를 연 다음 Azure Operational Insights 탭을 클릭합니다.
3. **Azure Operational Insights에 연결**을 선택합니다.
4. **작업 영역 ID** 상자에 Operational Insights 포털에서 제공된 작업 영역 ID를 입력합니다.
5. **계정 키** 상자에서 Operational Insights 포털의 **기본 키**를 붙여넣기한 다음 **확인**을 클릭합니다.

### 에이전트를 사용하지 않도록 설정하려면
1. 에이전트를 설치한 후 **제어판**을 엽니다.
2. Microsoft 모니터링 에이전트를 연 다음 **Azure Operational Insights** 탭을 클릭합니다.
3. **Azure Operational Insights에 연결**을 선택 취소합니다.

### 명령줄 또는 스크립트를 사용하여 에이전트를 사용하도록 설정하려면
- 다음 예제와 Windows PowerShell 또는 VB 스크립트를 사용할 수 있습니다.

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## 프록시 및 방화벽 설정 구성(선택 사항)
인터넷에 대한 액세스를 제한하는 사용자 환경에서 방화벽 또는 프록시 서버를 설정한 경우, Operations Manager 또는 에이전트를 사용하여 Operational Insights 서비스와 통신하도록 설정하려면 다음 절차를 따라야 합니다.

- [프록시 및 방화벽 설정 구성(선택 사항)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO4-->