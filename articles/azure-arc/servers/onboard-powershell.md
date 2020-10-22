---
title: PowerShell을 사용 하 여 하이브리드 컴퓨터를 Azure에 연결
description: 이 문서에서는 PowerShell을 사용 하 여 Azure Arc 사용 서버를 사용 하 여 에이전트를 설치 하 고 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376295"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>PowerShell을 사용 하 여 하이브리드 컴퓨터를 Azure에 연결

일련의 단계를 수동으로 수행 하 여 환경에서 하나 또는 소수의 Windows 또는 Linux 컴퓨터에 대해 Azure Arc 사용 서버를 사용 하도록 설정할 수 있습니다. 또는 PowerShell cmdlet [AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine)을 사용할 수 있습니다. 이 cmdlet은 다음 작업을 수행 합니다.

- Microsoft 다운로드 센터에서 Windows 에이전트를 다운로드 하도록 호스트 컴퓨터를 구성 하 고 packages.microsoft.com에서 Linux 에이전트 패키지를 다운로드 합니다.
- 연결 된 컴퓨터 에이전트를 설치 합니다.
- Azure Arc에 컴퓨터를 등록 합니다.

이 방법을 사용하려면 머신에 대한 관리자 권한으로 에이전트를 설치하고 구성할 수 있어야 합니다. Linux에서는 루트 계정을 사용하여 수행하고, Windows에서는 로컬 관리자 그룹의 멤버로 수행해야 합니다.

시작하려면 먼저 [사전 요구 사항](agent-overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다. 지원 되는 지역 및 기타 관련 고려 사항에 대 한 자세한 내용은 [지원 되는 Azure 지역](overview.md#supported-regions)을 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

Azure PowerShell를 사용 하 여 Arc 사용 서버에서 관리 하는 하이브리드 서버에서 VM 확장을 관리 하려면 먼저 모듈을 설치 해야 `Az.ConnectedMachine` 합니다. Arc 사용 서버에서 다음 명령을 실행 합니다.

```powershell
Install-Module -Name Az.ConnectedMachine
```

설치가 완료 되 면 다음 메시지가 반환 됩니다.

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>Windows에서 에이전트 설치 및 유효성 검사

1. 관리자 권한으로 PowerShell 콘솔을 엽니다.

2. 명령을 실행 하 여 Azure에 로그인 `Connect-AzAccount` 합니다.

3. 연결 된 컴퓨터 에이전트를 설치 하려면 `Connect-AzConnectedMachine` `-Name` , `-ResourceGroupName` 및 매개 변수와 함께를 사용 `-Location` 합니다. `-SubscriptionId`로그인 후 만든 Azure 컨텍스트의 결과로 기본 구독을 재정의 하려면 매개 변수를 사용 합니다.

    Azure와 직접 통신할 수 있는 대상 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하려면 다음 명령을 실행 합니다.

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    대상 머신이 프록시 서버를 통해 통신하는 경우 다음 명령을 실행합니다.
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*의 Windows와 */var/opt/azcmagent/log/himds.log*의 Linux

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치되고 Azure Arc 사용 서버에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://portal.azure.com)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [연결 된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM을 사용한 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)로 모니터링 등).

* [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. Windows 및 Linux 용 Log Analytics 에이전트는 운영 체제 및 워크 로드 모니터링 데이터를 수집 하거나, 자동화 runbook 또는 업데이트 관리 같은 기능을 사용 하 여 관리 하거나, [Azure Security Center](../../security-center/security-center-introduction.md)같은 다른 Azure 서비스를 사용 하려는 경우에 필요 합니다.