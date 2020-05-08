---
title: Azure Automation 업데이트 관리
description: 이 문서에서는 Windows 및 Linux 컴퓨터에 대 한 업데이트를 관리 하는 업데이트 관리 기능에 대해 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: ca5d8c35aea06143e058aade473282a038212605
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872156"
---
# <a name="update-management-in-azure-automation"></a>Azure Automation 업데이트 관리

Azure Automation에서 업데이트 관리를 사용 하 여 Azure, 온-프레미스 환경 및 기타 클라우드 환경에서 Windows 및 Linux 컴퓨터에 대 한 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속 하 게 평가 하 고 서버에 필요한 업데이트를 설치 하는 프로세스를 관리할 수 있습니다.

다음 방법을 사용 하 여 Vm (가상 머신)에 대 한 업데이트 관리를 사용 하도록 설정할 수 있습니다.

* 하나 이상의 Azure 컴퓨터에 대 한 [Azure Automation 계정](automation-onboard-solutions-from-automation-account.md) 에서
* 비 Azure 컴퓨터의 경우 수동으로.
* Azure Portal의 가상 컴퓨터 페이지에서 단일 Azure VM의 경우 이 시나리오는 [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) 및 [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) vm에서 사용할 수 있습니다.
* [여러 Azure vm](manage-update-multi.md) 의 경우 Azure Portal의 가상 컴퓨터 페이지에서 vm을 선택 합니다.

> [!NOTE]
> 업데이트 관리 Log Analytics 작업 영역을 Automation 계정에 연결 해야 합니다. 지원 되는 지역에 대 한 명확한 목록은 [Azure 작업 영역 매핑](how-to/region-mappings.md)을 참조 하세요. 지역 매핑은 Automation 계정과 별도의 지역에 있는 Vm을 관리 하는 기능에 영향을 주지 않습니다.

새 또는 기존 Automation 계정 및 Log Analytics 작업 영역에 업데이트 관리를 배포 하는 데 도움이 되는 [Azure Resource Manager 템플릿을](automation-update-management-deploy-template.md) 사용할 수 있습니다.

> [!NOTE]
> 업데이트 관리로 구성 된 컴퓨터는 Azure Automation에서 사용자 지정 스크립트를 실행 하는 데 사용할 수 없습니다. 이 컴퓨터는 Microsoft 서명 업데이트 스크립트만 실행할 수 있습니다. 

## <a name="update-management-overview"></a>업데이트 관리 개요

업데이트 관리에서 관리 하는 컴퓨터는 다음 구성을 사용 하 여 평가를 수행 하 고 배포를 업데이트 합니다.

* Windows 또는 Linux 용 Log Analytics 에이전트
* Linux용 PowerShell DSC(필요한 상태 구성)
* Automation Hybrid Runbook Worker
* Windows 컴퓨터용 WSUS (Microsoft 업데이트 또는 Windows Server Update Services)

다음 다이어그램은 업데이트 관리 작업 영역에서 연결 된 모든 Windows Server 및 Linux 컴퓨터에 보안 업데이트를 평가 하 고 적용 하는 방법을 보여 줍니다.

![업데이트 관리 워크플로](./media/automation-update-management/update-mgmt-updateworkflow.png)

업데이트 관리는 동일한 테넌트의 여러 구독에 있는 머신을 기본적으로 등록하는 데 사용할 수 있습니다.

패키지를 릴리스한 후에는 패치를 평가를 위해 Linux 컴퓨터에 표시 하는 데 2 ~ 3 시간이 걸립니다. Windows 컴퓨터의 경우 패치가 릴리스된 후 평가를 위해 패치가 표시 되는 데 12 ~ 15 시간이 걸립니다.

컴퓨터가 업데이트 준수에 대 한 검사를 완료 한 후 에이전트는 Azure Monitor 로그에 정보를 대량으로 전달 합니다. Windows 컴퓨터에서 준수 검사는 기본적으로 12 시간 마다 실행 됩니다.

검사 일정 외에도 업데이트 준수에 대 한 검사는 Log Analytics 에이전트가 다시 시작 되 고 업데이트 설치 전 및 업데이트 설치 후 15 분 이내에 시작 됩니다.

Linux 컴퓨터의 경우 호환성 검사는 기본적으로 매시간 수행 됩니다. Log Analytics 에이전트를 다시 시작 하는 경우 15 분 내에 준수 검사가 시작 됩니다.

업데이트 관리은 동기화 하도록 구성 된 원본을 기반으로 하는 컴퓨터의 최신 상태를 보고 합니다. Windows 컴퓨터가 WSUS에 보고 하도록 구성 된 경우 WSUS를 마지막으로 Microsoft 업데이트와 동기화 한 시기에 따라 결과가 Microsoft 업데이트 표시 되는 내용과 다를 수 있습니다. 이 동작은 공용 리포지토리가 아닌 로컬 리포지토리에 보고 하도록 구성 된 Linux 컴퓨터에 대해 동일 합니다.

> [!NOTE]
> 서비스에 제대로 보고하려면 업데이트 관리를 수행할 때 특정 URL 및 포트가 사용되도록 설정되어야 합니다. 이러한 요구 사항에 대해 자세히 알아보려면 [네트워크 구성](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning)을 참조 하세요.

예약 된 배포를 만들어 업데이트가 필요한 컴퓨터에 소프트웨어 업데이트를 배포 하 고 설치할 수 있습니다. 선택적으로 분류 된 업데이트는 Windows 컴퓨터의 배포 범위에 포함 되지 않습니다. 배포 범위에는 필수 업데이트만 포함됩니다.

예약 된 배포는 적용 가능한 업데이트를 수신 하는 대상 컴퓨터를 정의 합니다. 특정 컴퓨터를 명시적으로 지정 하거나 특정 컴퓨터 집합 (또는 지정 된 조건에 따라 Azure Vm을 동적으로 선택 하는 [azure 쿼리](automation-update-management-query-logs.md) )의 로그 검색을 기반으로 하는 [컴퓨터 그룹](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups) 을 선택 하 여이를 수행 합니다. 이러한 그룹은 업데이트 관리를 사용할 수 있도록 구성을 수신 하는 컴퓨터의 대상을 제어 하는 데 사용 되는 [범위 구성과](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)다릅니다. 이렇게 하면 업데이트 준수를 수행 하 고 보고 하지 않으며 승인 된 필수 업데이트를 설치할 수 없습니다.

배포를 정의 하는 동안 업데이트를 설치할 수 있는 기간을 승인 하 고 설정 하는 일정을 지정 합니다. 이 기간을 유지 관리 기간 이라고 합니다. 유지 관리 기간의 20 분 범위는 다시 부팅 하도록 예약 되어 있으며,이 중 하나가 필요 하 고 적절 한 다시 부팅 옵션을 선택 했다고 가정 합니다. 패치가 예상 보다 오래 걸리고 유지 관리 기간에 20 분 미만이 면 다시 부팅이 발생 하지 않습니다.

Azure Automation의 runbook에서 업데이트가 설치됩니다. 이러한 runbook은 볼 수 없으며, 구성이 필요 하지 않습니다. 업데이트 배포를 만들 때 포함 된 컴퓨터에 대해 지정 된 시간에 마스터 업데이트 runbook을 시작 하는 일정을 만듭니다. 이 마스터 Runbook은 각 에이전트에서 하위 Runbook을 시작하여 필수 업데이트를 설치합니다.

업데이트 배포에 지정 된 날짜 및 시간에 대상 컴퓨터는 배포를 병렬로 실행 합니다. 설치 전에, 업데이트가 여전히 필요한지 확인하기 위한 검사가 수행됩니다. Wsus 클라이언트 컴퓨터의 경우 업데이트가 WSUS에서 승인 되지 않으면 업데이트 배포가 실패 합니다.
업데이트 관리에 대해 등록 된 컴퓨터가 둘 이상의 Log Analytics 작업 영역 (멀티 호 밍 라고도 함)에 있는 경우 지원 되지 않습니다.

## <a name="clients"></a>클라이언트

### <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 업데이트 평가에 대해 지원 되는 운영 체제가 나와 있습니다. 패치를 적용 하려면 Hybrid Runbook Worker 필요 합니다. Hybrid Runbook Worker 요구 사항에 대 한 자세한 내용은 [Windows Hybrid Runbook Worker 배포](automation-windows-hrw-install.md) 및 [Linux Hybrid Runbook Worker 배포](automation-linux-hrw-install.md)를 참조 하세요.

> [!NOTE]
> Linux 컴퓨터의 업데이트 평가는 Automation 계정 및 Log Analytics 작업 영역 [매핑 테이블](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)에 나열 된 특정 지역 에서만 지원 됩니다. 

|운영 체제  |참고  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM 및 SP1 표준)| 업데이트 관리은이 운영 체제에 대 한 평가만 지원 합니다. [Hybrid Runbook Worker](automation-windows-hrw-install.md) Windows Server 2008 r 2에서 지원 되지 않으므로 패치를 지원 하지 않습니다. |
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트는 업데이트 리포지토리에 액세스 해야 합니다. 분류 기반 패치를 적용 `yum` 하려면 CENTOS가 RTM 릴리스에 없는 보안 데이터를 반환 해야 합니다. CentOS의 분류 기반 패치에 대 한 자세한 내용은 [Linux의 업데이트 분류](automation-view-update-assessments.md#linux-2)를 참조 하세요.          |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트는 업데이트 리포지토리에 액세스 해야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트는 업데이트 리포지토리에 액세스 해야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04(x86/x64)      |Linux 에이전트는 업데이트 리포지토리에 액세스 해야 합니다.         |

> [!NOTE]
> Azure 가상 머신 확장 집합은 업데이트 관리를 통해 관리할 수 있습니다. 업데이트 관리은 기본 이미지가 아닌 인스턴스 자체에 대해 작동 합니다. 모든 VM 인스턴스가 한 번에 업데이트 되는 것이 아니라 증분 방식으로 업데이트를 예약 해야 합니다.
> [비 Azure 컴퓨터](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)등록의 단계를 수행 하 여 가상 머신 확장 집합에 대 한 노드를 추가할 수 있습니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

다음 표에서는 지원 되지 않는 운영 체제를 보여 줍니다.

|운영 체제  |참고  |
|---------|---------|
|Windows 클라이언트     | 클라이언트 운영 체제(예: Windows 7 및 Windows 10)는 지원되지 않습니다.        |
|Windows Server 2016 Nano Server     | 지원 안 됨       |
|Azure Kubernetes Service 노드 | 지원 안 됨 [Azure Kubernetes 서비스에서 Linux 노드에 보안 및 커널 업데이트 적용 (AKS)](../aks/node-updates-kured.md) 에 설명 된 패치 처리를 사용 합니다.|

### <a name="client-requirements"></a>클라이언트 요구 사항

다음 정보는 운영 체제별 클라이언트 요구 사항을 설명 합니다. 추가 지침은 [네트워크 계획](#ports)을 참조 하세요.

#### <a name="windows"></a>Windows

WSUS 서버와 통신 하도록 Windows 에이전트를 구성 하거나 Microsoft 업데이트에 대 한 액세스가 필요 합니다. Windows 용 Log Analytics 에이전트를 설치 하는 방법에 대 한 자세한 내용은 [Azure Monitor에 windows 컴퓨터 연결](../log-analytics/log-analytics-windows-agent.md)을 참조 하세요.

Microsoft 끝점 Configuration Manager와 함께 업데이트 관리를 사용할 수 있습니다. 통합 시나리오에 대해 자세히 알아보려면 [업데이트 관리와 Configuration Manager 통합](updatemgmt-mecmintegration.md#configuration)을 참조 하세요. Configuration Manager 환경의 사이트에서 관리 하는 Windows 서버에는 [windows 용 Log Analytics 에이전트가](../azure-monitor/platform/agent-windows.md) 필요 합니다. 

기본적으로 Azure Marketplace에서 배포 된 Windows Vm은 Windows 업데이트 서비스에서 자동 업데이트를 받도록 설정 됩니다. Windows Vm을 작업 영역에 추가 하는 경우에는이 동작이 변경 되지 않습니다. 업데이트 관리를 사용 하 여 업데이트를 적극적으로 관리 하지 않는 경우 기본 동작 (업데이트 자동 적용)이 적용 됩니다.

> [!NOTE]
> 시스템이 아니라 사용자만 컴퓨터를 다시 부팅할 수 있도록 그룹 정책를 수정할 수 있습니다. 업데이트 관리 사용자의 수동 상호 작용 없이 컴퓨터를 다시 부팅할 수 있는 권한이 없는 경우 관리 컴퓨터를 사용할 수 있습니다. 자세한 내용은 [자동 업데이트에 대 한 그룹 정책 설정 구성](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)을 참조 하세요.

#### <a name="linux"></a>Linux

Linux의 경우 컴퓨터에서 개인 또는 공용의 업데이트 리포지토리에 액세스 해야 합니다. 업데이트 관리와 상호 작용하는 데 TLS 1.1 또는 TLS 1.2가 필요합니다. 업데이트 관리는 둘 이상의 Log Analytics 작업 영역에 보고 하도록 구성 된 Linux 용 Log Analytics 에이전트를 지원 하지 않습니다. 컴퓨터에 Python 2.x도 설치 되어 있어야 합니다.

> [!NOTE]
> Linux 컴퓨터의 업데이트 평가는 특정 지역 에서만 지원 됩니다. Automation 계정 및 Log Analytics 작업 영역 [매핑 표](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)를 참조 하세요. 

Linux 용 Log Analytics 에이전트를 설치 하 고 최신 버전을 다운로드 하는 방법에 대 한 자세한 내용은 [linux 용 Log Analytics 에이전트](../azure-monitor/platform/agent-linux.md)를 참조 하세요. 

Azure Marketplace에서 사용할 수 있는 주문형 Red Hat Enterprise Linux (RHEL) 이미지에서 만든 Vm은 Azure에 배포 된 [RHUI (Red Hat Update Infrastructure)](../virtual-machines/workloads/redhat/redhat-rhui.md) 에 액세스 하도록 등록 됩니다. 배포에서 지원 되는 메서드를 사용 하 여 배포의 온라인 파일 리포지토리에서 다른 모든 Linux 배포를 업데이트 해야 합니다.

## <a name="permissions"></a>사용 권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 사용 권한에 대 한 자세한 내용은 [역할 기반 액세스 – 업데이트 관리](automation-role-based-access-control.md#update-management-permissions)를 참조 하세요.

## <a name="update-management-components"></a>업데이트 관리 구성 요소

업데이트 관리은이 섹션에 설명 된 리소스를 사용 합니다. 이러한 리소스는 업데이트 관리을 사용 하도록 설정 하면 Automation 계정에 자동으로 추가 됩니다.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker 그룹

업데이트 관리를 사용 하도록 설정 하면 Log Analytics 작업 영역에 직접 연결 된 모든 Windows 컴퓨터가 업데이트 관리를 지 원하는 runbook을 지원 하기 위한 Hybrid Runbook Worker 자동으로 구성 됩니다.

업데이트 관리에서 관리 되는 각 Windows 컴퓨터는 하이브리드 작업자 그룹 창에 Automation 계정에 대 한 시스템 Hybrid worker 그룹으로 나열 됩니다. 그룹은 명명 규칙 `Hostname FQDN_GUID` 을 사용 합니다. 계정에서 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없습니다. 시도 하면 시도가 실패 합니다. 이러한 그룹은 업데이트 관리 지원 하기 위한 것입니다.

업데이트 관리 및 Hybrid Runbook Worker 그룹 구성원 자격에 동일한 계정을 사용 하는 경우 automation runbook을 지원 하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 Windows 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="management-packs"></a>관리 팩

Operations Manager 관리 그룹이 [Log Analytics 작업 영역에 연결](../azure-monitor/platform/om-agents.md)된 경우에는 Operations Manager에 다음 관리 팩이 설치 됩니다. 이러한 관리 팩은 직접 연결 된 Windows 컴퓨터에 대 한 업데이트 관리에도 설치 됩니다. 이러한 관리 팩을 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

> [!NOTE]
> 로그 데이터를 수집 하기 위해 관리 그룹에 구성 된 에이전트를 사용 하 여 Operations Manager 1807 또는 2019 관리 그룹이 Log Analytics 작업 영역에 연결 된 경우 매개 변수 `IsAutoRegistrationEnabled` 를 재정의 하 고 **Microsoft.intelligencepacks.updateassessment** 규칙에서 True로 설정 해야 합니다.

관리 팩 업데이트에 대 한 자세한 내용은 [Azure Monitor 로그에 Operations Manager 연결](../azure-monitor/platform/om-agents.md)을 참조 하세요.

> [!NOTE]
> Log Analytics 에이전트를 사용 하 여 컴퓨터를 완벽 하 게 관리 하려면 Windows 용 Log Analytics 에이전트 또는 Linux 용 Log Analytics 에이전트로 업데이트 해야 합니다. 업데이트 관리 에이전트를 업데이트하는 방법을 알아보려면 [Operations Manager 에이전트를 업그레이드하는 방법](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)을 참조하세요. Operations Manager를 사용 하는 환경에서는 2012 R2 UR 14 이상 System Center Operations Manager를 실행 해야 합니다.

## <a name="data-collection"></a>데이터 수집

### <a name="supported-sources"></a>지원 되는 원본

다음 표에서는 업데이트 관리에서 지 원하는 연결 된 원본을 설명 합니다.

| 연결된 원본 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |예 |업데이트 관리 Windows 에이전트에서 시스템 업데이트에 대 한 정보를 수집 하 고 필수 업데이트 설치를 시작 합니다. |
| Linux 에이전트 |예 |업데이트 관리은 Linux 에이전트에서 시스템 업데이트에 대 한 정보를 수집한 다음 지원 되는 배포에 필요한 업데이트의 설치를 시작 합니다. |
| Operations Manager 관리 그룹 |예 |업데이트 관리에서 연결된 관리 그룹의 에이전트로부터 시스템 업데이트에 대한 정보를 수집합니다.<br/><br/>Operations Manager 에이전트에서 Azure Monitor 로그로 직접 연결 하는 것은 필요 하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |

### <a name="collection-frequency"></a>수집 빈도

업데이트 관리는 다음 규칙을 사용 하 여 관리 컴퓨터에서 데이터를 검색 합니다. 대시보드는 관리 컴퓨터의 업데이트 된 데이터를 표시 하는 데 30 분에서 6 시간까지 걸릴 수 있습니다.

* 각 Windows 컴퓨터 업데이트 관리는 각 컴퓨터에 대해 하루에 두 번 검색을 수행 합니다. 15 분 마다 Windows API에서 마지막 업데이트 시간을 쿼리하여 상태가 변경 되었는지 여부를 확인 합니다. 상태가 변경 된 경우 업데이트 관리 준수 검사를 시작 합니다.

* 각 Linux 컴퓨터 업데이트 관리는 매시간 검색을 수행 합니다.

업데이트 관리를 사용 하는 컴퓨터에 대 한 Azure Monitor 로그의 평균 데이터 사용량은 매월 약 25MB입니다. 이 값은 근사값 이며 사용자 환경에 따라 변경 될 수 있습니다. 사용자 환경을 모니터링 하 여 정확한 사용량을 추적 하는 것이 좋습니다. 데이터 사용을 분석 하는 방법에 대 한 자세한 내용은 [사용량 및 비용 관리](../azure-monitor/platform/manage-cost-storage.md)를 참조 하세요.

## <a name="network-planning"></a><a name="ports"></a>네트워크 계획

다음 주소는 업데이트 관리를 위해 특별히 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 발생합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|* .azure-automation.net | *.azure-automation.us|

Windows 컴퓨터의 경우 Windows 업데이트에 필요한 끝점에 대 한 트래픽만 허용 해야 합니다. [HTTP/프록시와 관련 된 문제](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)에서 필수 끝점의 업데이트 된 목록을 찾을 수 있습니다. 로컬 [Windows 업데이트 서버가](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)있는 경우 [WSUS 키](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)에 지정 된 서버에 대 한 트래픽만 허용 해야 합니다.

Red Hat Linux 컴퓨터의 경우 필수 끝점에 대 한 [RHUI content delivery server에 대 한 ip](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 를 참조 하세요. 다른 Linux 배포의 경우 공급자 설명서를 참조 하세요.

Hybrid Runbook Worker에 필요한 포트에 대 한 자세한 내용은 [Hybrid Runbook Worker 업데이트 관리 주소](automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)를 참조 하십시오.

예외를 정의할 때 나열 된 주소를 사용 하는 것이 좋습니다. IP 주소의 경우 [데이터 센터 ip 범위 Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)다운로드할 수 있습니다. 이 파일은 매주 업데이트 되며 현재 배포 된 범위와 모든 예정 된 IP 범위 변경 내용을 반영 합니다.

인터넷에 액세스할 수 없는 [컴퓨터 연결](../azure-monitor/platform/gateway.md) 의 지침에 따라 인터넷에 액세스할 수 없는 컴퓨터를 구성 합니다.

## <a name="update-classifications"></a>업데이트 분류

다음 표에서는 업데이트 관리 Windows 업데이트에 대해 지원 되는 분류를 정의 합니다. 

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

다음 표에서는 Linux 업데이트에 대해 지원 되는 분류를 정의 합니다.

|분류  |설명  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 기본적으로 중요 하지 않거나 보안 업데이트가 아닌 다른 모든 업데이트.        |

업데이트 관리 Linux의 경우 클라우드의 데이터 보강 인해 평가 데이터를 표시 하는 동안 클라우드의 중요 업데이트와 보안 업데이트를 구분할 수 있습니다. 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포와 달리 CentOS는 RTM 버전에서이 정보를 사용할 수 없습니다. 다음 명령에 대 한 보안 데이터를 반환 하도록 구성 된 CentOS 컴퓨터가 있는 경우 분류에 따라 업데이트 관리 패치를 적용할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재 CentOS에서 네이티브 분류 데이터 가용성을 사용 하도록 설정 하는 방법이 지원 되지 않습니다. 지금은이 기능을 사용 하도록 설정 했을 수 있는 고객에 게 최상의 지원만 제공 됩니다. 

Red Hat Enterprise 버전 6에서 업데이트를 분류 하려면 yum-보안 플러그 인을 설치 해야 합니다. Red Hat Enterprise Linux 7에서 플러그 인은 이미 yum 자체의 일부 이며 어떤 것도 설치할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술](https://access.redhat.com/solutions/10021)항목을 참조 하세요.

## <a name="integrate-update-management-with-configuration-manager"></a>Configuration Manager와 업데이트 관리 통합

Pc, 서버 및 모바일 장치를 관리 하기 위해 Microsoft 끝점 Configuration Manager에 투자 한 고객은 소프트웨어 업데이트를 관리 하는 데 도움이 되는 Configuration Manager의 강도 및 완성도를 사용 하기도 합니다. Configuration Manager와 업데이트 관리를 통합 하는 방법에 대 한 자세한 내용은 [업데이트 관리와 Configuration Manager 통합](updatemgmt-mecmintegration.md)을 참조 하세요.

## <a name="third-party-updates-on-windows"></a>Windows의 타사 업데이트

업데이트 관리 로컬에서 구성 된 업데이트 리포지토리를 사용 하 여 WSUS 또는 Windows 업데이트 지원 되는 Windows 시스템을 업데이트 합니다. [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) 와 같은 도구를 사용 하 여 WSUS를 통해 사용자 지정 업데이트를 가져오고 게시할 수 있습니다. 이 시나리오를 통해 업데이트 관리는 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어와 함께 업데이트 리포지토리로 업데이트할 수 있습니다. 업데이트 게시자 구성 방법을 알아보려면 [업데이트 게시자 설치](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)를 참조하세요.

## <a name="enable-update-management"></a>업데이트 관리 사용

Azure [리소스 관리자 템플릿을](automation-update-management-deploy-template.md) 사용 하 여 새 또는 기존 Automation 계정에 업데이트 관리를 배포 하 고 구독의 Log Analytics 작업 영역을 Azure Monitor 수 있습니다. 관리 해야 하는 컴퓨터의 범위를 구성 하지 않습니다 .이는 템플릿을 사용한 후 별도의 단계로 수행 됩니다.

업데이트 관리를 사용 하도록 설정 하 고 관리할 컴퓨터를 선택할 수 있는 방법은 다음과 같습니다.

* [가상 컴퓨터에서](automation-onboard-solutions-from-vm.md).
* [여러 컴퓨터 검색에서](automation-onboard-solutions-from-browse.md).
* [Azure Automation 계정에서](automation-onboard-solutions.md)

## <a name="next-steps"></a>다음 단계

Azure Automation [FAQ](automation-faq.md) 를 검토 하 여 업데이트 관리에 대 한 일반적인 질문을 검토 합니다.
