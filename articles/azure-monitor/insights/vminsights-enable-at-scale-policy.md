---
title: Azure Policy를 사용 하 여 Vm에 대 한 Azure Monitor를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 여러 Azure virtual machines 또는 Azure Policy를 사용 하 여 가상 머신 확장 집합에 대 한 Vm에 대 한 Azure Monitor을 사용 하는 방법을 설명 합니다.
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
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524147"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Azure Policy를 사용 하 여 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정

이 문서에서는 Azure 가상 머신 또는 Azure Policy를 사용 하 여 가상 머신 확장 집합에 대 한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정 하는 방법에 설명 합니다. 이 프로세스의 끝은 성공적으로 Log Analytics 및 종속성 에이전트를 사용 하도록 설정 하는 구성 된 있고 호환 되지 않는 가상 컴퓨터를 식별 합니다.

검색 하 고, 관리 및 Azure 가상 머신 또는 가상 머신 확장 집합의 모든 Vm에 대 한 Azure Monitor를 사용 하도록 설정, Azure 정책 또는 Azure PowerShell을 사용할 수 있습니다. Azure Policy는 일관 된 호환성을 준수 하도록 구독을 효과적으로 제어 하는 정책 정의 관리할 수 있으며 Vm을 프로 비전 자동 사용 가능 하 게 새로 때문에 권장 되는 메서드입니다. 이러한 정책 정의 합니다.

* Log Analytics 에이전트 및 Dependency Agent를 배포합니다.
* 규정 준수 결과를 보고합니다.
* 비준수 VM을 수정합니다.

Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여이 수행 하는 데 관심이 있다면 참조 [Azure PowerShell 또는 Resource Manager 템플릿을 사용 하 여 사용 하도록 설정](vminsights-enable-at-scale-powershell.md)합니다. 

## <a name="manage-policy-coverage-feature-overview"></a>관리 정책 검사 기능 개요

원래 Vm에 대 한 Azure Monitor에 대 한 정책 정의 배포 및 관리에 대 한 Azure Policy를 사용 하 여 환경은 Azure Policy에서 단독으로 수행 되었습니다. 사용 하 여는 **관리 정책 검사** 기능을 하면 간단 하 고 검색, 관리 및 크기 조정에 사용 하도록 설정 하는 작업을 쉽게 합니다 **Vm에 대 한 Azure Monitor를 사용 하도록 설정** 이니셔티브 정책 정의 포함 하는 앞에서 언급 한 합니다. 이 새 기능에 액세스할 수 있습니다 합니다 **시작** 를 선택 하 여 Vm에 대 한 Azure Monitor에서 탭 **관리 정책 검사**합니다. Vm 정책 검사 페이지를 엽니다. 

![Vm 시작 탭에서 azure Monitor](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

여기에서 확인 및 이니셔티브에 대 한 검사에서 관리 그룹 및 구독 관리를 수 및을 얼마나 많은 Vm을 이해 각 준수 상태는 관리 그룹 및 구독에 존재 합니다.   

![Vm 관리 정책 페이지에 대 한 azure 모니터](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

이 정보는 계획 하 고 하나의 중앙 위치에서 Vm에 대 한 Azure Monitor에 대 한 거 버 넌 스 시나리오를 실행 하는 데 유용 합니다. 범위는 정책/이니셔티브 할당 되 면 준수 보기를 제공 하는 Azure Policy를이 새 페이지를 사용 하 여 검색할 수 있습니다 정책/이니셔티브 할당 되지 않았고 현재 위치를 할당 합니다. 모든 작업 (할당, 보기, 편집) Azure Policy를 직접 리디렉션됩니다. Vm 정책 검사 페이지에 대 한 azure Monitor는만 기술 하는 확장 된 통합된 환경을 **Vm에 대 한 Azure Monitor를 사용 하도록 설정**합니다. 

이 페이지에서 구성할 수도 있습니다 Log Analytics 작업 영역의 Azure Monitor에 대 한 vm의 경우 다음을 수행 하는:

- 서비스 맵을 설치 및 Infrastructure Insights 솔루션 설치
- 성능 차트, 통합 문서 및 사용자 지정 로그 쿼리 및 경고 사용 운영 체제 성능 카운터를 사용 하도록 설정 합니다.

![Vm에 대 한 azure Monitor는 작업 영역 구성](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

이 옵션 모든 정책 작업에 관련 되지 않습니다 되며를 충족 하는 쉬운 방법을 제공할 수 있는 합니다 [필수 구성 요소](vminsights-enable-overview.md) 필요한 Vm 용 Azure Monitor를 사용 하도록 설정 합니다.  

### <a name="what-information-is-available-on-this-page"></a>이 페이지 에서도 다운로드 가능 정보 인지 확인
다음 표에서 정책 검사 페이지에 어떤 정보가 표시 됩니다 및 해석 하는 방법에 대 한 분석을 제공 합니다.

| 함수 | 설명 | 
|----------|-------------| 
| **범위** | 관리 그룹 및가 수 있는 구독 또는 관리를 통해 드릴 다운 하는 기능을 사용 하 여 계층 구조 그룹에 대 한 액세스를 상속 합니다.|
| **역할** | 사용자 역할 범위에 판독기 소유자 또는 참가자 수 있습니다. 일부 경우에는 구독에 액세스할 수 있지만 하지 관리 그룹에 속한 빈 나타날 수 있습니다. 다른 열에 있는 정보는 어떤 데이터를 볼 수 있습니다 및 이니셔티브/정책 (소유자)을 할당, 편집, 또는 규정 준수 보기를 기준으로 수행할 수 있는 작업을 결정 하는 키 이므로 역할에 따라 달라 집니다. |
| **총 Vm** | 해당 범위 내에서 Vm의 수입니다. 관리 그룹, 구독 및/또는 자식 관리 그룹 아래에 중첩 된 Vm의 합계입니다. |
| **할당 검사** | 프로그램/정책 적용 되는 Vm의 백분율입니다. |
| **할당 상태** | 이 칼럼에서는 아래에서 정책의 상태 정보를 찾을 수 있습니다 / 이니셔티브 할당 합니다. |
| **호환 Vm** | 정책/이니셔티브 준수는 Vm의 수입니다. 이니셔티브에 대 한 **Vm에 대 한 Azure Monitor를 사용 하도록 설정** Log Analytics 에이전트와 종속성 에이전트는 Vm의 수입니다. 경우에 따라 빈 할당 없음 또는 Vm이 없는 또는 권한이 부족으로 인해 나타날 수 있습니다. 준수 상태의 정보가 제공 됩니다. |
| **규정 준수** | 전체 준수 번호가 모든 고유한 리소스의 합으로 고유한 리소스를 구분 하는 준수의 합계입니다. |
| **규정 준수 상태** | 정책에 대 한 준수 상태에 대 한 정보 / 이니셔티브 할당 합니다.|

프로그램/정책에 할당할 때 할당에서 선택한 범위를 나열 하는 범위 또는 해당 하위 집합 수 수 있습니다. 예를 들어 만들었을 수 있습니다 (정책 범위) 구독을 관리 그룹 (검사 범위)에 할당 합니다. 이 경우 값 **할당 검사** 은 검사 범위에서 Vm으로 나눈 Vm 정책 (또는 이니셔티브)에서 범위를 나타냅니다. 다른 경우에서 있습니다 수 있는 일부 Vm, 리소스 그룹 또는 구독 범위에서 제외 정책. 값이 비어 있으면 해당 정책/이니셔티브 존재 하지 않는 없거나 나타냅니다 수 있는 권한이 없습니다 (자세한 내용은 할당 상태).

## <a name="enable-using-azure-policy"></a>Azure Policy를 사용하여 설정

테넌트에서 Azure Policy를 사용하여 VM용 Azure Monitor를 사용하도록 설정하려면

- 범위(관리 그룹, 구독 또는 리소스 그룹)에 이니셔티브를 할당합니다.
- 규정 준수 결과를 검토 및 수정합니다.

계속하기 전에 [Azure Policy 개요](../../governance/policy/overview.md#policy-assignment)를 참조하고 [관리 그룹 개요](../../governance/management-groups/index.md)를 검토하여 Azure Policy 할당에 대해 자세히 알아보세요.

### <a name="policies-for-azure-vms"></a>Azure Vm에 대 한 정책

Azure VM에 대 한 정책 정의 다음 표에 나열 된:

|이름 |설명 |Type |
|-----|------------|-----|
|[미리 보기]: VM용 Azure Monitor 사용 |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 VMs(Virtual Machines)용 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. |이니셔티브 |
|[미리 보기]: Dependency Agent 배포 감사 - VM 이미지(OS)가 나열 취소됨 |VM 이미지(OS)가 목록에 정의되어 있지 않고 에이전트가 설치되어 있지 않은 경우 VM을 비준수로 보고합니다. |정책 |
|[미리 보기]: Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨 |VM 이미지(OS)가 목록에 정의되어 있지 않고 에이전트가 설치되어 있지 않은 경우 VM을 비준수로 보고합니다. |정책 |
|[미리 보기]: Linux VM용 Dependency Agent 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Linux VM용 Dependency Agent를 배포합니다. |정책 |
|[미리 보기]: Windows VM용 Dependency Agent 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Windows VM용 Dependency Agent를 배포합니다. |정책 |
|[미리 보기]: Linux VM용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Linux VM용 Log Analytics 에이전트를 배포합니다. |정책 |
|[미리 보기]: Windows VM용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Windows VM용 Log Analytics 에이전트를 배포합니다. |정책 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 가상 컴퓨터에 대 한 정책을 확장 집합

다음 표에 Azure 가상 머신 확장 집합에 대 한 정책 정의 같습니다.

|이름 |설명 |Type |
|-----|------------|-----|
|[미리 보기]: VM Scale Sets (VMSS)에 대 한 Azure Monitor를 사용 하도록 설정 |지정된 된 범위 (관리 그룹, 구독 또는 리소스 그룹)에서 가상 머신 확장 집합에 대 한 Azure Monitor를 사용 합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. 참고: 확장 집합 upgradePolicy 프로그램을 Manual로 설정 하는 경우 해야에 업그레이드를 호출 하 여 확장 집합의 모든 Vm에 적용 합니다. CLI에서 az vmss update-인스턴스가 됩니다. |이니셔티브 |
|[미리 보기]: VMSS – VM 이미지 (OS) 목록에 없는에서 종속성 에이전트 배포를 감사 합니다. |보고서 가상 머신 확장 집합에서 VM 이미지 (OS) 목록 및 에이전트에 정의 되어 있지 않으면 비준수 설치 되지 않았습니다. |정책 |
|[미리 보기]: VMSS – VM 이미지 (OS) 목록에 없는의 audit Log Analytics 에이전트 배포 |보고서 가상 머신 확장 집합에서 VM 이미지 (OS) 목록 및 에이전트에 정의 되어 있지 않으면 비준수 설치 되지 않았습니다. |정책 |
|[미리 보기]: Linux VM Scale Sets (VMSS)에 대 한 종속성 에이전트를 배포 합니다. |Linux VM 이미지 (OS) 목록에서 정의 되 고 에이전트가 설치 되지 않은 가상 머신 확장 집합에 대 한 종속성 에이전트를 배포 합니다. |정책 |
|[미리 보기]: Windows VM Scale Sets (VMSS)에 대 한 종속성 에이전트를 배포 합니다. |종속성 에이전트에 대 한 Windows VM 이미지 (OS) 목록에서 정의 되 고 에이전트가 설치 되지 않은 가상 머신 확장 집합을 배포 합니다. |정책 |
|[미리 보기]: Linux VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포 |가상 머신 확장 집합에서 VM 이미지 (OS) 목록에서 정의 되 고 에이전트가 설치 되지 않은 경우 Linux 용 Log Analytics 에이전트를 배포 합니다. |정책 |
|[미리 보기]: Windows VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포 |Log Analytics 에이전트에 대 한 Windows VM 이미지 (OS) 목록에서 정의 되 고 에이전트가 설치 되지 않은 가상 머신 확장 집합을 배포 합니다. |정책 |

다음은 독립 실행형 정책(이니셔티브에 포함되지 않음)에 대한 설명입니다.

|이름 |설명 |Type |
|-----|------------|-----|
|[미리 보기]: VM용 Log Analytics 작업 영역 감사 - 보고서 불일치 |정책/이니셔티브 할당에 지정된 Log Analytics 작업 영역에 로깅하지 않는 경우 VM을 비준수로 보고합니다. |정책 |

### <a name="assign-the-azure-monitor-initiative"></a>Azure Monitor 이니셔티브 할당
Vm 정책 검사 페이지에 대 한 Azure Monitor에서 정책 할당을 만들려면 다음 단계를 수행 합니다. 이러한 단계를 완료하는 방법을 알아보려면  [Azure Portal에서 정책 할당 만들기](../../governance/policy/assign-policy-portal.md)를 참조하세요.

프로그램/정책에 할당할 때 할당에서 선택한 범위는 여기에 나열 된 범위 또는의 하위 집합 수 있습니다. 예를 들어 만들었을 수 (정책 범위) 구독에 대 한 할당 및 있는 사례 검사 %477860 Vm 정책 (또는 이니셔티브)에서 범위 검사 범위에서 Vm으로 나눈 관리 그룹 (검사 범위) 되지 않습니다. 일부 다른 경우에서 있습니다 수 있는 일부 Vm 또는 Rg 또는 구독 범위에서 제외 정책.  빈 하는 경우에 나타냅니다 중 하나를 정책 / 이니셔티브 존재 하지 않거나 있습니다 권한이 없습니다 (할당 상태에 제공 된 정보)  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모니터**를 선택합니다. 

3. **인사이트** 섹션에서 **Virtual Machines(미리 보기)** 를 선택합니다.
 
4. 선택 된 **시작** 탭을 선택한 페이지의 **관리 정책 검사**합니다.

5. 테이블에서 관리 그룹 또는 구독을 선택 하 고 선택 **범위** 줄임표 (...)를 클릭 하 여 합니다.      이 예제에서는 범위에서 정책 할당을 가상 머신 그룹으로 제한하여 적용합니다.

6. 에 **Azure 정책 할당** 이니셔티브를 사용 하 여 미리 채워집니다 페이지 **Vm에 대 한 Azure Monitor를 사용 하도록 설정**. 
    합니다 **할당 이름** 이니셔티브 이름의 상자가 자동으로 채워집니다 있지만 변경할 수 있습니다. 선택적인 설명을 추가할 수도 있습니다. **할당한 사람** 상자는 로그인한 사용자를 기반으로 하여 자동으로 채워지며, 이 값은 선택 사항입니다.

7. (선택 사항) 범위에서 하나 이상의 리소스를 제거하려면 **제외**를 선택합니다.

8. 지원되는 지역에 대한 **Log Analytics 작업 영역** 드롭다운 목록에서 작업 영역을 선택합니다.

   > [!NOTE]
   > 작업 영역이 할당 범위를 벗어나는 경우 *Log Analytics 기여자* 권한을 정책 할당의 Principal ID에 부여합니다. 이렇게 하지 않으면 다음과 같은 배포 오류가 표시될 수 있습니다. `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` 액세스 권한을 부여하려면 [수동으로 관리 ID를 구성하는 방법](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)을 검토하세요.
   > 
   >  할당할 이니셔티브에 *deployIfNotExists*가 적용되는 정책이 포함되어 있으므로 **관리 ID** 확인란이 선택되어 있습니다.
    
9. **ID 위치 관리** 드롭다운 목록에서 해당 지역을 선택합니다.

10. **할당**을 선택합니다.

할당을 만든 후 Vm 정책 검사 페이지에 대 한 Azure Monitor는 할당 검사, 할당 상태, Vm 규정 준수 및 규정 준수 상태 변화를 반영 하도록 업데이트 됩니다. 

다음 매트릭스 이니셔티브에 대 한 각 가능한 규정 준수 상태를 매핑합니다.  

| 준수 상태 | 설명 | 
|------------------|-------------|
| **준수** | 범위에서 모든 Vm에 배포 된 Log Analytics 및 종속성 에이전트를 포함 합니다.|
| **호환 되지 않음** | 범위에서 모든 Vm에 Log Analytics 및 종속성 에이전트를 배포 하 고 수정 해야 할 수 있습니다.|
| **시작 되지 않음** | 새 할당을 추가 했습니다. |
| **잠금** | 관리 그룹에 충분 한 권한이 없습니다. <sup>1</sup> | 
| **비어 있음** | 할당 된 정책이 없는 합니다. | 

<sup>1</sup> 관리 그룹에는 액세스할 수 없는 경우 액세스를 제공 합니다. 또는 규정 준수를 보고, 자식 관리 그룹 또는 구독을 통해 할당을 관리 하는 소유자를 요청 합니다. 

다음 표에서 이니셔티브에 대 한 각 가능한 할당 상태를 보여 줍니다.

| 할당 상태 | 설명 | 
|------------------|-------------|
| **성공** | 범위에서 모든 Vm에 배포 된 Log Analytics 및 종속성 에이전트를 포함 합니다.|
| **Warning** | 구독 관리 그룹에서 아닙니다.|
| **시작 되지 않음** | 새 할당을 추가 했습니다. |
| **잠금** | 관리 그룹에 충분 한 권한이 없습니다. <sup>1</sup> | 
| **비어 있음** | Vm이 없는 없거나 정책이 할당 되지 합니다. | 
| **작업** | 할당 정책 또는 편집 할당 | 

<sup>1</sup> 관리 그룹에는 액세스할 수 없는 경우 액세스를 제공 합니다. 또는 규정 준수를 보고, 자식 관리 그룹 또는 구독을 통해 할당을 관리 하는 소유자를 요청 합니다. 

## <a name="review-and-remediate-the-compliance-results"></a>규정 준수 결과 검토 및 수정

다음 예제에서는 Azure VM에 대해 되었지만 가상 머신 확장 집합에도 적용 됩니다. [규정 비준수 결과 식별](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)을 참조하여 규정 준수 결과를 검토하는 방법을 알아볼 수 있습니다. Vm 정책 검사 페이지에 대 한 Azure Monitor에서 관리 그룹 또는 구독 테이블에서 선택한 **준수 보기** 줄임표 (...)를 클릭 하 여 합니다.   

![Azure VM에 대한 정책 준수](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

이니셔티브에 포함된 정책의 결과에 따라 VM이 비준수로 보고되는 시나리오는 다음과 같습니다.

* Log Analytics 또는 Dependency Agent가 배포되지 않았습니다.  
    이 시나리오는 일반적으로 기존 VM이 포함된 범위에 해당합니다. 이 문제를 완화하려면 비준수 정책에 대한 [수정 작업을 만들어](../../governance/policy/how-to/remediate-resources.md) 필요한 에이전트를 배포합니다.  
    - [미리 보기]: Deploy Dependency Agent for Linux VMs
    - [미리 보기]: Deploy Dependency Agent for Windows VMs
    - [미리 보기]: Deploy Log Analytics Agent for Linux VMs
    - [미리 보기]: Deploy Log Analytics Agent for Windows VMs

* VM 이미지(OS)가 정책 정의에서 식별되지 않습니다.  
    배포 정책의 조건에는 잘 알려진 Azure VM 이미지에서 배포된 VM만 포함됩니다. VM OS가 지원되는지 여부는 설명서를 확인하세요. 지원되지 않는 경우 배포 정책을 복제하고 업데이트 또는 수정하여 이미지가 준수되도록 해야 합니다.  
    - [미리 보기]: Dependency Agent 배포 감사 - VM 이미지(OS)가 나열 취소됨
    - [미리 보기]: Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨

* VM이 지정된 Log Analytics 작업 영역에 로그인되지 않습니다.  
    이니셔티브 범위의 일부 VM이 정책 할당에 지정된 작업 영역 이외의 Log Analytics 작업 영역에 로그인된 것일 수 있습니다. 이 정책은 비준수 작업 영역에 보고하는 VM을 식별하는 도구입니다.  
    - [미리 보기]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>이니셔티브 할당 편집

언제 든 지 관리 그룹 또는 구독에 이니셔티브 할당 한 후 다음 속성을 수정 하 고 편집할 수 있습니다.

- 할당 이름
- 설명
- 할당한 사람
- Log Analytics 작업 영역
- 예외

## <a name="next-steps"></a>다음 단계

가상 컴퓨터에 대 한 모니터링이 활성화 했으므로이 정보는 Vm에 대 한 Azure Monitor를 사용 하 여 분석을 위해 사용할 수 있습니다. 상태 기능을 사용하는 방법을 알아보려면 [VM용 Azure Monitor 상태 보기](vminsights-health.md)를 참조하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요. 병목 상태 및 VM 성능에 대한 전반적인 사용률을 확인하려면 [Azure VM 성능 보기](vminsights-performance.md)를 참조하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.