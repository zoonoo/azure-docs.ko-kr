---
title: Azure 자동화 관리 솔루션의 온보딩 문제 해결
description: 솔루션 온보딩 오류 문제를 해결하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679227"
---
# <a name="troubleshoot-solution-onboarding"></a>온보딩 솔루션 문제 해결

업데이트 관리 솔루션 또는 변경 추적 및 인벤토리 솔루션을 온보딩할 때 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이를 해결하는 방법에 대해 설명합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>시나리오: 등록된 노드의 이름을 바꾸려면 등록취소 또는 다시 등록해야 합니다.

#### <a name="issue"></a>문제

노드가 Azure 자동화에 등록된 다음 운영 체제 컴퓨터 이름이 변경됩니다. 노드의 보고서는 원래 이름으로 계속 표시됩니다.

#### <a name="cause"></a>원인

등록된 노드의 이름을 바꾸면 Azure Automation에서 노드 이름이 업데이트되지 않습니다.

#### <a name="resolution"></a>해결 방법

Azure 자동화 상태 구성에서 노드등록을 취소한 다음 다시 등록합니다. 해당 시간 이전에 서비스에 게시된 보고서는 더 이상 사용할 수 없습니다.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>시나리오: https 프록시를 통해 인증서에 다시 서명이 지원되지 않습니다.

#### <a name="issue"></a>문제

HTTPS 트래픽을 종료한 다음 새 인증서를 사용하여 트래픽을 다시 암호화하는 프록시 솔루션을 통해 연결할 때 서비스는 연결을 허용하지 않습니다.

#### <a name="cause"></a>원인

Azure Automation은 트래픽을 암호화하는 데 사용되는 인증서재서명을 지원하지 않습니다.

#### <a name="resolution"></a>해결 방법

현재 이 문제에 대한 해결 방법은 없습니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>시나리오: 메시지와 함께 온보딩 실패 - 솔루션을 사용할 수 없습니다.

#### <a name="issue"></a>문제

솔루션에 VM을 온보데 하려고 하면 다음 메시지 중 하나가 수신됩니다.

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>원인

이 오류는 VM 또는 작업 영역 또는 사용자에 대한 잘못된 사용 권한이 잘못되었거나 누락되어 발생합니다.

#### <a name="resolution"></a>해결 방법

[컴퓨터를 온보온한](../automation-role-based-access-control.md#onboarding-permissions) 다음 솔루션을 다시 온보온해 보십시오. 오류가 `The solution cannot be enabled on this VM because the permission to read the workspace is missing`발생하면 VM이 작업 영역에 `Microsoft.OperationalInsights/workspaces/read` 온보온되어 있는지 확인할 수 있는 권한이 있는지 확인합니다.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>시나리오: 메시지와 함께 온보딩 실패: 진단 로깅에 대 한 자동화 계정을 구성 하지 못했습니다.

#### <a name="issue"></a>문제

솔루션에 VM을 온보데 하려고 하면 다음 메시지가 나타납니다.

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>원인

이 오류는 가격 책정 계층이 구독의 청구 모델과 일치하지 않는 경우에 발생할 수 있습니다. [Azure 모니터의 사용량 및 예상 비용 모니터링을](https://aka.ms/PricingTierWarning)참조하십시오.

#### <a name="resolution"></a>해결 방법

Log Analytics 작업 영역을 수동으로 만들고 온보딩 프로세스를 반복하여 생성된 작업 영역을 선택합니다.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>시나리오: ComputerGroupQueryFormatError

#### <a name="issue"></a>문제

이 오류 코드는 솔루션을 대상으로 하는 데 사용된 저장된 검색 컴퓨터 그룹 쿼리가 올바르게 포맷되지 않음을 의미합니다. 

#### <a name="cause"></a>원인

쿼리를 변경했거나 시스템에서 쿼리를 변경했을 수 있습니다.

#### <a name="resolution"></a>해결 방법

솔루션에 대한 쿼리를 삭제한 다음 솔루션을 다시 온보데 하여 쿼리를 다시 만들 수 있습니다. 쿼리는 **저장된 검색에서**작업 영역에서 찾을 수 있습니다. 쿼리 의 이름은 **MicrosoftDefaultComputerGroup이며**쿼리 범주는 연결된 솔루션의 이름입니다. 여러 솔루션을 사용하도록 설정한 경우 **MicrosoftDefaultComputerGroup** 쿼리는 **저장된 검색**에서 여러 번 표시됩니다.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>시나리오: PolicyViolation

#### <a name="issue"></a>문제

이 오류 코드는 하나 이상의 정책 위반으로 인해 배포가 실패했음을 나타냅니다.

#### <a name="cause"></a>원인 

정책이 작업을 완료하지 못하도록 차단하고 있습니다.

#### <a name="resolution"></a>해결 방법

솔루션을 성공적으로 배포하려면 표시된 정책을 변경하는 것이 좋습니다. 정의할 수 있는 다양한 유형의 정책이 있기 때문에 필요한 변경 사항은 위반된 정책에 따라 다릅니다. 예를 들어 포함된 일부 리소스의 내용을 변경할 수 있는 권한을 거부하는 리소스 그룹에 정책이 정의된 경우 다음 수정 사항 중 하나를 선택할 수 있습니다.

* 정책을 모두 제거합니다.
* 솔루션을 다른 리소스 그룹에 온보로 보온해 보십시오.
* 자동화 계정과 같은 특정 리소스에 정책을 다시 대상으로 지정합니다.
* 정책이 거부하도록 구성된 리소스 집합을 수정합니다.

Azure 포털의 오른쪽 상단 모서리에 있는 알림을 확인하거나 자동화 계정이 포함된 리소스 그룹으로 이동한 다음 **설정에서** 배포를 선택하여 실패한 **배포를** 봅니다. Azure 정책에 대한 자세한 내용은 [Azure 정책 개요를](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)참조하십시오.

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>시나리오: 작업 영역의 연결을 해제하려는 오류

#### <a name="issue"></a>문제

작업 영역의 연결을 해제하려고 할 때 다음과 같은 오류가 발생합니다.

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>원인

이 오류는 자동화 계정 및 Log Analytics 작업 영역에 종속된 로그 분석 작업 영역에 여전히 활성 솔루션이 연결되어 있을 때 발생합니다.

### <a name="resolution"></a>해결 방법

다음 솔루션을 사용하는 경우 작업 영역에서 다음 솔루션을 제거합니다.

* 업데이트 관리
* 변경 내용 추적 및 인벤토리
* 작업이 없는 동안 VM 시작/중지

솔루션을 제거하면 작업 영역의 연결을 해제할 수 있습니다. 작업 영역 및 자동화 계정에서 이러한 솔루션의 기존 아티팩트를 정리하는 것이 중요합니다. 

* 업데이트 관리의 경우 자동화 계정에서 업데이트 배포(일정)를 제거합니다.
* 오프 시간 동안 VM 시작/중지의 경우 **설정** > **잠금**에서 자동화 계정의 솔루션 구성 요소에 대한 잠금을 제거합니다. [영업외 솔루션 중에 VM 시작/중지 를](../automation-solution-vm-management.md#remove-the-solution)참조하십시오.

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows 확장 실패에 대한 로그 분석

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Windows 확장을 위한 로그 분석 에이전트의 설치는 여러 가지 이유로 실패할 수 있습니다. 다음 섹션에서는 Windows 확장을 위한 Log Analytics 에이전트를 배포하는 동안 오류가 발생할 수 있는 온보딩 문제에 대해 설명합니다.

>[!NOTE]
>Windows용 로그 분석 에이전트는 현재 MMA(Microsoft 모니터링 에이전트)에 대한 Azure 자동화에서 사용되는 이름입니다.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>시나리오: WebClient 요청 중 예외가 발생했습니다.

VM의 Windows 용 로그 분석은 외부 리소스와 통신할 수 없으며 배포가 실패합니다.

#### <a name="issue"></a>문제

다음은 반환되는 오류 메시지의 예입니다.

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>원인

이러한 오류의 잠재적인 원인:

* VM에 구성된 프록시는 특정 포트만 허용합니다.
* 방화벽 설정이 필요한 포트 및 주소에 대한 액세스를 차단했습니다.

#### <a name="resolution"></a>해결 방법

통신을 위해 적절한 포트와 주소가 열려 있는지 확인합니다. 포트와 주소 목록은 [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>시나리오: 일시적인 환경 문제로 인해 설치실패

설치를 차단하는 다른 설치 또는 작업으로 인해 배포 중에 Windows 용 로그 분석 설치가 실패했습니다.

#### <a name="issue"></a>문제

다음은 반환될 수 있는 오류 메시지의 예입니다.

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

이러한 오류의 잠재적인 원인:

* 다른 설치가 진행 중입니다.
* 시스템은 템플릿 배포 중에 다시 부팅하도록 트리거됩니다.

#### <a name="resolution"></a>해결 방법

이 오류는 본질적으로 일시적입니다. 배포를 다시 시도하여 확장 프로그램을 설치하십시오.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>시나리오: 설치 시간 제한

시간 시간 때문에 Windows 용 로그 분석 설치가 완료되지 않았습니다.

#### <a name="issue"></a>문제

다음은 반환될 수 있는 오류 메시지의 예입니다.

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>원인

VM이 설치 중에 과부하가 발생했기 때문에 이러한 유형의 오류가 발생합니다.

### <a name="resolution"></a>해결 방법

VM이 부하가 낮은 경우 Windows 확장을 위한 로그 분석 에이전트를 설치해 보십시오.

## <a name="next-steps"></a>다음 단계

위에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 시도해 보십시오.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* [@AzureSupport](https://twitter.com/azuresupport)Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
