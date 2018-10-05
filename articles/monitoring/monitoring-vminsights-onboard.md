---
title: VM용 Azure Monitor 등록 | Microsoft Docs
description: 이 문서에서는 배포된 응용 프로그램의 수행 방식 및 확인된 상태 문제 이해를 시작할 수 있도록 VM용 Azure Monitor를 등록 및 구성하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 2f0568064eed556429675ffb34c84d588ac670d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064359"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>VM용 Azure Monitor를 등록하는 방법 
이 문서에서는 사용자의 Azure 가상 머신의 운영 체제 상태를 모니터링하고 해당 머신에서 호스트될 수 있는 응용 프로그램 종속성을 검색 및 매핑하도록 VM용 Azure Monitor를 설정하는 방법을 설명합니다.  

다음 방법 중 하나를 사용하여 VM용 Azure Monitor를 사용하도록 설정할 수 있습니다. 각 방법 사용에 관한 자세한 내용은 문서의 뒷부분에 제공됩니다.  

* VM에서 직접 **인사이트(미리 보기)** 를 선택하는 단일 Azure 가상 머신.
* Azure Policy를 사용하여 평가된 기존 및 새 VM에 필수 종속성이 설치되어 있고 올바르게 구성되어 있는지 확인하는 여러 Azure VM.  비준수 항목에 따라 수정할 방법을 결정할 수 있도록 비준수 VM이 보고됩니다.  
* PowerShell을 사용하여 지정된 구독 또는 리소스 그룹에 걸친 여러 Azure VM 또는 가상 머신 확장 집합.

## <a name="prerequisites"></a>필수 조건
시작하기 전에 아래 하위 섹션에 설명된 다음 사항이 있는지 확인하세요.

### <a name="log-analytics"></a>Log Analytics 

다음 지역의 Log Analytics 작업 영역이 현재 지원됩니다.

  - 미국 중서부  
  - 미국 동부  
  - 서유럽  
  - 동남 아시아<sup>1</sup>  

<sup>1</sup> 이 지역은 현재 VM용 Azure Monitor의 상태 기능을 지원하지 않습니다   

작업 영역이 없는 경우 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)를 통해, [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)에서 만들 수 있습니다.  

솔루션을 사용하려면 Log Analytics 기여자 역할의 구성원이 되어야 합니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../log-analytics/log-analytics-manage-access.md)를 참조하세요.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표 목록은 VM용 Azure Monitor에서 지원되는 Windows 및 Linux 운영 체제입니다.  지원되는 주 및 부 Linux OS 릴리스와 커널 버전에 대해 자세히 설명하는 전체 목록은 이 섹션의 뒷부분에서 제공됩니다.

|OS 버전 |성능 |지도 |상태 |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent OS Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> VM용 Azure Monitor의 성능 기능은 Azure Monitor에서만 사용할 수 있습니다, Azure VM의 왼쪽 창에서 직접 액세스할 수 있는 경우에는 사용할 수 없습니다.  

>[!NOTE]
>다음 정보는 Linux 운영 체제의 지원에 적용됩니다.  
> - 기본 및 SMP Linux 커널 릴리스만 지원됩니다.  
> - PAE 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 "2.6.16.21-0.8-xen"의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.  
> - 표준 커널의 재컴파일을 포함한 사용자 지정 커널은 지원되지 않습니다.  
> - CentOSPlus 커널은 지원되지 않습니다.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 버전 | 커널 버전 |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 버전 | 커널 버전 |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 버전 | 커널 버전 |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel을 갖춘 Oracle Enterprise Linux 6
| OS 버전 | 커널 버전
|:--|:--|
| 6.2 | Oracle 2.6.32-300(UEK R1) |
| 6.3 | Oracle 2.6.39-200(UEK R2) |
| 6.4 | Oracle 2.6.39-400(UEK R2) |
| 6.5 | Oracle 2.6.39-400(UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400(UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel을 갖춘 Oracle Enterprise Linux 5

| OS 버전 | 커널 버전
|:--|:--|
| 5.10 | Oracle 2.6.39-400(UEK R2) |
| 5.11 | Oracle 2.6.39-400(UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 버전 | 커널 버전
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="hybrid-environment-connected-sources"></a>하이브리드 환경에 연결된 원본
VM용 Azure Monitor는 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Dependency Agent는 Log Analytics 연결에 사용된 Log Analytics 에이전트에 따라 달라집니다. 즉, 시스템에 Log Analytics 에이전트를 설치하고 Dependency Agent로 구성해야 합니다.  다음 테이블은 하이브리드 환경에서 맵 기능이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
|:--|:--|:--|
| Windows 에이전트 | yes | [Windows용 Log Analytics 에이전트](../log-analytics/log-analytics-concept-hybrid.md) 외에도 Windows 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| Linux 에이전트 | yes | [Linux용 Log Analytics 에이전트](../log-analytics/log-analytics-concept-hybrid.md) 외에도 Linux 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| System Center Operations Manager 관리 그룹 | 아니요 | |  

Windows에서 System Center Operations Manager와 Log Analytics는 MMA(Microsoft Monitoring Agent)를 사용하여 모니터링 데이터를 수집하고 전송합니다. System Center Operations Manager 및 Log Analytics는 에이전트의 다양한 기본 버전을 제공합니다. 이러한 버전은 각각 System Center Operations Manager, Log Analytics 또는 양쪽 모두에 보고할 수 있습니다.  

Linux에서는 Linux용 Log Analytics 에이전트가 모니터링 데이터를 수집하여 Log Analytics에 보냅니다.   

Windows 또는 Linux 머신을 서비스에 직접 연결할 수 없으면, OMS 게이트웨이를 사용하여 Log Analytics에 연결하도록 Log Analytics 에이전트를 구성해야 합니다. OMS 게이트웨이를 배포하고 구성하는 방법에 자세한 내용은 [OMS 게이트웨이를 사용하여 인터넷 액세스 없이 컴퓨터 연결](../log-analytics/log-analytics-oms-gateway.md)을 참조합니다.  

종속성 에이전트는 다음 위치에서 다운로드할 수 있습니다.

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>진단 및 사용 현황 데이터
Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스의 품질, 보안 및 무결성을 제공 및 개선합니다. 정확하고 효율적인 문제 해결 기능을 제공하기 위해 운영 체제 및 버전, IP 주소, DNS 이름 및 워크스테이션 이름과 같은 소프트웨어의 구성 정보가 맵 기능의 데이터에 포함됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>사용하도록 설정된 성능 카운터
VM용 Azure Monitor는 솔루션에서 사용된 성능 카운터를 수집하도록 Log Analytics 작업 영역을 구성합니다.  다음 테이블에는 60초마다 수집되는 솔루션으로 구성된 개체 및 카운터가 나열되어 있습니다.

### <a name="windows-performance-counters"></a>Windows 성능 카운터

|개체 이름 |카운터 이름 |  
|------------|-------------|  
|LogicalDisk |% 사용 가능한 공간 |  
|LogicalDisk |평균 디스크 초/읽기 |  
|LogicalDisk |평균 디스크 초/전송 |  
|LogicalDisk |평균 디스크 초/쓰기 |  
|LogicalDisk |디스크 바이트/초 |  
|LogicalDisk |디스크 읽기 바이트/초  |  
|LogicalDisk |디스크 읽기/초  |  
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
|논리 디스크 |디스크 읽기 바이트/초  |  
|논리 디스크 |디스크 읽기/초  |  
|논리 디스크 |디스크 전송/초 |  
|논리 디스크 |디스크 쓰기 바이트/초 |  
|논리 디스크 |디스크 쓰기/초 |  
|논리 디스크 |사용 가능한 메가바이트 |  
|논리 디스크 |논리 디스크 바이트/초 |  
|메모리 |사용 가능한 MB 메모리 |  
|네트워크 |받은 총 바이트 |  
|네트워크 |전송된 총 바이트 |  
|프로세서 |% Processor Time |  

## <a name="enable-from-the-azure-portal"></a>Azure Portal에서 사용하도록 설정
Azure Portal에서 Azure VM의 모니터링을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Virtual Machines**를 선택합니다. 
2. 목록에서 VM을 선택합니다. 
3. VM 페이지에 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.
4. **인사이트(미리 보기)** 페이지에서 **지금 시도해 보기**를 선택합니다.

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. **Azure Monitor Insights 등록** 페이지에서 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 드롭다운 목록에서 해당 작업 영역을 선택합니다.  목록에는 구독에서 가상 머신이 배포된 기본 작업 영역 및 위치가 미리 선택되어 있습니다. 

    >[!NOTE]
    >VM의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 앞에 나열된 지원되는 지역 중 하나에서 [Log Analytics 작업 영역 만들기](../log-analytics/log-analytics-quick-create-workspace.md)의 지침을 참조하세요.   

모니터링을 사용하도록 설정하고 약 10분 후에 가상 머신에 대한 상태 메트릭을 볼 수 있습니다. 

![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Azure Policy를 사용하여 설정
프로비전된 새 VM에 대해 일관된 규정 준수 및 자동 사용을 보장하는 여러 Azure VM에 대한 솔루션을 사용하도록 설정하려면 [Azure Policy](../azure-policy/azure-policy-introduction.md)를 사용하는 것이 좋습니다.  제공된 정책과 함께 Azure Policy를 사용하면 새 VM에 다음과 같은 이점이 제공됩니다.

* 정의된 범위의 각 VM에 대해 VM용 Azure Monitor를 사용하도록 설정
* Log Analytics 에이전트 배포 
* 응용 프로그램 종속성을 검색하고 맵에 표시하도록 Dependency Agent 배포
* Azure VM OS 이미지가 정책 정의에서 미리 정의된 목록에 있는 경우 감사  
* Azure VM이 지정된 곳이 아닌 작업 영역에서 로깅되는 경우 감사
* 준수 결과 보고 
* 비준수 VM에 대한 재구성 지원

사용자 테넌트에 대해 활성화하려면 이 프로세스에 다음이 필요합니다.

- 여기에 나열된 단계를 사용하여 Log Analytics 작업 영역 구성
- 이니셔티브 정의를 사용자 테넌트에 가져오기(관리 그룹 또는 구독 수준에서)
- 정책을 원하는 범위에 할당
- 준수 결과를 검토

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>정책 및 이니셔티브를 구독에 추가
제공된 PowerShell 스크립트를 사용하면 정책을 사용할 수 있습니다. Azure PowerShell 갤러리에서 이 작업을 완료하기 위한 [Add-VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2)을 사용할 수 있습니다. 스크립트는 정책 및 이니셔티브를 사용자 구독에 추가합니다.  구독에서 Azure Policy를 구성하려면 다음 단계를 수행합니다. 

1. PowerShell 스크립트를 로컬 파일 시스템으로 다운로드합니다.

2. 정책을 추가할 폴더에서 다음 PowerShell 명령을 사용합니다. 스크립트는 다음과 같은 선택적 매개 변수를 지원합니다. 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >참고: 이니셔티브/정책을 여러 구독에 할당하려는 경우 정책을 할당할 구독이 포함된 관리 그룹에 정의를 저장해야 합니다. 따라서 -ManagementGroupID 매개 변수를 사용해야 합니다.
    >
   
    매개 변수가 없는 예제: `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>정책 할당 만들기
`Add-VMInsightsPolicy.ps1` PowerShell 스크립트를 실행한 후, 다음 이니셔티브 및 정책이 추가됩니다.

* **Windows VM용 Log Analytics 에이전트 배포 - 미리 보기**
* **Linux VM용 Log Analytics 에이전트 배포 - 미리 보기**
* **Windows VM용 Dependency Agent 배포 - 미리 보기**
* **Linux VM용 Dependency Agent 배포 - 미리 보기**
* **Log Analytics 에이전트 배포 감사 - VM 이미지(OS) 목록 없음 - 미리 보기**
* **Dependency Agent 배포 감사 - VM 이미지(OS) 목록 없음 - 미리 보기**

다음과 같은 이니셔티브 매개 변수가 추가됩니다.

- **Log Analytice 작업 영역**(PowerShell 또는 CLI를 사용하여 할당을 적용하는 경우 작업 영역의 ResourceID를 제공해야 함)

    **OS 범위 내 VM 아님...** 감사 정책에서 비준수로 발견된 VM의 경우 배포 정책의 기준은 잘 알려진 Azure VM 이미지에서 배포된 VM만 포함합니다. VM OS가 지원되는지 여부는 설명서를 확인하세요.  지원되지 않는 경우 이미지가 범위에 해당되도록 배포 정책을 복제하고 업데이트/수정해야 합니다.

다음과 같은 독립 실행형 선택적 정책이 추가됩니다.

- **VM이 일치하지 않는 Log Analytics 작업 영역에 대해 구성됨 - 미리 보기**

    이는 VM이 [Log Analytics VM 확장](../virtual-machines/extensions/oms-windows.md)에서 이미 구성되었으나 의도된 것과 다른 작업 영역으로 구성되었음을 식별하는 데 사용됩니다(정책 할당으로 표시됨). 이는 WorkspaceID에 대한 매개 변수를 사용합니다.

이 초기 릴리스를 사용하면 Azure Portal에서 정책 할당만 만들 수 있습니다. 이러한 단계를 완료하는 방법을 알아보려면 [Azure Portal에서 정책 할당 만들기](../azure-policy/assign-policy-definition.md)를 참조하세요.

## <a name="enable-with-powershell"></a>PowerShell을 통해 사용하도록 설정
여러 VM 또는 VM 확장 집합에 대해 VM용 Azure Monitor를 사용하도록 설정하기 위해 제공된 PowerShell 스크립트를 사용할 수 있습니다. Azure PowerShell 갤러리의 [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)을 사용하여 이 작업을 완료할 수 있습니다.  이 스크립트는 *ResourceGroup*에 의해 지정된 범위가 지정된 리소스 그룹의 사용자 구독에 있는 모든 가상 머신과 VM 확장 집합 또는 *Name*에 의해 지정된 단일 VM 또는 확장 집합에서 반복됩니다.  각 VM 또는 VM 확장 집합의 경우 스크립트에서 VM 확장이 이미 설치되어 있는지와 재설치를 시도하지 않았는지를 확인합니다.  그렇지 않은 경우 Log Analytics 및 Dependency Agent VM 확장 설치를 진행합니다.   

이 스크립트에는 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

스크립트에 대한 도움말을 보려면 `Get-Help`를 실행하여 인수 세부 정보 및 사용 예제를 가져올 수 있습니다.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

다음 예제에서는 VM용 Azure Monitor를 사용하도록 설정하고 예상되는 출력을 이해하기 위해 폴더에서 PowerShell 명령을 사용하는 방법을 보여줍니다.

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>하이브리드 환경에서 사용하도록 설정
이 섹션에서는 VM용 Azure Monitor에서 모니터링하도록 사용자의 데이터 센터 또는 다른 클라우드 환경에 호스팅된 가상 머신 또는 물리적 컴퓨터를 등록하는 방법을 설명합니다.  

VM용 Azure Monitor 맵 종속성 에이전트는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다. 맵의 데이터는 IT 보안 정책에서 네트워크의 컴퓨터가 인터넷에 연결하는 것을 허용하지 않는 경우 [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 통해 또는 직접 Log Analytics 에이전트에서 Azure Monitor 서비스로 항상 전송됩니다.

[Log Analytics Linux 및 Windows 에이전트](../log-analytics/log-analytics-concept-hybrid.md)에 대한 요구 사항 및 배포 방법을 검토합니다.

요약된 단계:

1. Windows 또는 Linux용 Log Analytics 에이전트를 설치합니다.
2. VM용 Azure Monitor 맵 종속성 에이전트를 설치합니다.
3. 성능 카운터의 컬렉션을 사용하도록 설정합니다.
4. VM용 Azure Monitor를 등록합니다.

### <a name="install-the-dependency-agent-on-windows"></a>Windows에 종속성 에이전트 설치 
`InstallDependencyAgent-Windows.exe`를 실행하면 Dependency Agent를 수동으로 Windows 컴퓨터에 설치할 수 있습니다. 옵션 없이 이 실행 파일을 실행하면 설치 마법사가 시작되고 대화형으로 설치를 수행할 수 있습니다.  

>[!NOTE]
>에이전트를 설치 또는 제거하려면 관리자 권한이 필요합니다.

다음 테이블에서는 명령줄에서 에이전트 설정을 통해 지원되는 특정 매개 변수가 강조 표시되어 있습니다.  

| 매개 변수 | 설명 |
|:--|:--|
| /? | 명령줄 옵션의 목록을 반환합니다. |
| /S | 사용자 상호 작용 없이 자동 설치를 수행합니다. |

예를 들어 `/?` 매개 변수를 사용하여 설치 프로그램을 실행하려면 `InstallDependencyAgent-Windows.exe /?`를 입력합니다.

Windows Dependency Agent에 대한 파일은 `C:\Program Files\Microsoft Dependency Agent`에 기본적으로 설치됩니다.  설정을 완료한 후 Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. 로그 디렉터리는 `%Programfiles%\Microsoft Dependency Agent\logs`입니다. 

### <a name="install-the-dependency-agent-on-linux"></a>Linux에 Dependency Agent 설치
Dependency Agent는 셀프 추출 이진이 포함된 셸 스크립트인 `InstallDependencyAgent-Linux64.bin`에서 Linux 서버에 설치됩니다. `sh`를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.

>[!NOTE]
> 에이전트를 설치 또는 구성하려면 루트 액세스가 필요합니다.
> 

| 매개 변수 | 설명 |
|:--|:--|
| -help | 명령줄 옵션 목록을 가져옵니다. |
| -s | 사용자 프롬프트 없이 자동 설치를 수행합니다. |
| --check | 권한 및 운영 체제를 확인하지만 에이전트는 설치하지 않습니다. |

예를 들어 `-help` 매개 변수를 사용하여 설치 프로그램을 실행하려면 `InstallDependencyAgent-Linux64.bin -help`를 입력합니다.

`sh InstallDependencyAgent-Linux64.bin` 명령을 실행하여 루트로 Linux Dependency Agent를 설치합니다.
    
Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 `/var/opt/microsoft/dependency-agent/log`입니다.

Dependency Agent에 대한 파일은 다음 디렉터리에 있습니다.

| 파일 | 위치 |
|:--|:--|
| 코어 파일 | /opt/microsoft/dependency-agent |
| 로그 파일 | /var/opt/microsoft/dependency-agent/log |
| 구성 파일 | /etc/opt/microsoft/dependency-agent/config |
| 서비스 실행 파일 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 이진 저장소 파일 | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>성능 카운터 사용하도록 설정
솔루션에서 참조되는 Log Analytics 작업 영역이 솔루션에 필요한 성능 카운터를 수집하도록 구성되지 않은 경우 사용하도록 설정해야 합니다. [여기](../log-analytics/log-analytics-data-sources-performance-counters.md)에 설명된 대로 수동으로 수행하거나 [Azure Powershell 갤러리](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)에서 확인할 수 있는 PowerShell 스크립트를 다운로드하고 실행하여 수행할 수 있습니다.
 
### <a name="onboard-azure-monitor-for-vms"></a>VM용 Azure Monitor를 등록합니다.
이 메서드는 Log Analytics 작업 영역에 솔루션 구성 요소를 사용하도록 구성을 지정하는 JSON 템플릿을 포함합니다.  

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

#### <a name="create-and-execute-a-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. 이 파일을 **installsolutionsforvminsights.json**으로 로컬 폴더에 저장합니다.
3. **WorkspaceName**, **ResourceGroupName** 및 **WorkspaceLocation;** 에 대한 값을 편집합니다.  **WorkspaceName**에 대한 값은 작업 영역 이름을 포함하는 Log Analytics 작업 영역의 전체 리소스 ID이며, **WorkspaceLocation**에 대한 값은 작업 영역이 정의되는 지역입니다.
4. 다음 PowerShell 명령을 사용하여 이 템플릿을 배포할 준비가 되었습니다.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    provisioningState       : Succeeded
    ```
모니터링을 사용하도록 설정하고 약 10분 후에 하이브리드 컴퓨터에 대한 상태 및 메트릭을 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계

가상 머신에 사용하도록 설정된 모니터링을 통해 VM용 Azure Monitor를 사용한 분석에 이 정보를 사용할 수 있습니다.  상태 기능을 사용하는 방법을 알아보려면 [VM용 Azure Monitor 상태 보기](monitoring-vminsights-health.md)를 참조하고, 검색된 응용 프로그램 종속성을 보려면 [VM 맵에 대한 Azure Monitor 보기](monitoring-vminsights-maps.md)를 참조하세요.  