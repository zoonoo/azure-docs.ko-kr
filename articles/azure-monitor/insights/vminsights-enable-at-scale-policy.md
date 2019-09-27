---
title: Azure Policy을 사용 하 여 VM용 Azure Monitor 사용 Microsoft Docs
description: 이 문서에서는 Azure Policy를 사용 하 여 여러 Azure 가상 머신 또는 가상 머신 확장 집합에 대해 VM용 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다.
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
ms.openlocfilehash: cbb471d337bd386b6c5f2c7a960565ef29855c9c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338231"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Azure Policy를 사용 하 여 VM용 Azure Monitor (미리 보기) 사용

이 문서에서는 Azure Policy를 사용 하 여 Azure 가상 머신 또는 가상 머신 확장 집합에 대해 VM용 Azure Monitor (미리 보기)를 사용 하도록 설정 하는 방법을 설명 합니다. 이 프로세스가 완료 되 면 Log Analytics 및 종속성 에이전트를 사용 하도록 설정 하 고 규정을 준수 하지 않는 가상 머신이 식별 됩니다.

모든 Azure 가상 머신 또는 가상 머신 확장 집합에 대 한 VM용 Azure Monitor를 검색, 관리 및 사용 하도록 설정 하려면 Azure Policy 또는 Azure PowerShell를 사용할 수 있습니다. Azure Policy은 일관 된 준수를 보장 하 고 새로 프로 비전 된 Vm을 자동으로 사용할 수 있도록 구독을 효과적으로 관리 하기 위해 정책 정의를 관리할 수 있기 때문에 권장 되는 방법입니다. 이러한 정책 정의는 다음과 같습니다.

* Log Analytics 에이전트 및 Dependency Agent를 배포합니다.
* 규정 준수 결과를 보고합니다.
* 비규격 Vm에 대해 재구성 합니다.

Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 이러한 작업을 수행 하는 데 관심이 있는 경우 [Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 VM용 Azure Monitor (미리 보기) 사용](vminsights-enable-at-scale-powershell.md)을 참조 하세요.

## <a name="manage-policy-coverage-feature-overview"></a>정책 검사 기능 관리 개요

처음에는 VM용 Azure Monitor에 대 한 정책 정의를 관리 및 배포 하는 Azure Policy에 대 한 경험이 Azure Policy 에서만 수행 되었습니다. 정책 적용 범위 관리 기능을 사용 하면 앞에서 언급 한 정책 정의를 포함 하 여 **VM용 Azure Monitor 사용** 이니셔티브를 보다 간단 하 고 쉽게 검색 하 고, 관리 하 고, 사용 하도록 설정할 수 있습니다. VM용 Azure Monitor의 **시작** 탭에서이 새로운 기능에 액세스할 수 있습니다. **정책 적용 범위 관리** 를 선택 하 여 **VM용 Azure Monitor 정책 적용 범위** 페이지를 엽니다.

![Vm에서 Azure Monitor 시작 탭](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

여기에서 관리 그룹 및 구독에 대 한 이니셔티브의 적용 범위를 확인 하 고 관리할 수 있습니다. 각 관리 그룹 및 구독에 존재 하는 Vm 수와 해당 규정 준수 상태를 이해할 수 있습니다.

![VM용 Azure Monitor 정책 관리 페이지](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

이 정보는 하나의 중앙 위치에서 VM용 Azure Monitor에 대 한 거 버 넌 스 시나리오를 계획 하 고 실행 하는 데 유용 합니다. 정책 또는 이니셔티브를 범위에 할당 하는 경우 Azure Policy 준수 보기를 제공 하는 반면,이 새 페이지를 사용 하 여 정책 또는 이니셔티브 할당 되지 않은 위치를 검색 하 고 해당 위치에 할당할 수 있습니다. 할당, 보기 및 편집 리디렉션과 같은 모든 작업은 직접 Azure Policy 합니다. **VM용 Azure Monitor 정책 적용 범위** 페이지는 이니셔티브 **사용 VM용 Azure Monitor**에 대 한 확장 및 통합 환경입니다.

또한이 페이지에서 다음과 같은 VM용 Azure Monitor에 대 한 Log Analytics 작업 영역을 구성할 수 있습니다.

- 설치 서비스 맵 및 Infrastructure Insights 솔루션을 설치 합니다.
- 성능 차트, 통합 문서, 사용자 지정 로그 쿼리 및 경고에서 사용 하는 운영 체제 성능 카운터를 사용 하도록 설정 합니다.

![작업 영역 구성 VM용 Azure Monitor](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

이 옵션은 정책 작업과 관련이 없습니다. VM용 Azure Monitor를 사용 하도록 설정 하는 데 필요한 [필수 구성 요소](vminsights-enable-overview.md) 를 쉽게 충족 하는 방법을 제공할 수 있습니다.  

### <a name="what-information-is-available-on-this-page"></a>이 페이지에서 사용할 수 있는 정보는 무엇 인가요?
다음 표에서는 정책 적용 범위 페이지에 표시 되는 정보와이를 해석 하는 방법에 대 한 분석을 제공 합니다.

| 기능 | 설명 | 
|----------|-------------| 
| **범위** | 관리 그룹 계층을 통해 드릴 다운할 수 있는 권한으로 액세스 하거나 상속한 관리 그룹 및 구독입니다.|
| **역할** | 범위에 대 한 사용자의 역할 (판독기, 소유자 또는 참가자 일 수 있음) 어떤 경우에는 구독에 대 한 액세스 권한은 있지만 자신이 속한 관리 그룹에 대 한 액세스 권한은 없다는 것을 나타내는 것이 빈 상태로 표시 될 수 있습니다. 다른 열의 정보는 사용자의 역할에 따라 달라 집니다. 역할은 정책 또는 이니셔티브 (소유자)를 할당 하거나 편집 하거나 준수를 확인 하는 등 볼 수 있는 데이터와 수행할 수 있는 작업을 결정 하는 데 중요 합니다. |
| **총 Vm** | 해당 범위 아래에 있는 Vm의 수입니다. 관리 그룹의 경우 구독 또는 자식 관리 그룹에 중첩 된 Vm의 합계입니다. |
| **할당 검사** | 정책 또는 이니셔티브에서 적용 되는 Vm의 백분율입니다. |
| **할당 상태** | 정책 또는 이니셔티브 할당 상태에 대 한 정보입니다. |
| **규격 Vm** | 정책 또는 이니셔티브를 준수 하는 Vm의 수입니다. 이니셔티브 **사용 VM용 Azure Monitor**에 대해 Log Analytics 에이전트와 종속성 에이전트를 모두 포함 하는 vm의 수입니다. 어떤 경우에는 할당 되지 않았거나 Vm이 없거나 사용 권한이 부족 하 여 빈 상태로 표시 될 수도 있습니다. 정보는 **준수 상태**에 따라 제공 됩니다. |
| **규정 준수** | 전반적인 규정 준수 번호는 규정을 준수 하는 고유 리소스의 합계를 모든 고유 리소스의 합계로 나눈 값입니다. |
| **규정 준수 상태** | 정책 또는 이니셔티브 할당에 대 한 준수 상태에 대 한 정보입니다.|

정책 또는 이니셔티브를 할당 하는 경우 할당에서 선택 된 범위는 나열 된 범위 또는 하위 집합 일 수 있습니다. 예를 들어 관리 그룹 (검사 범위)이 아닌 구독 (정책 범위)에 대 한 할당을 만들었을 수 있습니다. 이 경우 **할당** 범위의 값은 정책 또는 이니셔티브 범위에서 vm을 검사 범위의 vm으로 나눈 값을 나타냅니다. 다른 경우에는 일부 Vm, 리소스 그룹 또는 정책 범위에서 구독을 제외 했을 수 있습니다. 값이 비어 있으면 정책이 나 이니셔티브가 없거나 사용 권한이 없음을 나타냅니다. 정보는 **할당 상태**에서 제공 됩니다.

## <a name="enable-by-using-azure-policy"></a>Azure Policy를 사용하여 설정

테넌트에서 Azure Policy를 사용하여 VM용 Azure Monitor를 사용하도록 설정하려면

- 관리 그룹, 구독 또는 리소스 그룹의 범위에 이니셔티브를 할당 합니다.
- 준수 결과를 검토 하 고 수정 합니다.

계속하기 전에 [Azure Policy 개요](../../governance/policy/overview.md#policy-assignment)를 참조하고 [관리 그룹 개요](../../governance/management-groups/overview.md)를 검토하여 Azure Policy 할당에 대해 자세히 알아보세요.

### <a name="policies-for-azure-vms"></a>Azure Vm에 대 한 정책

Azure VM에 대 한 정책 정의는 다음 표에 나와 있습니다.

|이름 |설명 |형식 |
|-----|------------|-----|
|\[미리 보기\]: VM에 대해 Azure Monitor 사용 |지정 된 범위 (관리 그룹, 구독 또는 리소스 그룹)에서 가상 컴퓨터에 대 한 Azure Monitor를 사용 하도록 설정 합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. |이니셔티브 |
|\[미리 보기\]: 종속성 에이전트 배포 감사 – VM 이미지 (OS) 나열 되지 않음 |VM 이미지 (OS)가 목록에 정의 되어 있지 않고 에이전트가 설치 되어 있지 않은 경우 vm을 비규격으로 보고 합니다. |정책 |
|\[미리 보기\]: 감사 Log Analytics 에이전트 배포 – VM 이미지 (OS) 나열 되지 않음 |VM 이미지 (OS)가 목록에 정의 되어 있지 않고 에이전트가 설치 되어 있지 않은 경우 vm을 비규격으로 보고 합니다. |정책 |
|\[미리 보기\]: Linux Vm에 대 한 종속성 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux Vm에 대 한 종속성 에이전트를 배포 합니다. |정책 |
|\[미리 보기\]: Windows Vm에 대 한 종속성 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Windows Vm에 대 한 종속성 에이전트를 배포 합니다. |정책 |
|\[미리 보기\]: Linux Vm에 대 한 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux Vm에 대 한 Log Analytics 에이전트를 배포 합니다. |정책 |
|\[미리 보기\]: Windows Vm에 대 한 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Windows Vm에 대 한 Log Analytics 에이전트를 배포 합니다. |정책 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합에 대 한 정책

다음 표에는 Azure 가상 머신 확장 집합에 대 한 정책 정의가 나열 되어 있습니다.

|이름 |설명 |type |
|-----|------------|-----|
|\[미리 보기\]: 가상 머신 확장 집합에 대 한 Azure Monitor 사용 |지정 된 범위 (관리 그룹, 구독 또는 리소스 그룹)에서 가상 머신 확장 집합에 대 한 Azure Monitor를 사용 하도록 설정 합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. 참고: 확장 집합 업그레이드 정책을 수동으로 설정한 경우에는 업그레이드를 호출 하 여 집합의 모든 Vm에 확장을 적용 합니다. CLI에서이는 az vmss update-instances입니다. |이니셔티브 |
|\[미리 보기\]: 가상 머신 확장 집합에서 종속성 에이전트 배포 감사 – VM 이미지 (OS)가 나열 되지 않음 |VM 이미지 (OS)가 목록에 정의 되어 있지 않고 에이전트가 설치 되어 있지 않으면 가상 머신 확장 집합을 비규격으로 보고 합니다. |정책 |
|\[미리 보기\]: Virtual machine scale sets의 감사 Log Analytics 에이전트 배포 – VM 이미지 (OS)가 나열 되지 않음 |VM 이미지 (OS)가 목록에 정의 되어 있지 않고 에이전트가 설치 되어 있지 않으면 가상 머신 확장 집합을 비규격으로 보고 합니다. |정책 |
|\[미리 보기\]: Linux 가상 머신 확장 집합에 대 한 종속성 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux 가상 머신 확장 집합에 대 한 종속성 에이전트를 배포 합니다. |정책 |
|\[미리 보기\]: Windows 가상 머신 확장 집합에 대 한 종속성 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않으면 Windows 가상 머신 확장 집합에 대 한 종속성 에이전트를 배포 합니다. |정책 |
|\[미리 보기\]: Linux 가상 머신 확장 집합에 대 한 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux 가상 머신 확장 집합에 대 한 Log Analytics 에이전트를 배포 합니다. |정책 |
|\[미리 보기\]: Windows 가상 머신 확장 집합에 대 한 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Windows 가상 머신 확장 집합에 대 한 Log Analytics 에이전트를 배포 합니다. |정책 |

다음은 독립 실행형 정책(이니셔티브에 포함되지 않음)에 대한 설명입니다.

|이름 |설명 |형식 |
|-----|------------|-----|
|\[미리 보기\]: VM에 대 한 감사 Log Analytics 작업 영역-보고서 불일치 |정책 또는 이니셔티브 할당에 지정 된 Log Analytics 작업 영역에 로깅하지 않는 경우 Vm을 비규격으로 보고 합니다. |정책 |

### <a name="assign-the-azure-monitor-initiative"></a>Azure Monitor 이니셔티브 할당
**VM용 Azure Monitor 정책 검사** 페이지에서 정책 할당을 만들려면 다음 단계를 수행 합니다. 이러한 단계를 완료하는 방법을 알아보려면  [Azure Portal에서 정책 할당 만들기](../../governance/policy/assign-policy-portal.md)를 참조하세요.

정책 또는 이니셔티브를 할당할 때 할당에서 선택한 범위는 여기에 나열 된 범위 또는 하위 집합 일 수 있습니다. 예를 들어 관리 그룹 (범위 범위)이 아닌 구독 (정책 범위)에 대 한 할당을 만들었을 수 있습니다. 이 경우 적용 범위 비율은 정책 또는 이니셔티브 범위에서 Vm을 검사 범위의 Vm으로 나눈 값을 표시 합니다. 다른 경우에는 일부 Vm 또는 리소스 그룹이 나 정책 범위에서 구독을 제외 했을 수 있습니다. 비어 있는 경우 정책 또는 이니셔티브가 없거나 사용 권한이 없음을 나타냅니다. 정보는 **할당 상태**에서 제공 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모니터**를 선택합니다. 

3. **인사이트** 섹션에서 **Virtual Machines(미리 보기)** 를 선택합니다.
 
4. **시작** 탭을 선택 합니다. 페이지에서 **정책 적용 범위 관리**를 선택 합니다.

5. 테이블에서 관리 그룹 또는 구독 중 하나를 선택 합니다. 줄임표 (...)를 선택 하 여 **범위** 를 선택 합니다. 이 예에서 범위는 정책 할당을 적용을 위한 가상 머신 그룹으로 제한 합니다.

6. **Azure Policy 할당** 페이지에는 **VM용 Azure Monitor 사용 설정**으로 미리 채워져 있습니다. 
    **할당 이름** 상자는 이니셔티브 이름으로 자동으로 채워지며 변경할 수 있습니다. 설명 (선택 사항)을 추가할 수도 있습니다. **담당자** 상자는 로그인 한 사용자에 따라 자동으로 채워집니다. 이 값은 선택 사항입니다.

7. (선택 사항) 범위에서 하나 이상의 리소스를 제거하려면 **제외**를 선택합니다.

8. 지원되는 지역에 대한 **Log Analytics 작업 영역** 드롭다운 목록에서 작업 영역을 선택합니다.

   > [!NOTE]
   > 작업 영역이 할당 범위를 벗어나는 경우 *Log Analytics 기여자* 권한을 정책 할당의 Principal ID에 부여합니다. 이 작업을 수행 하지 않으면 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`과 같은 배포 오류가 표시 될 수 있습니다. 액세스 권한을 부여 하려면 [관리 id를 수동으로 구성 하는 방법을](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)검토 하세요.
   > 
   >  할당 되는 이니셔티브에 *Deployifnotexists* 효과가 적용 된 정책이 포함 되어 있기 때문에 **관리 id** 확인란이 선택 됩니다.
    
9. **ID 위치 관리** 드롭다운 목록에서 해당 지역을 선택합니다.

10. **할당**을 선택합니다.

할당을 만든 후에는 **VM용 Azure Monitor 정책 검사** 페이지에서 **할당 범위**, **할당 상태**, **호환 vm**및 **준수 상태** 를 업데이트 하 여 변경 내용을 반영 합니다. 

다음 행렬은 이니셔티브의 가능한 각 준수 상태를 매핑합니다.  

| 준수 상태 | 설명 | 
|------------------|-------------|
| **호환이** | 범위에 있는 모든 Vm에는 Log Analytics 및 종속성 에이전트가 배포 되어 있습니다.|
| **비준수** | 범위에 있는 모든 Vm에는 Log Analytics 및 종속성 에이전트가 배포 되지 않으므로 수정이 필요할 수 있습니다.|
| **시작 되지 않음** | 새 할당이 추가 되었습니다. |
| **잠기지** | 관리 그룹에 대 한 충분 한 권한이 없습니다. <sup>1</sup> | 
| **비어 있음** | 정책이 할당 되지 않았습니다. | 

<sup>1</sup> 관리 그룹에 대 한 액세스 권한이 없으면 소유자에 게 액세스를 제공 하도록 요청 합니다. 또는 자식 관리 그룹 또는 구독을 통해 규정 준수를 확인 하 고 할당을 관리 합니다. 

다음 표에서는 이니셔티브의 가능한 각 할당 상태를 매핑합니다.

| 할당 상태 | 설명 | 
|------------------|-------------|
| **성공** | 범위에 있는 모든 Vm에는 Log Analytics 및 종속성 에이전트가 배포 되어 있습니다.|
| **경고** | 구독이 관리 그룹에 없습니다.|
| **시작 되지 않음** | 새 할당이 추가 되었습니다. |
| **잠기지** | 관리 그룹에 대 한 충분 한 권한이 없습니다. <sup>1</sup> | 
| **비어 있음** | Vm이 없거나 정책이 할당 되지 않았습니다. | 
| **작업** | 정책을 할당 하거나 할당을 편집 합니다. | 

<sup>1</sup> 관리 그룹에 대 한 액세스 권한이 없으면 소유자에 게 액세스를 제공 하도록 요청 합니다. 또는 자식 관리 그룹 또는 구독을 통해 규정 준수를 확인 하 고 할당을 관리 합니다.

## <a name="review-and-remediate-the-compliance-results"></a>규정 준수 결과 검토 및 수정

다음 예제는 Azure VM에 대 한 것 이지만 가상 머신 확장 집합에도 적용 됩니다. 호환성 결과를 검토 하는 방법을 알아보려면 [비호환 결과 식별](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)을 참조 하세요. **정책 적용 VM용 Azure Monitor** 페이지에서 관리 그룹 또는 테이블에서 구독을 선택 합니다. 줄임표 (...)를 선택 하 여 **호환성 보기** 를 선택 합니다.   

![Azure VM에 대한 정책 준수](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

이니셔티브에 포함 된 정책의 결과에 따라 다음과 같은 시나리오에서 Vm은 비규격으로 보고 됩니다.

* Log Analytics 에이전트 또는 종속성 에이전트가 배포 되지 않았습니다.  
    이 시나리오는 일반적으로 기존 VM이 포함된 범위에 해당합니다. 이를 완화 하려면 비규격 정책에 대해 [재구성 작업을 만들어](../../governance/policy/how-to/remediate-resources.md) 필요한 에이전트를 배포 합니다.  
    - \[미리 보기\]: Linux Vm에 대 한 종속성 에이전트 배포
    - \[미리 보기\]: Windows Vm에 대 한 종속성 에이전트 배포
    - \[미리 보기\]: Linux Vm에 대 한 Log Analytics 에이전트 배포
    - \[미리 보기\]: Windows Vm에 대 한 Log Analytics 에이전트 배포

* VM 이미지 (OS)가 정책 정의에서 식별 되지 않습니다.  
    배포 정책의 조건에는 잘 알려진 Azure VM 이미지에서 배포된 VM만 포함됩니다. VM OS가 지원되는지 여부는 설명서를 확인하세요. 지원되지 않는 경우 배포 정책을 복제하고 업데이트 또는 수정하여 이미지가 준수되도록 해야 합니다.  
    - \[미리 보기\]: 종속성 에이전트 배포 감사 – VM 이미지 (OS) 나열 되지 않음
    - \[미리 보기\]: 감사 Log Analytics 에이전트 배포 – VM 이미지 (OS) 나열 되지 않음

* VM이 지정된 Log Analytics 작업 영역에 로그인되지 않습니다.  
    이니셔티브 범위의 일부 VM이 정책 할당에 지정된 작업 영역 이외의 Log Analytics 작업 영역에 로그인된 것일 수 있습니다. 이 정책은 비규격 작업 영역에 보고 하는 Vm을 식별 하는 도구입니다.  
    - \[미리 보기\]: VM에 대 한 감사 Log Analytics 작업 영역-보고서 불일치

## <a name="edit-an-initiative-assignment"></a>이니셔티브 할당 편집

관리 그룹 또는 구독에 이니셔티브를 할당 한 후 언제 든 지이를 편집 하 여 다음 속성을 수정할 수 있습니다.

- 할당 이름
- 설명
- 담당자
- Log Analytics 작업 영역
- 예외

## <a name="next-steps"></a>다음 단계

이제 가상 머신에 대 한 모니터링을 사용 하도록 설정 했으므로이 정보는 VM용 Azure Monitor 분석에 사용할 수 있습니다. 

- 상태 기능을 사용 하는 방법에 대 한 자세한 내용은 [VM용 Azure Monitor 상태 보기](vminsights-health.md)를 참조 하세요. 
- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요. 
- VM의 성능에 대 한 병목 및 전반적인 사용률을 식별 하려면 [AZURE vm 성능 보기](vminsights-performance.md)를 참조 하세요. 
- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.
