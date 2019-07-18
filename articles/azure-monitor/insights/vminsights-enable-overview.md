---
title: Vm (미리 보기) 개요에 대 한 Azure Monitor를 사용 하도록 설정 | Microsoft Docs
description: 배포 하 고 Vm에 대 한 Azure Monitor를 구성 하는 방법에 알아봅니다. 시스템 요구 사항에 대해 알아봅니다.
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
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 6fb7e6b9611b28dab856209aaf03aa93c25d3968
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478043"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Azure Monitor를 사용 하도록 설정에 대 한 Vm (미리 보기) 개요

이 문서에서는 Vm에 대 한 Azure Monitor를 설정 하는 옵션의 개요를 제공 합니다. Vm에 대 한 Azure Monitor를 사용 하 여 상태 모니터링 및 성능. Azure virtual machines (Vm)에서 실행 되는 응용 프로그램 종속성을 검색 하 고 가상 머신 확장 집합을 온-프레미스 Vm 또는 다른 클라우드 환경에서 호스팅되는 Vm.  

Azure Monitor Vm을 설정 합니다.

* 단일 Azure VM 또는 가상 머신 확장 집합을 선택 하 여 사용 하도록 설정 **Insights (미리 보기)** 직접 VM 또는 가상 머신 확장 집합에서.
* Azure Policy를 사용 하 여 자세한 Azure Vm 및 가상 머신 확장 집합 하거나 두 개를 사용 합니다. 이 메서드는 기존 및 새 Vm 및 확장 집합에서 필요한 종속성 설치 되어 올바르게 구성 되도록 합니다. 호환 되지 않는 Vm 및 확장 집합 수정 하 고 사용 하도록 설정 하려면 여부를 결정할 수 있도록 보고 됩니다.
* PowerShell을 사용하여 지정된 구독 또는 리소스 그룹에 걸친 둘 이상의 Azure VM 또는 가상 머신 확장 집합을 사용하도록 설정합니다.
* Vm 또는 물리적 컴퓨터를 회사 네트워크 또는 다른 클라우드 환경에서 호스트를 모니터링 하는 Vm에 대 한 Azure Monitor를 사용 하도록 설정 합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 섹션의 정보를 이해해야 합니다.

### <a name="log-analytics"></a>Log Analytics

Vm에 대 한 azure Monitor에는 다음 지역에서 Log Analytics 작업 영역을 지원합니다.

- 미국 중서부
- 미국 서 부 2<sup>1</sup>
- East US
- 캐나다 중부
- 영국 남부
- 서유럽
- 동남아시아

<sup>1</sup> 이 지역은 현재 VM용 Azure Monitor의 상태 기능을 지원하지 않습니다

>[!NOTE]
>모든 지역 간에 Azure Vm을 배포할 수 있습니다. 이러한 Vm에 Log Analytics 작업 영역에서 지 원하는 지역으로 제한 되지 않습니다.
>

작업 영역에 없는 경우 이러한 리소스 중 하나를 사용 하 여 만들 수 있습니다.
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 포털](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure 리소스 관리자](../../azure-monitor/platform/template-workspace-configuration.md)

Azure portal에서 설정 하는 단일 Azure VM 또는 가상 머신 확장에 대 한 모니터링을 사용 하는 동안에 작업 영역을 만들 수 있습니다.

Log Analytics 작업 영역에 Azure Policy, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하는 대규모 시나리오를 설정 하려면:

* ServiceMap 및 InfrastructureInsights 솔루션을 설치합니다. 제공 된 Azure Resource Manager 템플릿을 사용 하 여이 설치를 완료할 수 있습니다. 또는 합니다 **시작** 탭을 선택 **작업 영역 구성**합니다.
* 성능 카운터를 수집하도록 Log Analytics 작업 영역을 구성합니다.

대규모 시나리오에 대 한 작업 영역을 구성 하려면 다음 방법 중 하나를 사용 합니다.

* 사용 하 여 [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)합니다.
* Vm에 대 한 Azure Monitor [ **정책 검사** ](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) 페이지에서 **작업 영역 구성**합니다. 

### <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표에서 Vm에 대 한 Azure Monitor에서 지 원하는 Windows 및 Linux 운영 체제를 나열 합니다. 나중에이 섹션에서는 있습니다 전체 목록을 자세히 설명을 하는 주 및 부 Linux OS 릴리스 및 지원 되는 커널 버전.

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
|SLES(SUSE Linux Enterprise Server) 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> VM용 Azure Monitor의 성능 기능은 Azure Monitor에서만 사용할 수 있습니다. Azure VM의 왼쪽된 창에서 직접 사용할 수 없습니다.

>[!NOTE]
>Vm에 대 한 Azure Monitor의 상태 기능은 지원 하지 않습니다 [중첩 된 가상화](../../virtual-machines/windows/nested-virtualization.md) Azure VM에서.
>

>[!NOTE]
>Linux 운영 체제:
> - 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
> - PAE(Physical Address Extension) 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 *2.6.16.21-0.8-xen*의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
> - 표준 커널의 재컴파일을 포함 한 사용자 지정 커널은 지원 되지 않습니다.
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

#### <a name="centosplus"></a>CentOSPlus
| OS 버전 | 커널 버전 |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 버전 | 커널 버전 |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 버전 | 커널 버전
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |
|12 SP4 | 4.4.* |
|12 SP4 | 커널 azure 조정 |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

Vm에 대 한 Azure Monitor에서 맵 기능 Microsoft 종속성 에이전트에서 해당 데이터를 가져옵니다. Dependency Agent는 Log Analytics 연결에 사용된 Log Analytics 에이전트에 따라 달라집니다. 따라서 시스템에 설치 하 고 종속성 에이전트를 사용 하 여 구성한 Log Analytics 에이전트를 있어야 합니다.

든 단일 Azure VM에 대 한 Vm에 대 한 Azure Monitor를 사용 하면 대규모 배포 메서드를 사용 하면 환경의 일부로 에이전트를 설치 하는 Azure VM 종속성 에이전트 확장을 사용 합니다.

하이브리드 환경에서 다운로드 하 고 종속성 에이전트를 수동으로 설치할 수 있습니다. Azure 외부에서 Vm에 호스팅되는, 경우에 자동화 된 배포 방법을 사용 합니다.

다음 테이블은 하이브리드 환경에서 맵 기능이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
|:--|:--|:--|
| Windows 에이전트 | 예 | 와 함께 합니다 [Windows에 대 한 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md), Windows 에이전트는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](#supported-operating-systems)합니다. |
| Linux 에이전트 | 예 | 와 함께 합니다 [Linux 용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md), Linux 에이전트는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](#supported-operating-systems)합니다. |
| System Center Operations Manager 관리 그룹 | 아닙니다. | |

이러한 위치에서 종속성 에이전트를 다운로드할 수 있습니다.

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

을 사용 하려면 Vm에 대 한 Azure Monitor의 기능에 액세스 해야 합니다 *Log Analytics 참가자* 역할입니다. 를 성능 상태를 보고 하려면 데이터 매핑 있어야 합니다 *판독기 모니터링* Azure VM에 대 한 역할입니다. VM용 Azure Monitor에 대해 Log Analytics 작업 영역을 구성해야 합니다.

Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../../azure-monitor/platform/manage-access.md)를 참조하세요.

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Vm (미리 보기)에 대 한 Azure Monitor를 사용 하는 방법

이 표에 설명 된 방법 중 하나를 사용 하 여 Vm에 대 한 Azure Monitor를 사용 합니다.

| 배포 상태 | 방법 | 설명 |
|------------------|--------|-------------|
| 단일 Azure VM 또는 가상 머신 확장 집합 | [VM에서 사용 하도록 설정](vminsights-enable-single-vm.md) | 단일 Azure VM을 선택 하 여 설정할 수 있습니다 **Insights (미리 보기)** 직접 VM 또는 가상 머신 확장 집합에서. |
| 여러 Azure Vm 또는 가상 머신 확장 집합 | [Azure 정책을 통해 사용 하도록 설정](vminsights-enable-at-scale-policy.md) | Azure 정책 및 사용 가능한 정책 정의 사용 하 여 여러 Azure Vm을 사용할 수 있습니다. |
| 여러 Azure Vm 또는 가상 머신 확장 집합 | [Azure PowerShell 또는 Azure Resource Manager 템플릿을 통해 사용 하도록 설정](vminsights-enable-at-scale-powershell.md) | Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 지정 된 구독 또는 리소스 그룹에서 여러 Azure Vm 또는 가상 머신 확장 집합을 사용할 수 있습니다. |
| 하이브리드 클라우드 | [하이브리드 환경에 대해 사용 하도록 설정](vminsights-enable-hybrid-cloud.md) | 데이터 센터 또는 다른 클라우드 환경에서 호스트 되는 물리적 컴퓨터 또는 Vm을 배포할 수 있습니다. |

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

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 품질, 보안 및 서비스의 무결성을 개선 하기 위해이 데이터를 사용 합니다. 

정확 하 고 효율적인 문제 해결 기능을 제공 하려면 맵 기능에는 소프트웨어의 구성에 대 한 데이터가 포함 됩니다. 데이터는 운영 체제 및 버전, IP 주소, DNS 이름 및 워크스테이션 이름 같은 정보를 제공 합니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

지금 VM에 대 한 모니터링을 활성화 한 모니터링 정보는 Vm에 대 한 Azure Monitor에서 분석을 위해 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

상태 기능을 사용하는 방법을 알아보려면 [VM용 Azure Monitor 상태 보기](vminsights-health.md)를 참조하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.
