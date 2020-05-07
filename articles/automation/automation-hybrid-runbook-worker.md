---
title: Azure Automation의 Hybrid Runbook Worker 개요
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 공급자의 컴퓨터에서 runbook을 실행 하는 데 사용할 수 있는 Azure Automation 기능인 Hybrid Runbook Worker의 개요를 제공 합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9a5a5c57d6646e46e83794698d2c3d41fd377d1e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787671"
---
# <a name="hybrid-runbook-worker-overview"></a>Hybrid Runbook Worker 개요

Azure Automation의 Runbook은 Azure 클라우드 플랫폼에서 실행되므로 다른 클라우드 또는 온-프레미스 환경의 리소스에 액세스하지 못할 수도 없습니다. Azure Automation의 Hybrid Runbook Worker 기능을 사용하여 역할을 호스팅하는 컴퓨터에서 직접 그리고 환경의 리소스에 대해 Runbook을 실행하여 해당 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 할당된 컴퓨터에 전달됩니다.

이 기능은 다음 이미지에 나와 있습니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation.png)

각 Hybrid Runbook Worker는 에이전트를 설치할 때 지정한 Hybrid Runbook Worker 그룹의 구성원입니다. 그룹은 단일 에이전트를 포함할 수 있지만 고가용성을 위해 그룹에 여러 에이전트를 설치할 수 있습니다. 각 컴퓨터는 하나의 Automation 계정에 하나의 하이브리드 작업자 보고를 호스트할 수 있습니다.

Hybrid Runbook Worker에서 Runbook을 시작할 경우 이를 실행할 그룹을 지정합니다. 그룹의 각 작업자는 Azure Automation을 폴링하여 사용할 수 있는 작업이 있는지 확인합니다. 작업을 사용할 수 있는 경우 작업을 가져올 수 있는 첫 번째 작업자가 해당 작업을 수행합니다. 작업 큐의 처리 시간은 하이브리드 작업자 하드웨어 프로필 및 로드에 따라 달라집니다. 특정 작업자를 지정할 수 없습니다. Hybrid Runbook Worker는 Azure의 샌드박스에 있는 제한 중 다수를 공유하지 않습니다. 디스크 공간, 메모리 또는 네트워크 소켓에 동일한 제한이 없습니다. Hybrid Runbook Worker는 Hybrid Runbook Worker 자체의 리소스에 의해서만 제한됩니다. 또한 Hybrid Runbook Worker는 Azure 샌드박스에 있는 180 분 [공평 공유](automation-runbook-execution.md#fair-share) 시간 제한을 공유 하지 않습니다. Azure 샌드박스 및 Hybrid Runbook Worker에 대 한 서비스 제한에 대 한 자세한 내용은 작업 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)을 참조 하세요.

## <a name="installation-of-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker 설치

Hybrid Runbook Worker를 설치 하는 프로세스는 운영 체제에 따라 달라 집니다. 다음 표에서는 배포 유형을 정의 합니다.

|OS  |배포 형식  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[수동](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

권장 되는 설치 방법은 자동화 runbook을 사용 하 여 Windows 컴퓨터를 구성 하는 프로세스를 완전히 자동화 하는 것입니다. 두 번째 방법은 단계별 절차에 따라 역할을 수동으로 설치 하 고 구성 하는 것입니다. Linux 컴퓨터의 경우 Python 스크립트를 실행하여 컴퓨터에 에이전트를 설치합니다.

> [!NOTE]
> 상태 구성 (DSC)에서 Hybrid Runbook Worker 역할을 지 원하는 서버의 구성을 관리 하려면 DSC 노드로 서버를 추가 해야 합니다. 온 보 딩에 대 한 자세한 내용은 [상태 구성 (DSC)을 통한 관리를 위한 컴퓨터](automation-dsc-onboarding.md)등록을 참조 하세요.

[업데이트 관리](automation-update-management.md)사용 하도록 설정 하는 경우 Azure Log Analytics 작업 영역에 연결 된 모든 컴퓨터는 업데이트 관리를 위해 포함 된 runbook을 지원 하기 위한 Hybrid Runbook Worker 자동으로 구성 됩니다. 그러나 컴퓨터는 Automation 계정에 이미 정의 된 Hybrid Runbook Worker 그룹에 등록 되어 있지 않습니다. 

업데이트 관리 및 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용 하는 한 automation runbook을 지원 하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker 버전 7.2.12024.0에 추가 되었습니다.

이 항목의 정보에 대해 잘 알고 있는 경우 사용자 환경에 Hybrid Runbook Worker를 배포할 시간입니다. Windows Hybrid Runbook Worker [windows Hybrid Runbook Worker 배포](automation-windows-hrw-install.md)를 참조 하세요. Linux 작업자는 [linux Hybrid Runbook Worker 배포](automation-linux-hrw-install.md)를 참조 하세요.

## <a name="network-configuration"></a><a name="network-planning"></a>네트워크 구성

### <a name="hybrid-worker-role"></a>Hybrid Worker 역할

Hybrid Runbook Worker Azure Automation에 연결 하 고 등록 하려면이 섹션에서 설명 하는 포트 번호 및 Url에 대 한 액세스 권한이 있어야 합니다. 이 액세스는 Log Analytics 에이전트가 Azure Monitor 로그에 연결 하는 데 [필요한 포트 및 url](../azure-monitor/platform/agent-windows.md) 위에 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

에이전트와 Azure Automation 서비스 간의 통신에 프록시 서버를 사용 하는 경우 적절 한 리소스에 액세스할 수 있는지 확인 합니다. Hybrid Runbook Worker 및 자동화 서비스의 요청에 대 한 제한 시간은 30 초입니다. 3 번 시도 하면 요청이 실패 합니다. 방화벽을 사용하여 인터넷 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다. Log Analytics 게이트웨이를 프록시로 사용하면 하이브리드 작업자에 대해 구성되었는지 확인합니다. 이 작업을 수행하는 방법에 대한 지침은 [Automation Hybrid Worker에 대한 Log Analytics 게이트웨이 구성](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)을 참조하세요.

Hybrid Runbook Worker 역할에서 Automation 역할과 통신하려면 다음 포트와 URL이 필요합니다.

* 포트: 아웃바운드 인터넷 액세스에는 443 TCP 포트만 필요합니다.
* 글로벌 URL: *.azure-automation.net
* US Gov 버지니아의 전역 URL: *.azure-automation.us
* 에이전트 서비스: https://\<workspaceId\>.agentsvc.azure-automation.net

예외를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 Ip 범위](https://www.microsoft.com/en-us/download/details.aspx?id=56519)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 포함합니다.

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터에 대한 통신을 제한할 수 있습니다. 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 미국 서부 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 중부 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동부 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) XML 파일을 다운로드하세요.

Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 컴퓨팅, SQL 및 스토리지 범위가 포함되어 있습니다.

업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다.

새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 이 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

## <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker에 대 한 업데이트 관리 주소

Hybrid Runbook Worker에 필요한 표준 주소 및 포트 외에도, 업데이트 관리를 위해 특별히 다음 주소가 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 수행됩니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 실행

로컬 컴퓨터에서 리소스를 관리 하거나 Hybrid Runbook Worker 배포 된 로컬 환경의 리소스에 대해 실행 하는 runbook이 있을 수 있습니다. 이 경우 Automation 계정 대신 hybrid worker에서 runbook을 실행 하도록 선택할 수 있습니다. Hybrid Runbook Worker에서 실행 되는 runbook은 Automation 계정에서 실행 하는 runbook과 동일 합니다. [Hybrid Runbook Worker에서 Runbook 실행을](automation-hrw-run-runbooks.md)참조 하세요.

### <a name="hybrid-runbook-worker-jobs"></a>작업 Hybrid Runbook Worker

Hybrid Runbook Worker 작업은 Windows의 로컬 **시스템** 계정 또는 Linux의 **nxautomation** 계정에서 실행 됩니다. Azure Automation는 Azure 샌드박스에서 실행 되는 작업과 약간 다른 방식으로 Hybrid Runbook Worker에 대 한 작업을 처리 합니다. 한 가지 중요 한 차이점은 runbook worker의 작업 기간에 제한이 없다는 것입니다. Azure 샌드박스에서 실행 되는 runbook은 공평 하 게 [공유](automation-runbook-execution.md#fair-share)되므로 3 시간으로 제한 됩니다.

Hybrid Runbook Worker 호스트 컴퓨터가 다시 부팅 되는 경우 실행 중인 모든 Runbook 작업은 처음부터 또는 PowerShell 워크플로 runbook에 대 한 마지막 검사점에서 다시 시작 됩니다. Runbook 작업이 세 번 이상 다시 시작 되 면 일시 중단 됩니다.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에 대 한 Runbook 권한

비 Azure 리소스에 액세스 하므로 Hybrid Runbook Worker에서 실행 되는 runbook은 일반적으로 Azure 리소스를 인증 하는 runbook에서 사용 하는 인증 메커니즘을 사용할 수 없습니다. Runbook은 로컬 리소스에 대 한 자체 인증을 제공 하거나 [Azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)를 사용 하 여 인증을 구성 합니다. 또한 모든 runbook에 대 한 사용자 컨텍스트를 제공 하는 실행 계정을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.
* Hybrid Runbook Worker 문제를 해결 하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조 하세요.