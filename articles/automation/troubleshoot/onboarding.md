---
title: Azure Automation 관리 솔루션 온 보 딩 문제 해결
description: Azure Automation 솔루션 등록 오류 문제를 해결 하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 371094ecba5168fd32a7af9fb81a71eb722efc91
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836532"
---
# <a name="troubleshoot-solution-onboarding"></a>솔루션 온 보 딩 문제 해결

Azure Automation 업데이트 관리 솔루션 또는 변경 내용 추적 및 인벤토리 솔루션을 등록할 때 오류 메시지가 표시 될 수 있습니다. 이 문서에서는 발생할 수 있는 다양 한 오류와 해결 방법을 설명 합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>시나리오: 등록 된 노드의 이름을 바꾸려면 등록을 취소 하거나 다시 등록 해야 합니다.

#### <a name="issue"></a>문제

노드가 Azure Automation 등록 된 다음 운영 체제 컴퓨터 이름이 변경 됩니다. 노드의 보고서는 원래 이름을 사용 하 여 계속 표시 됩니다.

#### <a name="cause"></a>원인

등록 된 노드의 이름을 변경 해도 Azure Automation의 노드 이름은 업데이트 되지 않습니다.

#### <a name="resolution"></a>해결 방법

Azure Automation 상태 구성에서 노드를 등록 취소 한 후 다시 등록 하십시오. 이 시간 전에 서비스에 게시 된 보고서는 더 이상 사용할 수 없습니다.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>시나리오: HTTPS 프록시를 통해 인증서를 다시 서명 하는 것이 지원 되지 않음

#### <a name="issue"></a>문제

HTTPS 트래픽을 종료 하 고 새 인증서를 사용 하 여 트래픽을 다시 암호화 하는 프록시 솔루션을 통해 연결 하는 경우 서비스에서 연결을 허용 하지 않습니다.

#### <a name="cause"></a>원인

Azure Automation는 트래픽을 암호화 하는 데 사용 되는 인증서 다시 서명을 지원 하지 않습니다.

#### <a name="resolution"></a>해결 방법

현재이 문제에 대 한 해결 방법이 없습니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-onboarding-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>시나리오: "솔루션을 사용 하도록 설정할 수 없습니다." 라는 메시지와 함께 온 보 딩 실패

#### <a name="issue"></a>문제

VM을 솔루션에 등록 하려고 할 때 다음 메시지 중 하나가 표시 됩니다.

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>원인

이 오류는 VM 또는 작업 영역에 대 한 권한이 잘못 되었거나 없거나 사용자에 게 발생 한 경우에 발생 합니다.

#### <a name="resolution"></a>해결 방법

컴퓨터를 등록 하는 [데 필요한 올바른 권한이](../automation-role-based-access-control.md#onboarding-permissions)있는지 확인 한 다음 솔루션을 다시 등록 하십시오. 오류 메시지가 `The solution cannot be enabled on this VM because the permission to read the workspace is missing`표시 되 면 VM이 작업 영역에 등록 여부 `Microsoft.OperationalInsights/workspaces/read` 를 확인할 수 있는 권한이 있는지 확인 합니다.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>시나리오: "진단 로깅을 위한 자동화 계정을 구성 하지 못했습니다." 라는 메시지와 함께 온 보 딩 실패

#### <a name="issue"></a>문제

VM을 솔루션에 등록 하려고 할 때 다음 메시지가 표시 됩니다.

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>원인

가격 책정 계층이 구독의 청구 모델과 일치 하지 않는 경우이 오류가 발생할 수 있습니다. 자세한 내용은 [Azure Monitor의 사용량 및 예상 비용 모니터링](https://aka.ms/PricingTierWarning)을 참조 하세요.

#### <a name="resolution"></a>해결 방법

수동으로 Log Analytics 작업 영역을 만들고 온 보 딩 프로세스를 반복 하 여 만든 작업 영역을 선택 합니다.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>시나리오: ComputerGroupQueryFormatError

#### <a name="issue"></a>문제

이 오류 코드는 솔루션을 대상으로 하는 데 사용 되는 저장 된 검색 컴퓨터 그룹 쿼리가 올바르게 포맷 되지 않았음을 의미 합니다. 

#### <a name="cause"></a>원인

쿼리가 변경 되었거나 시스템이 변경 했을 수 있습니다.

#### <a name="resolution"></a>해결 방법

솔루션에 대 한 쿼리를 삭제 한 다음 다시 솔루션을 다시 등록할 수 있습니다. 그러면 쿼리를 다시 만듭니다. **저장 된 검색**의 작업 영역에서 쿼리를 찾을 수 있습니다. 쿼리 이름은 **MicrosoftDefaultComputerGroup**이 고 쿼리 범주는 연결 된 솔루션의 이름입니다. 여러 솔루션을 사용 하는 경우 **MicrosoftDefaultComputerGroup** 쿼리는 **저장 된 검색**에서 여러 번 표시 됩니다.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>시나리오: PolicyViolation

#### <a name="issue"></a>문제

이 오류 코드는 하나 이상의 정책 위반으로 인해 배포가 실패 했음을 나타냅니다.

#### <a name="cause"></a>원인 

정책에서 작업이 완료 되는 것을 차단 하는 중입니다.

#### <a name="resolution"></a>해결 방법

솔루션을 성공적으로 배포 하려면 표시 된 정책을 변경 하는 것을 고려해 야 합니다. 정의할 수 있는 여러 가지 유형의 정책이 있으므로 위반 되는 정책에 따라 변경 해야 합니다. 예를 들어 일부 포함 된 리소스의 콘텐츠를 변경 하는 권한을 거부 하는 리소스 그룹에 정책을 정의 하는 경우 다음 수정 사항 중 하나를 선택할 수 있습니다.

* 정책을 모두 제거합니다.
* 다른 리소스 그룹에 솔루션을 등록 해 봅니다.
* 정책의 대상을 특정 리소스 (예: Automation 계정)로 변경 합니다.
* 정책에서 거부 하도록 구성 된 리소스 집합을 수정 합니다.

Azure Portal의 오른쪽 위 모퉁이에 있는 알림을 확인 하거나 Automation 계정이 포함 된 리소스 그룹으로 이동한 다음 **설정** 에서 **배포** 를 선택 하 여 실패 한 배포를 확인 합니다. Azure Policy에 대 한 자세한 내용은 [Azure Policy 개요](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)를 참조 하세요.

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>시나리오: 작업 영역의 연결을 끊는 동안 오류 발생

#### <a name="issue"></a>문제

작업 영역의 연결을 해제 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>원인

이 오류는 연결 된 Log Analytics 작업 영역 및 Automation 계정에 종속 된 Log Analytics 작업 영역에서 솔루션을 활성 상태로 유지 하는 경우에 발생 합니다.

### <a name="resolution"></a>해결 방법

사용 중인 경우 작업 영역에서 다음 솔루션을 제거 합니다.

* 업데이트 관리
* 변경 내용 추적 및 인벤토리
* 작업이 없는 동안 VM 시작/중지

솔루션을 제거한 후 작업 영역의 연결을 끊을 수 있습니다. 작업 영역 및 Automation 계정에서 이러한 솔루션의 기존 아티팩트를 정리 하는 것이 중요 합니다.

* 업데이트 관리의 경우 Automation 계정에서 **업데이트 배포 (일정)** 를 제거 합니다.
* 작업 시간 외 VM 시작/중지의 경우 **설정** > **잠금**에서 Automation 계정의 솔루션 구성 요소에 대 한 잠금을 제거 합니다. 자세한 내용은 [작업 시간 외 VM 시작/중지 솔루션 제거](../automation-solution-vm-management.md#remove-the-solution)를 참조 하세요.

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows 확장 오류에 대 한 Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Windows 확장에 대 한 Log Analytics 에이전트 설치는 여러 가지 이유로 실패할 수 있습니다. 다음 섹션에서는 Windows 확장용 Log Analytics 에이전트를 배포 하는 동안 오류를 발생 시킬 수 있는 온 보 딩 문제에 대해 설명 합니다.

>[!NOTE]
>Log Analytics agent for Windows는 Microsoft Monitoring Agent (MMA)에 대해 현재 Azure Automation에서 사용 되는 이름입니다.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>시나리오: WebClient 요청 중 예외가 발생했습니다.

VM의 Windows 확장에 대 한 Log Analytics는 외부 리소스와 통신할 수 없으며 배포가 실패 합니다.

#### <a name="issue"></a>문제

다음은 반환되는 오류 메시지의 예입니다.

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>원인

이 오류는 다음과 같은 경우에 발생할 수 있습니다.

* VM에 구성 된 프록시는 특정 포트만 허용 합니다.
* 방화벽 설정이 필요한 포트 및 주소에 대한 액세스를 차단했습니다.

#### <a name="resolution"></a>해결 방법

통신을 위해 적절한 포트와 주소가 열려 있는지 확인합니다. 포트 및 주소 목록은 [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조 하세요.

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>시나리오: 일시적인 환경 문제로 인해 설치 하지 못했습니다.

설치를 차단 하는 다른 설치 또는 작업으로 인해 배포 중에 Windows 확장에 대 한 Log Analytics를 설치 하지 못했습니다.

#### <a name="issue"></a>문제

반환 될 수 있는 오류 메시지의 예는 다음과 같습니다.

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>원인

이 오류는 다음과 같은 경우에 발생할 수 있습니다.

* 다른 설치가 진행 중입니다.
* 시스템은 템플릿 배포 중에 다시 부팅 하도록 트리거됩니다.

#### <a name="resolution"></a>해결 방법

이 오류는 본질적으로 일시적입니다. 배포를 다시 시도하여 확장 프로그램을 설치하십시오.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>시나리오: 설치 시간 제한

시간 초과로 인해 Windows 확장에 대 한 Log Analytics 설치가 완료 되지 않았습니다.

#### <a name="issue"></a>문제

반환 될 수 있는 오류 메시지의 예는 다음과 같습니다.

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>원인

이 유형의 오류는 설치 하는 동안 VM이 과도 하 게 로드 되기 때문에 발생 합니다.

### <a name="resolution"></a>해결 방법

VM의 부하가 낮은 경우 Windows 용 Log Analytics 에이전트 확장을 설치 해 보세요.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 추가 지원을 위해 다음 채널 중 하나를 시도해 보세요.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* 사용자 환경을 [@AzureSupport](https://twitter.com/azuresupport)개선 하기 위한 공식 Microsoft Azure 계정인를 사용 하 여 연결 하세요. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에 게 연결 합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동 하 여 **지원 받기**를 선택 합니다.
