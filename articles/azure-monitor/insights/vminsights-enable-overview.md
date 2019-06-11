---
title: Vm (미리 보기) 개요에 대 한 Azure Monitor를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 배포 하 고 Vm 및 필요한 시스템 요구 사항에 대 한 Azure Monitor를 구성 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: c84c3eb74d214a5c98aabef7b2e2987dfdf67c0f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472595"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Azure Monitor를 사용 하도록 설정에 대 한 Vm (미리 보기) 개요

이 문서에서는 Azure Monitor 상태, 성능, 모니터링 및 Azure virtual machines 및 virtual machine scale sets를 실행 하는 응용 프로그램 종속성을 검색 하는 Vm에 대 한 설치 프로그램을 온-프레미스 Vm 사용할 수 있는 옵션의 개요를 제공 하거나 Vm에서 호스트 다른 클라우드 환경입니다.  

다음 방법 중 하나를 사용하여 VM용 Azure Monitor를 사용하도록 설정합니다.

* 단일 Azure 가상 머신 또는 가상 머신 확장 집합을 선택 하 여 사용 하도록 설정 **Insights (미리 보기)** 직접 VM 또는 가상 머신 확장 집합에서.
* Azure Policy를 사용 하 여 자세한 Azure Vm 및 가상 머신 확장 집합 하거나 두 개를 사용 합니다. 이 메서드를 통해 기존 및 새 Vm 및 확장 집합의 필요한 종속성 설치 되어 올바르게 구성 합니다. 규격이 아닌 Vm 및 확장 집합을 사용 하도록 설정할 여부와 수정 하고자 하는 방법을 결정할 수 있도록 보고 됩니다.
* PowerShell을 사용하여 지정된 구독 또는 리소스 그룹에 걸친 둘 이상의 Azure VM 또는 가상 머신 확장 집합을 사용하도록 설정합니다.
* 가상 머신 또는 물리적 컴퓨터를 회사 네트워크 또는 다른 클라우드 환경에서 호스트 모니터링을 사용 하도록 설정 합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 섹션의 정보를 이해해야 합니다.

### <a name="log-analytics"></a>Log Analytics

Vm에 대 한 azure Monitor에는 다음 지역에서 Log Analytics 작업 영역을 지원합니다.

- 미국 중서부
- 미국 서 부 2<sup>1</sup>
- 미국 동부
- 캐나다 중부<sup>1</sup>
- 영국 남부<sup>1</sup>
- 서유럽
- 동남 아시아<sup>1</sup>

<sup>1</sup> 이 지역은 현재 VM용 Azure Monitor의 상태 기능을 지원하지 않습니다

>[!NOTE]
>Azure 가상 머신은 모든 지역에서 배포될 수 있으며, Log Analytics 작업 영역에 지원되는 지역으로 제한되지 않습니다.
>

작업 영역이 없는 경우 다음 방법 중 하나로 만들 수 있습니다.
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 포털](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure 리소스 관리자](../../azure-monitor/platform/template-workspace-configuration.md)

단일 Azure VM 또는 Azure portal에서 가상 머신 확장 집합에 대 한 모니터링을 사용 하는 경우에이 프로세스 중 작업 영역을 만들 수 있습니다.

Log Analytics 작업은 Azure Policy, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 대규모 시나리오의 경우 먼저 다음이 구성 해야 합니다.

* ServiceMap 및 InfrastructureInsights 솔루션을 설치합니다. 제공 되는 Azure Resource Manager 템플릿을 사용 하 여 또는 사용 하 여이 설치를 완료할 수는 **작업 영역 구성** 옵션에는 **시작** 탭 합니다.
* 성능 카운터를 수집하도록 Log Analytics 작업 영역을 구성합니다.

대규모 시나리오에 대 한 작업 영역을 구성 하려면 다음 방법 중 하나를 사용 하 여 구성할 수 있습니다.

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* **작업 영역 구성** Vm에 대 한 Azure Monitor에서 옵션 [정책 검사](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) 페이지

### <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표 목록은 VM용 Azure Monitor에서 지원되는 Windows 및 Linux 운영 체제입니다. 주 및 부 Linux OS 릴리스와 지원되는 커널 버전에 대해 자세히 설명하는 전체 목록은 이 섹션의 뒷부분에서 제공됩니다.

|OS 버전 |성능 |지도 |상태 |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> VM용 Azure Monitor의 성능 기능은 Azure Monitor에서만 사용할 수 있습니다. Azure VM의 왼쪽 창에서 직접 액세스하면 사용할 수 없습니다.

>[!NOTE]
>다음 정보는 Linux 운영 체제의 지원에 적용됩니다.
> - 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
> - PAE(Physical Address Extension) 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 *2.6.16.21-0.8-xen*의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
> - 표준 커널의 재컴파일을 포함한 사용자 지정 커널은 지원되지 않습니다.
> - CentOSPlus 커널은 지원 됩니다.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 버전 | 커널 버전 |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 버전 | 커널 버전 |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| OS 버전 | 커널 버전 |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 버전 | 커널 버전 |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| OS 버전 | 커널 버전
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 버전 | 커널 버전
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

VM용 Azure Monitor 맵 기능은 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Dependency Agent는 Log Analytics 연결에 사용된 Log Analytics 에이전트에 따라 달라집니다. 따라서 시스템에 Log Analytics 에이전트를 설치하고 Dependency Agent로 구성해야 합니다.

단일 Azure VM에 VM용 Azure Monitor를 사용하도록 설정하거나 대규모 배포 방법을 사용하는 경우 Azure VM Dependency Agent 확장을 사용하여 작업 환경의 일부로 에이전트를 설치해야 합니다.

하이브리드 환경에서는 다운로드 고 두 가지 방법 중 하나에서 종속성 에이전트를 설치할 수 있습니다: 수동으로 또는 Azure 외부에서 호스팅되는 가상 컴퓨터에 대 한 자동화 된 배포 방법을 사용 하 여 합니다.

다음 테이블은 하이브리드 환경에서 맵 기능이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
|:--|:--|:--|
| Windows 에이전트 | 예 | [Windows용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 외에도 Windows 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| Linux 에이전트 | 예 | [Linux용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 외에도 Linux 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| System Center Operations Manager 관리 그룹 | 아닙니다. | |

Dependency Agent는 다음 위치에서 다운로드할 수 있습니다.

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

VM용 Azure Monitor의 기능을 사용하도록 설정하고 액세스하려면 다음 액세스 역할이 할당되어야 합니다.

- 기능을 사용 하려면 해야 합니다 *Log Analytics 참가자* 역할입니다.

- 성능, 상태 및 맵 데이터를 보려면 Azure VM에 대해 *모니터링 리더* 역할이 있어야 합니다. VM용 Azure Monitor에 대해 Log Analytics 작업 영역을 구성해야 합니다.

Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../../azure-monitor/platform/manage-access.md)를 참조하세요.

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Vm (미리 보기)에 대 한 Azure Monitor를 사용 하는 방법

다음 표에 설명 된 다음 방법 중 하나를 사용 하 여 Vm에 대 한 Azure 모니터를 사용 합니다.

| 배포 상태 | 방법 | 설명 |
|------------------|--------|-------------|
| 단일 Azure VM 또는 가상 머신 확장 집합 | [VM에서 직접](vminsights-enable-single-vm.md) | 단일 Azure 가상 컴퓨터를 선택 하 여 설정할 수 있습니다 **Insights (미리 보기)** 직접 VM 또는 가상 머신 확장 집합에서. |
| 여러 Azure Vm 또는 가상 머신 확장 집합 | [Azure Policy](vminsights-enable-at-scale-policy.md) | Azure 정책 및 사용 가능한 정책 정의 사용 하 여 여러 Azure Vm을 사용할 수 있습니다. |
| 여러 Azure Vm 또는 가상 머신 확장 집합 | [Azure PowerShell 또는 Azure Resource Manager 템플릿](vminsights-enable-at-scale-powershell.md) | Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 지정 된 구독 또는 리소스 그룹에 걸쳐 여러 Azure Vm 또는 가상 머신 확장 집합을 사용할 수 있습니다. |
| 하이브리드 클라우드 | [하이브리드 환경에 대 한 사용 하도록 설정](vminsights-enable-hybrid-cloud.md) | 데이터 센터 또는 다른 클라우드 환경에서 호스트 되는 물리적 컴퓨터나 가상 컴퓨터를 배포할 수 있습니다. |

## <a name="performance-counters-enabled"></a>사용하도록 설정된 성능 카운터

Vm에 대 한 azure Monitor를 사용 하는 성능 카운터를 수집 하도록 Log Analytics 작업 영역을 구성 합니다. 다음 표에서 개체 및 60 초 마다 수집 되는 카운터를 나열 합니다.

### <a name="windows-performance-counters"></a>Windows 성능 카운터

|개체 이름 |카운터 이름 |
|------------|-------------|
|LogicalDisk |% 사용 가능한 공간 |
|LogicalDisk |평균 디스크 초/읽기 |
|LogicalDisk |평균 디스크 초/전송 |
|LogicalDisk |평균 디스크 초/쓰기 |
|LogicalDisk |디스크 바이트/초 |
|LogicalDisk |디스크 읽기 바이트/초 |
|LogicalDisk |디스크 읽기/초 |
|LogicalDisk |디스크 전송/초 |
|LogicalDisk |디스크 쓰기 바이트/초 |
|LogicalDisk |디스크 쓰기/초 |
|LogicalDisk |사용 가능한 메가바이트 |
|메모리 |Available MBytes |
|네트워크 어댑터 |Bytes Received/sec |
|네트워크 어댑터 |Bytes Sent/sec |
|프로세서 |% Processor Time |

### <a name="linux-performance-counters"></a>Linux 성능 카운터

|개체 이름 |카운터 이름 |
|------------|-------------|
|논리 디스크 |% 사용된 공간 |
|논리 디스크 |디스크 읽기 바이트/초 |
|논리 디스크 |디스크 읽기/초 |
|논리 디스크 |디스크 전송/초 |
|논리 디스크 |디스크 쓰기 바이트/초 |
|논리 디스크 |디스크 쓰기/초 |
|논리 디스크 |사용 가능한 메가바이트 |
|논리 디스크 |논리 디스크 바이트/초 |
|메모리 |사용 가능한 MB 메모리 |
|네트워크 |받은 총 바이트 |
|네트워크 |전송된 총 바이트 |
|프로세서 |% Processor Time |

## <a name="diagnostic-and-usage-data"></a>진단 및 사용 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스의 품질, 보안 및 무결성을 제공 및 개선합니다. 정확하고 효율적인 문제 해결 기능을 제공하기 위해 운영 체제 및 버전, IP 주소, DNS 이름 및 워크스테이션 이름과 같은 소프트웨어의 구성 정보가 맵 기능의 데이터에 포함됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>다음 단계

지금까지 가상 머신에 모니터링을 사용하도록 설정했으므로 VM용 Azure Monitor를 사용한 분석에 이 정보를 사용할 수 있습니다. 상태 기능을 사용하는 방법을 알아보려면 [VM용 Azure Monitor 상태 보기](vminsights-health.md)를 참조하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.
