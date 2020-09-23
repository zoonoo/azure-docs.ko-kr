---
title: Azure Automation에 Linux Hybrid Runbook Worker 배포
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Linux 기반 컴퓨터에서 runbook을 실행 하는 Azure Automation Hybrid Runbook Worker를 설치 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: conceptual
ms.openlocfilehash: fb975305e18315fa8d0a39e4fe0ab6902c98b7e7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987233"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker 배포

Azure Automation의 Hybrid Runbook Worker 기능을 사용 하 여 역할을 호스트 하는 컴퓨터 및 환경의 리소스에 대해 runbook을 직접 실행 하 여 해당 로컬 리소스를 관리할 수 있습니다. Linux Hybrid Runbook Worker는 Runbook을 승격이 필요한 명령을 실행하도록 승격할 수 있는 특수 사용자로 실행합니다. Azure Automation는 runbook을 저장 하 고 관리 한 다음 하나 이상의 지정 된 컴퓨터에 전달 합니다. 이 문서에서는 Linux 컴퓨터에 Hybrid Runbook Worker를 설치하는 방법, 작업자를 제거하는 방법 및 Hybrid Runbook Worker 그룹을 제거하는 방법을 설명합니다.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 항목이 있는지 확인하십시오.

### <a name="a-log-analytics-workspace"></a>Log Analytics 작업 영역

Hybrid Runbook Worker 역할은 Azure Monitor Log Analytics 작업 영역에 따라 역할을 설치 하 고 구성 합니다. [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)을 통해 또는 [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)에서 [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)를 통해 만들 수 있습니다.

Azure Monitor Log Analytics 작업 영역이 없는 경우 작업 영역을 만들기 전에 [Azure Monitor 로그 디자인 지침](../azure-monitor/platform/design-logs-deployment.md) 을 검토 합니다.

작업 영역이 있지만 Automation 계정에 연결 되어 있지 않은 경우 자동화 기능을 사용 하도록 설정 하면 Hybrid Runbook Worker에 대 한 지원을 포함 하 여 Azure Automation 기능을 추가할 수 있습니다. Log Analytics 작업 영역의 Azure Automation 기능 중 하나 (특히 [업데이트 관리](update-management/update-mgmt-overview.md) 또는 [변경 내용 추적 및 인벤토리](change-tracking.md))를 사용 하도록 설정 하면 작업자 구성 요소가 에이전트 컴퓨터에 자동으로 푸시됩니다.

작업 영역에 업데이트 관리 기능을 추가 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

작업 영역에 변경 내용 추적 및 인벤토리 기능을 추가 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics 에이전트

Hybrid Runbook Worker 역할에는 지원 되는 Linux 운영 체제에 대 한 [Log Analytics 에이전트가](../azure-monitor/platform/log-analytics-agent.md) 필요 합니다.

### <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제

Hybrid Runbook Worker 기능은 다음 배포를 지원합니다.

* Amazon Linux 2012.09 ~ 2015.09(x86/x64)
* CentOS Linux 5, 6 및 7(x86/x64)
* Oracle Linux 5, 6 및 7(x86/x64)
* Red Hat Enterprise Linux Server 5, 6 및 7(x86/x64)
* Debian GNU/Linux 6, 7, 8(x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS 및 18.04(x86/x64)
* SUSE Linux Enterprise Server 12 (x86/x64)

### <a name="minimum-requirements"></a>최소 요구 사항

Linux Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* 두 개의 코어
* 4GB의 RAM
* 443 포트(아웃바운드)

| **필수 패키지** | **설명** | **최소 버전**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 라이브러리| 2.5-12 |
|Openssl| OpenSSL 라이브러리 | 1.0(TLS 1.1 및 TLS 1.2가 지원됨)|
|Curl | cURL 웹 클라이언트 | 7.15.5|
|Python-ctypes | Python 2.x 필요 |
|PAM | 플러그형 인증 모듈|
| **선택적 패키지** | **설명** | **최소 버전**|
| PowerShell Core | PowerShell runbook을 실행 하려면 PowerShell Core를 설치 해야 합니다. 설치하는 방법을 알아보려면 [Linux에 PowerShell Core 설치](/powershell/scripting/install/installing-powershell-core-on-linux)를 참조하세요. | 6.0.0 |

## <a name="supported-linux-hardening"></a>지원 되는 Linux 강화

다음은 아직 지원 되지 않습니다.

* 들

## <a name="supported-runbook-types"></a>지원되는 Runbook 유형

Linux Hybrid Runbook Worker는 Azure Automation의 제한 된 runbook 형식 집합을 지원 하며 다음 표에 설명 되어 있습니다.

|Runbook 형식 | 지원됨 |
|-------------|-----------|
|Python 2 |예 |
|PowerShell |예<sup>1</sup> |
|PowerShell 워크플로 |예 |
|그래픽 |아니요 |
|그래픽 PowerShell 워크플로 |예 |

<sup>1</sup> PowerShell runbook을 설치 하려면 PowerShell Core가 Linux 컴퓨터에 설치 되어 있어야 합니다. 설치하는 방법을 알아보려면 [Linux에 PowerShell Core 설치](/powershell/scripting/install/installing-powershell-core-on-linux)를 참조하세요.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker 설치

Linux Hybrid Runbook Worker를 설치 하 고 구성 하려면 다음 단계를 수행 합니다.

1. Log Analytics 에이전트를 대상 컴퓨터에 배포 합니다.

    * Azure Vm의 경우 [linux 용 가상 머신 확장](../virtual-machines/extensions/oms-linux.md)을 사용 하 여 linux 용 Log Analytics 에이전트를 설치 합니다. 확장은 Azure 가상 컴퓨터에 Log Analytics 에이전트를 설치 하 고 Azure Resource Manager 템플릿이나 Azure CLI를 사용 하 여 기존 Log Analytics 작업 영역에 가상 컴퓨터를 등록 합니다. 에이전트가 설치되면 VM을 Automation 계정의 Hybrid Runbook Worker 그룹에 추가할 수 있습니다.

    * 비 Azure Vm의 경우 [Azure Monitor에 linux 컴퓨터 연결](../azure-monitor/platform/agent-linux.md) 문서에 설명 된 배포 옵션을 사용 하 여 linux 용 Log Analytics 에이전트를 설치 합니다. 여러 컴퓨터에 대해이 프로세스를 반복 하 여 환경에 여러 작업자를 추가할 수 있습니다. 에이전트가 설치 되 면 Automation 계정의 Hybrid Runbook Worker 그룹에 Vm을 추가할 수 있습니다.

    > [!NOTE]
    > DSC (필요한 상태 구성)를 사용 하 여 Hybrid Runbook Worker 역할을 지 원하는 컴퓨터의 구성을 관리 하려면 DSC 노드로 컴퓨터를 추가 해야 합니다.

    > [!NOTE]
    > Linux Hybrid Worker 설치 도중 해당 sudo 권한이 포함된 [nxautomation 계정](automation-runbook-execution.md#log-analytics-agent-for-linux)이 있어야 합니다. 작업자 설치를 시도하고 계정이 존재하지 않거나 적절한 권한이 없는 경우 설치는 실패합니다.

2. 에이전트가 작업 영역에 보고 하는지 확인 합니다.

    Linux 용 Log Analytics 에이전트는 Azure Monitor Log Analytics 작업 영역에 컴퓨터를 연결 합니다. 컴퓨터에 에이전트를 설치 하 고 작업 영역에 연결 하면 Hybrid Runbook Worker에 필요한 구성 요소가 자동으로 다운로드 됩니다.

    몇 분 후에 에이전트가 Log Analytics 작업 영역에 연결되면 다음 쿼리를 실행하여 하트비트 데이터가 작업 영역으로 전송되는지 확인할 수 있습니다.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    검색 결과에는 컴퓨터에 대 한 하트 비트 레코드가 표시 되 고이 레코드가 서비스에 연결 되 고 보고 되 고 있음을 나타냅니다. 기본적으로 모든 에이전트는 하트비트 레코드를 할당된 작업 영역으로 전달합니다.

3. 다음 명령을 실행 하 여 컴퓨터를 Hybrid Runbook Worker 그룹에 추가 하 고,, 및 매개 변수의 값을 지정 합니다 `-w` `-k` `-g` `-e` .

    매개 변수 `-k` 및 `-e` Automation 계정의 **키** 페이지에서 필요한 정보를 가져올 수 있습니다. 페이지 왼쪽의 **계정 설정** 섹션에서 **키** 를 선택 합니다.

    ![키 관리 페이지](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e`매개 변수의 경우 **URL**에 대 한 값을 복사 합니다.

    * `-k`매개 변수의 경우 **기본 액세스 키**에 대 한 값을 복사 합니다.

    * `-g`매개 변수의 경우 새 Linux Hybrid Runbook Worker가 가입 해야 하는 Hybrid Runbook Worker 그룹의 이름을 지정 합니다. 이 그룹이 Automation 계정에 이미 있으면 현재 컴퓨터가 추가 됩니다. 이 그룹이 없으면 해당 이름을 사용 하 여 만들어집니다.

    * `-w`매개 변수의 경우 Log Analytics 작업 영역 ID를 지정 합니다.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. 명령이 완료 되 면 Automation 계정의 Hybrid Worker 그룹 페이지에 새 그룹 및 멤버 수가 표시 됩니다. 기존 그룹인 경우 멤버 수가 증가합니다. Hybrid Worker 그룹 페이지의 목록에서 그룹을 선택하고 **Hybrid Worker** 타일을 선택합니다. Hybrid Worker 페이지에서 나열된 그룹의 각 멤버를 확인합니다.

    > [!NOTE]
    > Azure VM에 대 한 Linux 용 Log Analytics 가상 머신 확장을 사용 하는 경우 `autoUpgradeMinorVersion` `false` 자동 업그레이드 버전에서 Hybrid Runbook Worker 문제를 일으킬 수 있으므로를로 설정 하는 것이 좋습니다. 수동으로 확장을 업그레이드하는 방법은 [Azure CLI 배포](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)를 참조하세요.

## <a name="turn-off-signature-validation"></a>서명 유효성 검사 해제

기본적으로 Linux Hybrid Runbook Worker는 서명 유효성 검사를 요구합니다. 작업자에 대한 서명되지 않은 Runbook을 실행하는 경우 `Signature validation failed` 오류가 표시됩니다. 서명 유효성 검사를 해제하려면 다음 명령을 실행합니다. 두 번째 매개 변수를 Log Analytics 작업 영역 ID로 바꿉니다.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>온-프레미스 Linux 컴퓨터에서 Hybrid Runbook Worker 제거

Hybrid Runbook Worker에서 `ls /var/opt/microsoft/omsagent` 명령을 사용하여 작업 영역 ID를 가져올 수 있습니다. 작업 영역 ID를 사용하여 이름이 지정된 폴더가 만들어집니다.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 이 스크립트는 컴퓨터에서 Linux 용 Log Analytics 에이전트를 제거 하지 않습니다. Hybrid Runbook Worker 역할의 기능 및 구성만 제거합니다.

## <a name="remove-a-hybrid-worker-group"></a>Hybrid Worker 그룹 제거

Linux 컴퓨터의 Hybrid Runbook Worker 그룹을 제거 하려면 Windows Hybrid Worker 그룹의 경우와 동일한 단계를 사용 합니다. [Hybrid Worker 그룹 제거](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결 - Linux](troubleshoot/hybrid-runbook-worker.md#linux)를 참조하세요.
