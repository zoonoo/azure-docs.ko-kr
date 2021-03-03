---
title: VM insights 사용 개요
description: VM insights를 배포 및 구성 하는 방법에 대해 알아봅니다. 시스템 요구 사항을 확인 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 661c65f33f0c65f2f7ccd038afdffbf3c8e241c9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719698"
---
# <a name="enable-vm-insights-overview"></a>VM insights 사용 개요

이 문서에서는 VM 정보를 사용 하 여 다음의 상태와 성능을 모니터링할 수 있는 옵션에 대 한 개요를 제공 합니다.

- Azure 가상 머신 
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 머신
- 온-프레미스 가상 머신
- 다른 클라우드 환경에서 호스트 되는 가상 컴퓨터.  

VM insights를 설정 하려면:

* Azure Portal의 메뉴에서 직접 **정보** 를 선택 하 여 단일 azure 가상 머신, azure 가상 머신 확장 집합 또는 azure Arc 컴퓨터를 사용 하도록 설정 합니다.
* Azure Policy를 사용 하 여 여러 Azure virtual machines, Azure virtual machines 또는 Azure Arc 컴퓨터를 사용 하도록 설정 합니다. 이 방법을 사용 하면 기존 및 새 Vm 및 확장 집합에서 필요한 종속성이 설치 되 고 올바르게 구성 됩니다. 호환 되지 않는 가상 컴퓨터 및 크기 집합을 보고 하므로이를 사용 하도록 설정할지 여부를 결정할 수 있습니다.
* PowerShell을 사용 하 여 지정 된 구독 또는 리소스 그룹에서 여러 Azure virtual machines, Azure Arc virtual machines, Azure virtual machine scale sets 또는 Azure Arc 컴퓨터를 사용 하도록 설정 합니다.
* VM 정보를 사용 하 여 회사 네트워크 또는 다른 클라우드 환경에서 호스트 되는 Vm 또는 물리적 컴퓨터를 모니터링 합니다.

## <a name="supported-machines"></a>지원되는 머신
VM insights는 다음 컴퓨터를 지원 합니다.

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 컴퓨터


## <a name="supported-azure-arc-machines"></a>지원 되는 Azure Arc 컴퓨터
Azure Arc 사용 가능 서버는 Arc 확장 서비스를 사용할 수 있는 지역에서 VM 정보를 사용할 수 있습니다. Arc 에이전트 버전 0.9 이상을 실행 해야 합니다.

| 연결된 원본 | 지원됨 | Description |
|:--|:--|:--|
| Windows 에이전트 | 예 | [Windows에 대 한 Log Analytics 에이전트](../agents/log-analytics-agent.md)와 함께 windows 에이전트에는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](../agents/agents-overview.md#supported-operating-systems)를 참조 하세요. |
| Linux 에이전트 | 예 | Linux [에 대 한 Log Analytics 에이전트](../agents/log-analytics-agent.md)와 함께 linux 에이전트에는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](#supported-operating-systems)를 참조 하세요. |
| System Center Operations Manager 관리 그룹 | No | |

## <a name="supported-operating-systems"></a>지원되는 운영 체제

VM insights는 Log Analytics 에이전트 및 종속성 에이전트를 지 원하는 모든 운영 체제를 지원 합니다. 전체 목록은 [Azure Monitor 에이전트 개요 ](../agents/agents-overview.md#supported-operating-systems) 를 참조 하세요.

> [!IMPORTANT]
> VM insights 게스트 상태 기능은 공개 미리 보기로 제공 되는 동안 보다 제한 된 운영 체제를 지원 합니다. 자세한 목록은 [VM insights 게스트 상태 설정 (미리 보기)](../vm/vminsights-health-enable.md) 을 참조 하세요.

VM 정보를 지 원하는 종속성 에이전트의 Linux 지원에 대 한 다음 고려 사항 목록을 참조 하세요.

- 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
- PAE(Physical Address Extension) 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 *2.6.16.21-0.8-xen* 의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
- 표준 커널 다시 컴파일이 포함 된 사용자 지정 커널은 지원 되지 않습니다.
- 9.4 버전 이외의 Debian 배포판에서는 맵 기능이 지원 되지 않으며 성능 기능은 Azure Monitor 메뉴 에서만 사용할 수 있습니다. Azure VM의 왼쪽 창에서 직접 사용할 수 없습니다.
- CentOSPlus 커널이 지원 됩니다.
- 스펙터 취약성에 대해 Linux 커널의 패치를 적용 해야 합니다. 자세한 내용은 Linux 배포 공급 업체에 문의 하세요.
## <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
VM 정보를 Log Analytics 작업 영역이 필요 합니다. 이 작업 영역의 세부 정보 및 요구 사항은 [VM insights에 대 한 Log Analytics 작업 영역 구성](vminsights-configure-workspace.md) 을 참조 하세요.
## <a name="agents"></a>에이전트
VM 정보를 사용 하려면 모니터링할 가상 머신 또는 가상 머신 확장 집합에 다음 두 개의 에이전트를 설치 해야 합니다. 리소스를 등록 하려면 이러한 에이전트를 설치 하 고 작업 영역에 연결 합니다.  이러한 에이전트의 네트워크 요구 사항은 [네트워크 요구 사항](../agents/log-analytics-agent.md#network-requirements) 을 참조 하세요.

- [에이전트를 Log Analytics](../agents/log-analytics-agent.md)합니다. 가상 머신 또는 가상 머신 확장 집합에서 이벤트 및 성능 데이터를 수집 하 여 Log Analytics 작업 영역으로 전달 합니다. Azure 리소스의 Log Analytics 에이전트에 대 한 배포 방법에서는 [Windows](../../virtual-machines/extensions/oms-windows.md) 및 [Linux](../../virtual-machines/extensions/oms-linux.md)용 VM 확장을 사용 합니다.
- 종속성 에이전트. [VM insights의 Map 기능](../vm/vminsights-maps.md)에서 사용 되는 가상 컴퓨터 및 외부 프로세스 종속성에서 실행 중인 프로세스에 대 한 검색 된 데이터를 수집 합니다. 종속성 에이전트는 Log Analytics 에이전트를 사용 하 여 데이터를 Azure Monitor으로 배달 합니다. Azure 리소스에 대 한 종속성 에이전트 배포 방법에서는 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md)용 VM 확장을 사용 합니다.

> [!NOTE]
> Log Analytics 에이전트는 System Center Operations Manager에서 사용 하는 것과 동일한 에이전트입니다. VM insights는 직접 연결 된 경우에도 Operations Manager 모니터링 하는 에이전트를 모니터링 하 고 종속성 에이전트를 설치할 수 있습니다. [관리 그룹 연결](../tform/../agents/om-agents.md) 을 통해 Azure Monitor에 연결 된 에이전트는 VM insights에서 모니터링할 수 없습니다.

다음은 이러한 에이전트를 배포 하는 여러 가지 방법입니다. 

| 방법 | Description |
|:---|:---|
| [Azure Portal](../vm/vminsights-enable-portal.md) | 단일 가상 머신, 가상 머신 확장 집합 또는 Azure Arc와 연결 된 하이브리드 가상 머신에 두 에이전트를 모두 설치 합니다. |
| [리소스 관리자 템플릿](../vm/vminsights-enable-resource-manager.md) | 지원 되는 방법 중 하나를 사용 하 여 CLI 및 PowerShell을 비롯 한 리소스 관리자 템플릿을 배포 하는 두 에이전트를 모두 설치 합니다. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | 가상 머신 또는 가상 머신 확장 집합을 만들 때 에이전트를 자동으로 설치 하도록 Azure Policy 이니셔티브를 할당 합니다. |
| [수동 설치](../vm/vminsights-enable-hybrid.md) | 데이터 센터 또는 다른 클라우드 환경을 포함 하 여 Azure 외부에 호스트 된 컴퓨터에 게스트 운영 체제에 에이전트를 설치 합니다. |


## <a name="network-requirements"></a>네트워크 요구 사항

- Log Analytics 에이전트에 대 한 네트워크 요구 사항은 [네트워크 요구 사항](../agents/log-analytics-agent.md#network-requirements) 을 참조 하세요.
- 종속성 에이전트를 사용 하려면 가상 컴퓨터에서 169.254.169.254 주소로 연결 해야 합니다. Azure metadata service 끝점입니다. 방화벽 설정에서이 끝점에 대 한 연결을 허용 하는지 확인 합니다.


## <a name="management-packs"></a>관리 팩
VM insights에 대 한 Log Analytics 작업 영역이 구성 된 경우 해당 작업 영역에 연결 된 모든 Windows 컴퓨터에 두 개의 관리 팩이 전달 됩니다. 관리 팩은 *microsoft.intelligencepacks.updateassessment. ApplicationDependencyMonitor* 및 *microsoft.intelligencepacks.updateassessment* 로 이름이 지정 되며 *%Programfiles%\Microsoft 모니터링 Agent\Agent\Health Service State\agent\health Service state\management pack* 에 기록 됩니다. 

*ApplicationDependencyMonitor* 관리 팩에서 사용 하는 데이터 원본은 **% Program files%\Microsoft Monitoring \Agent\health Service state\resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll* 입니다. *VMInsights* 관리 팩에서 사용 하는 데이터 원본은 *% Program files%\Microsoft Monitoring \Agent\health Service state\sources\ \<AutoGeneratedID> Microsoft.VirtualMachineMonitoringModule.dll* 입니다.

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는이 데이터를 사용 하 여 서비스의 품질, 보안 및 무결성을 향상 시킵니다. 

정확 하 고 효율적인 문제 해결 기능을 제공 하기 위해 지도 기능에는 소프트웨어의 구성에 대 한 데이터가 포함 됩니다. 데이터는 운영 체제, 버전, IP 주소, DNS 이름 및 워크스테이션 이름과 같은 정보를 제공 합니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>다음 단계

성능 모니터링 기능을 사용 하는 방법에 대 한 자세한 내용은 [VIEW VM Insights Performance](../vm/vminsights-performance.md)을 참조 하세요. 검색 된 응용 프로그램 종속성을 보려면 [VM Insights 맵 보기](../vm/vminsights-maps.md)를 참조 하세요.
