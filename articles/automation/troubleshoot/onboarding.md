---
title: Azure 자동화 관리 솔루션 온보딩 문제 해결
description: 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션에 관한 온보딩 오류 문제를 해결하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536967"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>업데이트 관리, 변경 정보 추적 및 인벤토리온보딩 시 오류 문제 해결

업데이트 관리나 변경 내용 추적 및 인벤토리와 같은 솔루션을 온보딩할 때 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이것을 해결하는 방법을 설명합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>시나리오: 등록된 노드의 이름을 바꾸려면 등록 취소/다시 등록해야 합니다.

#### <a name="issue"></a>문제

노드가 Azure 자동화에 등록된 다음 운영 체제 컴퓨터 이름이 변경됩니다.  노드의 보고서는 원래 이름으로 계속 표시됩니다.

#### <a name="cause"></a>원인

등록된 노드의 이름을 바꾸면 Azure Automation에서 노드 이름이 업데이트되지 않습니다.

#### <a name="resolution"></a>해결 방법

Azure 자동화 상태 구성에서 노드등록을 취소한 다음 다시 등록합니다.  해당 시간 이전에 서비스에 게시된 보고서는 더 이상 사용할 수 없습니다.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>시나리오: https 프록시를 통해 인증서에 다시 서명이 지원되지 않습니다.

#### <a name="issue"></a>문제

고객은 https 트래픽을 종료한 다음 새 인증서를 사용하여 트래픽을 다시 암호화하는 프록시 솔루션을 통해 연결할 때 서비스가 연결을 허용하지 않는다고 보고했습니다.

#### <a name="cause"></a>원인

Azure Automation은 트래픽을 암호화하는 데 사용되는 인증서재서명을 지원하지 않습니다.

#### <a name="resolution"></a>해결 방법

이 문제에 대한 해결 방법이 없습니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>시나리오: 메시지와 함께 온보딩 실패 - 솔루션을 사용할 수 없습니다.

#### <a name="issue"></a>문제

솔루션에 가상 컴퓨터를 온보로 보전하려고 할 때 다음 메시지 중 하나가 수신됩니다.

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>원인

이 오류는 가상 컴퓨터, 작업 영역 또는 사용자에 대한 사용 권한이 잘못되었거나 누락되어 발생합니다.

#### <a name="resolution"></a>해결 방법

가상 머신 온보딩을 위한 권한이 올바른지 확인합니다. [가상 머신을 온보딩하는 데 필요한 권한](../automation-role-based-access-control.md#onboarding-permissions)을 검토한 후에 솔루션을 다시 온보딩해 보세요. 오류가 `The solution cannot be enabled on this VM because the permission to read the workspace is missing`발생하면 VM이 작업 영역에 `Microsoft.OperationalInsights/workspaces/read` 온보온되어 있는지 확인할 수 있는 권한이 있는지 확인합니다.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>시나리오: 메시지와 함께 온보딩 실패 - 진단 로깅을 위해 자동화 계정을 구성하지 못했습니다.

#### <a name="issue"></a>문제

가상 머신을 솔루션에 온보딩하려고 하면 다음 메시지가 표시됩니다.

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>원인

이 오류는 가격 책정 계층이 구독의 청구 모델과 일치하지 않는 경우에 발생할 수 있습니다. 자세한 내용은 [Azure Monitor의 사용량 및 예상 비용 모니터링을](https://aka.ms/PricingTierWarning)참조하십시오.

#### <a name="resolution"></a>해결 방법

Log Analytics 작업 영역을 수동으로 만들고 온보딩 프로세스를 반복하여 생성된 작업 영역을 선택합니다.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>시나리오: ComputerGroupQueryFormatError

#### <a name="issue"></a>문제

이 오류 코드는 솔루션을 대상으로 사용되는 저장된 검색 컴퓨터 그룹 쿼리 형식이 올바르지 않음을 의미합니다. 

#### <a name="cause"></a>원인

사용자가 쿼리를 변경했거나 시스템에서 변경되었을 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 솔루션에 대한 쿼리를 삭제하고, 솔루션을 다시 온보딩하여 쿼리를 다시 만들 수 있습니다. 쿼리를 작업 영역 내의 **저장된 검색**에서 찾을 수 있습니다. 쿼리의 이름은 **MicrosoftDefaultComputerGroup**이고, 쿼리의 범주는 이 쿼리와 연결된 솔루션의 이름입니다. 여러 솔루션이 사용되도록 설정되면 **MicrosoftDefaultComputerGroup**이 **저장된 검색** 아래에 여러 번 표시됩니다.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>시나리오: PolicyViolation

#### <a name="issue"></a>문제

이 오류 코드는 하나 이상의 정책 위반으로 인해 배포가 실패했음을 의미합니다.

#### <a name="cause"></a>원인 

작업이 완료될 수 없는 위치에 정책이 있습니다.

#### <a name="resolution"></a>해결 방법

솔루션을 성공적으로 배포하려면 표시된 정책을 변경하는 것이 좋습니다. 정의할 수 있는 정책 유형이 다양하기 때문에 필요한 특정 변경 사항은 위반된 정책에 따라 달라집니다. 예를 들어 리소스 그룹 내 특정 유형의 리소스 콘텐츠를 변경할 수 있는 권한을 거부한 리소스 그룹에 정책이 정의된 경우, 예를 들면 다음과 같은 조치를 취할 수 있습니다.

* 정책을 모두 제거합니다.
* 다른 리소스 그룹에 등록을 시도합니다.
* 정책을 수정합니다. 예를 들면 다음과 같이 수정합니다.
  * 정책의 대상을 특정 리소스(예: 특정 Automation 계정)로 다시 지정합니다.
  * 정책이 거부하도록 구성된 리소스 집합을 수정합니다.

Azure 포털의 오른쪽 상단 모서리에 있는 알림을 확인하거나 자동화 계정이 포함된 리소스 그룹으로 이동한 다음 **설정에서** 배포를 선택하여 실패한 **배포를** 봅니다. Azure Policy에 대해 자세히 알아보려면 [Azure Policy 개요](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)를 참조하세요.

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>시나리오: 작업 영역의 연결을 해제하려는 오류

#### <a name="issue"></a>문제

작업 영역의 연결을 해제하려고 할 때 다음과 같은 오류가 발생합니다.

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>원인

이 오류는 자동화 계정 및 로그 분석 작업 영역이 연결되는 지에 따라 Log Analytics 작업 영역에 여전히 활성 솔루션이 있는 경우에 발생합니다.

### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 솔루션을 사용하는 경우 작업 영역에서 다음 솔루션을 제거해야 합니다.

* 업데이트 관리
* 변경 내용 추적
* 작업이 없는 동안 VM 시작/중지

솔루션을 제거하면 작업 영역의 연결을 해제할 수 있습니다. 작업 영역 및 자동화 계정에서 이러한 솔루션의 기존 아티팩트도 정리하는 것이 중요합니다.  

* 업데이트 관리
  * 자동화 계정에서 업데이트 배포(일정) 제거
* 작업이 없는 동안 VM 시작/중지
  * **설정** > **잠금**에서 자동화 계정에서 솔루션 구성 요소에 대한 잠금을 제거합니다.
  * 오프 시간 솔루션 동안 시작/중지 VM을 제거하는 추가 단계는 [오프 시간 솔루션 동안 시작/중지 VM을 제거합니다.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>MMA 확장 오류

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

솔루션을 배포하는 경우 다양한 관련 리소스가 배포됩니다. Microsoft Monitoring Agent 확장이나 Linux용 Log Analytics 에이전트는 이러한 리소스 중 하나입니다. 이러한 가상 컴퓨터확장은 구성된 Log Analytics 작업 영역과 통신하는 가상 머신 확장기능으로, 나중에 이너리 및 기타 파일을 다운로드하는 작업을 조정하기 위해 온보딩중인 솔루션이 실행을 시작하면 달라집니다.
일반적으로 알림 허브에 나타나는 알림을 통해 MMA 또는 Linux용 Log Analytics 에이전트 설치 실패를 먼저 인식하게 됩니다. 알림을 클릭하면 특정 실패에 대한 자세한 정보가 제공됩니다. 리소스 그룹 리소스로 이동한 다음, 여기에 포함된 배포 요소로 이동하면 배포 실패에 대한 자세한 내용이 제공됩니다.
MMA나 Linux용 Log Analytics 에이전트 설치는 다양한 이유로 인해 실패할 수 있으며 이러한 실패를 처리하는 단계는 문제에 따라 달라집니다. 구체적인 문제 해결 단계가 이어집니다.

다음 섹션에서는 MMA 확장 배포에 오류가 발생하는 온보딩 할 때 발생할 수 있는 다양한 문제에 대해 설명합니다.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>시나리오: WebClient 요청 중 예외가 발생했습니다.

가상 머신의 MMA 확장이 외부 리소스와 통신할 수 없으며 배포가 실패합니다.

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

* VM에는 특정 포트만 허용하는 프록시가 구성됩니다.

* 방화벽 설정이 필요한 포트 및 주소에 대한 액세스를 차단했습니다.

#### <a name="resolution"></a>해결 방법

통신을 위해 적절한 포트와 주소가 열려 있는지 확인합니다. 포트와 주소 목록은 [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>시나리오: 일시적인 환경 문제로 인해 설치실패

설치를 차단하는 다른 설치 또는 작업으로 인해 배포 중에 Microsoft 모니터링 에이전트 확장의 설치가 실패했습니다.

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

* 다른 설치 프로그램이 실행 중입니다.
* 시스템이 템플릿 배포 중에 재부팅하도록 트리거됩니다.

#### <a name="resolution"></a>해결 방법

이 오류는 본질적으로 일시적인 문제입니다. 배포를 다시 시도하여 확장 프로그램을 설치하십시오.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>시나리오: 설치 시간 제한

시간 시간 때문에 MMA 확장 프로그램의 설치가 완료되지 않았습니다.

#### <a name="issue"></a>문제

다음 예제는 반환될 수 있는 오류 메시지입니다.

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>원인

이 오류는 가상 시스템이 설치 중에 과부하를 받고 있기 때문에 발생합니다.

### <a name="resolution"></a>해결 방법

VM의 부하가 낮을 때 MMA 확장을 설치합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
