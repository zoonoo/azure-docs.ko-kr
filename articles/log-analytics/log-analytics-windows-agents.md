---
title: "Log Analytics에 Windows 컴퓨터 연결 | Microsoft Docs"
description: "이 문서는 사용자 지정 버전의 MMA(Microsoft Monitoring Agent)를 사용하여 온-프레미스 인프라의 Windows 컴퓨터를 OMS에 직접 연결하는 단계를 보여줍니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6836cd4c1f9fe53691ae8330b25e497da4c2e0d5
ms.openlocfilehash: 161bb18579db7a4615cbc62c539e8b6286a424ac


---
# <a name="connect-windows-computers-to-log-analytics"></a>Log Analytics에 Windows 컴퓨터 연결

이 문서는 사용자 지정 버전의 MMA(Microsoft Monitoring Agent)를 사용하여 온-프레미스 인프라의 Windows 컴퓨터를 OMS 작업 영역에 직접 연결하는 단계를 보여줍니다. 에이전트에서 OMS로 데이터를 보내고 OMS 포털에서 해당 데이터를 보고 사용할 수 있도록 등록하려는 전체 컴퓨터에 대한 에이전트를 설치 및 연결해야 합니다. 각 에이전트는 여러 작업 영역에 보고할 수 있습니다.

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


## <a name="system-requirements-and-required-configuration"></a>시스템 요구 사항 및 필수 구성
에이전트를 설치 또는 배포하기 전에 아래 정보를 검토하여 필수 구성 요소를 충족하는지 확인합니다.

- OMS MMA는 Windows Server 2008 SP 1 이상 또는 Windows 7 SP1 이상을 실행 중인 컴퓨터에만 설치할 수 있습니다.
- OMS 구독이 필요합니다.  자세한 내용은 [Log Analytics 시작](log-analytics-get-started.md)을 참조하세요.
- 각 Windows 컴퓨터는 HTTPS 또는 OMS Gateway를 사용하여 인터넷에 연결할 수 있어야 합니다. 직접 연결하거나 프록시를 통하거나 OMS Gateway를 통해 연결할 수 있습니다.
- OMS MMA를 독립 실행형 컴퓨터, 서버, 가상 컴퓨터에 설치할 수 있습니다. Azure에서 호스팅되는 가상 컴퓨터를 OMS에 연결하려면 [Log Analytics에 Azure 가상 컴퓨터 연결](log-analytics-azure-vm-extension.md)을 참조하세요.
- 에이전트는 다양한 리소스에 TCP 포트 443을 사용해야 합니다. 자세한 내용은 [Log Analytics에서 프록시 및 방화벽 설정 구성](log-analytics-proxy-firewall.md)을 참조하세요.

## <a name="download-the-agent-setup-file-from-oms"></a>OMS에서 에이전트 설치 파일 다운로드
1. OMS 포털의 **개요** 페이지에서 **설정** 타일을 클릭합니다.  위쪽에서 **연결된 원본** 탭을 클릭합니다.  
    ![연결된 원본 탭](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. **Windows Servers**를 클릭한 다음 사용하는 컴퓨터 프로세서 유형에 해당하는 **Windows 에이전트 다운로드**를 클릭하여 설치 파일을 다운로드합니다.
3. **작업 영역 ID**오른쪽에서 복사 아이콘을 클릭하고 ID를 메모장에 붙여넣습니다.
4. **기본 키**오른쪽에서 복사 아이콘을 클릭하고 키를 메모장에 붙여넣습니다.     

## <a name="install-the-agent-using-setup"></a>설치 프로그램을 사용하여 에이전트 설치
1. 설치 프로그램을 실행하여 관리하려는 컴퓨터에 에이전트를 설치합니다.
2. Welcome 페이지에서 **다음**을 클릭합니다.
3. 사용 조건 페이지에서 라이선스를 읽고 **동의함**을 클릭합니다.
4. 대상 폴더 페이지에서 기본 설치 폴더를 변경 또는 유지하고 **다음**을 클릭합니다.
5. 에이전트 설치 옵션 페이지에서 에이전트를 Azure Log Analytics(OMS), Operations Manager에 연결하도록 선택하거나 에이전트를 나중에 구성하려는 경우 선택 항목을 비워둘 수 있습니다. **다음**을 클릭합니다.   
    - Azure Log Analytics(OMS)에 연결하려는 경우 이전 절차에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키(기본 키)**를 붙여 넣은 후 **다음**을 클릭합니다.  
        ![작업 영역 ID 및 기본 키 붙여넣기](./media/log-analytics-windows-agents/connect-workspace.png)
    - Operations Manager에 연결할 경우 **관리 그룹 이름**, **관리 서버** 이름, **관리 서버 포트**를 입력하고 **다음**을 클릭합니다. 에이전트 작업 페이지에서 로컬 시스템 계정 또는 로컬 도메인 계정을 선택하고 **다음**을 클릭합니다.  
        ![관리 그룹 구성](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![에이전트 작업 계정](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. 설치 준비 페이지에서 선택 항목을 검토한 다음 **설치**를 클릭합니다.
7. 구성 완료 페이지에서 **마침**을 클릭합니다.
8. 완료되면 **제어판**에 **Microsoft Monitoring Agent**가 나타납니다. 여기에서 구성을 검토하고 에이전트가 Operational Insights(OMS)에 연결되었는지 확인합니다. OMS에 연결되면 에이전트에 **Microsoft Monitoring Agent가 Microsoft Operations Management Suite 서비스에 성공적으로 연결되었습니다.**와 같은 메시지가 표시됩니다.

## <a name="install-the-agent-using-the-command-line"></a>명령줄을 사용하여 에이전트 설치
- 명령줄을 사용하여 에이전트를 설치하려면 다음 예제를 수정한 다음 사용합니다.

    >[!NOTE]
    에이전트를 업그레이드하려는 경우 Log Analytics 스크립팅 API를 사용해야 합니다. 에이전트를 업그레이드하려면 다음 섹션을 참조하세요.

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>스크립트를 사용하여 에이전트 업그레이드 및 작업 영역 추가
다음 PowerShell 예처럼 Log Analytics 스크립팅 API를 사용하여 에이전트를 업그레이드하고 작업 영역을 추가할 수 있습니다.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[!NOTE]
이전에 명령줄 또는 스크립트를 사용하여 에이전트를 설치 또는 구성한 경우 `EnableAzureOperationalInsights`가 `AddCloudWorkspace`로 바뀌었습니다.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Azure 자동화에서 DSC를 사용하여 에이전트 설치

>[!NOTE]
이 절차 및 스크립트 예제는 기존 에이전트를 업그레이드하지 않습니다.

1. [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 에서 Azure 자동화로 xPSDesiredStateConfiguration DSC 모듈을 가져옵니다.  
2.  *OPSINSIGHTS_WS_ID* 및 *OPSINSIGHTS_WS_KEY*의 Azure Automation 변수 자산을 만듭니다. *OPSINSIGHTS_WS_ID*를 OMS Log Analytics 작업 영역 ID에 설정하고 *OPSINSIGHTS_WS_KEY*를 작업 영역의 기본 키에 설정합니다.
3.  아래 스크립트를 사용하여 MMAgent.ps1에 저장합니다.
4.  다음 예제를 수정 및 사용하여 Azure 자동화에 DSC를 사용하여 에이전트를 설치합니다. Azure 자동화 인터페이스 또는 cmdlet을 사용하여 MMAgent.ps1을 Azure 자동화로 가져옵니다.
5.  구성에 노드를 할당합니다. 15분 이내에 노드가 구성을 점검하고 MMA가 노드로 푸시됩니다.

```
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
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
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

## <a name="optionally-configure-agents-to-use-the-oms-gateway"></a>또는 에이전트에서 OMS Gateway를 사용하도록 구성합니다.

인터넷에 연결되지 않은 서버 또는 클라이언트가 있는 경우 해당 서버 또는 클라이언트에서 OMS Gateway를 사용하여 OMS로 데이터를 전송하도록 설정할 수 있습니다.  해당 Gateway를 사용하는 경우 에이전트의 모든 데이터가 인터넷에 액세스하는 단일 서버를 통해 전송됩니다. Gateway는 전송되는 데이터를 분석하지 않고 에이전트의 데이터를 OMS로 직접 전송합니다.

설치 및 구성을 포함하는 Gateway에 대한 자세한 내용을 알아보려면 [OMS Gateway](log-analytics-oms-gateway.md)를 참조하세요.

에이전트에서 프록시 서버(이 경우 OMS Gateway)를 사용하도록 구성하는 방법은 [Log Analytics에서 프록시 및 방화벽 설정 구성](log-analytics-proxy-firewall.md)을 참조하세요.

## <a name="optionally-configure-proxy-and-firewall-settings"></a>프록시 및 방화벽 설정 구성(선택 사항)
환경에 인터넷 액세스를 차단하는 프록시 서버 또는 방화벽이 있는 경우 [Log Analytics에 프록시 및 방화벽 설정 구성](log-analytics-proxy-firewall.md) 을 참조하여 에이전트가 OMS 서비스와 통신하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

- [솔루션 갤러리에서 Log Analytics 솔루션을 추가](log-analytics-add-solutions.md) 하여 기능을 추가하고 데이터를 수집합니다.
- [Log Analytics에서 프록시 및 방화벽 설정 구성](log-analytics-proxy-firewall.md) 합니다.



<!--HONumber=Nov16_HO3-->


