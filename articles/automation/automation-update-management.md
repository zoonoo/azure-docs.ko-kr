---
title: Azure의 업데이트 관리 솔루션
description: 이 문서는 Azure 업데이트 관리 솔루션을 사용하여 Windows 및 Linux 컴퓨터에 대한 업데이트를 관리하는 방법을 이해할 수 있도록 제공됩니다.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1751e8d67f59285d011df33a2d4d1d6d8abcec6a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376046"
---
# <a name="update-management-solution-in-azure"></a>Azure의 업데이트 관리 솔루션

Azure Automation에서 업데이트 관리 솔루션을 사용 하 여 Azure, 온-프레미스 환경 또는 다른 클라우드 공급자에서 Windows 및 Linux 컴퓨터에 대 한 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버에 대한 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

Azure Automation 계정에서 직접 가상 머신에 업데이트 관리를 사용하도록 설정할 수 있습니다. Automation 계정에서 가상 머신에 업데이트 관리를 사용하는 방법을 알아보려면 [여러 가상 머신에 대한 업데이트 관리](manage-update-multi.md)를 참조하세요. Azure Portal의 가상 머신 페이지에서 가상 머신에 대한 업데이트 관리를 사용할 수도 있습니다. 이 시나리오는 [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) 및 [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) 가상 머신에서 지원됩니다.

> [!NOTE]
> 업데이트 관리 솔루션을 사용 하려면 Log Analytics 작업 영역을 Automation 계정에 연결 해야 합니다. 지원 되는 지역에 대 한 명확한 목록은 [Azure 작업 영역 매핑](./how-to/region-mappings.md)을 참조 하세요. 지역 매핑은 Automation 계정과는 다른 지역에 있는 가상 컴퓨터를 관리 하는 기능에 영향을 주지 않습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>솔루션 개요

업데이트 관리를 통해 관리되는 컴퓨터는 다음 구성을 사용하여 평가 및 업데이트 배포를 수행합니다.

* Windows 또는 Linux용 MMA(Microsoft Monitoring Agent)
* Linux용 PowerShell DSC(필요한 상태 구성)
* Automation Hybrid Runbook Worker
* Windows 컴퓨터용 Microsoft Update 또는 WSUS(Windows Server Update Services)

다음 다이어그램에서는 솔루션이 작업 영역에서 보안 업데이트를 평가하고, 연결된 모든 Windows Server 및 Linux 컴퓨터에 적용하는 방식과 관련된 동작 및 데이터 흐름을 개념적으로 보여 줍니다.

![업데이트 관리 프로세스 흐름](./media/automation-update-management/update-mgmt-updateworkflow.png)

업데이트 관리는 동일한 테넌트의 여러 구독에 있는 머신을 기본적으로 등록하는 데 사용할 수 있습니다.

패키지가 릴리스되면 패치가 평가를 위해 Linux 컴퓨터에 표시 되는 데 2-3 시간이 걸립니다. Windows 머신의 경우 릴리스된 후 평가할 패치가 나타날 때까지 12-15시간이 걸립니다.

컴퓨터에서 업데이트 준수에 대 한 검사를 완료 한 후 에이전트는 Azure Monitor 로그에 정보를 대량으로 전달 합니다. Windows 컴퓨터는 기본적으로 12시간마다 준수 검사가 실행됩니다.

검사 일정 외에도, MMA가 다시 시작되면 업데이트 설치 전과 업데이트 설치 후 15분 이내에 업데이트 준수 검사가 시작됩니다.

Linux 컴퓨터의 경우 호환성 검사는 기본적으로 매시간 수행 됩니다. MMA 에이전트가 다시 시작되면 15분 이내에 준수 검사가 시작됩니다.

솔루션은 동기화하도록 구성된 소스를 기반으로 컴퓨터가 얼마나 최신 상태인지를 보고합니다. Windows 컴퓨터가 WSUS에 보고하도록 구성된 경우 WSUS가 Microsoft Update와 마지막으로 동기화된 시기에 따라 그 결과는 Microsoft Update가 표시하는 것과 다를 수 있습니다. 이 동작은 공용 리포지토리 대신 로컬 리포지토리에 보고하도록 구성된 Linux 컴퓨터에서도 동일합니다.

> [!NOTE]
> 서비스에 제대로 보고하려면 업데이트 관리를 수행할 때 특정 URL 및 포트가 사용되도록 설정되어야 합니다. 이러한 요구 사항에 대한 자세한 내용은 [Hybrid Worker에 대한 네트워크 계획](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

예약 배포를 만들어서 업데이트가 필요한 컴퓨터에 소프트웨어 업데이트를 배포하고 설치할 수 있습니다. *선택 사항*으로 분류된 업데이트는 Windows 컴퓨터의 배포 범위에 포함되지 않습니다. 배포 범위에는 필수 업데이트만 포함됩니다.

예약 된 배포는 컴퓨터를 명시적으로 지정 하거나 특정 컴퓨터 집합의 로그 검색을 기반으로 하는 [컴퓨터 그룹](../azure-monitor/platform/computer-groups.md) 또는 [Azure 쿼리](automation-update-management-query-logs.md) 를 선택 하 여 해당 업데이트를 수신 하는 대상 컴퓨터를 정의 합니다. 지정 된 조건에 따라 Azure Vm을 동적으로 선택 합니다. 이러한 그룹은 솔루션을 사용 하도록 설정 하는 관리 팩을 가져오는 컴퓨터를 확인 하는 데에만 사용 되는 [범위 구성과](../azure-monitor/insights/solution-targeting.md)다릅니다.

또한 업데이트를 설치할 수 있는 기간을 승인 및 설정하는 일정을 지정합니다. 이러한 기간을 유지 관리 기간 이라고 합니다. 다시 부팅이 필요 하 고 적절 한 재부팅 옵션을 선택한 경우 유지 관리 기간이 20 분 이면 다시 부팅 하도록 예약 됩니다. 패치가 예상 보다 오래 걸리고 유지 관리 기간에 20 분 미만이 면 다시 부팅이 발생 하지 않습니다.

Azure Automation의 runbook에서 업데이트가 설치됩니다. 이러한 Runbook은 볼 수 없으며 구성이 필요하지 않습니다. 업데이트 배포가 생성되면 업데이트 배포는 포함된 컴퓨터에 대해 지정된 시간에 마스터 업데이트 Runbook을 시작하는 일정을 만듭니다. 이 마스터 Runbook은 각 에이전트에서 하위 Runbook을 시작하여 필수 업데이트를 설치합니다.

업데이트 배포에 지정된 날짜 및 시간에 대상 컴퓨터는 배포를 병렬로 실행합니다. 설치 전에, 업데이트가 여전히 필요한지 확인하기 위한 검사가 수행됩니다. WSUS 클라이언트 컴퓨터의 경우 업데이트가 WSUS에서 승인되지 않으면 업데이트 배포가 실패합니다.

업데이트 관리에 대해 등록 된 컴퓨터가 둘 이상의 Log Analytics 작업 영역 (멀티 호 밍)에서 지원 되지 않습니다.

## <a name="clients"></a>클라이언트

### <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에서는 업데이트 평가에 대해 지원 되는 운영 체제의 목록을 보여 줍니다. 패치를 적용 하려면 Hybrid Runbook Worker 필요 합니다. Hybrid Runbook Worker 요구 사항에 대 한 자세한 내용은 [WINDOWS hrw](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) 및 [Linux hrw](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)에 대 한 설치 가이드를 참조 하세요.

|운영 체제  |참고  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM 및 SP1 표준)||
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 분류 기반 패치에는 CentOS에 기본 제공되지 않은 보안 데이터를 반환하기 위해 'yum'이 필요합니다. CentOS의 분류 기반 패치에 대 한 자세한 내용은 [Linux의 업데이트 분류](#linux-2) 를 참조 하세요.          |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04(x86/x64)      |Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.         |

> [!NOTE]
> Azure 가상 머신 확장 집합은 업데이트 관리를 사용 하 여 관리할 수 있습니다. 업데이트 관리은 기본 이미지가 아닌 인스턴스 자체에 대해 작동 합니다. 모든 VM 인스턴스를 한 번에 업데이트 하지 않도록 증분 방식으로 업데이트를 예약 해야 합니다.
> [비 Azure 컴퓨터 등록의](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)단계에 따라 Vmss 노드를 추가할 수 있습니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

다음 표에는 지원되지 않는 운영 체제가 나열되어 있습니다.

|운영 체제  |참고  |
|---------|---------|
|Windows 클라이언트     | 클라이언트 운영 체제(예: Windows 7 및 Windows 10)는 지원되지 않습니다.        |
|Windows Server 2016 Nano Server     | 지원되지 않습니다.       |
|Azure Kubernetes Service 노드 | 지원되지 않습니다. [Azure Kubernetes 서비스에서 Linux 노드에 보안 및 커널 업데이트 적용 (AKS)](../aks/node-updates-kured.md) 에서 자세히 설명 하는 패치 처리를 사용 합니다.|

### <a name="client-requirements"></a>클라이언트 요구 사항

아래 정보는 OS 특정 클라이언트 요구 사항을 설명 합니다.  또한 추가 지침은 [네트워크 계획](#ports) 을 검토 해야 합니다.

#### <a name="windows"></a>Windows

Windows 에이전트는 WSUS 서버와 통신하도록 구성되거나 Microsoft 업데이트에 대한 액세스 권한을 가지고 있어야 합니다.

System Center Configuration Manager에서 업데이트 관리를 사용할 수 있습니다. 통합 시나리오에 대한 자세한 내용은 [업데이트 관리와 System Center Configuration Manager 통합](oms-solution-updatemgmt-sccmintegration.md#configuration)을 참조하세요. [Windows 에이전트](../azure-monitor/platform/agent-windows.md)가 필요합니다. 이 에이전트는 Azure Virtual Machine을 등록하는 경우 자동으로 설치됩니다.

Azure Marketplace에서 배포된 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동으로 업데이트를 받도록 설정됩니다. 이 동작은 이 솔루션을 추가하거나 작업 영역에 Windows 가상 머신을 추가해도 달라지지 않습니다. 이 솔루션을 사용하여 업데이트를 적극적으로 관리하지 않는 경우 기본 동작(업데이트 자동 적용)이 적용됩니다.

> [!NOTE]
> 사용자가 컴퓨터를 다시 부팅 하는 것은 시스템이 아니라 사용자만 수행할 수 있도록 그룹 정책를 수정할 수 있습니다. 업데이트 관리에 사용자의 수동 상호 작용 없이 컴퓨터를 다시 부팅할 수 있는 권한이 없는 경우 관리 컴퓨터가 중지 될 수 있습니다.
>
> 자세한 내용은 [자동 업데이트에 대 한 그룹 정책 설정 구성](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)을 참조 하세요.

#### <a name="linux"></a>Linux

Linux의 경우 컴퓨터에 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 업데이트 리포지토리는 프라이빗 또는 공용일 수 있습니다. 업데이트 관리와 상호 작용하는 데 TLS 1.1 또는 TLS 1.2가 필요합니다. 이 솔루션은 여러 Azure Log Analytics 작업 영역에 보고하도록 구성된 Linux용 Log Analytics 에이전트를 지원하지 않습니다.  컴퓨터에 Python 2.x도 설치 되어 있어야 합니다.

Linux 용 Log Analytics 에이전트를 설치 하 고 최신 버전을 다운로드 하는 방법에 대 한 자세한 내용은 [linux 용 Log Analytics 에이전트](https://github.com/microsoft/oms-agent-for-linux)를 참조 하세요. Windows 용 Log Analytics 에이전트를 설치 하는 방법에 대 한 자세한 내용은 [windows 용 Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md)을 참조 하세요.

Azure Marketplace에서 사용할 수 있는 RHEL(주문형 Red Hat Enterprise Linux) 이미지에서 만든 가상 머신은 Azure에 배포된 [RHUI(Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)에 액세스하도록 등록됩니다. 다른 모든 Linux 배포판은 지원되는 방법에 따라 배포판 온라인 파일 리포지토리에서 업데이트되어야 합니다.

## <a name="permissions"></a>권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 권한에 대한 내용은 [역할 기반 액세스 - 업데이트 관리](automation-role-based-access-control.md#update-management)를 참조하세요.

## <a name="solution-components"></a>솔루션 구성 요소

솔루션은 다음 리소스로 이루어져 있습니다. 리소스는 Automation 계정에 추가됩니다. 이러한 리소스는 직접 연결된 에이전트이거나 Operations Manager에 연결된 관리 그룹에 속합니다.

### <a name="hybrid-worker-groups"></a>Hybrid Worker 그룹

이 솔루션을 사용하도록 설정하면 이 솔루션에 포함된 Runbook을 지원하기 위해 Log Analytics 작업 영역에 직접 연결된 모든 Windows 컴퓨터가 자동으로 Hybrid Runbook Worker로 구성됩니다.

솔루션에서 관리되는 각 Windows 컴퓨터의 경우 Automation 계정에 대한 **시스템 하이브리드 작업자 그룹**으로 **Hybrid Worker 그룹** 창에 나열됩니다. 솔루션은 명명 규칙 *Hostname FQDN_GUID*를 사용합니다. 계정에서 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없습니다. 이렇게 하려고 하면 실패합니다. 이러한 그룹은 관리 솔루션을 지원하는 용도로만 사용할 수 있습니다.

솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 경우, Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 Windows 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="management-packs"></a>관리 팩

System Center Operations Manager 관리 그룹이 Log Analytics 작업 영역에 연결되면 다음 관리 팩이 Operations Manager에 설치됩니다. 이 솔루션을 추가한 후 직접 연결된 Windows 컴퓨터에 이러한 관리 팩도 함께 설치됩니다. 이러한 관리 팩을 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

> [!NOTE]
> 관리 그룹 수준에서 구성 된 에이전트를 사용 하 여 작업 영역에 연결 하는 Operations Manager 1807 또는 2019 관리 그룹이 있는 경우이를 표시 하기 위해 최신 해결 방법은 **IsAutoRegistrationEnabled** 을 **True** 로 재정의 하는 것입니다. **microsoft.intelligencepacks.updateassessment. eventmessage** 규칙입니다.

솔루션 관리 팩을 업데이트 하는 방법에 대 한 자세한 내용은 [Azure Monitor 로그에 Operations Manager 연결](../azure-monitor/platform/om-agents.md)을 참조 하세요.

> [!NOTE]
> Operations Manger 에이전트가 있는 시스템을 업데이트 관리로 완전히 관리할 수 있으려면 에이전트를 Microsoft Monitoring Agent로 업데이트해야 합니다. 에이전트를 업데이트하는 방법을 알아보려면 [Operations Manager 에이전트를 업그레이드하는 방법](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)을 참조하세요. Operations Manager를 사용 하는 환경의 경우 2012 R2 UR 14 이상 System Center Operations Manager를 실행 해야 합니다.

## <a name="data-collection"></a>데이터 수집

### <a name="supported-agents"></a>지원되는 에이전트

다음 표는 이 솔루션이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |yes |솔루션은 Windows 에이전트에서 시스템 업데이트에 대한 정보를 수집한 후 필수 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |yes |이 솔루션은 Linux 에이전트에서 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필수 업데이트 설치를 시작합니다. |
| Operations Manager 관리 그룹 |yes |솔루션은 연결된 관리 그룹의 에이전트에서 시스템 업데이트에 대한 정보를 수집합니다.<br/>Operations Manager 에이전트에서 Azure Monitor 로그로 직접 연결 하는 것은 필요 하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |

### <a name="collection-frequency"></a>수집 빈도

관리되는 Windows 컴퓨터 각각에서 하루에 두 번 검사가 수행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하고 상태가 변경되었는지 확인합니다. 상태가 변경되었으면 준수 검사가 시작됩니다.

검색은 각 관리 되는 Linux 컴퓨터에 대해 매시간 수행 됩니다.

관리되는 컴퓨터의 업데이트 데이터를 대시보드에 표시하는 데 30분에서 6시간이 걸릴 수 있습니다.

업데이트 관리를 사용 하는 컴퓨터의 평균 Azure Monitor 로그 데이터 사용량은 매월 약 25MB입니다. 이 값은 근사값이며, 사용자 환경에 따라 변경될 수 있습니다. 사용자 환경을 모니터링하여 정확한 사용량을 확인하는 것이 좋습니다.

## <a name="ports"></a>네트워크 계획

다음 주소는 업데이트 관리를 위해 특별히 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 발생합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Windows 컴퓨터의 경우 Windows 업데이트에 필요한 끝점에 대 한 트래픽만 허용 해야 합니다.  [HTTP/프록시와 관련 된 문제](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)에서 필수 끝점의 업데이트 된 목록을 찾을 수 있습니다. 로컬 [Windows 업데이트 서버가](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)있는 경우 [WSUS 키](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)에 지정 된 서버에 대 한 트래픽만 허용 해야 합니다.

Red Hat Linux 컴퓨터의 경우 필요한 끝점 [은 RHUI content delivery server에 대 한 ip](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) 를 참조 하세요. 다른 Linux 배포의 경우 공급자 설명서를 참조 하세요.

Hybrid Runbook Worker에 필요한 포트에 대한 자세한 내용은 [Hybrid Worker 역할 포트](automation-hybrid-runbook-worker.md#hybrid-worker-role)를 참조하세요.

예외를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 반영합니다.

인터넷에 액세스할 수 없는 [컴퓨터 연결](../azure-monitor/platform/gateway.md) 의 지침에 따라 인터넷에 액세스할 수 없는 컴퓨터를 구성 합니다.

## <a name="view-update-assessments"></a>업데이트 평가 보기

Automation 계정에서 **업데이트 관리**를 선택하여 컴퓨터 상태를 확인합니다.

이 보기에서는 컴퓨터, 누락 업데이트, 업데이트 배포 및 예약된 업데이트 배포에 대한 정보를 제공합니다. **준수 열**에서 컴퓨터를 마지막으로 평가한 시간을 확인할 수 있습니다. **에이전트 업데이트 준비** 열에서 에이전트 업데이트의 상태를 확인할 수 있습니다. 문제가 있는 경우 링크를 선택하여 문제를 해결하기 위해 수행할 단계를 알려주는 문제 해결 설명서로 이동합니다.

컴퓨터, 업데이트 또는 배포에 대한 정보를 반환하는 로그 검색을 실행하려면 목록에서 항목을 선택합니다. 이렇게 하면 선택한 항목에 대한 쿼리가 있는 **로그 검색** 창이 열립니다.

![업데이트 관리 기본 보기](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>누락 업데이트 보기

**누락 업데이트**를 선택하여 컴퓨터에서 누락된 업데이트 목록을 표시합니다. 각 업데이트가 나열되며 선택 가능합니다. 업데이트가 필요한 컴퓨터 수, 운영 체제 및 세부 정보에 대한 링크와 관련된 정보가 표시됩니다. **로그 검색** 창에 업데이트에 대한 자세한 정보가 표시됩니다.

![누락 된 업데이트](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>업데이트 분류

다음 표에는 각 분류에 대한 정의와 함께 업데이트 관리의 업데이트 분류가 나열됩니다.

### <a name="windows"></a>Windows

|분류  |설명  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 애플리케이션 또는 파일에 대한 업데이트입니다.        |

### <a name="linux-2"></a>Linux

|분류  |설명  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 특성 또는 보안 업데이트에 중요하지 않은 기타 모든 업데이트입니다.        |

Linux의 경우 클라우드의 풍부한 데이터로 인해 평가 데이터가 표시되면서, 업데이트 관리가 클라우드에서 중요 및 보안 업데이트 간을 구분할 수 있습니다. 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리, CentOS에서는 기본적으로 이 정보를 사용할 수 없습니다. CentOS 컴퓨터가 다음 명령에 대해 보안 데이터를 반환하는 방식으로 구성된 경우 업데이트 관리는 분류에 따라 패치를 수행할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재, CentOS에서 네이티브 분류 데이터 가용성을 지원하는 메서드 지원 메서드가 없습니다. 따라서 이러한 기능을 직접 사용하도록 설정했을 수 있는 고객에게만 최선의 지원이 제공됩니다.

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager와 상호 작용

PC, 서버 및 모바일 디바이스를 관리하기 위해 System Center Configuration Manager에 투자하는 고객들은 소프트웨어 업데이트 관리에 도움을 얻기 위해 Configuration Manager의 강점과 완성도에 의존합니다. Configuration Manager는 해당 SUM(소프트웨어 업데이트 관리) 주기에 속합니다.

System Center Configuration Manager와 관리 솔루션을 통합하는 방법을 알아보려면 [업데이트 관리와 System Center Configuration Manager 통합](oms-solution-updatemgmt-sccmintegration.md)을 참조하세요.

### <a name="third-party-patches-on-windows"></a>Windows의 타사 패치

업데이트 관리는 로컬로 구성 된 업데이트 리포지토리를 사용 하 여 지원 되는 Windows 시스템을 패치 합니다. WSUS 또는 Windows 업데이트입니다. [System Center Updates Publisher](/sccm/sum/tools/updates-publisher)(Updates Publisher)와 같은 도구를 사용하면 WSUS에 사용자 지정 업데이트를 게시할 수 있습니다. 이 시나리오를 사용 하면 System Center Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어를 사용 하 여 업데이트 리포지토리로 패치 업데이트 관리 수 있습니다. 업데이트 게시자 구성 방법을 알아보려면 [업데이트 게시자 설치](/sccm/sum/tools/install-updates-publisher)를 참조하세요.

## <a name="patch-linux-machines"></a>Linux 컴퓨터 패치

다음 섹션에서는 Linux 패치의 잠재적 문제에 대해 설명합니다.

### <a name="unexpected-os-level-upgrades"></a>예기치 않은 운영 체제 수준 업그레이드

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서는 OS 수준 업그레이드가 패키지를 통해 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. 업데이트 관리는 관리자가 Linux 컴퓨터에서 로컬로 사용하는 것과 동일한 방법으로 패키지를 업데이트하므로 이 동작은 의도적 인 것입니다.

업데이트 관리 실행을 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 redhat-release-server.x86_64입니다.

![제외할 Linux용 패키지](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>중요/보안 패치가 적용되지 않음

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 그러면 지정된 조건에 맞는 머신에 적용되는 업데이트가 필터링됩니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 보강를 수행 하기 때문에 로컬 컴퓨터에 해당 정보가 없더라도 일부 업데이트는 업데이트 관리 보안에 영향을 주는 것으로 플래그가 지정 될 수 있습니다. 따라서 Linux 시스템에 중요 업데이트를 적용하면 해당 컴퓨터에 보안에 영향을 주는 것으로 표시되지 않은 일부 업데이트가 있을 수 있으며, 이 경우 업데이트가 적용되지 않습니다.

그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있으므로 해당 컴퓨터를 호환되지 않는 것으로 보고할 수 있습니다.

업데이트 분류에 따라 업데이트를 배포하는 것은 CentOS에서 기본적으로 지원되지 않습니다. CentOS에 대한 업데이트를 제대로 배포하려면 업데이트를 적용할 수 있도록 모든 분류를 선택합니다. SUSE의 경우 분류로 ‘기타 업데이트’*만* 선택하면 zypper(패키지 관리자)와 관련된 보안 업데이트나 해당 종속성이 먼저 필요한 경우에도 일부 보안 업데이트가 설치될 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 경우에 따라 업데이트 배포를 다시 실행해야 할 수도 있습니다. 확인하려면 업데이트 로그를 확인합니다.

### <a name="multi-tenant"></a> 테넌트 간 업데이트 배포

패치해야 하는 업데이트 관리에 대해 보고하는 또 다른 Azure 테넌트에 머신이 있는 경우 예약하려면 다음 해결 방법을 사용해야 합니다. `-ForUpdate` 스위치와 함께 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용하여 일정을 만들고 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet을 사용한 후 다른 테넌트의 머신을 `-NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제는 이 작업을 수행하는 방법의 예를 보여 줍니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>업데이트 관리 사용

시스템 패치를 시작하려면 업데이트 관리 솔루션을 사용하도록 설정해야 합니다. 다양한 방법으로 업데이트 관리에 컴퓨터를 등록할 수 있습니다. 아래에는 권장/지원되는 솔루션 등록 방법이 나와 있습니다.

* [가상 머신에서 등록](automation-onboard-solutions-from-vm.md)
* [여러 컴퓨터에서 찾아보기](automation-onboard-solutions-from-browse.md)
* [Automation 계정에서 등록](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation Runbook 사용](automation-onboard-solutions.md)

## <a name="next-steps"></a>다음 단계

Windows 가상 머신에 대한 업데이트를 관리하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Windows VM에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)

* [Azure Monitor 로그](../log-analytics/log-analytics-log-searches.md) 의 로그 검색을 사용 하 여 자세한 업데이트 데이터를 확인 합니다.
* 업데이트 배포 상태에 대한 [경고 만들기](automation-tutorial-update-management.md#configure-alerts).

* REST API를 통해 업데이트 관리와 상호 작용하는 방법은 [소프트웨어 업데이트 구성](/rest/api/automation/softwareupdateconfigurations)을 참조하세요.
* 업데이트 관리 문제를 해결하는 방법을 알아보려면 [업데이트 관리 문제 해결](troubleshoot/update-management.md)을 참조하세요.
