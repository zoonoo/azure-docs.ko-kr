---
title: Azure Automation Hybrid Runbook Worker 개요
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 공급자의 머신에서 Runbook을 실행하는 데 사용할 수 있는 Hybrid Runbook Worker에 대한 개요를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835294"
---
# <a name="hybrid-runbook-worker-overview"></a>Hybrid Runbook Worker 개요

Azure Automation의 Runbook은 Azure 클라우드 플랫폼에서 실행되므로 다른 클라우드 또는 온-프레미스 환경의 리소스에 액세스하지 못할 수도 없습니다. Azure Automation의 Hybrid Runbook Worker 기능을 사용하여 역할을 호스팅하는 컴퓨터에서 직접 그리고 환경의 리소스에 대해 Runbook을 실행하여 해당 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 할당된 컴퓨터에 전달됩니다.

이 기능은 다음 이미지에 나와 있습니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation.png)

Hybrid Runbook Worker는 Windows 또는 Linux 운영 체제를 실행할 수 있습니다. 모니터링을 위해서는 지원되는 운영 체제용 Azure Monitor 및 Log Analytics 에이전트를 사용해야 합니다. 자세한 내용은 [Azure Monitor](automation-runbook-execution.md#azure-monitor)를 참조하세요.

각 Hybrid Runbook Worker는 에이전트를 설치할 때 지정한 Hybrid Runbook Worker 그룹의 구성원입니다. 그룹은 단일 에이전트를 포함할 수 있지만 고가용성을 위해 그룹에 여러 에이전트를 설치할 수 있습니다. 각 머신은 하나의 Automation 계정에 보고하는 하나의 하이브리드 작업자를 호스팅할 수 있습니다. 

Hybrid Runbook Worker에서 Runbook을 시작할 경우 이를 실행할 그룹을 지정합니다. 그룹의 각 작업자는 Azure Automation을 폴링하여 사용할 수 있는 작업이 있는지 확인합니다. 작업을 사용할 수 있는 경우 작업을 가져올 수 있는 첫 번째 작업자가 해당 작업을 수행합니다. 작업 큐의 처리 시간은 하이브리드 작업자 하드웨어 프로필 및 로드에 따라 달라집니다. 특정 작업자를 지정할 수 없습니다. 

디스크 공간, 메모리 또는 네트워크 소켓에 대한 샌드박스 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)이 많지 않기 때문에 [Azure 샌드박스](automation-runbook-execution.md#runbook-execution-environment) 대신 Hybrid Runbook Worker를 사용하세요. 하이브리드 작업자에 대한 제한은 작업자의 자체 리소스하고만 관련이 있습니다. 

> [!NOTE]
> Hybrid Runbook Worker에는 Azure 샌드박스에 대한 [공평 분배](automation-runbook-execution.md#fair-share) 시간 제한이 적용되지 않습니다. 

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker 설치

Hybrid Runbook Worker를 설치하는 프로세스는 운영 체제에 따라 다릅니다. 다음 표에는 배포 유형이 정의되어 있습니다.

|운영 체제  |배포 유형  |
|---------|---------|
|Windows     | [자동](automation-windows-hrw-install.md#automated-deployment)<br>[수동](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

권장되는 설치 방법은 Azure Automation Runbook을 사용하여 Windows 컴퓨터를 구성하는 프로세스를 완전히 자동화하는 것입니다. 두 번째 방법은 단계별 절차에 따라 역할을 수동으로 설치하고 구성하는 것입니다. Linux 컴퓨터의 경우 Python 스크립트를 실행하여 컴퓨터에 에이전트를 설치합니다.

## <a name="network-planning"></a><a name="network-planning"></a>네트워크 계획

Hybrid Runbook Worker에서 Azure Automation에 연결하고 등록하려면 이 섹션에서 설명하는 포트 번호 및 URL에 대한 액세스 권한이 있어야 합니다. 또한 작업자가 Azure Monitor Log Analytics 작업 영역에 연결하려면 [Log Analytics 에이전트에 필요한 포트 및 URL](../azure-monitor/platform/agent-windows.md)에 액세스할 수 있어야 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Hybrid Runbook Worker에는 다음 포트와 URL이 필요합니다.

* 포트: 아웃바운드 인터넷 액세스에는 TCP 443만 필요
* 글로벌 URL: *.azure-automation.net
* US Gov 버지니아의 전역 URL: *.azure-automation.us
* 에이전트 서비스: https://\<workspaceId\>.agentsvc.azure-automation.net

[예외](automation-runbook-execution.md#exceptions)를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/en-us/download/details.aspx?id=56519)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 포함합니다.

### <a name="dns-records-per-region"></a>지역별 DNS 레코드

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터와 Hybrid Runbook Worker의 통신을 제한할 수 있습니다. 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 오스트레일리아 중부 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동부 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 미국 서부 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) XML 파일을 다운로드하세요. 업데이트된 IP 주소 파일이 매주 게시됩니다. 

IP 주소 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이는 컴퓨팅, SQL 및 스토리지 범위를 포함하며 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 반영합니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다.

새 IP 주소 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. 

> [!NOTE]
> Azure ExpressRoute 사용자는 IP 주소 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

### <a name="proxy-server-use"></a>프록시 서버 사용

Azure Automation과 Log Analytics 에이전트 간의 통신에 프록시 서버를 사용하는 경우 적절한 리소스에 액세스할 수 있는지 확인합니다. Hybrid Runbook Worker 및 Automation 서비스의 요청 제한 시간은 30초입니다. 3회 시도 후에는 요청이 실패합니다. 

### <a name="firewall-use"></a>방화벽 사용

방화벽을 사용하여 인터넷 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다. Log Analytics 게이트웨이를 프록시로 사용하는 경우 Hybrid Runbook Worker용으로 구성되었는지 확인합니다. [Automation Hybrid Worker에 대한 Log Analytics 게이트웨이 구성](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)을 참조하세요.

## <a name="update-management-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 업데이트 관리

Azure Automation [업데이트 관리](automation-update-management.md)를 사용하도록 설정하면 Log Analytics 작업 영역에 연결된 모든 컴퓨터가 Hybrid Runbook Worker로 자동으로 구성됩니다. 각 작업자는 업데이트 관리를 대상으로 하는 Runbook을 지원할 수 있습니다. 

이렇게 구성된 컴퓨터가 Automation 계정에 이미 정의된 어떤 Hybrid Runbook Worker 그룹에도 등록되지 않았습니다. Hybrid Runbook Worker 그룹에 컴퓨터를 추가할 수 있지만 업데이트 관리 및 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용 해야 합니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 업데이트 관리 주소

Hybrid Runbook Worker에 필요한 표준 주소 및 포트 외에도, 업데이트 관리를 위해 다음 표에 나열된 주소가 필요합니다. 이러한 주소에 대한 통신에는 443 포트가 사용됩니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 Azure Automation State Configuration

[Hybrid Runbook Worker의 Azure Automation State Configuration](automation-dsc-overview.md)을 실행할 수 있습니다. Hybrid Runbook Worker를 지원하는 서버 구성을 관리하려면 서버를 DSC 노드로 추가해야 합니다. [머신에 Azure Automation State Configuration을 통한 관리 사용](automation-dsc-onboarding.md)을 참조하세요.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 Runbook

로컬 컴퓨터의 리소스를 관리하거나 Hybrid Runbook Worker가 배포된 로컬 환경의 리소스에 대해 실행되는 Runbook이 있을 수 있습니다. 이 경우 Automation 계정 대신 하이브리드 작업자에서 Runbook을 실행하도록 선택할 수 있습니다. Hybrid Runbook Worker에서 실행되는 Runbook은 Automation 계정에서 실행되는 Runbook 구조와 동일합니다. [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker 작업

Hybrid Runbook Worker 작업은 Windows의 경우 로컬 **시스템** 계정으로 실행되고, Linux의 경우 [nxautomation 계정](automation-runbook-execution.md#log-analytics-agent-for-linux)으로 실행됩니다. Azure Automation은 Azure 샌드박스에서 실행되는 작업과 약간 다른 방식으로 Hybrid Runbook Worker에 대한 작업을 처리합니다. [Runbook 실행 환경](automation-runbook-execution.md#runbook-execution-environment)을 참조하세요.

Hybrid Runbook Worker 호스트 머신이 다시 부팅되면, 실행 중인 모든 Runbook 작업이 처음부터 다시 시작되거나 PowerShell 워크플로 Runbook의 마지막 검사점에서 다시 시작됩니다. Runbook 작업이 4회 이상 다시 시작된 후 일시 중단됩니다.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에 대한 Runbook 권한

Hybrid Runbook Worker에서 실행하는 Runbook은 Azure에 없는 리소스에 액세스하기 때문에 일반적으로 Azure 리소스에 인증하는 Runbook에 사용되는 인증 메커니즘을 사용할 수 없습니다. Runbook은 로컬 리소스에 고유한 인증을 제공하거나 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)를 사용하여 인증을 구성합니다. 실행 계정을 지정하여 모든 Runbook에 대한 사용자 컨텍스트를 제공할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.
* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.