---
title: Azure Policy를 사용하여 VM용 Azure Monitor를 사용하도록 설정
description: 이 문서에서는 Azure Policy를 사용하여 여러 Azure 가상 시스템 또는 가상 시스템 규모 집합에 대해 VM에 대한 Azure 모니터를 사용하도록 설정하는 방법에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 73c18d45136eea90ad29dc1bd40c4539dddc0ee6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767264"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Azure Policy를 사용하여 VM용 Azure Monitor를 사용하도록 설정

이 문서에서는 Azure 정책을 사용하여 Azure 가상 시스템 또는 가상 시스템 규모 집합에 대한 VM용 Azure 모니터를 사용하도록 설정하는 방법을 설명합니다. 이 프로세스가 끝나면 Log 분석 및 종속성 에이전트를 사용하도록 성공적으로 구성하고 호환되지 않는 가상 시스템을 식별합니다.

모든 Azure 가상 컴퓨터 또는 가상 시스템 규모 집합에 대해 VM에 대한 Azure 모니터를 검색, 관리 및 사용하도록 설정하려면 Azure Policy 또는 Azure PowerShell을 사용할 수 있습니다. Azure Policy는 정책 정의를 관리하여 구독을 효과적으로 관리하여 일관된 규정 준수및 새로 프로비전된 VM의 자동 사용이 가능하도록 보장하기 때문에 권장하는 방법입니다. 이러한 정책 정의:

* Log Analytics 에이전트 및 Dependency Agent를 배포합니다.
* 규정 준수 결과를 보고합니다.
* 비준수 VM에 대한 수정.

Azure PowerShell 또는 Azure 리소스 관리자 템플릿을 사용하여 이러한 작업을 수행하려는 경우 [Azure PowerShell 또는 Azure 리소스 관리자 템플릿을 사용하여 VM에 대한 Azure 모니터 활성화를 참조하세요.](vminsights-enable-at-scale-powershell.md)

## <a name="prerequisites"></a>사전 요구 사항
정책을 사용하여 Azure VM 및 가상 시스템 확장 집합을 VM용 Azure 모니터링에 온보싱하기 전에 모니터링 데이터를 저장하는 데 사용할 작업 영역에서 VMInsights 솔루션을 사용하도록 설정해야 합니다. 이 작업은 **다른 온보딩 옵션** 탭의 Azure 모니터의 **시작 받기** 페이지에서 완료할 수 있습니다.  **구성할 작업 영역을**선택하라는 메시지가 표시되는 작업 영역 구성을 선택합니다.

![작업 영역 구성](media/vminsights-enable-at-scale-policy/configure-workspace.png)

**정책 사용 활성화를** 선택한 다음 작업 영역 구성 도구 모음 단추를 선택하여 **작업 영역을** 구성할 수도 있습니다.  이렇게 하면 선택한 작업 영역에 VMInsights 솔루션이 설치되어 작업 영역이 정책을 사용하여 활성화한 VM 및 가상 시스템 스케일 집합에서 보낸 모니터링 데이터를 저장할 수 있습니다. 

![정책 사용 활성화](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>보험 적용 범위 기능 개요 관리

VM 정책 범위에 대한 Azure 모니터는 앞에서 언급한 정책 정의를 포함하는 **VM용 Azure 모니터 활성화** 이니셔티브를 검색, 관리 및 대규모로 활성화하는 것을 간소화합니다. 이 기능에 액세스하려면 VM용 Azure 모니터의 **시작** 탭에서 **다른 온보딩 옵션을** 선택합니다. 정책 **적용 범위 관리를** 선택하여 **VM 정책 적용 범위에 대한 Azure 모니터를 엽니다.**

![VM의 Azure 모니터 시작 탭](./media/vminsights-enable-at-scale-policy/get-started-page.png)

여기에서 관리 그룹 및 구독 전반에서 이니셔티브에 대한 적용 범위를 확인하고 관리할 수 있습니다. 각 관리 그룹 및 구독에 존재하는 VM 수와 해당 규정 준수 상태를 이해할 수 있습니다.

![VM에 대한 Azure 모니터 관리 정책 페이지](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

이 정보는 한 중앙 위치에서 VM용 Azure Monitor에 대한 거버넌스 시나리오를 계획하고 실행하는 데 유용합니다. Azure Policy는 정책 또는 이니셔티브가 범위에 할당될 때 규정 준수 보기를 제공하지만 이 새 페이지를 사용하면 정책 또는 이니셔티브가 할당되지 않은 위치를 발견하고 해당 정책을 할당할 수 있습니다. Azure Policy로 리디렉션을 직접 할당, 보기 및 편집과 같은 모든 작업을 수행합니다. **VM에 대한 Azure 모니터 정책 적용 범위** 페이지는 **VM에**대한 Azure 모니터 활성화 이니셔티브에 대해서만 확장되고 통합된 환경입니다.

이 페이지에서 VM용 Azure 모니터에 대한 로그 분석 작업 영역을 구성할 수도 있습니다.

- 서비스 맵 솔루션을 설치합니다.
- 성능 차트, 통합 문서 및 사용자 지정 로그 쿼리 및 경고에서 사용하는 운영 체제 성능 카운터를 활성화합니다.

![VM용 Azure 모니터가 작업 영역을 구성합니다.](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

이 옵션은 정책 작업과 관련이 없습니다. VM에 대한 Azure 모니터를 사용하도록 설정하는 데 필요한 [필수 구성 조건을](vminsights-enable-overview.md) 충족하는 쉬운 방법을 제공할 수 있습니다.  

### <a name="what-information-is-available-on-this-page"></a>이 페이지에서 사용할 수 있는 정보는 무엇입니까?

다음 표에서는 정책 적용 범위 페이지에 표시되는 정보와 이를 해석하는 방법에 대한 분석이 제공됩니다.

| 함수 | Description | 
|----------|-------------| 
| **범위** | 관리 그룹 계층 구조를 드릴다운할 수 있는 기능을 사용하여 액세스 권한을 갖거나 상속한 관리 그룹 및 구독입니다.|
| **Role** | 판독기, 소유자 또는 기여자일 수 있는 범위에 대한 역할입니다. 경우에 따라 구독에 액세스할 수 있지만 구독이 속한 관리 그룹에는 액세스할 수 없음을 나타내기 위해 비어 있는 것처럼 보일 수 있습니다. 다른 열의 정보는 역할에 따라 다릅니다. 역할은 볼 수 있는 데이터와 정책 또는 이니셔티브(소유자)를 할당하거나 편집하거나 규정 준수를 확인하는 측면에서 수행할 수 있는 작업을 결정하는 데 핵심적인 역할을 합니다. |
| **총 VM** | 해당 범위의 VM 수입니다. 관리 그룹의 경우 구독 또는 하위 관리 그룹 아래에 중첩된 VM의 합계입니다. |
| **과제 적용 범위** | 정책 또는 이니셔티브가 적용되는 VM의 백분율입니다. |
| **과제 상태** | 정책 또는 이니셔티브 할당 상태에 대한 정보입니다. |
| **규정을 준수하는 VM** | 정책 또는 이니셔티브에 따라 준수하는 VM 수입니다. **VM에 대한 Azure 모니터 사용 이니셔티브의**경우 로그 분석 에이전트와 종속성 에이전트가 모두 있는 VM 의 수입니다. 경우에 따라 할당, VM 또는 충분한 사용 권한이 없기 때문에 비어 있는 것처럼 보일 수 있습니다. 정보는 규정 **준수 상태**하에 제공됩니다. |
| **규정 준수** | 전체 규정 준수 수는 규정을 준수하는 고유 리소스의 합계를 모든 고유 리소스의 합계로 나눈 값입니다. |
| **규정 준수 상태** | 정책 또는 이니셔티브 할당에 대한 규정 준수 상태에 대한 정보입니다.|

정책 또는 이니셔티브를 할당할 때 할당에서 선택한 범위는 나열된 범위 또는 해당 이니셔티브의 하위 집합일 수 있습니다. 예를 들어 관리 그룹(범위 범위)이 아닌 구독(정책 범위)에 대한 할당을 만들었을 수 있습니다. 이 경우 **할당 범위의** 값은 정책 또는 이니셔티브 범위의 VM을 커버리지 범위의 VM으로 나눈 값을 나타냅니다. 다른 경우에는 정책 범위에서 일부 VM, 리소스 그룹 또는 구독을 제외했을 수 있습니다. 값이 비어 있으면 정책 또는 이니셔티브가 존재하지 않거나 권한이 없는 것을 나타냅니다. 정보는 할당 **상태에**따라 제공됩니다.

## <a name="enable-by-using-azure-policy"></a>Azure Policy를 사용하여 설정

테넌트에서 Azure Policy를 사용하여 VM용 Azure Monitor를 사용하도록 설정하려면

- 관리 그룹, 구독 또는 리소스 그룹과 같은 범위에 이니셔티브를 할당합니다.
- 규정 준수 결과를 검토하고 수정합니다.

계속하기 전에 [Azure Policy 개요](../../governance/policy/overview.md#assignments)를 참조하고 [관리 그룹 개요](../../governance/management-groups/overview.md)를 검토하여 Azure Policy 할당에 대해 자세히 알아보세요.

### <a name="policies-for-azure-vms"></a>Azure VM에 대한 정책

Azure VM에 대한 정책 정의는 다음 표에 나열됩니다.

|속성 |Description |Type |
|-----|------------|-----|
|VM용 Azure Monitor 사용 |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)의 가상 시스템에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. |이니셔티브 |
|감사 종속성 에이전트 배포 – VM 이미지(OS) 미등록 |VM 이미지(OS)가 목록에 정의되지 않고 에이전트가 설치되지 않은 경우 VM을 비규격으로 보고합니다. |정책 |
|감사 로그 분석 에이전트 배포 – VM 이미지(OS) 미등록 |VM 이미지(OS)가 목록에 정의되지 않고 에이전트가 설치되지 않은 경우 VM을 비규격으로 보고합니다. |정책 |
|Linux VM용 Dependency Agent 배포 |VM 이미지(OS)가 목록에 정의되고 에이전트가 설치되지 않은 경우 Linux VM용 종속성 에이전트를 배포합니다. |정책 |
|Windows VM용 Dependency Agent 배포 |VM 이미지(OS)가 목록에 정의되고 에이전트가 설치되지 않은 경우 Windows VM용 종속성 에이전트를 배포합니다. |정책 |
|Linux VM용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되지 않은 경우 Linux VM용 로그 분석 에이전트를 배포합니다. |정책 |
|Windows VM용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되지 않은 경우 Windows VM용 로그 분석 에이전트를 배포합니다. |정책 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 가상 시스템 규모 집합에 대한 정책

Azure 가상 시스템 규모 집합에 대한 정책 정의는 다음 표에 나열됩니다.

|속성 |Description |Type |
|-----|------------|-----|
|가상 시스템 규모 집합에 대한 Azure 모니터 사용 |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 가상 시스템 규모 집합에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. 참고: 확장 집합 업그레이드 정책이 수동으로 설정된 경우 업그레이드를 호출하여 집합의 모든 VM에 확장을 적용합니다. CLI에서 이 것입니다. `az vmss update-instances` |이니셔티브 |
|가상 시스템 규모 집합에서 종속 에이전트 배포 감사 – VM 이미지(OS) 미등록 |VM 이미지(OS)가 목록에 정의되지 않고 에이전트가 설치되지 않은 경우 가상 시스템 크기 집합을 비규격으로 보고합니다. |정책 |
|가상 머신 스케일 세트에서 로그 분석 에이전트 배포 감사 – VM 이미지(OS) 미등록 |VM 이미지(OS)가 목록에 정의되지 않고 에이전트가 설치되지 않은 경우 가상 시스템 크기 집합을 비규격으로 보고합니다. |정책 |
|Linux 가상 머신 확장 집합용 Dependency Agent 배포 |VM 이미지(OS)가 목록에 정의되고 에이전트가 설치되지 않은 경우 Linux 가상 시스템 스케일에 대한 종속성 에이전트 를 배포합니다. |정책 |
|Windows 가상 머신 확장 집합용 Dependency Agent 배포 |VM 이미지(OS)가 목록에 정의되고 에이전트가 설치되지 않은 경우 Windows 가상 시스템 크기 에 대한 종속성 에이전트를 배포합니다. |정책 |
|Linux 가상 머신 확장 집합용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되고 에이전트가 설치되지 않은 경우 Linux 가상 시스템 크기 집합에 대한 Log Analytics 에이전트를 배포합니다. |정책 |
|Windows 가상 머신 확장 집합용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되고 에이전트가 설치되지 않은 경우 Windows 가상 시스템 크기 집합에 대한 Log Analytics 에이전트를 배포합니다. |정책 |

다음은 독립 실행형 정책(이니셔티브에 포함되지 않음)에 대한 설명입니다.

|속성 |Description |Type |
|-----|------------|-----|
|VM에 대한 로그 분석 작업 영역 감사 – 보고서 불일치 |정책 또는 이니셔티브 할당에 지정된 Log Analytics 작업 영역에 로그하지 않는 경우 VM을 비규격으로 보고합니다. |정책 |

### <a name="assign-the-azure-monitor-initiative"></a>Azure Monitor 이니셔티브 할당

**VM 정책 적용 범위에 대한 Azure 모니터에서** 정책 할당을 만들려면 다음 단계를 따르십시오. 이러한 단계를 완료하는 방법을 알아보려면  [Azure Portal에서 정책 할당 만들기](../../governance/policy/assign-policy-portal.md)를 참조하세요.

정책 또는 이니셔티브를 할당할 때 할당에서 선택한 범위는 여기에 나열된 범위 또는 정책 의 하위 집합일 수 있습니다. 예를 들어 관리 그룹(범위 범위)이 아닌 구독(정책 범위)에 대한 할당을 만들었을 수 있습니다. 이 경우 적용 범위 백분율은 보험 범위 범위의 VM으로 나눈 정책 또는 이니셔티브 범위의 VM을 나타냅니다. 다른 경우에는 일부 VM 또는 리소스 그룹 또는 구독을 정책 범위에서 제외했을 수 있습니다. 비어 있는 경우 정책 또는 이니셔티브가 존재하지 않거나 사용 권한이 없는 것을 나타냅니다. 정보는 할당 **상태에**따라 제공됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure 포털에서 **모니터**를 선택합니다. 

3. **인사이트** 섹션에서 **가상 컴퓨터를** 선택합니다.
 
4. **시작** 받기 탭을 선택합니다. 페이지에서 **보험 적용 범위 관리를 선택합니다.**

5. 테이블에서 관리 그룹 또는 구독을 선택합니다. 타원(...)을 선택하여 **범위를** 선택합니다. 이 예제에서 범위는 정책 할당을 적용을 위해 가상 컴퓨터 그룹화로 제한합니다.

6. Azure **정책 할당** 페이지에서 **VM에 대한 Azure 모니터 사용**이니셔티브로 미리 채워집니다. 
    **과제 이름** 상자는 이니셔티브 이름으로 자동으로 채워지지만 변경할 수 있습니다. 선택적 설명을 추가할 수도 있습니다. 상자에 **의해 할당된** 사람은 로그인한 사람에 따라 자동으로 채워집니다. 이 값은 선택 사항입니다.

7. (선택 사항) 범위에서 하나 이상의 리소스를 제거하려면 **제외**를 선택합니다.

8. 지원되는 지역에 대한 **Log Analytics 작업 영역** 드롭다운 목록에서 작업 영역을 선택합니다.

   > [!NOTE]
   > 작업 영역이 할당 범위를 벗어나는 경우 *Log Analytics 기여자* 권한을 정책 할당의 Principal ID에 부여합니다. 이렇게 하지 않으면 액세스 권한을 부여하고 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` [관리되는 ID를 수동으로 구성하는 방법을 검토하는](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)것과 같은 배포 실패가 발생할 수 있습니다.
   > 
   >  할당되는 이니셔티브에 *deployIfNotExists* 효과가 있는 정책이 포함되어 있기 때문에 **관리되는 ID** 확인란이 선택됩니다.
    
9. **ID 위치 관리** 드롭다운 목록에서 해당 지역을 선택합니다.

10. **을 선택합니다.**

할당을 만든 후 **VM 정책 검사에 대한 Azure 모니터는** **할당 범위, 할당** **상태,** **준수 VM**및 **규정 준수 상태를** 업데이트하여 변경 내용을 반영합니다. 

다음 행렬은 이니셔티브에 대한 가능한 각 규정 준수 상태를 매핑합니다.  

| 규정 준수 상태 | 설명 | 
|------------------|-------------|
| **호환** | 범위의 모든 VM에는 로그 분석 및 종속성 에이전트가 배포됩니다.|
| **호환되지 않음** | 범위의 모든 VM에 Log Analytics 및 종속성 에이전트가 배포된 것은 아니며 업데이트가 필요할 수 있습니다.|
| **시작되지 않음** | 새 할당이 추가되었습니다. |
| **잠금** | 관리 그룹에 대한 충분한 권한이 없습니다. <sup>1개</sup> | 
| **비어 있음** | 정책이 할당되지 않았습니다. | 

<sup>1</sup> 관리 그룹에 액세스할 수 없는 경우 소유자에게 액세스 권한을 제공하도록 요청합니다. 또는 하위 관리 그룹 또는 구독을 통해 규정 준수를 보고 할당을 관리합니다. 

다음 표는 이니셔티브에 대해 가능한 각 할당 상태를 매핑합니다.

| 과제 상태 | 설명 | 
|------------------|-------------|
| **Success** | 범위의 모든 VM에는 로그 분석 및 종속성 에이전트가 배포됩니다.|
| **Warning** | 구독이 관리 그룹 아래에 있지 않습니다.|
| **시작되지 않음** | 새 할당이 추가되었습니다. |
| **잠금** | 관리 그룹에 대한 충분한 권한이 없습니다. <sup>1개</sup> | 
| **비어 있음** | VM이 없거나 정책이 할당되지 않습니다. | 
| **작업** | 정책을 할당하거나 할당을 편집합니다. | 

<sup>1</sup> 관리 그룹에 액세스할 수 없는 경우 소유자에게 액세스 권한을 제공하도록 요청합니다. 또는 하위 관리 그룹 또는 구독을 통해 규정 준수를 보고 할당을 관리합니다.

## <a name="review-and-remediate-the-compliance-results"></a>규정 준수 결과 검토 및 수정

다음 예제는 Azure VM에 대 한 이지만 가상 컴퓨터 규모 집합에도 적용 됩니다. 규정 준수 결과를 검토하는 방법을 알아보려면 [비준수 결과 식별을](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)참조하십시오. **VM 정책 적용 범위에 대한 Azure 모니터** 페이지에서 관리 그룹 또는 테이블에서 구독을 선택합니다. 타원(...)을 선택하여 **준수 보기를** 선택합니다.   

![Azure VM에 대한 정책 준수](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

이니셔티브에 포함된 정책의 결과에 따라 VM은 다음 시나리오에서 비준수로 보고됩니다.

* 로그 분석 에이전트 또는 종속성 에이전트가 배포되지 않습니다.  
    이 시나리오는 일반적으로 기존 VM이 포함된 범위에 해당합니다. 이를 완화하려면 비규격 정책에 [업데이트 관리 작업을 만들어](../../governance/policy/how-to/remediate-resources.md) 필요한 에이전트를 배포합니다.  
    - Linux VM용 Dependency Agent 배포
    - Windows VM용 Dependency Agent 배포
    - Linux VM용 Log Analytics 에이전트 배포
    - Windows VM용 Log Analytics 에이전트 배포

* VM 이미지(OS)는 정책 정의에서 식별되지 않습니다.  
    배포 정책의 조건에는 잘 알려진 Azure VM 이미지에서 배포된 VM만 포함됩니다. VM OS가 지원되는지 여부는 설명서를 확인하세요. 지원되지 않는 경우 배포 정책을 복제하고 업데이트 또는 수정하여 이미지가 준수되도록 해야 합니다.  
    - 감사 종속성 에이전트 배포 – VM 이미지(OS) 미등록
    - 감사 로그 분석 에이전트 배포 – VM 이미지(OS) 미등록

* VM이 지정된 Log Analytics 작업 영역에 로그인되지 않습니다.  
    이니셔티브 범위의 일부 VM이 정책 할당에 지정된 작업 영역 이외의 Log Analytics 작업 영역에 로그인된 것일 수 있습니다. 이 정책은 비규격 작업 영역에 보고하는 VM을 식별하는 도구입니다.  
    - VM에 대한 로그 분석 작업 영역 감사 – 보고서 불일치

## <a name="edit-an-initiative-assignment"></a>이니셔티브 과제 편집

관리 그룹 또는 구독에 이니셔티브를 할당한 후 언제든지 이니셔티브를 편집하여 다음 속성을 수정할 수 있습니다.

- 과제 이름
- 설명
- 에 의해 할당
- Log Analytics 작업 영역
- 예외

## <a name="next-steps"></a>다음 단계

이제 가상 시스템에 대해 모니터링이 활성화되었으므로 이 정보는 VM용 Azure 모니터를 통해 분석할 수 있습니다. 

- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요. 

- VM 성능으로 병목 현상 및 전반적인 사용률을 식별하려면 [Azure VM 성능 보기를](vminsights-performance.md)참조하십시오. 
