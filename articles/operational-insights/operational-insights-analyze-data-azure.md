<properties
   pageTitle="Microsoft Azure의 서버에서 데이터 분석"
   description="Azure 진단을 사용하도록 설정하여 클라우드 서비스 및 가상 컴퓨터용 이벤트 및 IIS 로그를 검색합니다."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders"/>
# Microsoft Azure의 서버에서 데이터 분석

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights는 온-프레미스 또는 클라우드 인프라의 서버에서 데이터를 사용합니다. Azure 진단에 의해 생성된 경우에 Azure 저장소에서 컴퓨터 데이터를 수집할 수 있습니다.

Azure 저장소에서 수집한 데이터를 사용하여 [Azure 진단](operational-insights-log-collection.md)을 사용하여 클라우드 서비스 및 가상 컴퓨터에 대한 이벤트 및 IIS 로그를 빠르게 검색할 수 있습니다. Microsoft Monitoring Agent를 설치하여 가상 컴퓨터에서 자세한 내용을 알아볼 수도 있습니다.

업데이트 평가, 변경 추적 및 SQL 평가 솔루션 모두는 Microsoft 모니터링 에이전트와 작업하여 가상 컴퓨터에 심층적인 통찰력을 제공합니다. 아직 작업하지 않은 경우, [Operational Insights 포털](https://preview.opinsights.azure.com/)로 로그인한 경우 [솔루션을 추가](operational-insights-add-solution.md)할 수 있습니다.

Azure 가상 컴퓨터의 경우, 에이전트 기반 데이터 수집을 설정하는 쉬운 두 가지 방법이 있습니다.

- Microsoft Azure 관리 포털에서

- PowerShell 사용

로그 데이터에 대한 에이전트 기반 컬렉션을 사용하는 경우, [Operational Insights 포털](https://preview.opinsights.azure.com/)의 로그 관리 구성 페이지에서 수집할 로그를 구성해야 합니다

 >[AZURE.NOTE]Azure 진단을 사용하여 로그 데이터를 인덱스하도록 Operational Insights 에이전트를 구성하고 로그를 수집하도록 에이전트를 구성한 경우, 동일한 로그가 두 번 인덱싱됩니다. 두 데이터 소스에 대한 일반적인 데이터 요금이 청구됩니다. 에이전트가 설치되어 있는 경우, 에이전트를 사용하여 로그 데이터를 수집해야 하며 Azure 진단으로 수집된 로그를 인덱싱하지 않아야 합니다.

## Microsoft Azure 관리 포털

[Azure 포털](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces)에서 Operational Insights용 에이전트를 설치할 수 있습니다.

### Operational Insight용 에이전트를 설치하려면
1. Azure 포털의 Operational Insights 작업 영역으로 이동하고 **서버** 탭을 선택합니다.
2. 탭에 가상 컴퓨터의 목록이 표시됩니다. 에이전트를 설치할 가상 컴퓨터를 선택한 다음 **Op Insights 사용**을 클릭합니다.

Operational Insights 작업 영역에 대해 에이전트가 자동적으로 설치되고 구성됩니다.

![Operational Insights 서버 페이지의 이미지](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]자동으로 Operational Insights용 에이전트를 설치하려면 [Azure VM 에이전트](https://msdn.microsoft.com/library/azure/dn832621.aspx)를 설치해야 합니다.



## PowerShell

Azure 가상 컴퓨터를 변경하는 데 스크립팅을 선호하는 경우, PowerShell을 사용하여 Microsoft 모니터링 에이전트를 활성화할 수 있습니다.

Microsoft Monitoring 에이전트는 [Azure 가상 컴퓨터 확장](https://msdn.microsoft.com/library/azure/dn832621.aspx) 이고 아래 예제처럼 PowerShell을 사용하여 관리할 수 있습니다.

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

PowerShell을 사용하여 구성할 때 작업 영역 ID 및 기본 키를 제공해야 합니다. Operational Insights 포털의 **설정** 페이지에서 작업 영역 ID와 기본 키를 찾을 수 있습니다.

![소스](./media/operational-insights-analyze-data-azure/sources01.png)

## Azure 진단을 사용한 데이터 수집

Operational Insights는 Azure 진단에서 Azure 저장소에 작성된 데이터를 분석할 수 있습니다. 다음 기본 두 단계를 수행합니다.

- Azure 저장소에 진단 데이터 수집 구성
- 저장소 계정의 데이터를 분석하도록 Operational Insights 구성

아래 항목에는 Azure 저장소에 진단 데이터의 컬렉션을 사용하는 방법 및 Azure 저장소의 데이터를 분석하도록 Operational Insights를 구성하는 방법을 설명합니다.

Azure 진단은 Azure에서 실행 중인 작업자 역할, 웹 역할 또는 가상 컴퓨터에서 진단 데이터를 수집하는 데 사용할 수 있는 Azure 확장입니다. 데이터는 Azure 저장소 계정에 저장되며 Operational Insights에서 사용될 수 있습니다.

>[AZURE.NOTE]진단을 계정으로 보내는 경우 저장소 및 트랜잭션에 대해, 그리고 Operational Insights이 저장소 계정에서 데이터를 읽는 경우에 일반 데이터 요금이 청구될 수 있습니다.

Azure 진단에서는 다음과 같은 유형의 원격 분석 데이터를 수집할 수 있습니다.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>데이터 원본</b></td>
		<td><b> 설명 </b></td>
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
		<td>응용 프로그램 크래시가 발생할 경우의 프로세스 상태에 대한 정보입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>사용자 지정 오류 로그</td>
		<td>응용 프로그램 또는 서비스에서 생성하는 로그입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>.NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource 클래스를 사용하여 코드에서 생성하는 이벤트입니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>매니페스트 기반 ETW</td>
		<td>프로세스에서 생성하는 ETW 이벤트입니다.</td>
    &lt;/tr
    <tr align="left" valign="top">
		<td>Syslog</td>
		<td>Syslog 또는 Rsyslog daemon으로 전송된 이벤트</td>
    </tr>
    </tbody>
    </table>


현재 Operational Insights은 분석할 수 있습니다.

- 웹 역할 및 가상 컴퓨터에서 IIS 로그
- Windows 운영 체제를 실행하는 웹 역할, 작업자 역할 및 Azure 가상 컴퓨터에서 Windows 이벤트 로그
- Linux 운영 체제를 실행하는 Azure 가상 컴퓨터에서 Syslog

로그는 다음 위치에 있어야 합니다.

- WADWindowsEventLogsTable(테이블 저장소)는 Windows 이벤트 로그의 정보를 포함합니다.
- wad-iis-logfiles (Blob Storage) – IIS 로그에 관한 정보를 포함합니다.
- LinuxsyslogVer2v0(테이블 저장소)는 Linux syslog 이벤트를 포함합니다.

 >[AZURE.NOTE]Azure 웹사이트에서 IIS 로그는 현재 지원되지 않습니다.

가상 컴퓨터의 경우, 가상 컴퓨터로의 [Microsoft 모니터링 에이전트](http://go.microsoft.com/fwlink/?LinkId=517269) 설치 옵션도 있어 추가로 insights를 사용합니다. IIS 로그 및 이벤트 로그 분석 외에도 구성 변경 내용 추적, SQL 평가를 포함한 추가 분석을 수행하고 평가를 업데이트할 수도 있습니다.

[피드백 페이지](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)에서 투표에 참여하여 분석할 Operational Insights에 대한 추가 로그의 우선순위를 지정하는 데 도움이 될 수 있습니다.

## IIS 로그 및 이벤트 컬렉션에 대한 웹 역할에서 Azure 진단 사용

[클라우드 서비스의 진단 기능을 사용하는 방법](https://msdn.microsoft.com/library/azure/dn482131.aspx)을 참조하십시오. 여기에서 기본 정보를 사용하고 Microsoft Azure Operational Insights와 사용하기 위해 여기에 나오는 단계를 사용자 지정합니다.

Azure 진단을 사용하는 경우:

- IIS 로그는 기본적으로 scheduledTransferPeriod 전송 간격에 전송 하는 로그 데이터와 함께 저장됩니다.

- Windows 이벤트 로그는 기본적으로 전송되지 않습니다.

### 진단을 사용하도록 설정하려면

Windows 이벤트 로그를 사용하도록 설정하거나 scheduledTransferPeriod를 변경하려면 클라우드 서비스에서 진단을 사용하도록 설정하는 방법 항목에서 [2단계: Visual Studio 솔루션에 diagnostics.wadcfg 파일 추가](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) 및 [3단계: 응용 프로그램에 대한 진단 구성](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3)에서 보이는 것처럼 XML 구성 파일(diagnostics.wadcfg)를 사용하여 Azure 진단을 구성하십시오. 다음 예제 구성 파일은 IIS 로그 및 모든 이벤트를 응용 프로그램 및 시스템 로그에서 수집합니다.

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


클라우드 서비스에서 진단을 사용하도록 설정하는 방법 항목의 [4단계: 진단 데이터 저장소 구성](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4)에서 ConfigurationSettings가 다음 예제처럼 저장소 계정을 지정하는지 확인합니다.


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


**AccountName** 및 **AccountKey** 값은 Microsoft Azure 관리 포털 아래 저장소 계정 대시보드의 Microsoft Azure 관리 포털에 있습니다. 연결 문자열에 대한 프로토콜은 **https**이어야 합니다.

업데이트된 진단 구성이 클라우드 서비스에 적용되고 Azure 저장소에 진단을 작성하면, Operational Insights를 구성할 준비가 됩니다.

## 이벤트 로그 및 IIS 로그 컬렉션에 대한 Azure 진단을 가상 컴퓨터에서 사용

다음 절차에 따라 Microsoft Azure 관리 포털을 사용하여 이벤트 로그와 IIS 로그 컬렉션에 대한 가상 컴퓨터에서 Azure 진단을 사용하도록 설정합니다.

### Azure 관리 포털을 사용하여 가상 컴퓨터에서 Azure 진단을 사용하도록 설정하려면

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

Azure PowerShell을 사용하여 Azure 저장소에 기록된 이벤트를 보다 정확하게 지정할 수 있습니다. 샘플 구성 파일 및 해당 스키마에 대한 자세한 설명서에 대한 Azure 진단 1.2 구성 스키마를 참조하십시오. [Azure PowerShell 설치 및 구성하는 방법](powershell-install-configure)으로 Azure PowerShell 버전 0.8.7 이상을 설치하고 구성해야 합니다. 설치된 Microsoft Azure 진단의 버전이 버전 1.2 보다 이전 버전인 경우, 새 포털을 사용하여 진단을 사용하거나 구성할 수 없습니다.

다음 PowerShell 스크립트를 사용하여 에이전트를 사용하고 업데이트할 수 있습니다. 또한 사용자 지정 로깅 구성을 사용하여 이 스크립트를 사용할 수 있습니다. 저장소 계정, 서비스 이름 및 가상 컴퓨터 이름을 설정하도록 스크립트를 수정해야 합니다.

### Azure PowerShell을 사용한 가상 컴퓨터에서 진단을 사용하도록 설정 하려면

다음 스크립트 샘플을 검토하고 복사하며 필요에 따라 수정하고, 샘플을 PowerShell 스크립트 파일로 저장한 다음 스크립트를 실행합니다.


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## Operational Insights에서 Azure 저장소 분석을 사용합니다.

저장소 분석을 사용하고 Azure 진단을 사용하여 Azure 저장소 계정에서 읽도록 Operational Insights을 구성하려면 다음 절차를 사용합니다.

### Operational Insights에서 분석을 사용하려면

1. 기본 Azure 포털에서 Operational Insights 작업 영역으로 이동하고 **저장소** 탭을 선택합니다. ![작업 영역 저장소 탭](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. **저장소 계정 추가**를 클릭하고 **저장소 계정 추가** 상자를 엽니다.
3. 사용하려는 저장소 계정을 선택합니다.
4. **데이터 형식** 목록에서 **이벤트**, **IIS 로그** 또는 **Syslog(Linux)** 중에 데이터 형식을 선택합니다.
5. **확인** 이미지를 클릭합니다. ![저장소 계정 상자](./media/operational-insights-analyze-data-azure/storage-account.png)
6. 수집하려는 데이터 형식 및 저장소 계정 조합에 대해 위의 단계를 반복 합니다.

약 1 시간 동안에서 Operational Insights 내에서 분석에 사용할 수 있는 저장소 계정에서 데이터를 보기 시작합니다.

## 관련 콘텐츠

- [Operational Insights에 컴퓨터를 직접 연결](operational-insights-direct-agent)
- [블로그 게시물: Azure 가상 컴퓨터에 대한 Operational Insights를 사용하도록 설정](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## 다음 단계

[프록시 및 방화벽 설정 구성(선택 사항)](../operational-insights-proxy-filewall.md)

<!---HONumber=July15_HO2-->