---
title: 업데이트 관리 오류 문제 해결
description: 업데이트 관리 문제 해결 방법 살펴보기
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9bcc871ecc9413f02545e6aec4caa6342d563b44
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474577"
---
# <a name="troubleshooting-issues-with-update-management"></a>업데이트 관리 문제 해결

이 문서에서는 업데이트 관리 사용 중에 발생할 수 있는 문제 해결을 위한 솔루션을 살펴봅니다.

기본 문제를 확인하기 위한 Hybrid Worker 에이전트용 에이전트 문제 해결사가 제공됩니다. 이 문제 해결사에 대한 자세한 내용은 [업데이트 에이전트 문제 해결](update-agent-issues.md)을 참조하세요. 다른 모든 문제의 경우 아래에서 가능한 문제에 대한 추가 정보를 참조하세요.

## <a name="general"></a>일반

### <a name="components-enabled-not-working"></a>시나리오: ‘업데이트 관리’ 솔루션의 구성 요소를 사용하도록 설정되었으며, 이제 이 가상 머신을 구성 중입니다.

#### <a name="issue"></a>문제

온보딩 후 15분이 지났는데도 가상 머신에 다음 메시지가 계속 표시됩니다.

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>원인

이 오류는 다음과 같은 원인으로 인해 발생할 수 있습니다.

1. Automation 계정과의 통신이 차단되었습니다.
2. 온보딩 중인 VM이 현재 설치된 Microsoft Monitoring Agent를 사용하여 sysprep 되지 않은 머신에서 복제된 것일 수 있습니다.

#### <a name="resolution"></a>해결 방법

1. [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 방문하여 업데이트 관리가 작동하려면 어떤 주소 및 포트를 허용해야 하는지 알아보세요.
2. 복제된 이미지를 사용하는 경우:
   1. Log Analytics 작업 영역의 범위 구성에 대 한 저장된 된 검색에서 VM을 제거할 `MicrosoftDefaultScopeConfig-Updates` 표시 됩니다. 저장된 검색은 작업 영역의 **일반**에서 찾을 수 있습니다.
   2. `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` 실행
   3. `Restart-Service HealthService`를 실행하여 `HealthService`를 다시 시작합니다. 그러면 키가 다시 생성되고 새 UUID가 생성됩니다.
   4. 이 작동 하지 않으면 sysprep 이미지 첫 번째 사후 MMA 에이전트를 설치 합니다.

### <a name="multi-tenant"></a>시나리오: 다른 Azure 테넌트에 있는 컴퓨터의 업데이트 배포를 만들 때 연결된 구독 오류가 발생합니다.

#### <a name="issue"></a>문제

다른 Azure 테넌트에 있는 컴퓨터의 업데이트 배포를 만들려고 하면 다음 오류가 발생합니다.

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>원인

이 오류는 업데이트 배포에 포함된 다른 테넌트에 Azure Virtual Machines가 있는 업데이트 배포를 만들 때 발생합니다.

#### <a name="resolution"></a>해결 방법

예약하려면 해결 방법을 사용해야 합니다. `-ForUpdate` 스위치와 함께 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용하여 일정을 만들고 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet을 사용한 후 다른 테넌트의 머신을 `-NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제는 이 작업을 수행하는 방법의 예를 보여 줍니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>시나리오: 컴퓨터는 업데이트 관리 포털에서 표시 되지 않습니다.

#### <a name="issue"></a>문제

다음 시나리오에서 실행할 수 있습니다.

* 사용자 컴퓨터 표시 **구성 되지 않은** VM의 업데이트 관리 보기에서

* Automation 계정 업데이트 관리 보기에서 누락 된 컴퓨터

* 로 표시 되는 컴퓨터가 **평가 되지 않음** 아래에서 **준수**, Hybrid Runbook Worker 하지만 하지 업데이트 관리에 대 한 Azure Monitor 로그에서 하트 비트 데이터를 표시 하지만 합니다.

#### <a name="cause"></a>원인

이 잠재적인 로컬 구성 문제 또는 잘못 구성 된 범위 구성에 의해 발생할 수 있습니다.

Hybrid Runbook Worker를 다시 등록하고 다시 설치해야 할 수 있습니다.

할당량에 도달 하 고 중지 데이터로 저장 된 작업 영역에서 정의한 수 있습니다.

#### <a name="resolution"></a>해결 방법

* 컴퓨터에 올바른 작업 영역에 보고를 확인 합니다. 컴퓨터에 보고 하는 어떤 작업 영역을 확인 합니다. 이 확인 하는 방법에 지침은 [Log Analytics에 대 한 에이전트 연결 확인](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)합니다. 그런 다음 Azure Automation 계정에 연결 된 작업 영역 인지 확인 합니다. 이 확인 하려면 Automation 계정으로 이동 하 고 클릭 **연결 된 작업 영역** 아래에서 **관련 된 리소스**합니다.

* Log Analytics 작업 영역에서 컴퓨터를 표시 하는지 확인 합니다. Automation 계정에 연결 된 Log Analytics 작업 영역에서 다음 쿼리를 실행 합니다. 쿼리 결과에서 컴퓨터에 표시 되지 않으면 컴퓨터 하트 가능성이 있는 로컬 구성 문제가 있다는 의미는 아닙니다. 에 대 한 문제 해결사를 실행할 수 있습니다 [Windows](update-agent-issues.md#troubleshoot-offline) 하거나 [Linux](update-agent-issues-linux.md#troubleshoot-offline) 수는 OS에 따라 [에이전트를 다시 설치](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)합니다. 컴퓨터에 쿼리 결과에 표시를 매우 해야 할 경우 다음 글머리 기호에서 지정 된 범위 구성 합니다.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* 범위 구성 문제를 확인 합니다. [범위 구성을](../automation-onboard-solutions-from-automation-account.md#scope-configuration) 어떤 컴퓨터 솔루션에 대 한 구성를 결정 합니다. 컴퓨터 작업 영역에 표시 되는지 아닌 경우 컴퓨터를 대상으로 범위 구성을 구성 해야 합니다 있습니다 표시 합니다. 이 작업을 수행 하는 방법에 알아보려면 참조 [작업 영역에서 컴퓨터 등록](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)합니다.

* 위의 단계에는 문제가 해결 되지 않으면의 단계에 따라 [Windows Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md) 하이브리드 작업자에 대 한 Windows를 다시 설치 또는 [Linux Hybrid Runbook Worker 배포](../automation-linux-hrw-install.md) Linux에 대 한 합니다.

* 작업 영역에서 다음 쿼리를 실행 합니다. 결과 표시 하는 경우 `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` 할당량에 도달 했으며 데이터 저장을 중지 하는 작업 영역에서 정의 해야 합니다. 작업 영역으로 이동 **사용량 및 예상된 비용** > **데이터 볼륨 관리** 및 할당량을 확인 또는 있는 할당량을 제거 합니다.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

가상 머신에 솔루션을 온보드할 때 문제가 발생한 경우 로컬 컴퓨터에서 이벤트 ID가 **4502**인 이벤트에 대해 **애플리케이션 및 서비스 로그**의 **작업 관리자** 이벤트 로그와, **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**를 포함하고 있는 이벤트 메시지를 확인합니다.

다음 섹션에는 특정 오류 메시지 및 각각의 해결 방법이 설명되어 있습니다. 다른 온보드 문제는 [온보드 문제 해결 솔루션](onboarding.md)을 참조하세요.

### <a name="machine-already-registered"></a>시나리오: 컴퓨터가 이미 다른 계정에 등록되어 있습니다.

#### <a name="issue"></a>문제

다음과 같은 오류 메시지가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>원인

컴퓨터가 이미 업데이트 관리를 위한 다른 작업 영역에 등록되었습니다.

#### <a name="resolution"></a>해결 방법

컴퓨터에서 [Hybrid Runbook 그룹을 삭제](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)하여 오래된 아티팩트를 정리하고 다시 시도합니다.

### <a name="machine-unable-to-communicate"></a>시나리오: 컴퓨터가 서비스와 통신할 수 없습니다.

#### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>원인

네트워크 통신을 차단하는 프록시, 게이트웨이 또는 방화벽이 있을 수 있습니다.

#### <a name="resolution"></a>해결 방법

네트워크를 검토하고 적합한 포트 및 주소가 허용되었는지 확인합니다. 업데이트 관리 및 Hybrid Runbook Worker에 필요한 포트 및 주소 목록은 [네트워크 요구 사항](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="unable-to-create-selfsigned-cert"></a>시나리오: 자체 서명된 인증서를 만들 수 없습니다.

#### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>원인

Hybrid Runbook Worker가 자체 서명 인증서를 만들지 못함

#### <a name="resolution"></a>해결 방법

시스템 계정에 **C:\ProgramData\Microsoft\Crypto\RSA** 폴더에 대한 읽기 액세스 권한이 있는지 확인하고 다시 시도합니다.

### <a name="failed-to-start"></a>시나리오: 컴퓨터에서 업데이트 배포를 시작 하려면 실패를 보여 줍니다.

#### <a name="issue"></a>문제

컴퓨터 상태가 **시작 하지 못했습니다** 컴퓨터용입니다. 컴퓨터에 대 한 특정 세부 정보를 볼 때 다음 오류가 표시:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>원인

이 오류는 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 컴퓨터는 더 이상 존재 하지 않습니다.
* 해제 하 고 연결할 수 없는 컴퓨터 설정 되어 있습니다.
* 컴퓨터에 네트워크 연결 문제 이며 hybrid worker 컴퓨터에 연결할 수 없습니다.
* SourceComputerId 변경 된 Microsoft Monitoring Agent로 업데이트가 했습니다.
* Automation 계정에서 2,000 동시 작업 제한에 도달한 경우 업데이트 실행에 제한 된 수 있습니다. 각 배포는 작업 및 각 컴퓨터에 업데이트 배포 수 작업으로 간주 됩니다. 다른 자동화 작업 또는 업데이트 배포 하면 Automation 계정 수가 동시 작업 제한에 대해 현재 실행 중인 합니다.

#### <a name="resolution"></a>해결 방법

때 사용 하 여 해당 [동적 그룹](../automation-update-management.md#using-dynamic-groups) 업데이트 배포에 대 한 합니다.

* 컴퓨터를 계속 유지 되 고 연결할 수를 확인 합니다. 존재 하지 않는 경우에 배포를 편집 하 고 컴퓨터를 제거 합니다.
* 섹션을 참조 하세요 [네트워크 계획](../automation-update-management.md#ports) 목록은 한 포트 및 주소는 업데이트 관리를 위해 필요 하 고 컴퓨터에 이러한 요구 사항을 충족 하는지 확인 합니다.
* Log Analytics에서 다음 쿼리를 실행 합니다. 찾으려는 컴퓨터 환경의 갖는 `SourceComputerId` 변경 합니다. 동일한 컴퓨터를 검색할 `Computer` 값을 다른 `SourceComputerId` 값입니다. 이러한 컴퓨터를 대상으로 하거나 제거 하 고 컴퓨터를 다시 추가 하는 업데이트 배포를 편집 해야 영향을 받는 컴퓨터를 찾으면 되므로 `SourceComputerId` 올바른 값을 반영 합니다.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>시나리오: 컴퓨터가 평가되지 않음으로 표시되고 HResult 예외를 나타냅니다.

#### <a name="issue"></a>문제

컴퓨터가 **규정 준수**에서 **평가되지 않음**으로 표시되며 그 아래에 예외 메시지가 표시됩니다.

#### <a name="cause"></a>원인

Windows 업데이트 또는 WSUS가 컴퓨터에서 올바르게 구성되지 않았습니다. 업데이트 관리는 Windows 업데이트 또는 WSUS를 사용하여 필요한 업데이트, 패치의 상태 및 배포된 패치의 결과를 제공합니다. 이 정보가 없으면 업데이트 관리에서 필요하거나 설치된 패치를 제대로 보고할 수 없습니다.

#### <a name="resolution"></a>해결 방법

전체 예외 메시지를 보려면 빨간색으로 표시된 예외를 두 번 클릭합니다. 다음 표에서 잠재적인 해결 방법이나 수행할 작업을 확인합니다.

|예외  |해결 방법 또는 작업  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | [Windows 업데이트 오류 코드 목록](https://support.microsoft.com/help/938205/windows-update-error-code-list)에서 관련 오류 코드를 검색하여 예외의 원인에 대한 추가 세부 정보를 찾을 수 있습니다.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 이러한 오류는 네트워크 연결 문제입니다. 컴퓨터가 네트워크를 통해 업데이트 관리에 적절히 연결되어 있는지 확인합니다. 필요한 주소 및 포트 목록을 보려면 [네트워크 계획](../automation-update-management.md#ports) 관련 섹션을 참조하세요.        |
|`0x8024001E`| 서비스 또는 시스템이 종료 때문에 업데이트 작업이 완료 되지 않았습니다.|
|`0x8024002E`| Windows Update 서비스가 비활성화 됩니다.|
|`0x8024402C`     | WSUS 서버를 사용하는 경우 레지스트리 키 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 아래에 있는 `WUServer` 및 `WUStatusServer`의 레지스트리 값에 올바른 WSUS 서버가 포함되어 있는지 확인합니다.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows 업데이트 서비스(wuauserv)가 실행되고 있으며 사용 안 함으로 설정되어 있지 않은지 확인합니다.        |
|다른 제네릭 예외     | 인터넷에서 가능한 해결 방법을 검색하고 현지 IT 지원 팀과 함께 해결합니다.         |

검토를 `windowsupdate.log` 여 가능한 원인을 확인 하려고 하는 데 도움이 됩니다. 로그를 읽는 방법에 대 한 자세한 내용은 참조 하세요. [Windowsupdate.log 파일을 읽는 방법](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)합니다.

또한 [Windows 업데이트 문제 해결사](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)를 다운로드한 후 실행하여 컴퓨터의 Windows 업데이트에 문제가 있는지 확인할 수 있습니다.

> [!NOTE]
> [Windows 업데이트 문제 해결사](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)는 Windows 클라이언트용이지만 Windows 서버에도 작동한다는 사실을 설명합니다.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>시나리오: 업데이트 실행을 시작하지 못했습니다.

#### <a name="issue"></a>문제

Linux 컴퓨터에서 업데이트 실행을 시작하지 못합니다.

#### <a name="cause"></a>원인

Linux Hybrid Worker가 비정상 상태입니다.

#### <a name="resolution"></a>해결 방법

다음 로그 파일의 사본을 작성하여 문제 해결 목적으로 보존합니다.

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>시나리오: 업데이트 실행이 시작되지만 오류가 발생합니다.

#### <a name="issue"></a>문제

업데이트 실행은 시작되지만 실행 중 오류가 발생합니다.

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* 패키지 관리자가 비정상 상태임
* 특정 패키지가 클라우드 기반 패치를 방해할 수 있음
* 기타 원인

#### <a name="resolution"></a>해결 방법

Linux에서 업데이트 실행이 제대로 시작된 뒤 업데이트 실행 중에 실패할 경우 실행에서 해당 컴퓨터의 ㅈ가업 출력을 확인합니다. 컴퓨터의 패키지 관리자에서 특정 오류 메시지를 찾아 조치를 취할 수 있습니다. 업데이트 관리에서 업데이트 배포에 성공하려면 패키지 관리자가 정상 상태여야 합니다.

일부 경우 패키지 업데이트가 업데이트 관리를 차단하여 업데이트 배포가 완료되지 못할 수 있습니다. 이 경우 이 패키지를 향후 업데이트 실행에서 제외하거나 직접 수동으로 설치합니다.

패치 문제를 해결할 수 없는 경우 문제 해결을 위해 다음 업데이트 배포가 시작되기 **전에** 다음 로그 파일의 사본을 작성하여 보관합니다.

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
