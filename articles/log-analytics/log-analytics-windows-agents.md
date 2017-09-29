---
title: "Azure Log Analytics에 Windows 컴퓨터 연결 | Microsoft Docs"
description: "이 문서에서는 사용자 지정 버전의 MMA(Microsoft Monitoring Agent)를 사용하여 온-프레미스 인프라의 Windows 컴퓨터를 Log Analytics 서비스에 연결하는 단계를 보여 줍니다."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e5f04f3b9135167c0f339c58323ebd931b260109
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Azure에서 Log Analytics 서비스에 Windows 컴퓨터 연결

이 문서에서는 사용자 지정 버전의 MMA(Microsoft Monitoring Agent)를 사용하여 온-프레미스 인프라의 Windows 컴퓨터를 OMS 작업 영역에 연결하는 단계를 보여 줍니다. 에이전트에서 Log Analytics 서비스에 데이터를 보내고 해당 데이터를 보고 사용할 수 있도록 등록하려는 전체 컴퓨터에 대한 에이전트를 설치 및 연결해야 합니다. 각 에이전트는 여러 작업 영역에 보고할 수 있습니다.

Azure 자동화에서 설정, 명령줄 또는 DSC(필요한 상태 구성)를 사용하여 에이전트를 설치할 수 있습니다.  

>[!NOTE]
Azure에서 실행되는 가상 컴퓨터의 경우 [가상 컴퓨터 확장](log-analytics-azure-vm-extension.md)을 사용하여 설치를 간소화할 수 있습니다.

에이전트는 인터넷 연결이 가능한 컴퓨터에서 연결을 사용하여 인터넷에 연결하고 OMS로 데이터를 전송합니다. 인터넷 연결이 없는 컴퓨터의 경우 프록시 또는 [OMS Gateway](log-analytics-oms-gateway.md)를 사용할 수 있습니다.

Windows 컴퓨터를 OMS에 연결하려면 간단한 3단계를 수행하기만 하면 됩니다.

1. OMS 포털에서 에이전트 설치 파일을 다운로드합니다.
2. 선택한 방법을 사용하여 에이전트를 설치합니다
3. 에이전트를 구성하거나 필요한 경우 추가 작업 영역을 추가합니다.

아래 다이어그램은 에이전트를 설치 및 구성한 이후 Windows 컴퓨터와 OMS 사이의 관계를 보여줍니다.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

IT 보안 정책이 네트워크에서 컴퓨터를 인터넷에 연결하도록 허용하지 않는 경우 OMS 게이트웨이에 연결하도록 컴퓨터를 구성할 수 있습니다. 서버가 OMS 게이트웨이를 통해 OMS 서비스와 통신할 수 있도록 구성하는 방법에 대한 자세한 내용 및 단계에 대해서는 [OMS 게이트웨이 사용하여 OMS에 컴퓨터 연결](log-analytics-oms-gateway.md)을 참조하세요.

## <a name="system-requirements-and-required-configuration"></a>시스템 요구 사항 및 필수 구성
에이전트를 설치 또는 배포하기 전에 아래 정보를 검토하여 필수 조건을 충족하는지 확인합니다.

- OMS MMA는 Windows Server 2008 SP 1 이상 또는 Windows 7 SP1 이상을 실행 중인 컴퓨터에만 설치할 수 있습니다.
- Azure 구독이 필요합니다.  자세한 내용은 [Log Analytics 시작](log-analytics-get-started.md)을 참조하세요.
- 각 Windows 컴퓨터는 HTTPS 또는 OMS Gateway를 사용하여 인터넷에 연결할 수 있어야 합니다. 직접 연결하거나 프록시를 통하거나 OMS Gateway를 통해 연결할 수 있습니다.
- OMS MMA를 독립 실행형 컴퓨터, 서버, 가상 컴퓨터에 설치할 수 있습니다. Azure에서 호스팅되는 가상 컴퓨터를 OMS에 연결하려면 [Log Analytics에 Azure 가상 컴퓨터 연결](log-analytics-azure-vm-extension.md)을 참조하세요.
- 에이전트는 다양한 리소스에 TCP 포트 443을 사용해야 합니다.

### <a name="network"></a>네트워크

Windows 에이전트를 OMS 서비스에 연결하고 등록한 경우 포트 번호 및 도메인 URL을 비롯한 네트워크 리소스에 대한 액세스 권한을 가져야 합니다.

- 프록시 서버의 경우 적절한 프록시 서버 리소스가 에이전트 설정에 구성되어 있는지 확인해야 합니다.
- 인터넷에 대한 액세스를 제한하는 방화벽의 경우 사용자 또는 네트워킹 엔지니어는 OMS에 대한 액세스를 허용하도록 방화벽을 구성해야 합니다. 에이전트 설정에서 아무 작업도 필요하지 않습니다.

다음 표에서는 통신에 필요한 리소스를 보여줍니다.

>[!NOTE]
>다음 리소스 중 일부는 Log Analytics의 이전 이름인 Operational Insights를 언급합니다.

| 에이전트 리소스 | 포트 | HTTPS 검사 무시 |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | 예 |
| *.oms.opinsights.azure.com | 443 | 예 |
| *.blob.core.windows.net | 443 | 예 |
| *.azure-automation.net | 443 | 예 |



## <a name="download-the-agent-setup-file-from-oms"></a>OMS에서 에이전트 설치 파일 다운로드
1. [OMS 포털](https://www.mms.microsoft.com)의 **개요** 페이지에서 **설정** 타일을 클릭합니다.  위쪽에서 **연결된 원본** 탭을 클릭합니다.  
    ![연결된 원본 탭](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. **Windows Servers**를 클릭한 다음 사용하는 컴퓨터 프로세서 유형에 해당하는 **Windows 에이전트 다운로드**를 클릭하여 설치 파일을 다운로드합니다.
3. **작업 영역 ID**오른쪽에서 복사 아이콘을 클릭하고 ID를 메모장에 붙여넣습니다.
4. **기본 키**오른쪽에서 복사 아이콘을 클릭하고 키를 메모장에 붙여넣습니다.     

## <a name="install-the-agent-using-setup"></a>설치 프로그램을 사용하여 에이전트 설치
1. 설치 프로그램을 실행하여 관리하려는 컴퓨터에 에이전트를 설치합니다.
2. Welcome 페이지에서 **다음**을 클릭합니다.
3. 사용 조건 페이지에서 라이선스를 읽고 **동의함**을 클릭합니다.
4. 대상 폴더 페이지에서 기본 설치 폴더를 변경 또는 유지하고 **다음**을 클릭합니다.
5. 에이전트 설치 옵션 페이지에서 에이전트를 Azure Log Analytics(OMS), Operations Manager에 연결하도록 선택하거나 에이전트를 나중에 구성하려는 경우 선택 항목을 비워둘 수 있습니다. **다음**을 누릅니다.   
    - Azure Log Analytics(OMS)에 연결하려는 경우 이전 절차에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키(기본 키)**를 붙여 넣은 후 **다음**을 클릭합니다.  
        ![작업 영역 ID 및 기본 키 붙여넣기](./media/log-analytics-windows-agents/connect-workspace.png)
    - Operations Manager에 연결할 경우 **관리 그룹 이름**, **관리 서버** 이름, **관리 서버 포트**를 입력하고 **다음**을 클릭합니다. 에이전트 작업 페이지에서 로컬 시스템 계정 또는 로컬 도메인 계정을 선택하고 **다음**을 클릭합니다.  
        ![관리 그룹 구성](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![에이전트 작업 계정](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. 설치 준비 페이지에서 선택 항목을 검토한 다음 **설치**를 클릭합니다.
7. 구성 완료 페이지에서 **마침**을 클릭합니다.
8. 완료되면 **제어판**에 **Microsoft Monitoring Agent**가 나타납니다. 여기에서 구성을 검토하고 에이전트가 Operational Insights(OMS)에 연결되었는지 확인합니다. OMS에 연결되면 에이전트에 **Microsoft Monitoring Agent가 Microsoft Operations Management Suite 서비스에 성공적으로 연결되었습니다.**와 같은 메시지가 표시됩니다.

## <a name="configure-proxy-settings"></a>프록시 설정 구성

제어판을 사용하여 Microsoft 모니터링 에이전트에 대한 프록시 설정을 구성하려면 다음 절차를 사용할 수 있습니다. 각 서버에 이 절차를 사용해야 합니다. 많은 서버를 구성해야 하는 경우, 스크립트를 사용하여 보다 쉽게 이 프로세스를 자동화할 수 있습니다. 그럴 경우 다음 절차 [스크립트를 사용하여 Microsoft 모니터링 에이전트에 대한 프록시 설정을 구성하려면](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script)을 참조하세요.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>제어판을 사용하여 Microsoft Monitoring Agent에 대한 프록시 설정을 구성하려면
1. **제어판**을 엽니다.
2. **Microsoft Monitoring Agent**를 엽니다.
3. **프록시 설정** 탭을 클릭합니다.  
    ![프록시 설정 탭](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. **프록시 서버 사용** 을 선택하고 URL과 포트 번호를 입력하고, 필요한 경우 표시된 예제와 비슷합니다. 프록시 서버에 인증이 필요한 경우 프록시 서버에 액세스 하려면 사용자 이름과 암호를 입력합니다.


### <a name="verify-agent-connectivity-to-oms"></a>OMS에 대한 에이전트 연결 확인

에이전트가 다음 절차를 사용하여 OMS와 통신하고 있는지 여부를 쉽게 확인할 수 있습니다.

1.  Windows 에이전트가 설치된 컴퓨터에서 제어판을 엽니다.
2.  Microsoft Monitoring Agent를 엽니다.
3.  Azure Log Analytics(OMS) 탭을 클릭합니다.
4.  상태 열에는 Operations Management Suite 서비스에 성공적으로 연결된 에이전트가 표시됩니다.

![연결된 에이전트](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>스크립트를 사용하여 Microsoft 모니터링 에이전트에 대한 프록시 설정을 구성하려면
다음 샘플을 복사하여 사용자 환경에 특정한 정보로 업데이트하고 PS1 파일 이름 확장명으로 저장한 다음 OMS 서비스에 직접 연결되는 각 컴퓨터에서 스크립트를 실행합니다.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)



## <a name="install-the-agent-using-the-command-line"></a>명령줄을 사용하여 에이전트 설치
- 명령줄을 사용하여 에이전트를 설치하려면 다음 예제를 수정한 다음 사용합니다. 예제에서는 완전 자동 설치를 수행합니다.

    >[!NOTE]
    에이전트를 업그레이드하려는 경우 Log Analytics 스크립팅 API를 사용해야 합니다. 에이전트를 업그레이드하려면 다음 섹션을 참조하세요.

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

에이전트는 `/c`를 통해 IExpress를 자동 압축 풀기 프로그램으로 사용합니다. [IExpress 소프트웨어 업데이트 패키지의 명령줄 스위치](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)의 명령줄 스위치를 확인하고 필요에 맞게 예제를 업데이트할 수 있습니다.

|MMA 관련 옵션                   |참고 사항         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = 작업 영역에 보고하도록 에이전트 구성                |
|OPINSIGHTS_WORKSPACE_ID                | 추가할 작업 영역의 작업 영역 ID(guid)                    |
|OPINSIGHTS_WORKSPACE_KEY               | 작업 영역에서 처음 인증하는 데 사용되는 작업 영역 키 |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | 작업 영역이 있는 클라우드 환경 지정 <br> 0 = Azure 상용 클라우드(기본값) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | 사용할 프록시의 URI |
|OPINSIGHTS_PROXY_USERNAME               | 인증된 프록시에 액세스할 사용자 이름 |
|OPINSIGHTS_PROXY_PASSWORD               | 인증된 프록시에 액세스할 암호 |

>[!NOTE]
IExpress의 명령줄 길이 제한에 도달하지 않으려면 작업 영역이 구성되지 않은 상태로 에이전트를 설치한 후 스크립트를 사용하여 작업 영역에 대한 구성을 설정합니다.

>[!NOTE]
`OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE` 매개 변수를 사용할 때 `Command line option syntax error.`이 발생하는 경우 다음과 같은 해결 방법을 사용할 수 있습니다.
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>스크립트를 사용하여 작업 영역 추가
다음 예제처럼 Log Analytics 에이전트 스크립팅 API를 사용하여 작업 영역을 추가합니다.

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

Azure에서 미국 정부에 대한 작업 영역을 추가하려면 다음 스크립트 샘플을 사용합니다.
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
이전에 명령줄 또는 스크립트를 사용하여 에이전트를 설치 또는 구성한 경우 `EnableAzureOperationalInsights`가 `AddCloudWorkspace`로 바뀌었습니다.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Azure 자동화에서 DSC를 사용하여 에이전트 설치

다음 스크립트 예제를 사용하여 Azure Automation에 DSC를 사용하여 에이전트를 설치합니다. 예제는 64비트 에이전트를 설치하고 `URI` 값으로 식별됩니다. 또한 URI 값을 바꿔 32비트 버전을 사용할 수 있습니다. 두 버전에 대한 URL

- Windows 64비트 에이전트 - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32비트 에이전트 - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
이 절차 및 스크립트 예제는 기존 에이전트를 업그레이드하지 않습니다.

1. [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 에서 Azure 자동화로 xPSDesiredStateConfiguration DSC 모듈을 가져옵니다.  
2.  *OPSINSIGHTS_WS_ID* 및 *OPSINSIGHTS_WS_KEY*의 Azure Automation 변수 자산을 만듭니다. *OPSINSIGHTS_WS_ID*를 OMS Log Analytics 작업 영역 ID에 설정하고 *OPSINSIGHTS_WS_KEY*를 작업 영역의 기본 키에 설정합니다.
3.  다음 스크립트를 사용하고 MMAgent.ps1로 저장합니다.
4.  다음 예제를 수정 및 사용하여 Azure 자동화에 DSC를 사용하여 에이전트를 설치합니다. Azure 자동화 인터페이스 또는 cmdlet을 사용하여 MMAgent.ps1을 Azure 자동화로 가져옵니다.
5.  구성에 노드를 할당합니다. 15분 이내에 노드가 구성을 점검하고 MMA가 노드로 푸시됩니다.

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>최신 ProductId 값 가져오기

MMAgent.ps1 스크립트에 있는 `ProductId value`은 각 에이전트 버전에 대해 고유합니다. 각 에이전트의 업데이트된 버전이 게시되면, ProductId 값이 변경됩니다. 따라서 나중에 ProductId가 변경되면 간단한 스크립트를 사용하여 에이전트 버전을 찾을 수 있습니다. 테스트 서버에 최신 에이전트 버전을 설치한 후에 다음 스크립트를 사용하여 설치된 ProductId 값을 가져올 수 있습니다. 최신 ProductId 값을 사용하면 MMAgent.ps1 스크립트에서 값을 업데이트할 수 있습니다.

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>에이전트를 수동으로 구성하거나 추가 작업 영역 추가
에이전트를 설치하고 구성하지 않은 경우 또는 에이전트가 여러 작업 영역에 보고하도록 하려는 경우 다음 정보를 참조하여 사용하도록 설정하거나 다시 구성할 수 있습니다. 에이전트를 구성한 후, 에이전트 서비스로 등록하고 필요한 구성 정보와 솔루션 정보를 포함하는 관리 팩을 가져옵니다.

1. Microsoft Monitoring Agent를 설치한 다음 **제어판**을 엽니다.
2. **Microsoft Monitoring Agent**를 연 다음 **Azure Log Analytics(OMS)** 탭을 클릭합니다.   
3. **추가**를 클릭하여 **Log Analytics 작업 영역 추가** 상자를 엽니다.
4. 추가할 작업 영역에 대해 이전 절차에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키(기본 키)**를 붙여 넣은 다음 **확인**을 클릭합니다.  
    ![Operational Insights 구성](./media/log-analytics-windows-agents/add-workspace.png)

에이전트에서 모니터링하는 컴퓨터에서 데이터를 수집한 후, OMS에서 모니터링하는 컴퓨터의 수가 **설정**의 **연결된 원본** 탭에 **연결된 서버**로 나타납니다.


## <a name="to-disable-an-agent"></a>에이전트를 사용하지 않도록 설정하려면
1. 에이전트를 설치한 후 **제어판**을 엽니다.
2. Microsoft Monitoring Agent를 연 다음 **Azure Log Analytics(OMS)** 탭을 클릭합니다.
3. 작업 영역을 선택하고 **제거**를 클릭합니다. 다른 모든 작업 영역에 대해 이 단계를 반복합니다.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>또는 에이전트에서 Operations Manager 관리 그룹으로 보고하도록 구성합니다.

IT 인프라에서 Operations Manager를 사용할 경우 Operations Manager 에이전트로 MMA 에이전트를 사용할 수 있습니다.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>MMA 에이전트에서 Operations Manager 관리 그룹으로 보고하도록 구성하려면
1.  에이전트가 설치된 컴퓨터에서 **제어판**을 엽니다.  
2.  **Microsoft Monitoring Agent**를 연 다음 **Operations Manager** 탭을 클릭합니다.  
    ![Microsoft Monitoring Agent Operations Manager 탭](./media/log-analytics-windows-agents/om-mg01.png)
3.  Operations Manager 서버가 Active Directory와 통합된 경우 **AD DS에서 관리 그룹 할당 자동 업데이트**를 클릭합니다.
4.  **추가**를 클릭하여 **관리 그룹 추가** 대화 상자를 엽니다.  
    ![Microsoft Monitoring Agent 관리 그룹 추가](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  **관리 그룹 이름** 상자에 관리 그룹의 이름을 입력합니다.
6.  **기본 관리 서버** 상자에 기본 관리 서버의 컴퓨터 이름을 입력합니다.
7.  **관리 서버 포트** 상자에 TCP 포트 번호를 입력합니다.
8.  **에이전트 작업 계정**에서 로컬 시스템 계정 또는 로컬 도메인 계정을 선택합니다.
9.  **확인**을 클릭하여 **관리 그룹 추가** 대화 상자를 닫은 다음 **확인**을 클릭하여 **Microsoft Monitoring Agent 속성** 대화 상자를 닫습니다.


## <a name="next-steps"></a>다음 단계

- [솔루션 갤러리에서 Log Analytics 솔루션을 추가](log-analytics-add-solutions.md) 하여 기능을 추가하고 데이터를 수집합니다.

