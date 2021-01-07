---
title: PowerShell을 사용 하 여 Azure에 하이브리드 컴퓨터 연결
description: 이 문서에서는 Azure Arc 사용 서버를 사용 하 여 에이전트를 설치 하 고 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다. 이 작업은 PowerShell을 사용하여 수행할 수 있습니다.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0218235179e1a8a883360d0061e685c04079cbf4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95492944"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>PowerShell을 사용 하 여 Azure에 하이브리드 컴퓨터 연결

Azure Arc에서 사용 하도록 설정 된 서버의 경우 수동 단계를 수행 하 여 환경에서 하나 이상의 Windows 또는 Linux 컴퓨터에 대해 사용 하도록 설정할 수 있습니다. 또는 PowerShell cmdlet [AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) 을 사용 하 여 연결 된 컴퓨터 에이전트를 다운로드 하 고, 에이전트를 설치 하 고, Azure Arc에 컴퓨터를 등록할 수 있습니다. 이 cmdlet은 microsoft 다운로드 센터에서 Windows 에이전트 패키지 (Windows Installer)를 다운로드 하 고 Microsoft 패키지 리포지토리의 Linux 에이전트 패키지를 다운로드 합니다.

이 방법을 사용하려면 머신에 대한 관리자 권한으로 에이전트를 설치하고 구성할 수 있어야 합니다. Linux에서는 루트 계정을 사용하여 수행하고, Windows에서는 로컬 관리자 그룹의 멤버로 수행해야 합니다. [PowerShell 원격](/powershell/scripting/learn/ps101/08-powershell-remoting)을 사용 하 여 Windows 서버에서 대화형으로 또는 원격으로이 프로세스를 완료할 수 있습니다.

시작 하기 전에 [필수 구성 요소](agent-overview.md#prerequisites) 를 검토 하 고 구독과 리소스가 요구 사항을 충족 하는지 확인 합니다. 지원 되는 지역 및 기타 관련 고려 사항에 대 한 자세한 내용은 [지원 되는 Azure 지역](overview.md#supported-regions)을 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure PowerShell 있는 컴퓨터입니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

PowerShell을 사용 하 여 Azure Arc 사용 서버에서 관리 하는 하이브리드 서버에서 VM 확장을 관리 합니다. PowerShell을 사용 하기 전에 모듈을 설치 `Az.ConnectedMachine` 합니다. Azure Arc에서 사용 하도록 설정 된 서버에서 다음 명령을 실행 합니다.

```powershell
Install-Module -Name Az.ConnectedMachine
```

설치가 완료 되 면 다음 메시지가 표시 됩니다.

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

1. 상승 된 권한으로 PowerShell 콘솔을 엽니다.

2. 명령을 실행 하 여 Azure에 로그인 `Connect-AzAccount` 합니다.

3. 연결 된 컴퓨터 에이전트를 설치 하려면 `Connect-AzConnectedMachine` `-Name` , `-ResourceGroupName` 및 매개 변수와 함께를 사용 `-Location` 합니다. `-SubscriptionId`로그인 후 만든 Azure 컨텍스트의 결과로 기본 구독을 재정의 하려면 매개 변수를 사용 합니다. 다음 명령 중 하나를 실행합니다.

    * Azure와 직접 통신할 수 있는 대상 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하려면 다음을 실행 합니다.

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * 프록시 서버를 통해 통신 하는 대상 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하려면 다음을 실행 합니다.
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. Windows에서 다음 파일을 확인 합니다. *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Linux에서 다음 파일을 확인 합니다. */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>PowerShell 원격을 사용 하 여 설치 및 연결

Azure Arc로 설정 된 서버를 사용 하 여 하나 이상의 Windows server를 구성 하는 방법은 다음과 같습니다. 원격 컴퓨터에서 PowerShell 원격을 사용 하도록 설정 해야 합니다. `Enable-PSRemoting` cmdlet을 사용하여 이를 수행하십시오.

1. 관리자 권한으로 PowerShell 콘솔을 엽니다.

2. 명령을 실행 하 여 Azure에 로그인 `Connect-AzAccount` 합니다.

3. 연결 된 컴퓨터 에이전트를 설치 하려면 `Connect-AzConnectedMachine` `-ResourceGroupName` , 및 매개 변수와 함께를 사용 `-Location` 합니다. Azure 리소스 이름에는 각 서버의 호스트 이름이 자동으로 사용 됩니다. `-SubscriptionId`로그인 후 만든 Azure 컨텍스트의 결과로 기본 구독을 재정의 하려면 매개 변수를 사용 합니다.

    * Azure와 직접 통신할 수 있는 대상 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하려면 다음 명령을 실행 합니다.
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * 동시에 여러 원격 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하려면 원격 컴퓨터 이름 목록을 쉼표로 구분 하 여 추가 합니다.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    다음 예에서는 단일 컴퓨터를 대상으로 하는 명령의 결과를 보여 줍니다.
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

Azure Arc 사용 서버에 등록 하도록 에이전트를 설치 하 고 구성한 후 Azure Portal으로 이동 하 여 서버가 성공적으로 연결 되었는지 확인 합니다. [Azure Portal](https://portal.azure.com)에서 머신을 확인합니다.

![성공적인 서버 연결을 보여 주는 서버 대시보드의 스크린샷](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

* 필요한 경우 [연결 된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)를 참조 하세요.

* [Azure Policy](../../governance/policy/overview.md)를 사용 하 여 컴퓨터를 관리 하는 방법을 알아봅니다. VM [게스트 구성을](../../governance/policy/concepts/guest-configuration.md)사용 하 여 컴퓨터가 예상 Log Analytics 작업 영역에 보고 하는지 확인 하 고 [vm에서 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)를 사용 하 여 모니터링을 사용 하도록 설정할 수 있습니다.

* [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. Windows 및 Linux 용 Log Analytics 에이전트는 운영 체제 및 워크 로드 모니터링 데이터를 수집 하거나 Azure Automation runbook 또는 업데이트 관리와 같은 기능을 사용 하 여 관리 하려는 경우에 필요 합니다. 또한이 에이전트는 [Azure Security Center](../../security-center/security-center-introduction.md)같은 다른 Azure 서비스를 사용 하는 데 필요 합니다.
