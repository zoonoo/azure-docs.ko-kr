---
title: VM에 대한 Azure 모니터 사용 개요
description: VM용 Azure 모니터를 배포하고 구성하는 방법을 알아봅니다. 시스템 요구 사항을 알아보십시오.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2020
ms.openlocfilehash: 26ed33e967aff1714d2a6fb174eab623e71534c2
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382735"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>VM에 대한 Azure 모니터 사용 개요

이 문서에서는 가상 컴퓨터에서 VM에 대한 Azure 모니터를 사용하여 상태 및 성능을 모니터링하는 데 사용할 수 있는 옵션에 대한 개요를 제공합니다. Azure 가상 시스템(VM) 및 가상 시스템 규모 집합, 온-프레미스 VM 또는 다른 클라우드 환경에서 호스팅되는 VM에서 실행되는 응용 프로그램 종속성을 검색합니다.  

VM에 대한 Azure 모니터를 설정하려면 다음을 수행하십시오.

* VM 또는 가상 시스템 규모 집합에서 직접 **Insights를** 선택하여 단일 Azure VM 또는 가상 시스템 확장 집합을 활성화합니다.
* Azure 정책을 사용하여 둘 이상의 Azure VM 및 가상 시스템 확장 집합을 사용하도록 설정합니다. 이 방법을 사용하면 기존 및 새 VM 및 확장 집합에서 필요한 종속성이 설치되고 올바르게 구성됩니다. 비준수 VM 및 배율 집합이 보고되므로 VM을 사용하도록 설정하고 수정할지 여부를 결정할 수 있습니다.
* PowerShell을 사용하여 지정된 구독 또는 리소스 그룹에 걸친 둘 이상의 Azure VM 또는 가상 머신 확장 집합을 사용하도록 설정합니다.
* VM용 Azure 모니터를 사용하여 회사 네트워크 또는 기타 클라우드 환경에서 호스팅되는 VM 또는 물리적 컴퓨터를 모니터링합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 섹션의 정보를 이해해야 합니다. 

>[!NOTE]
>이 섹션에서 설명하는 다음 정보는 서비스 [맵 솔루션에도](service-map.md)적용됩니다.  

### <a name="log-analytics"></a>Log Analytics

VM용 Azure 모니터는 다음 리전에서 로그 분석 작업 영역을 지원합니다.

- 미국 중서부
- 미국 서부
- 미국 서부 2
- 미국 중남부
- 미국 동부
- 미국 동부2
- 미국 중부
- 미국 중북부
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
>모든 리전에서 Azure VM을 모니터링할 수 있습니다. VM 자체는 Log Analytics 작업 영역에서 지원하는 리전에만 국한되지 않습니다.
>

로그 분석 작업 영역이 없는 경우 리소스 중 하나를 사용하여 작업 영역을 만들 수 있습니다.
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [Powershell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 포털](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure 리소스 관리자](../../azure-monitor/platform/template-workspace-configuration.md)

Azure 포털에서 설정된 단일 Azure VM 또는 가상 시스템 규모에 대한 모니터링을 사용하도록 설정하는 동안 작업 영역을 만들 수도 있습니다.

로그 분석 작업 영역에서 Azure Policy, Azure PowerShell 또는 Azure 리소스 관리자 템플릿을 사용하는 대규모 시나리오를 설정하려면 다음을 수행하십시오.

* *ServiceMap* 및 *인프라인사이트* 솔루션을 설치합니다. 제공된 Azure 리소스 관리자 템플릿을 사용하여 이 설치를 완료할 수 있습니다. 또는 Azure 포털의 **시작 받기** 탭에서 작업 **영역 구성을**선택합니다.
* 성능 카운터를 수집하도록 Log Analytics 작업 영역을 구성합니다.

대규모 시나리오에 대한 작업 영역을 구성하려면 다음 방법 중 하나를 사용합니다.

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)을 사용합니다.
* VM 정책 적용 [**범위에**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) 대한 Azure 모니터에서 **작업 영역 구성을**선택합니다. 

### <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표에는 VM용 Azure 모니터가 지원하는 Windows 및 Linux 운영 체제가 나열되어 있습니다. 이 섹션의 후반부에서는 주 및 부 Linux OS 릴리스 및 지원되는 커널 버전에 대해 자세히 설명한 전체 목록을 찾을 수 있습니다.

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
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|레드 햇 엔터프라이즈 리눅스 (RHEL) 6, 7| X | X| 
|우분투 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SLES(SUSE Linux Enterprise Server) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> VM용 Azure Monitor의 성능 기능은 Azure Monitor에서만 사용할 수 있습니다. Azure VM의 왼쪽 창에서 직접 사용할 수 없습니다.

>[!NOTE]
>리눅스 운영 체제에서 :
> - 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
> - PAE(Physical Address Extension) 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 *2.6.16.21-0.8-xen*의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
> - 표준 커널의 재컴파일을 포함한 사용자 지정 커널은 지원되지 않습니다.
> - 센트로스플러스 커널이 지원됩니다.
> - Linux 커널은 유령 취약점에 대해 패치해야 합니다. 자세한 내용은 Linux 배포판 에 문의하십시오.

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

#### <a name="centosplus"></a>센트로스플러스

| OS 버전 | 커널 버전 |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 버전 | 커널 버전 |
|:--|:--|
| 18.04 | 5.0(Azure 조정 커널 포함)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 버전 | 커널 버전 |
|:--|:--|
|12 SP4 | 4.12.* (Azure 조정 커널 포함) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| OS 버전 | 커널 버전 |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

VM용 Azure 모니터의 맵 기능은 Microsoft 종속성 에이전트에서 데이터를 가져옵니다. Dependency Agent는 Log Analytics 연결에 사용된 Log Analytics 에이전트에 따라 달라집니다. 따라서 시스템에 Log Analytics 에이전트가 설치되고 종속성 에이전트로 구성되어야 합니다.

단일 Azure VM에 대해 VM에 대한 Azure 모니터를 사용하도록 설정하든 대규모 배포 방법을 사용하든 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 또는 [Linux용](../../virtual-machines/extensions/agent-dependency-linux.md) Azure VM 종속성 에이전트 확장을 사용하여 에이전트를 환경의 일부로 설치합니다.

>[!NOTE]
>이 섹션에서 설명하는 다음 정보는 서비스 [맵 솔루션에도](service-map.md)적용됩니다.  

하이브리드 환경에서는 종속성 에이전트를 수동으로 다운로드하여 설치하거나 자동화된 방법을 사용할 수 있습니다.

다음 테이블은 하이브리드 환경에서 맵 기능이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
|:--|:--|:--|
| Windows 에이전트 | yes | [Windows용 로그 분석 에이전트와](../../azure-monitor/platform/log-analytics-agent.md)함께 Windows 에이전트에는 종속성 에이전트가 필요합니다. 자세한 내용은 [지원되는 운영 체제](#supported-operating-systems)를 참조하십시오. |
| Linux 에이전트 | yes | [Linux용 로그 분석 에이전트와](../../azure-monitor/platform/log-analytics-agent.md)함께 Linux 에이전트에는 종속성 에이전트가 필요합니다. 자세한 내용은 [지원되는 운영 체제](#supported-operating-systems)를 참조하십시오. |
| System Center Operations Manager 관리 그룹 | 예 | |

다음 위치에서 종속성 에이전트를 다운로드할 수 있습니다.

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.2.9060 | B7725B6B205CF8C336D9AD87795336C816412740E9D6499BCACB6F862AE3896  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.2.9060 | C6995A67A77882AEC312647D74A99C3C823F68F5F5FFA490FD4BB6006A2FF2941B0 |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

VM용 Azure 모니터의 기능을 활성화하고 액세스하려면 *로그 분석 기여자* 역할이 있어야 합니다. 성능, 상태 및 맵 데이터를 보려면 Azure VM에 대한 *모니터링 판독기* 역할이 있어야 합니다. VM용 Azure Monitor에 대해 Log Analytics 작업 영역을 구성해야 합니다.

Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../../azure-monitor/platform/manage-access.md)를 참조하세요.

## <a name="how-to-enable-azure-monitor-for-vms"></a>VM에 대한 Azure 모니터를 사용하도록 설정하는 방법

이 표에 설명된 방법 중 하나를 사용하여 VM에 대한 Azure 모니터를 사용하도록 설정합니다.

| 배포 상태 | 방법 | 설명 |
|------------------|--------|-------------|
| 단일 Azure VM 또는 가상 시스템 규모 집합 | [VM에서 사용](vminsights-enable-single-vm.md) | VM 또는 가상 시스템 규모 집합에서 직접 **인사이트를** 선택하여 단일 Azure VM을 활성화할 수 있습니다. |
| 여러 Azure VM 또는 가상 시스템 규모 집합 | [Azure 정책을 통해 활성화](vminsights-enable-at-scale-policy.md) | Azure 정책 및 사용 가능한 정책 정의를 사용하여 여러 Azure VM을 활성화할 수 있습니다. |
| 여러 Azure VM 또는 가상 시스템 규모 집합 | [Azure PowerShell 또는 Azure 리소스 관리자 템플릿을 통해 활성화](vminsights-enable-at-scale-powershell.md) | Azure PowerShell 또는 Azure 리소스 관리자 템플릿을 사용하여 지정된 구독 또는 리소스 그룹에서 여러 Azure VM 또는 가상 시스템 확장 집합을 활성화할 수 있습니다. |
| 하이브리드 클라우드 | [하이브리드 환경에 대한 사용](vminsights-enable-hybrid-cloud.md) | 데이터 센터 또는 기타 클라우드 환경에서 호스팅되는 VM 또는 물리적 컴퓨터에 배포할 수 있습니다. |

## <a name="management-packs"></a>관리 팩

VM용 Azure 모니터를 사용하도록 설정하고 Log Analytics 작업 영역으로 구성하면 관리 팩이 해당 작업 영역으로 보고되는 모든 Windows 컴퓨터로 전달됩니다. 시스템 센터 운영 관리자 관리 그룹을 Log Analytics 작업 영역과 [통합한](../../azure-monitor/platform/om-agents.md) 경우 서비스 맵 관리 팩은 관리 그룹에서 관리 그룹에 보고하는 Windows 컴퓨터로 배포됩니다.  

관리 팩의 이름은 *Microsoft.IntelligencePacks.응용 프로그램 독립 모니터.* 폴더에 `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` 기록됩니다. 관리 팩에서 사용하는 데이터 `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`원본은 .

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스의 품질, 보안 및 무결성을 개선합니다. 

정확하고 효율적인 문제 해결 기능을 제공하기 위해 Map 기능에는 소프트웨어 구성에 대한 데이터가 포함되어 있습니다. 데이터는 운영 체제 및 버전, IP 주소, DNS 이름 및 워크스테이션 이름과 같은 정보를 제공합니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

VM에 대한 모니터링을 사용하도록 설정되었으므로 VM용 Azure 모니터에서 모니터링 정보를 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

성능 모니터링 기능을 사용하는 방법에 대해 알아보려면 [VM 성능에 대한 Azure 모니터 보기를](vminsights-performance.md)참조하십시오. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.
