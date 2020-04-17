---
title: Azure의 업데이트 관리 솔루션
description: 이 문서에서는 Azure 업데이트 관리 솔루션을 사용하여 Windows 및 Linux 컴퓨터에 대한 업데이트를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: eaba4bf7760e150f2477ee743c797f94784b8506
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535505"
---
# <a name="update-management-solution-in-azure"></a>Azure의 업데이트 관리 솔루션

Azure 자동화의 업데이트 관리 솔루션을 사용하여 Azure, 온-프레미스 환경 및 기타 클라우드 환경에서 Windows 및 Linux 컴퓨터에 대한 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버에 필요한 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

다음 방법을 사용하여 가상 시스템(VM)에 대한 업데이트 관리를 활성화할 수 있습니다.

- 하나 이상의 Azure 컴퓨터에 대한 [Azure 자동화 계정과](automation-onboard-solutions-from-automation-account.md) 비 Azure 컴퓨터에 대해 수동으로.

- Azure 포털의 가상 시스템 페이지에서 단일 Azure VM의 경우 이 시나리오는 [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) 및 [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM에서 사용할 수 있습니다.

- Azure 포털의 **가상 시스템** 페이지에서 선택하여 여러 [Azure VM의](manage-update-multi.md) 경우 

> [!NOTE]
> 업데이트 관리 솔루션에서는 Log Analytics 작업 영역을 자동화 계정에 연결해야 합니다. 지원되는 지역의 최종 목록은 [Azure 작업 영역 매핑을](./how-to/region-mappings.md)참조하십시오. 지역 매핑은 자동화 계정과 별도의 리전에서 VM을 관리하는 기능에 영향을 주지 않습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure [리소스 관리자 템플릿을](automation-update-management-deploy-template.md) 사용할 수 있으므로 새 또는 기존 자동화 계정 및 구독의 Log Analytics 작업 영역에 업데이트 관리 솔루션을 배포할 수 있습니다.

## <a name="solution-overview"></a>솔루션 개요

Update Management에서 관리하는 컴퓨터는 다음 구성을 사용하여 평가를 수행하고 배포를 업데이트합니다.

* 윈도우 또는 리눅스에 대한 로그 분석 에이전트
* Linux용 PowerShell DSC(필요한 상태 구성)
* Automation Hybrid Runbook Worker
* 윈도우 머신에 대한 마이크로 소프트 업데이트 또는 윈도우 서버 업데이트 서비스 (WSUS)

다음 다이어그램은 솔루션이 작업 영역에서 연결된 모든 Windows Server 및 Linux 컴퓨터에 보안 업데이트를 평가하고 적용하는 방법을 보여 줍니다.

![업데이트 관리 프로세스 흐름](./media/automation-update-management/update-mgmt-updateworkflow.png)

업데이트 관리는 동일한 테넌트의 여러 구독에 있는 머신을 기본적으로 등록하는 데 사용할 수 있습니다.

패키지가 릴리스된 후 패치가 평가를 위해 Linux 컴퓨터에 표시되는 데 2~3시간이 걸립니다. Windows 컴퓨터의 경우 패치가 릴리스된 후 평가를 위해 패치가 표시되려면 12~15시간이 걸립니다.

컴퓨터가 업데이트 준수에 대한 검사를 완료한 후 에이전트는 정보를 Azure Monitor 로그에 대량으로 전달합니다. Windows 컴퓨터에서 규정 준수 검사는 기본적으로 12시간마다 실행됩니다.

검색 일정 외에도 Log Analytics 에이전트가 다시 시작되고 업데이트 설치 전 및 업데이트 설치 후 15분 이내에 업데이트 준수에 대한 검색이 시작됩니다.

Linux 컴퓨터의 경우 규정 준수 검사는 기본적으로 매시간 수행됩니다. Log Analytics 에이전트가 다시 시작되면 15분 이내에 규정 준수 검색이 시작됩니다.

이 솔루션은 동기화하도록 구성된 소스를 기반으로 컴퓨터가 최신 상태로 유지되는 방식을 보고합니다. Windows 컴퓨터가 WSUS에 보고하도록 구성된 경우 WSUS가 Microsoft 업데이트와 마지막으로 동기화된 시기에 따라 결과는 Microsoft 업데이트에서 보여지는 것과 다를 수 있습니다. 이 동작은 공용 리포지토리 대신 로컬 리포지토리에 보고하도록 구성된 Linux 컴퓨터에서도 마찬가지입니다.

> [!NOTE]
> 서비스에 제대로 보고하려면 업데이트 관리를 수행할 때 특정 URL 및 포트가 사용되도록 설정되어야 합니다. 이러한 요구 사항에 대한 자세한 내용은 [Hybrid Worker에 대한 네트워크 계획](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

예약된 배포를 만들어 업데이트가 필요한 컴퓨터에 소프트웨어 업데이트를 배포하고 설치할 수 있습니다. *선택 사항으로* 분류된 업데이트는 Windows 컴퓨터의 배포 범위에 포함되지 않습니다. 배포 범위에는 필수 업데이트만 포함됩니다.

예약된 배포는 적용 가능한 업데이트를 받는 대상 컴퓨터를 정의합니다. 특정 컴퓨터를 명시적으로 지정하거나 특정 컴퓨터 집합의 로그 검색을 기반으로 하는 [컴퓨터 그룹을](../azure-monitor/platform/computer-groups.md) 선택하거나 지정된 기준에 따라 Azure VM을 동적으로 선택하는 [Azure 쿼리를](automation-update-management-query-logs.md) 선택합니다. 이러한 그룹은 솔루션을 사용하도록 설정하는 관리 팩을 받는 컴퓨터를 결정하는 데만 사용되는 [범위 구성과](../azure-monitor/insights/solution-targeting.md)다릅니다.

또한 업데이트를 설치할 수 있는 기간을 승인하고 설정하는 일정을 지정합니다. 이 기간을 유지 관리 기간이라고 합니다. 유지 관리 기간의 20분 범위는 재부팅을 위해 예약되어 있으며, 한 번이 필요하다고 가정하고 적절한 재부팅 옵션을 선택했습니다. 패치가 예상보다 오래 걸리고 유지 관리 기간이 20분 미만인 경우 재부팅이 발생하지 않습니다.

Azure Automation의 runbook에서 업데이트가 설치됩니다. 이러한 Runbook을 볼 수 없으며 구성이 필요하지 않습니다. 업데이트 배포가 만들어지면 포함된 컴퓨터에 대해 지정된 시간에 마스터 업데이트 Runbook을 시작하는 일정을 만듭니다. 이 마스터 Runbook은 각 에이전트에서 하위 Runbook을 시작하여 필수 업데이트를 설치합니다.

업데이트 배포에 지정된 날짜와 시간에 대상 컴퓨터는 병렬로 배포를 실행합니다. 설치 전에, 업데이트가 여전히 필요한지 확인하기 위한 검사가 수행됩니다. WSUS 클라이언트 컴퓨터의 경우 WSUS에서 업데이트가 승인되지 않으면 업데이트 배포가 실패합니다.

하나 이상의 Log Analytics 작업 영역(멀티호밍이라고도 함)에 업데이트 관리를 위해 등록된 컴퓨터를 갖는 것은 지원되지 않습니다.

## <a name="clients"></a>클라이언트

### <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 업데이트 평가를 위해 지원되는 운영 체제가 나열되어 있습니다. 패치를 사용하려면 하이브리드 Runbook 작업자가 필요합니다. 하이브리드 Runbook 작업자 요구 사항에 대한 자세한 내용은 [Windows 하이브리드 Runbook 작업자](automation-windows-hrw-install.md) 및 Linux 하이브리드 [Runbook 작업자를](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)설치하기 위한 설치 가이드를 참조하십시오.

|운영 체제  |참고  |
|---------|---------|
|Windows 서버 2019(데이터 센터/데이터 센터 코어/표준)<br><br>Windows 서버 2016(데이터 센터/데이터 센터 코어/표준)<br><br>윈도우 서버 2012 R2(데이터 센터/표준)<br><br>Windows Server 2012 || 
|윈도우 서버 2008 R2 (RTM 및 SP1 표준)| 업데이트 관리는 이 운영 체제에 대한 평가 수행만 지원하며 [하이브리드 Runbook 작업자가](automation-windows-hrw-install.md) Windows Server 2008 R2에서 지원되지 않기 때문에 패치가 지원되지 않습니다. |
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트는 업데이트 리포지토리에 액세스해야 합니다. 분류 기반 패치는 `yum` CentOS가 RTM 릴리스에 없는 보안 데이터를 반환해야 합니다. CentOS의 분류 기반 패치에 대한 자세한 내용은 [Linux의 분류 업데이트 를](automation-view-update-assessments.md#linux-2)참조하십시오.          |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트는 업데이트 리포지토리에 액세스해야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트는 업데이트 리포지토리에 액세스해야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04(x86/x64)      |Linux 에이전트는 업데이트 리포지토리에 액세스해야 합니다.         |

> [!NOTE]
> Azure 가상 시스템 규모 집합은 업데이트 관리를 통해 관리할 수 있습니다. 업데이트 관리는 기본 이미지가 아닌 인스턴스 자체에서 작동합니다. 모든 VM 인스턴스가 한 번에 업데이트되지 않도록 증분 방식으로 업데이트를 예약해야 합니다.
> Azure가 아닌 컴퓨터 [온보보드](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)단계의 단계를 수행하여 가상 시스템 확장 집합에 대한 노드를 추가할 수 있습니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

다음 표에는 지원되지 않는 운영 체제가 나열되어 있습니다.

|운영 체제  |참고  |
|---------|---------|
|Windows 클라이언트     | 클라이언트 운영 체제(예: Windows 7 및 Windows 10)는 지원되지 않습니다.        |
|Windows Server 2016 Nano Server     | 지원되지 않습니다.       |
|Azure Kubernetes 서비스 노드 | 지원되지 않습니다. [Azure Kubernetes 서비스(AKS)의 Linux 노드에 보안 및 커널 업데이트 적용에](../aks/node-updates-kured.md) 설명된 패치 프로세스 사용|

### <a name="client-requirements"></a>클라이언트 요구 사항

다음 정보는 OS 관련 클라이언트 요구 사항을 설명합니다. 추가 지침은 [네트워크 계획](#ports)을 참조하십시오.

#### <a name="windows"></a>Windows

Windows 에이전트는 WSUS 서버와 통신하도록 구성되어야 하거나 Microsoft 업데이트에 액세스해야 합니다.

구성 관리자와 함께 업데이트 관리를 사용할 수 있습니다. 통합 시나리오에 대한 자세한 내용은 [구성 관리자와 업데이트 관리 통합을 참조하십시오.](oms-solution-updatemgmt-sccmintegration.md#configuration) [로그 분석 Windows 에이전트가](../azure-monitor/platform/agent-windows.md) 필요합니다. Azure VM을 온보딩하는 경우 에이전트가 자동으로 설치됩니다.

기본적으로 Azure 마켓플레이스에서 배포되는 Windows VM은 Windows 업데이트 서비스에서 자동 업데이트를 받도록 설정되어 있습니다. 이 솔루션을 추가하거나 작업 영역에 Windows VM을 추가할 때 이 동작은 변경되지 않습니다. 이 솔루션을 사용하여 업데이트를 적극적으로 관리하지 않는 경우 기본 동작(업데이트 자동 적용)이 적용됩니다.

> [!NOTE]
> 사용자는 그룹 정책을 수정하여 시스템대신 사용자만 컴퓨터를 재부팅할 수 있습니다. 업데이트 관리에서 사용자의 수동 상호 작용 없이 컴퓨터를 다시 부팅할 권한이 없는 경우 관리되는 컴퓨터가 멈출 수 있습니다.
>
> 자세한 내용은 [자동 업데이트에 대한 그룹 정책 설정 구성을](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)참조하십시오.

#### <a name="linux"></a>Linux

Linux의 경우 컴퓨터에서 업데이트 리포지토리에 액세스해야 합니다. 업데이트 리포지토리는 프라이빗 또는 공용일 수 있습니다. 업데이트 관리와 상호 작용하는 데 TLS 1.1 또는 TLS 1.2가 필요합니다. 두 개 이상의 로그 분석 작업 영역에 보고하도록 구성된 Linux용 로그 분석 에이전트는 이 솔루션에서 지원되지 않습니다. 또한 기기에 파이썬 2.x가 설치되어 있어야 합니다.

Linux용 로그 분석 에이전트를 설치하고 최신 버전을 다운로드하는 방법에 대한 자세한 내용은 [Linux용 로그 분석 에이전트를](../azure-monitor/platform/agent-linux.md)참조하십시오. Windows용 로그 분석 에이전트를 설치하는 방법에 대한 자세한 내용은 [Windows 컴퓨터를 Azure 모니터에 연결 을](../log-analytics/log-analytics-windows-agent.md)참조하십시오.

Azure 마켓플레이스에서 사용할 수 있는 주문형 Red Hat 엔터프라이즈 Linux(RHEL) 이미지에서 생성된 VM은 Azure에 배포된 [Red Hat 업데이트 인프라(RHUI)에](../virtual-machines/workloads/redhat/redhat-rhui.md) 액세스하도록 등록됩니다. 다른 Linux 배포판은 배포판의 지원되는 메서드를 사용하여 배포판의 온라인 파일 리포지토리에서 업데이트해야 합니다.

## <a name="permissions"></a>사용 권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 권한에 대해 자세히 알아보려면 [역할 기반 액세스 - 업데이트 관리를](automation-role-based-access-control.md#update-management-permissions)참조하십시오.

## <a name="solution-components"></a>솔루션 구성 요소

솔루션은 다음 리소스로 이루어져 있습니다. 이러한 리소스는 솔루션을 사용하도록 설정하면 자동화 계정에 자동으로 추가됩니다. 

### <a name="hybrid-worker-groups"></a>Hybrid Worker 그룹

이 솔루션을 사용하도록 설정하면 Log Analytics 작업 영역에 직접 연결된 모든 Windows 컴퓨터가 이 솔루션에 포함된 Runbook을 지원하기 위해 하이브리드 Runbook 워커로 자동으로 구성됩니다.

솔루션에서 관리하는 각 Windows 컴퓨터는 하이브리드 **작업자 그룹** 창에 자동화 계정에 대한 **시스템 하이브리드 작업자 그룹으로** 나열됩니다. 솔루션은 *호스트 이름 FQDN_GUID* 명명 규칙을 사용합니다. 계정에서 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없습니다. 시도하면 시도가 실패합니다. 이러한 그룹은 이 관리 솔루션만 지원하기 위한 것입니다.

솔루션 및 하이브리드 Runbook Worker 그룹 구성원 모두에 대해 동일한 계정을 사용하는 경우 자동화 실행책을 지원하기 위해 자동화 계정의 하이브리드 Runbook Worker 그룹에 Windows 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="management-packs"></a>관리 팩

시스템 센터 운영 관리자 관리 그룹이 [Log Analytics 작업 영역에 연결되어](../azure-monitor/platform/om-agents.md)있으면 다음 관리 팩이 운영 관리자에 설치됩니다. 이러한 관리 팩은 솔루션을 추가한 후 직접 연결된 Windows 컴퓨터에도 설치됩니다. 이러한 관리 팩을 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

> [!NOTE]
> 로그 데이터를 수집하기 위해 관리 그룹에 구성된 에이전트가 있는 로그 분석 작업 영역에 연결된 운영 관리자 1807 또는 2019 관리 그룹이 있는 경우 업데이트 관리로 관리하기 위해 **True** 다음 규칙을 **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 재정의해야 **합니다.**

솔루션 관리 팩을 업데이트하는 방법에 대한 자세한 내용은 [운영 관리자를 Azure Monitor 로그에 연결](../azure-monitor/platform/om-agents.md)참조하세요.

> [!NOTE]
> 운영 관리 에이전트가 있는 컴퓨터의 경우 업데이트 관리에서 완전히 관리하려면 에이전트를 Windows 또는 Linux용 로그 분석 에이전트로 업데이트해야 합니다. 에이전트를 업데이트하는 방법을 알아보려면 [Operations Manager 에이전트를 업그레이드하는 방법](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)을 참조하세요. Operations Manager를 사용하는 환경에서는 시스템 센터 운영 관리자 2012 R2 UR 14 이상을 실행해야 합니다.

## <a name="data-collection"></a>데이터 수집

### <a name="supported-agents"></a>지원되는 에이전트

다음 표에서는 솔루션이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | Description |
| --- | --- | --- |
| Windows 에이전트 |예 |솔루션은 Windows 에이전트에서 시스템 업데이트에 대한 정보를 수집한 후 필수 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |이 솔루션은 Linux 에이전트에서 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필수 업데이트 설치를 시작합니다. |
| Operations Manager 관리 그룹 |예 |솔루션은 연결된 관리 그룹의 에이전트에서 시스템 업데이트에 대한 정보를 수집합니다.<br/><br/>운영 관리자 에이전트에서 Azure Monitor 로그에 직접 연결할 필요는 없습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |

### <a name="collection-frequency"></a>수집 빈도

검사는 관리되는 각 Windows 컴퓨터에 대해 하루에 두 번 수행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하고 상태가 변경되었는지 확인합니다. 상태가 변경되었으면 준수 검사가 시작됩니다.

검사는 관리되는 각 Linux 컴퓨터에 대해 매시간 수행됩니다.

대시보드가 관리되는 컴퓨터에서 업데이트된 데이터를 표시하는 데 30분에서 6시간 정도 걸릴 수 있습니다.

업데이트 관리를 사용하는 컴퓨터의 Azure Monitor 로그에 의한 평균 데이터 사용량은 매월 약 25MB입니다. 이 값은 근사치일 뿐이며 환경에 따라 변경될 수 있습니다. 정확한 사용량을 추적하려면 환경을 모니터링하는 것이 좋습니다. 데이터 사용량을 분석하는 자세한 내용은 [사용 및 비용 관리를](../azure-monitor/platform/manage-cost-storage.md)참조하십시오.

## <a name="network-planning"></a><a name="ports"></a>네트워크 계획

다음 주소는 업데이트 관리를 위해 특별히 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 발생합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|* .azure-automation.net | *.azure-automation.us|

Windows 컴퓨터의 경우 Windows 업데이트에 필요한 모든 끝점에 대한 트래픽도 허용해야 합니다. [HTTP/프록시](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)와 관련된 문제에서 업데이트된 필수 끝점 목록을 찾을 수 있습니다. 로컬 [Windows Update 서버가](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)있는 경우 [WSUS 키에](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)지정된 서버에 대한 트래픽도 허용해야 합니다.

Red Hat Linux 컴퓨터의 경우 필요한 끝점에 [대한 RHUI 콘텐츠 전송 서버에 대한 IP를](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 참조하십시오. 다른 Linux 배포판의 경우 공급자 설명서를 참조하세요.

하이브리드 Runbook 작업자에 필요한 포트에 대한 자세한 내용은 [하이브리드 작업자 역할 포트를](automation-hybrid-runbook-worker.md#hybrid-worker-role)참조하십시오.

예외를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 IP 범위를 다운로드할](https://www.microsoft.com/download/details.aspx?id=41653)수 있습니다. 이 파일은 매주 업데이트되며 현재 배포된 범위와 IP 범위에 대한 향후 변경 사항을 반영합니다.

인터넷에 액세스할 [수 없는 컴퓨터 연결의](../azure-monitor/platform/gateway.md) 지침을 따라 인터넷에 액세스할 수 없는 컴퓨터를 구성합니다.

## <a name="update-classifications"></a>업데이트 분류

다음 표에는 각 분류에 대한 정의와 함께 업데이트 관리의 업데이트 분류가 나열됩니다.

### <a name="windows"></a>Windows

|분류  |Description  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 애플리케이션 또는 파일에 대한 업데이트입니다.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|분류  |Description  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 본질적으로 중요하지 않거나 보안 업데이트가 아닌 다른 모든 업데이트.        |

Linux의 경우 업데이트 관리는 클라우드의 데이터 보강으로 인한 평가 데이터를 표시하면서 클라우드에서 중요한 업데이트와 보안 업데이트를 구분할 수 있습니다. 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리 CentOS에는 RTM 버전에서 이 정보를 사용할 수 없습니다. 다음 명령에 대한 보안 데이터를 반환하도록 구성된 CentOS 컴퓨터가 있는 경우 업데이트 관리는 분류에 따라 패치할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재 CentOS에서 기본 분류 데이터 가용성을 활성화하는 지원되는 방법이 없습니다. 현재 는 이 기능을 자체적으로 활성화한 고객에게만 최상의 지원이 제공됩니다. 

Red Hat 엔터프라이즈 버전 6의 업데이트를 분류하려면 yum-security 플러그인을 설치해야 합니다. 레드 햇 엔터프라이즈 리눅스 7에, 플러그인은 이미 yum 자체의 일부, 아무것도 설치 할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술 문서를](https://access.redhat.com/solutions/10021)참조하십시오.

## <a name="integrate-with-configuration-manager"></a>Configuration Manager와 상호 작용

PC, 서버 및 모바일 장치를 관리하기 위해 Microsoft 엔드포인트 구성 관리자에 투자한 고객은 구성 관리자의 강도와 성숙도에 의존하여 소프트웨어 업데이트를 관리할 수 있습니다. Configuration Manager는 해당 SUM(소프트웨어 업데이트 관리) 주기에 속합니다.

관리 솔루션을 구성 관리자와 통합하는 방법에 대해 알아보려면 [구성 관리자와 업데이트 관리 통합을](oms-solution-updatemgmt-sccmintegration.md)참조하십시오.

### <a name="third-party-updates-on-windows"></a>Windows에서 타사 업데이트

업데이트 관리는 지원되는 Windows 시스템을 업데이트하기 위해 로컬로 구성된 업데이트 리포지토리를 기반으로 합니다. 이것은 WSUS 또는 Windows 업데이트입니다. 시스템 [센터 업데이트 게시자(업데이트](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) 게시자)와 같은 도구를 사용하면 WSUS를 사용하여 사용자 지정 업데이트를 가져오고 게시할 수 있습니다. 이 시나리오를 사용하면 업데이트 관리를 사용하여 구성 관리자를 타사 소프트웨어와 함께 업데이트 리포지토리로 사용하는 컴퓨터를 업데이트할 수 있습니다. 업데이트 게시자 구성 방법을 알아보려면 [업데이트 게시자 설치](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)를 참조하세요.

## <a name="enable-update-management"></a><a name="onboard"></a>업데이트 관리 사용

시스템 업데이트를 시작하려면 업데이트 관리 솔루션을 사용하도록 설정해야 합니다. 다음은 솔루션을 온보보드로 하는 권장 및 지원되는 방법입니다.

- [가상 머신에서 등록](automation-onboard-solutions-from-vm.md)

- [여러 컴퓨터에서 찾아보기](automation-onboard-solutions-from-browse.md)

- [Automation 계정에서 등록](automation-onboard-solutions-from-automation-account.md)

- [Azure Automation Runbook 사용](automation-onboard-solutions.md)

- [Azure 리소스 관리자 템플릿 사용](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>다음 단계

Azure 자동화 [FAQ를](automation-faq.md) 검토하여 이 솔루션에 대한 일반적인 질문을 검토합니다.
