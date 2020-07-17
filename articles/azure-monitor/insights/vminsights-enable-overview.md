---
title: VM용 Azure Monitor 개요 사용
description: VM용 Azure Monitor를 배포 하 고 구성 하는 방법을 알아봅니다. 시스템 요구 사항을 확인 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 261e5f17e787fd96697b06a9b338e74ea0409454
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507078"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>VM용 Azure Monitor 개요 사용

이 문서에서는 VM용 Azure Monitor에서 다음의 상태와 성능을 모니터링할 수 있도록 하는 데 사용할 수 있는 옵션의 개요를 제공 합니다.

- Azure 가상 머신 
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 머신
- 온-프레미스 가상 머신
- 다른 클라우드 환경에서 호스트 되는 가상 컴퓨터.  

VM용 Azure Monitor을 설정 하려면:

* Azure Portal의 메뉴에서 직접 **정보** 를 선택 하 여 단일 azure VM, AZURE vmss 또는 azure Arc 컴퓨터를 사용 하도록 설정 합니다.
* Azure Policy를 사용 하 여 여러 Azure Vm, Azure VMSS 또는 Azure Arc 컴퓨터를 사용 하도록 설정 합니다. 이 방법을 사용 하면 기존 및 새 Vm 및 확장 집합에서 필요한 종속성이 설치 되 고 올바르게 구성 됩니다. 비규격 Vm 및 크기 집합을 보고 하므로이를 사용 하도록 설정할지 여부를 결정할 수 있습니다.
* PowerShell을 사용 하 여 지정 된 구독 또는 리소스 그룹에서 여러 Azure Vm, Azure Arc Vm, Azure VMSS 또는 Azure Arc 컴퓨터를 사용 하도록 설정 합니다.
* VM용 Azure Monitor 사용 하 여 회사 네트워크 또는 다른 클라우드 환경에서 호스트 되는 Vm 또는 물리적 컴퓨터를 모니터링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 섹션의 정보를 이해해야 합니다. 

>[!NOTE]
>이 섹션에서 설명 하는 다음 정보는 [서비스 맵 솔루션](service-map.md)에도 적용 됩니다.  

### <a name="log-analytics"></a>Log Analytics

VM용 Azure Monitor는 다음 지역에서 Log Analytics 작업 영역을 지원 합니다.

- 미국 중서부
- 미국 서부
- 미국 서부 2
- 미국 중남부
- 미국 동부
- 미국 동부2
- 미국 중부
- 미국 중북부
- US Gov Az
- US Gov Va
- 캐나다 중부
- 영국 남부
- 북유럽
- 서유럽
- 동아시아
- 동남아시아
- 인도 중부
- 일본 동부
- 오스트레일리아 동부
- 오스트레일리아 남동부

>[!NOTE]
>모든 지역에서 Azure Vm을 모니터링할 수 있습니다. Vm 자체는 Log Analytics 작업 영역에서 지원 되는 지역으로 제한 되지 않습니다.
>

Log Analytics 작업 영역이 없는 경우 리소스 중 하나를 사용 하 여 작업 영역을 만들 수 있습니다.
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure 리소스 관리자](../../azure-monitor/platform/template-workspace-configuration.md)

Azure Portal에서 단일 Azure VM 또는 가상 머신 확장 집합에 대 한 모니터링을 사용 하도록 설정 하는 동안 작업 영역을 만들 수도 있습니다.

Azure Policy, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하는 확장 가능한 시나리오를 설정 하려면 *VMInsights* 솔루션을 설치 해야 합니다. 다음 방법 중 하나를 사용 하 여이 작업을 수행할 수 있습니다.

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)를 사용 합니다.
* [**정책 적용**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) VM용 Azure Monitor 페이지에서 **작업 영역 구성**을 선택 합니다. 

### <a name="azure-arc-machines"></a>Azure Arc 컴퓨터
VM용 Azure Monitor는 Arc 확장 서비스를 사용할 수 있는 지역에서 Azure Arc 사용 서버에 사용할 수 있습니다. 사용자는 Arc 사용 서버에서 VM용 Azure Monitor를 사용 하도록 설정 하려면 Arc 에이전트 버전 0.9 이상을 실행 해야 합니다.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표에는 VM용 Azure Monitor에서 지 원하는 Windows 및 Linux 운영 체제가 나와 있습니다. 이 섹션의 뒷부분에는 주요 Linux OS 릴리스와 지원 되는 커널 버전에 대 한 전체 목록이 나와 있습니다.

|OS 버전 |성능 |지도 |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SLES(SUSE Linux Enterprise Server) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> VM용 Azure Monitor의 성능 기능은 Azure Monitor에서만 사용할 수 있습니다. Azure VM의 왼쪽 창에서 직접 사용할 수 없습니다.

>[!NOTE]
>Linux 운영 체제에서 다음을 수행 합니다.
> - 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
> - PAE(Physical Address Extension) 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 *2.6.16.21-0.8-xen*의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
> - 표준 커널 다시 컴파일이 포함 된 사용자 지정 커널은 지원 되지 않습니다.
> - CentOSPlus 커널이 지원 됩니다.
> - 스펙터 취약성에 대해 Linux 커널의 패치를 적용 해야 합니다. 자세한 내용은 Linux 배포 공급 업체에 문의 하세요.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 버전 | 커널 버전 |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 버전 | 커널 버전 |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| OS 버전 | 커널 버전 |
|:--|:--|
| 6.10 | 2.6.32 커널을-754.3.5<br>2.6.32 커널을-696.30.1 |
| 6.9 | 2.6.32 커널을-696.30.1<br>2.6.32 커널을-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 버전 | 커널 버전 |
|:--|:--|
| 18.04 | 5.3.0-1020<br>5.0 (Azure 조정 커널 포함)<br>4.18* <br> 4.15* |
| 16.04.3 | 4.15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 버전 | 커널 버전 |
|:--|:--|
|12 SP4 | 4.12. * (Azure 조정 커널 포함) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| OS 버전 | 커널 버전 |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

VM용 Azure Monitor 맵 기능은 Microsoft 종속성 에이전트에서 해당 데이터를 가져옵니다. Dependency Agent는 Log Analytics 연결에 사용된 Log Analytics 에이전트에 따라 달라집니다. 따라서 시스템에는 종속성 에이전트를 사용 하 여 Log Analytics 에이전트를 설치 하 고 구성 해야 합니다.

단일 Azure VM에 대해 VM용 Azure Monitor를 사용 하도록 설정 하거나 규모에 맞는 배포 방법을 사용 하는 경우에는 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 또는 [LINUX](../../virtual-machines/extensions/agent-dependency-linux.md) 용 Azure VM 종속성 에이전트 확장을 사용 하 여 환경의 일부로 에이전트를 설치 합니다.

>[!NOTE]
>이 섹션에서 설명 하는 다음 정보는 [서비스 맵 솔루션](service-map.md)에도 적용 됩니다.  

하이브리드 환경에서 수동으로 또는 자동화 된 방법을 사용 하 여 종속성 에이전트를 다운로드 하 고 설치할 수 있습니다.

다음 테이블은 하이브리드 환경에서 맵 기능이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | Description |
|:--|:--|:--|
| Windows 에이전트 | 예 | [Windows에 대 한 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)와 함께 windows 에이전트에는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](#supported-operating-systems)를 참조 하세요. |
| Linux 에이전트 | 예 | Linux [에 대 한 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)와 함께 linux 에이전트에는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](#supported-operating-systems)를 참조 하세요. |
| System Center Operations Manager 관리 그룹 | 아니요 | |

다음 위치에서 종속성 에이전트를 다운로드할 수 있습니다.

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

VM용 Azure Monitor의 기능을 사용 하도록 설정 하 고 액세스 하려면 *Log Analytics 참여자* 역할이 있어야 합니다. 성능, 상태 및 지도 데이터를 보려면 Azure VM에 대 한 *모니터링 독자* 역할이 있어야 합니다. VM용 Azure Monitor에 대해 Log Analytics 작업 영역을 구성해야 합니다.

Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../../azure-monitor/platform/manage-access.md)를 참조하세요.

## <a name="how-to-enable-azure-monitor-for-vms"></a>VM용 Azure Monitor를 사용 하도록 설정 하는 방법

이 표에 설명 된 방법 중 하나를 사용 하 여 VM용 Azure Monitor를 사용 하도록 설정 합니다.

| 배포 상태 | 메서드 | 설명 |
|------------------|--------|-------------|
| 단일 Azure VM, Azure VMSS 또는 Azure Arc 컴퓨터 | [포털에서 사용](vminsights-enable-single-vm.md) | Azure Portal의 메뉴에서 직접 **정보** 를 선택 합니다. |
| 여러 Azure VM, Azure VMSS 또는 Azure Arc 컴퓨터 | [Azure Policy 통해 사용](vminsights-enable-at-scale-policy.md) | VM 또는 VMSS가 만들어질 때 자동으로 사용 하도록 설정 하려면 Azure Policy을 사용 합니다. |
| | [Azure PowerShell 또는 Azure Resource Manager 템플릿을 통해 사용](vminsights-enable-at-scale-powershell.md) | Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 지정 된 구독 또는 리소스 그룹에서 여러 Azure VM, Azure Arc VM 또는 Azure VMSS를 사용 하도록 설정 합니다. |
| 하이브리드 클라우드 | [하이브리드 환경에서 사용](vminsights-enable-hybrid-cloud.md) | 데이터 센터 또는 다른 클라우드 환경에서 호스팅되는 Vm 또는 물리적 컴퓨터에 배포 합니다. |

## <a name="management-packs"></a>관리 팩

VM용 Azure Monitor를 사용 하도록 설정 하 고 Log Analytics 작업 영역을 사용 하 여 구성 하면 관리 팩이 해당 작업 영역에 보고 하는 모든 Windows 컴퓨터에 전달 됩니다. [System Center Operations Manager 관리 그룹](../../azure-monitor/platform/om-agents.md) 을 Log Analytics 작업 영역과 통합 한 경우 서비스 맵 관리 팩은 관리 그룹에서 관리 그룹에 보고 하는 Windows 컴퓨터에 배포 됩니다.  

관리 팩의 이름은 *microsoft.intelligencepacks.updateassessment. ApplicationDependencyMonitor*입니다. 폴더에 기록 `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` 됩니다. 관리 팩에서 사용 하는 데이터 원본은입니다 `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll` .

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는이 데이터를 사용 하 여 서비스의 품질, 보안 및 무결성을 향상 시킵니다. 

정확 하 고 효율적인 문제 해결 기능을 제공 하기 위해 지도 기능에는 소프트웨어의 구성에 대 한 데이터가 포함 됩니다. 데이터는 운영 체제, 버전, IP 주소, DNS 이름 및 워크스테이션 이름과 같은 정보를 제공 합니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

이제 VM에 대 한 모니터링을 사용 하도록 설정 했으므로 모니터링 정보를 VM용 Azure Monitor 분석에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

성능 모니터링 기능을 사용 하는 방법에 대 한 자세한 내용은 [VM용 Azure Monitor 성능 보기](vminsights-performance.md)를 참조 하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.
