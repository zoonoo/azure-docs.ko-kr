---
title: Azure Automation Hybrid Runbook Worker 개요
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 공급자의 머신에서 Runbook을 실행하는 데 사용할 수 있는 Hybrid Runbook Worker에 대한 개요를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: ea2b8deb07a899ab35ddd761df3e3ddb413dd45d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95509082"
---
# <a name="hybrid-runbook-worker-overview"></a>Hybrid Runbook Worker 개요

Azure Automation의 Runbook은 Azure 클라우드 플랫폼에서 실행되므로 다른 클라우드 또는 온-프레미스 환경의 리소스에 액세스하지 못할 수도 없습니다. Azure Automation의 Hybrid Runbook Worker 기능을 사용 하 여 역할을 호스트 하는 컴퓨터 및 환경의 리소스에 대해 runbook을 직접 실행 하 여 해당 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에서 저장 되 고 관리 된 다음 하나 이상의 할당 된 컴퓨터에 배달 됩니다.

이 기능은 다음 이미지에 나와 있습니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation.png)

Runbook Worker에는 시스템 및 사용자의 두 가지 유형이 있습니다. 다음 표에서는 이러한 차이점을 설명 합니다.

|유형 | 설명 |
|-----|-------------|
|**System** |는 Windows 및 Linux 컴퓨터에 사용자 지정 업데이트를 설치 하도록 디자인 된 업데이트 관리 기능에서 사용 하는 숨겨진 runbook 집합을 지원 합니다.<br> 이 유형의 Hybrid Runbook Worker는 Hybrid Runbook Worker 그룹의 구성원이 아니므로 Runbook Worker 그룹을 대상으로 하는 runbook을 실행 하지 않습니다. |
|**사용자** |는 하나 이상의 Runbook Worker 그룹의 멤버인 Windows 및 Linux 컴퓨터에서 직접 실행 하기 위한 사용자 정의 runbook을 지원 합니다. |

Hybrid Runbook Worker은 Windows 또는 Linux 운영 체제에서 실행할 수 있으며이 역할은 Azure Monitor [Log Analytics 작업 영역](../azure-monitor/platform/design-logs-deployment.md)에 대 한 [Log Analytics 에이전트](../azure-monitor/platform/log-analytics-agent.md) 보고를 사용 합니다. 작업 영역에서는 지원 되는 운영 체제에 대 한 컴퓨터를 모니터링 하는 것 뿐만 아니라 Hybrid Runbook Worker을 설치 하는 데 필요한 구성 요소를 다운로드할 수 있습니다.

Azure Automation [업데이트 관리](update-management/update-mgmt-overview.md) 를 사용 하도록 설정 하면 Log Analytics 작업 영역에 연결 된 모든 컴퓨터가 자동으로 시스템 Hybrid Runbook Worker 구성 됩니다.

각 사용자 Hybrid Runbook Worker는 작업자를 설치할 때 지정 하는 Hybrid Runbook Worker 그룹의 구성원입니다. 그룹에는 단일 작업자를 포함할 수 있지만, 고가용성을 위해 그룹에 여러 작업자를 포함할 수 있습니다. 각 컴퓨터는 하나의 Automation 계정에 보고 한 Hybrid Runbook Worker를 호스트할 수 있습니다. 하이브리드 작업자는 여러 Automation 계정에 등록할 수 없습니다. 이는 hybrid worker가 단일 Automation 계정의 작업만 수신할 수 있기 때문입니다. 업데이트 관리에서 관리 하는 시스템 Hybrid Runbook worker를 호스트 하는 컴퓨터의 경우 Hybrid Runbook Worker 그룹에 추가할 수 있습니다. 하지만 업데이트 관리와 Hybrid Runbook Worker 그룹 구성원 자격에 대해 동일한 Automation 계정을 사용 해야 합니다.

사용자 Hybrid Runbook Worker에서 runbook을 시작 하는 경우 실행 되는 그룹을 지정 합니다. 그룹의 각 작업자는 Azure Automation을 폴링하여 사용할 수 있는 작업이 있는지 확인합니다. 작업을 사용할 수 있는 경우 작업을 가져올 수 있는 첫 번째 작업자가 해당 작업을 수행합니다. 작업 큐의 처리 시간은 하이브리드 작업자 하드웨어 프로필 및 로드에 따라 달라집니다. 특정 작업자를 지정할 수 없습니다. Hybrid worker는 폴링 메커니즘 (30 초 마다)에서 작동 하 고 첫 번째 제공의 순서를 따릅니다. 작업이 푸시되는 시기에 따라 자동화 서비스에서 작업을 선택 하는 모든 하이브리드 작업자를 ping 합니다. 단일 hybrid worker는 일반적으로 ping 당 4 개의 작업 (즉, 30 초 마다)을 선택할 수 있습니다. 푸시 작업의 속도가 30 초를 초과 하는 경우에는 Hybrid Runbook Worker 그룹의 다른 hybrid worker가 작업을 선택할 가능성이 높습니다.

Hybrid Runbook Worker에서 runbook의 배포를 제어 하 고 작업을 트리거하는 시기 또는 방법을 제어 하려면 Automation 계정 내의 서로 다른 Hybrid Runbook Worker 그룹에 대해 Hybrid worker를 등록할 수 있습니다. 실행 정렬을 지원 하기 위해 특정 그룹에 대해 작업을 대상으로 지정 합니다.

디스크 공간, 메모리 또는 네트워크 소켓에 대한 샌드박스 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)이 많지 않기 때문에 [Azure 샌드박스](automation-runbook-execution.md#runbook-execution-environment) 대신 Hybrid Runbook Worker를 사용하세요. 하이브리드 작업자에 대한 제한은 작업자의 자체 리소스하고만 관련이 있습니다.

> [!NOTE]
> Hybrid Runbook Worker에는 Azure 샌드박스에 대한 [공평 분배](automation-runbook-execution.md#fair-share) 시간 제한이 적용되지 않습니다.

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker 설치

사용자 Hybrid Runbook Worker를 설치 하는 프로세스는 운영 체제에 따라 달라 집니다. 다음 표에는 배포 유형이 정의되어 있습니다.

|운영 체제  |배포 유형  |
|---------|---------|
|Windows     | [자동](automation-windows-hrw-install.md#automated-deployment)<br>[수동](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [수동](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Windows 컴퓨터에 권장 되는 설치 방법은 Azure Automation runbook을 사용 하 여 구성 프로세스를 완전히 자동화 하는 것입니다. 이렇게 할 수 없는 경우 단계별 절차에 따라 수동으로 역할을 설치 하 고 구성할 수 있습니다. Linux 컴퓨터의 경우 Python 스크립트를 실행하여 컴퓨터에 에이전트를 설치합니다.

## <a name="network-planning"></a><a name="network-planning"></a>네트워크 계획

시스템 및 사용자 Hybrid Runbook Worker 모두에 연결 하 여 Azure Automation에 등록 하려면이 섹션에서 설명 하는 포트 번호 및 Url에 대 한 액세스 권한이 있어야 합니다. 또한 작업자가 Azure Monitor Log Analytics 작업 영역에 연결하려면 [Log Analytics 에이전트에 필요한 포트 및 URL](../azure-monitor/platform/agent-windows.md)에 액세스할 수 있어야 합니다.

Hybrid Runbook Worker에는 다음 포트와 URL이 필요합니다.

* 포트: 아웃바운드 인터넷 액세스에는 TCP 443만 필요
* 전역 URL: `*.azure-automation.net`
* US Gov 버지니아의 글로벌 URL: `*.azure-automation.us`
* 에이전트 서비스: `https://<workspaceId>.agentsvc.azure-automation.net`

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터와 Hybrid Runbook Worker의 통신을 제한할 수 있습니다. [Azure Automation에서 사용 하는 dns 레코드](how-to/automation-region-dns-records.md) 를 검토 하 여 필요한 dns 레코드를 확인 합니다.

### <a name="proxy-server-use"></a>프록시 서버 사용

Azure Automation와 Log Analytics 에이전트를 실행 하는 컴퓨터 간의 통신에 프록시 서버를 사용 하는 경우 적절 한 리소스에 액세스할 수 있는지 확인 합니다. Hybrid Runbook Worker 및 Automation 서비스의 요청 제한 시간은 30초입니다. 3회 시도 후에는 요청이 실패합니다.

### <a name="firewall-use"></a>방화벽 사용

방화벽을 사용 하 여 인터넷에 대 한 액세스를 제한 하는 경우 액세스를 허용 하도록 방화벽을 구성 해야 합니다. Log Analytics 게이트웨이를 프록시로 사용하는 경우 Hybrid Runbook Worker용으로 구성되었는지 확인합니다. [Automation Hybrid Runbook worker에 대 한 Log Analytics 게이트웨이 구성](../azure-monitor/platform/gateway.md)을 참조 하세요.

### <a name="service-tags"></a>서비스 태그

Azure Automation는 서비스 태그 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)부터 Azure virtual network 서비스 태그를 지원 합니다. 서비스 태그를 사용 하 여 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) 또는 [Azure 방화벽](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 **GuestAndHybridManagement**  을 지정 하 여 자동화 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. 이 서비스 태그는 IP 범위를 특정 지역으로 제한 하 여 보다 세부적인 제어를 허용 하는 기능을 지원 하지 않습니다.

Azure Automation 서비스에 대 한 서비스 태그는 다음 시나리오에 사용 되는 Ip만 제공 합니다.

* 가상 네트워크 내에서 웹 후크 트리거
* VNet에서 Hybrid Runbook Worker 또는 상태 구성 에이전트가 Automation 서비스와 통신할 수 있도록 허용

>[!NOTE]
>현재 서비스 태그 **GuestAndHybridManagement** 는 Azure 샌드박스에서 runbook 작업 실행을 지원 하지 않습니다 .이 작업은 Hybrid Runbook Worker 에서만 직접 지원 됩니다.

## <a name="support-for-impact-level-5-il5"></a>영향 수준 5에 대 한 지원 (IL5)

Azure Automation Hybrid Runbook Worker를 Azure Government에서 사용 하 여 다음 두 가지 구성 중 하나에서 영향 수준 5 작업을 지원할 수 있습니다.

* [격리 된 가상 컴퓨터](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). 배포 되 면 해당 컴퓨터에 대 한 전체 실제 호스트를 사용 하 여 IL5 워크 로드를 지 원하는 데 필요한 수준의 격리를 제공 합니다.

* 하나 이상의 가상 컴퓨터를 호스트할 수 있는 물리적 서버를 제공 하는 [Azure 전용 호스트](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts)는 하나의 azure 구독 전용입니다.

>[!NOTE]
>Hybrid Runbook Worker 역할을 통한 계산 격리는 Azure 상용 및 미국 정부 클라우드에서 사용할 수 있습니다. 

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 업데이트 관리 주소

Hybrid Runbook Worker에 필요한 표준 주소 및 포트 외에도 업데이트 관리 [네트워크 계획](update-management/update-mgmt-overview.md#ports) 섹션에 설명 된 추가 네트워크 구성 요구 사항이 있습니다.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 Azure Automation State Configuration

[Hybrid Runbook Worker의 Azure Automation State Configuration](automation-dsc-overview.md)을 실행할 수 있습니다. Hybrid Runbook Worker를 지원하는 서버 구성을 관리하려면 서버를 DSC 노드로 추가해야 합니다. [머신에 Azure Automation State Configuration을 통한 관리 사용](automation-dsc-onboarding.md)을 참조하세요.

## <a name="runbook-worker-limits"></a>Runbook Worker 제한

Automation 계정 당 Hybrid Worker 그룹의 최대 수는 4000이 고 system & 사용자 하이브리드 작업자에 모두 적용 됩니다. 관리할 컴퓨터가 4000 대 이상인 경우 추가 Automation 계정을 만드는 것이 좋습니다.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 Runbook

로컬 컴퓨터에서 리소스를 관리 하거나 사용자 Hybrid Runbook Worker 배포 된 로컬 환경의 리소스에 대해 실행 하는 runbook이 있을 수 있습니다. 이 경우 Automation 계정 대신 하이브리드 작업자에서 Runbook을 실행하도록 선택할 수 있습니다. Hybrid Runbook Worker에서 실행되는 Runbook은 Automation 계정에서 실행되는 Runbook 구조와 동일합니다. [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker 작업

Hybrid Runbook Worker 작업은 Windows의 경우 로컬 **시스템** 계정으로 실행되고, Linux의 경우 [nxautomation 계정](automation-runbook-execution.md#log-analytics-agent-for-linux)으로 실행됩니다. Azure Automation는 Azure 샌드박스에서 실행 되는 작업과는 다른 Hybrid Runbook Worker 작업을 처리 합니다. [Runbook 실행 환경](automation-runbook-execution.md#runbook-execution-environment)을 참조하세요.

Hybrid Runbook Worker 호스트 머신이 다시 부팅되면, 실행 중인 모든 Runbook 작업이 처음부터 다시 시작되거나 PowerShell 워크플로 Runbook의 마지막 검사점에서 다시 시작됩니다. Runbook 작업이 4회 이상 다시 시작된 후 일시 중단됩니다.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에 대한 Runbook 권한

비 Azure 리소스에 액세스 하기 때문에 사용자 Hybrid Runbook Worker에서 실행 되는 runbook은 일반적으로 Azure 리소스를 인증 하는 runbook에서 사용 하는 인증 메커니즘을 사용할 수 없습니다. Runbook은 로컬 리소스에 고유한 인증을 제공하거나 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)를 사용하여 인증을 구성합니다. 실행 계정을 지정하여 모든 Runbook에 대한 사용자 컨텍스트를 제공할 수도 있습니다.

## <a name="view-system-hybrid-runbook-workers"></a>시스템 Hybrid Runbook Worker 보기

Windows 또는 Linux 컴퓨터에서 업데이트 관리 기능을 사용 하도록 설정한 후 Azure Portal에서 system Hybrid Runbook Worker 그룹 목록을 인벤토리에 추가할 수 있습니다. 선택한 Automation 계정에 대 한 왼쪽 창의 **hybrid worker 그룹** 옵션에서 탭 **시스템 hybrid worker 그룹** 을 선택 하 여 포털에서 최대 2000 작업자를 볼 수 있습니다.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Automation 계정 시스템 hybrid worker 그룹 페이지" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

하이브리드 작업 자가 2000 개를 초과 하는 경우 모든 항목의 목록을 가져오려면 다음 PowerShell 스크립트를 실행할 수 있습니다.

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.