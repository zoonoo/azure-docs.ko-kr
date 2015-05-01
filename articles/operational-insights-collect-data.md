<properties 
   pageTitle="컴퓨터 데이터 수집" 
   description="온-프레미스 또는 클라우드 인프라의 서버에서 컴퓨터 데이터를 수집합니다." 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
# 컴퓨터 데이터 수집

Operational Insights는 온-프레미스 또는 클라우드 인프라의 서버에서 데이터를 사용합니다. 다음 원본에서 컴퓨터 데이터를 수집할 수 있습니다.

- Operations Manager 에이전트
- Operational Insights에 직접 연결된 컴퓨터
- Azure 저장소 서비스의 가상 컴퓨터 진단 데이터

데이터를 수집한 후 Operational Insights 서비스에 전송됩니다.

## System Center Operations Manager에서 Operational Insights에 연결 

기존 System Center Operations Manager 환경에 Operational Insights를 연결할 수 있습니다. 이렇게 하면 기존 Operations Manager 에이전트를 Operational Insights 에이전트로 사용할 수 있습니다. Operational Insights로 Operations Manager 사용에 대한 추가 정보는 [Operational Insights를 사용한 Operations Manager 고려 사항](operational-insights-operations-manager.md) 을 참조하세요.

Operations Manager를 사용하여 다음 작업을 모니터링하는 경우 Operations Manager 실행 계정을 설정해야 합니다. 계정 설정에 대한 자세한 내용은 [Operational Insights에 대한 Operations Manager 실행 계정](operational-insights-run-as.md)을 참조하세요.

- SQL 평가
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] Operational Insights 지원은 Operations Manager 2012 SP1 UR6 및 Operations Manager 2012 R2 UR2 기준으로 사용 가능합니다. 프록시 지원은 System Center Operations Manager 2012 SP1 UR7 및 System Center Operations Manager 2012 R2 UR3에 추가되었습니다.

#### Operations Manager를 Operational Insights에 연결하고 에이전트를 추가하려면

1. Operations Manager 콘솔에서 **관리**를 클릭합니다.

2. **Operational Insights** 노드를 확장하고 **Operational Insights 연결**을 클릭합니다.

3. **Operational Insights로 등록** 링크를 클릭하고 화면의 지시에 따릅니다. 

4. 등록 마법사를 완료한 후 **컴퓨터/그룹 추가**를 클릭합니다.

5. **컴퓨터 검색** 대화 상자에서, Operations Manager에서 모니터링하는 그룹 또는 컴퓨터를 검색할 수 있습니다. Operational Insights에 등록할 컴퓨터 또는 그룹을 선택하고 **추가**를 클릭한 다음 **확인**을 클릭합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Operational Insights에 컴퓨터를 직접 연결 

등록하려는 각 컴퓨터에 Operational Insights 에이전트를 설치하여 컴퓨터를 Operational Insights에 직접 연결할 수 있습니다. 


### 에이전트 다운로드 및 설치

#### 에이전트 설치 파일을 다운로드하려면
1. **Operations Insights 포털**의 **개요** 페이지에서 **서버 및 사용량**을 클릭합니다.
1. **직접 연결된 서버**에서 **구성**을 클릭합니다.
1. **에이전트 추가** 옆에서 에이전트 링크를 클릭하여 설치 파일을 다운로드합니다.
1. **기본 작업 영역 키** 상자에서 키를 선택하고 복사합니다(CTRL + c).


#### 설치 프로그램을 사용하여 에이전트를 설치하려면
1. 설치 프로그램을 실행하여 관리하려는 컴퓨터에 에이전트를 설치합니다.
1. **Microsoft Azure Operational Insights에 에이전트를 연결**을 선택한 다음 **다음**을 클릭합니다.
1. 메시지가 표시되면 4단계에서 복사한 정보를 입력합니다.
1. 완료되면 **Microsoft 관리 에이전트**는 **제어판**에 나타납니다.

#### 명령줄을 사용하여 에이전트를 설치하려면
명령줄을 사용하여 에이전트를 설치하려면 다음 예제를 수정한 다음 사용합니다.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Microsoft 모니터링 에이전트 구성(선택 사항)
다음 정보를 사용하여 Microsoft Azure Operational Insights 서비스와 직접 통신하는 에이전트를 사용하도록 합니다. 에이전트를 구성한 후, 에이전트 서비스로 등록하고 필요한 구성 정보와 인텔리전스 팩 정보를 포함하는 관리 팩을 가져옵니다.

에이전트에서 모니터링된 컴퓨터에서 데이터를 수집한 후, 모니터링되는 컴퓨터 수가 **직접 연결된 에이전트** 아래 사용량 페이지의 Operational Insights 포털에 표시됩니다. 데이터를 전송하는 모든 컴퓨터에 대해, Operational Insights 포털에서 해당 데이터 및 평가 정보를 볼 수 있습니다.

필요한 경우 에이전트를 비활성화하거나 명령줄 또는 스크립트를 사용하여 활성화할 수도 있습니다.

#### 에이전트를 구성하려면
- **Microsoft 모니터링 에이전트**를 설치한 후 **제어판**을 엽니다.
- Microsoft 모니터링 에이전트를 연 다음 Azure Operational Insights 탭을 클릭합니다.
- **Azure Operational Insights에 연결**을 선택합니다.
- **작업 영역 ID** 상자에 Operational Insights 포털에서 제공된 작업 영역 ID를 입력합니다.
- 계정 키 상자에 에이전트를 설치할 때 복사한 **기본 작업 영역 키**를 붙여넣은 다음 **확인**을 클릭합니다.

#### 에이전트를 사용하지 않도록 설정하려면
- 에이전트를 설치한 후 **제어판**을 엽니다.
- Microsoft 모니터링 에이전트를 연 다음 **Azure Operational Insights** 탭을 클릭합니다.
- **Azure Operational Insights**에 연결을 선택 취소합니다.

#### 명령줄 또는 스크립트를 사용하여 에이전트를 사용하도록 설정하려면
다음 예제와 Windows PowerShell 또는 VB 스크립트를 사용할 수 있습니다.

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### 프록시 및 방화벽 설정 구성(선택 사항)
인터넷에 대한 액세스를 제한하는 사용자 환경에서 방화벽 또는 프록시 서버를 설정한 경우, Operations Manager 및/또는 에이전트를 사용하여 Operational Insights 서비스와 통신하도록 설정하려면 다음 절차를 따라야 합니다. 



- [프록시 및 방화벽 설정 구성(선택 사항)](operational-insights-proxy-filewall.md) 

## Microsoft Azure의 서버에서 데이터 분석

Operational Insights에서 [Azure 클라우드 서비스의 진단 기능](operational-insights-log-collection.md) 를 사용하여 클라우드 서비스 및 가상 컴퓨터에 대한 이벤트 및 IIS 로그를 빠르게 검색할 수 있습니다. Microsoft Monitoring Agent를 설치하여 가상 컴퓨터에서 자세한 내용을 알아볼 수도 있습니다.

업데이트 평가, 변경 추적 및 SQL 평가 인텔리전스 팩 모두는 Microsoft 모니터링 에이전트와 작업하여 가상 컴퓨터에 심층적인 통찰력을 제공합니다. 아직 작업하지 않은 경우, [Operational Insights 포털](https://preview.opinsights.azure.com/) 로 로그인한 경우 이 인텔리전스 팩을 설정할 수 있습니다.

Azure 가상 컴퓨터의 경우, 에이전트 기반 데이터 수집을 설정하는 쉬운 두 가지 방법이 있습니다.

- Microsoft Azure 관리 포털에서

- PowerShell 사용

로그 데이터에 대한 에이전트 기반 컬렉션을 사용하는 경우, [Operational Insights 포털](https://preview.opinsights.azure.com/) 의 로그 관리 구성 페이지에서 수집할 로그를 구성해야 합니다.

Azure 클라우드 서비스에서 진단 유틸리티를 사용하여 로그 데이터를 인덱스하도록 Operational Insights 에이전트를 구성하고 로그를 수집하도록 에이전트를 구성한 경우, 동일한 이벤트 로그에 인덱싱된 데이터의 두 복사본이 있습니다. 에이전트가 설치되어 있는 경우, 에이전트를 사용하여 로그 데이터를 수집해야 하며 Azure 클라우드 서비스의 진단으로 수집된 로그를 인덱싱하지 않아야 합니다.

### Microsoft Azure 관리 포털

[Operational Insights 포털](https://preview.opinsights.azure.com/) 에서, Operational Insights 작업 영역으로 이동하고 서버 탭을 선택합니다. 탭에 가상 컴퓨터의 목록이 표시됩니다. 에이전트를 설치할 가상 컴퓨터를 선택한 다음 **Op Insights 사용**을 클릭합니다.

Operational Insights 작업 영역에 대해 에이전트가 설치되고 구성됩니다.

![Operational Insights 서버 페이지의 이미지](./media/operational-insights-collect-data/servers.png)

### PowerShell

Azure 가상 컴퓨터를 변경하는 데 스크립팅을 선호하는 경우, PowerShell을 사용하여 Microsoft 모니터링 에이전트를 활성화할 수 있습니다.

Microsoft 모니터링 에이전트는 [Azure 가상 컴퓨터 확장](https://msdn.microsoft.com/library/azure/dn832621.aspx) 이며 아래 예에 표시된 것처럼 PowerShell을 사용하여 관리할 수 있습니다.

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

PowerShell을 사용하여 구성할 때 작업 영역 ID 및 작업 영역 키를 제공해야 합니다. Operational Insights 포털의 서버 및 사용량 페이지에서 작업 영역 ID와 작업 영역 키를 찾을 수 있습니다.

![Operational Insights 직접 에이전트 구성의 이미지](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Azure 클라우드 서비스에서 진단을 사용하여 데이터 수집

Operational Insights는 Azure 저장소 서비스에 대한 Azure 클라우드 서비스의 진단으로 작성된 데이터를 분석할 수 있습니다. 다음 기본 두 단계를 수행합니다.

- Azure 저장소에 진단 데이터 수집 구성
- 저장소 계정의 데이터를 분석하도록 Operational Insights 구성

아래 항목에는 Azure 저장소에 진단 데이터의 컬렉션을 사용하는 방법 및 Azure 저장소의 데이터를 분석하도록 Operational Insights를 구성하는 방법을 설명합니다.

Azure 진단은 Azure에서 실행 중인 작업자 역할, 웹 역할 또는 가상 컴퓨터에서 진단 원격 분석 데이터를 수집하는 데 사용할 수 있는 Azure 확장입니다. 원격 분석 데이터는 Azure 저장소 계정에 저장되며 Operational Insights에서 사용될 수 있습니다.

>[AZURE.NOTE] 저장소 계정에 진단을 보내면 저장소 및 트랜잭션에 일반적인 데이터 속도를 청구합니다.

Azure 진단에서는 다음 유형의 원격 분석 데이터를 수집할 수 있습니다.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>데이터 원본</b></td>
		<td><b>

설명 </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>IIS 로그</td>
		<td>IIS 웹 사이트에 대한 정보입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Azure 진단 인프라 로그</td>
		<td>진단 프로그램 자체에 대한 정보입니다.</td>
    </tr>
	<tr align="left" valign="top">
		<td>IIS 실패한 요청 로그 </td>
		<td>IIS 사이트 또는 응용 프로그램에 대한 실패한 요청과 관련된 정보입니다.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Windows 이벤트 로그</td>
		<td>Windows 이벤트 로깅 시스템으로 전송되는 정보입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>성능 카운터</td>
		<td>운영 체제 및 사용자 지정 성능 카운터입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>크래시 덤프</td>
		<td>응용 프로그램 작동 중단이 발생할 경우의 프로세스 상태에 대한 정보입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>사용자 지정 오류 로그</td>
		<td>응용 프로그램 또는 서비스에서 생성하는 로그입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>를 사용하여 코드에서 생성된 이벤트.NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource class/a></td>
    </tr>
    <tr align="left" valign="top">
		<td>매니페스트 기반 ETW</td>
		<td>프로세스에서 생성하는 ETW 이벤트입니다.</td>
    </tr>
    </tbody>
    </table>


현재 Operational Insights는 웹 역할에서 IIS 로그를 분석하며 웹과 작업자 역할 및 Azure 가상 컴퓨터에서 Windows 이벤트 로그를 분석할 수 있습니다. 로그는 다음 위치에 있어야 합니다.

- WADWindowsEventLogsTable (Table Storage)- Windows 이벤트 로그의 정보를 포함합니다.

- wad-iis-logfiles (Blob Storage) - IIS 로그에 관한 정보를 포함합니다.

가상 컴퓨터의 경우, 가상 컴퓨터로의 [Microsoft 모니터링 에이전트](http://go.microsoft.com/fwlink/?LinkId=517269) 설치 옵션도  있습니다. 이렇게 하면 IIS 로그, 이벤트 로그를 분석하여 구성 변경 내용 추적을 포함한 추가 분석을 수행하고 평가를 업데이트할 수도 있습니다. [Microsoft 모니터링 에이전트](http://go.microsoft.com/fwlink/?LinkId=517269) 를 설치하여 보다 세부적인 통찰 사용.

>[AZURE.NOTE] Azure 웹사이트에서 IIS 로그는 현재 지원되지 않습니다.

[피드백 페이지](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy) 에서 투표에 참여하여 분석할 Operational Insights에 대한 추가 로그의 우선순위를 지정하는 데 도움이 될 수 있습니다.

### IIS 로그 및 이벤트 컬렉션에 대한 웹 역할에서 Azure 진단 사용

[클라우드 서비스의 진단 기능을 사용하는 방법](https://msdn.microsoft.com/library/azure/dn482131.aspx) 을 참조하세요. 여기에서 기본 정보를 사용하고 Microsoft Azure Operational Insights와 사용하기 위해 여기에 나오는 단계를 사용자 지정합니다.

Azure 진단을 사용하는 경우:

- IIS 로그는 기본적으로 scheduledTransferPeriod 전송 간격에 전송 하는 로그 데이터와 함께 저장됩니다.

- Windows 이벤트 로그는 기본적으로 전송되지 않습니다.

#### 진단을 사용하도록 설정하려면

Windows 이벤트 로그를 사용하도록 설정하거나 scheduledTransferPeriod를 변경하려면, XML 구성 파일(diagnostics.wadcfg)을 사용하여 Azure 진단을 구성합니다(클라우드 서비스에서 진단을 사용하도록 설정하는 방법 항목의 [2단계: Visual Studio 솔루션에 diagnostics.wadcfg 파일 추가](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) 및 [3 단계: 응용 프로그램에 대한 진단 구성](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) 에 표시된대로. 다음 예제 구성 파일은 IIS 로그 및 모든 이벤트를 응용 프로그램 및 시스템 로그에서 수집합니다.

    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">
     
      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>
     
      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>
     
    </DiagnosticMonitorConfiguration>


[4 단계: 진단 데이터의 저장소 구성](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) (클라우드 서비스에서 진단을 사용하도록 설정하는 방법 항목에 있는)에서, ConfigurationSetting이 다음 예제처럼 저장 계정을 지정하는지 확인합니다.


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


**AccountName** 및 **AccountKey** 값은 Microsoft Azure 관리 포털 아래 저장소 계정 대시보드의 Microsoft Azure 관리 포털에 있습니다. 연결 문자열에 대한 프로토콜은 **https**이어야 합니다.

업데이트된 진단 구성이 클라우드 서비스에 적용되고 Azure 저장소에 진단을 작성하면, Operational Insights를 구성할 준비가 됩니다.

### 이벤트 로그 및 IIS 로그 컬렉션에 대한 Azure 클라우드 서비스 진단을 가상 컴퓨터에서 사용

다음 절차에 따라 Microsoft Azure 관리 포털을 사용하여 이벤트 로그와 IIS 로그 컬렉션에 대한 가상 컴퓨터에서 Azure 클라우드 서비스 진단을 사용하도록 설정합니다.

#### Azure 관리 포털을 사용하여 가상 컴퓨터에서 Azure 클라우드 서비스 진단을 사용하도록 설정하려면

1. 가상 컴퓨터를 만들 때 VM 에이전트를 설치합니다. 가상 컴퓨터가 이미 있는 경우 VM 에이전트가 이미 설치되어 있는지 확인합니다.
	- 기본 Azure 관리 포털을 사용하여 가상 컴퓨터를 만든 경우, **사용자 지정 만들기**를 수행하고 **VM 에이전트 설치**를 선택합니다.
	- 새 Azure 관리 포털을 사용하여 가상 컴퓨터를 만든 경우, **선택적 구성**을 선택한 다음, **진단**을 선택하고 **상태**를 **켬**으로 설정합니다.
	
	완료되면 VM은 자동으로 Azure 진단 확장을 설치하고 진단 데이터 수집을 담당하는 확장 기능을 실행합니다.

2. 모니터링을 설정하고 기존 VM에 대한 이벤트 로깅을 구성합니다. VM 수준에서 진단을 설정할 수 있습니다. 진단을 사용하도록 설정한 다음 이벤트 로깅을 구성하려면 다음 단계를 수행합니다.
	1. VM을 선택합니다.
	2. **모니터링**을 클릭합니다.
	3. **진단**을 클릭합니다.
	4. **상태**를 **켬**으로 설정합니다.
	5. 사용하려는 각 진단 메트릭을 선택합니다. Operational Insights는 Windows 이벤트 시스템 로그, Windows 이벤트 응용 프로그램 로그와 IIS 로그를 분석할 수 있습니다.
	7. **확인**을 클릭합니다.

Azure PowerShell을 사용하여 Azure 저장소에 기록된 이벤트를 보다 정확하게 지정할 수 있습니다. 샘플 구성 파일 및 해당 스키마에 대한 자세한 설명서에 대한 Azure 진단 1.2 구성 스키마를 참조하십시오. [Azure PowerShell 설치 및 구성하는 방법](install-configure-powershell/) 으로 Azure PowerShell 버전 0.8.7 이상을 설치하고 구성해야 합니다. 설치된 Microsoft Azure 진단의 버전이 버전 1.2 보다 이전 버전인 경우, 새 포털을 사용하여 진단을 사용하거나 구성할 수 없습니다.

다음 PowerShell 스크립트를 사용하여 에이전트를 사용하고 업데이트할 수 있습니다. 또한 사용자 지정 로깅 구성을 사용하여 이 스크립트를 사용할 수 있습니다. 저장소 계정, 서비스 이름 및 가상 컴퓨터 이름을 설정하도록 스크립트를 수정해야 합니다.

#### Azure PowerShell을 사용한 가상 컴퓨터에서 진단을 사용하도록 설정 하려면

다음 스크립트 샘플을 검토하고 복사하며 필요에 따라 수정하고, 샘플을 PowerShell 스크립트 파일로 저장한 다음 스크립트를 실행합니다.

    # Azure에 연결
    Add-AzureAccount 
     
    # 변경할 설정:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"
     
    #공용 Azure 진단 생성 및 구성 형식으로 변환 
    
    # 시스템 오류 이벤트만 수집:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"
    
    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)
    
    #Azure 진단 개인 구성 생성
    
    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)
    
    #가상 컴퓨터에 대한 진단 확장 활성화
    
    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).버전 # 확장의 최신 버전을 가져옵니다
    
    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
    

### Operational Insights에서 Azure 저장소 분석을 사용합니다.

기본 Azure 포털에서 Operational Insights 작업 영역으로 이동하고 **저장소** 탭을 선택합니다. Azure 진단을 사용하여 Azure 저장소 계정에서 읽도록 Operational Insights을 구성하려면 다음 정보를 사용합니다.

#### Operational Insights에서 분석을 사용하려면

1. **추가**를 클릭하고 **저장소 계정 추가** 상자를 엽니다.

2. **저장소 계정**을 선택합니다.

3. 데이터 형식으로 **이벤트** 또는 **IIS 로그**를 선택합니다.

4. **확인**을 클릭합니다.

5. 수집하려는 데이터 형식 및 저장소 계정 조합에 대해 위의 단계를 반복 합니다.

약 1 시간 동안에서 Operational Insights 내에서 분석에 사용할 수 있는 저장소 계정에서 데이터를 보기 시작합니다.




## 관련 콘텐츠

- [블로그 게시물: Operational Insights에 서버를 직접 연결](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [블로그 게시물: Azure 가상 컴퓨터에 대한 Operational Insights를 사용하도록 설정](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## 다음 단계

[프록시 및 방화벽 설정 구성(선택 사항)](operational-insights-proxy-filewall.md)




<!--HONumber=52-->