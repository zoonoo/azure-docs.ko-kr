---
title: Azure 정책 개요
description: Azure Policy는 Azure 환경에서 정책 정의를 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2dd31ab29479fade21d27b8e2c23952f905f530a
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979150"
---
# <a name="overview-of-the-azure-policy-service"></a>Azure Policy 서비스 개요

거버넌스는 조직이 효과적이고 효율적으로 IT를 사용하여 목표를 달성할 수 있는지 확인합니다. 비즈니스 목표와 IT 프로젝트를 구분하여 이 요구를 충족합니다.

회사에서 해결할 수 없는 IT 문제가 많이 있나요?
좋은 IT 거버넌스에는 문제를 관리하고 방지할 수 있는 이니셔티브 계획과 전략적 수준의 우선 순위 설정이 필요합니다. 이 전략적 요구를 충족하는 데 Azure Policy가 사용됩니다.

Azure Policy는 정책을 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다. 이러한 정책은 리소스에 대해 다양한 규칙과 효과를 적용하여 리소스를 회사 표준 및 서비스 수준 계약을 준수하는 상태로 유지합니다. Azure Policy는 할당된 정책의 비준수에 대해 리소스를 평가하여 이 요구를 충족합니다. 예를 들어 환경에서 특정 SKU 크기의 가상 머신만을 허용하는 정책이 있을 수 있습니다. 이 정책을 구현하면 새 리소스와 기존 리소스의 규정 준수가 평가됩니다. 올바른 정책 유형을 사용하여 기존 리소스가 규정을 준수하도록 만들 수 있습니다. 이 문서의 뒷부분에서 Azure Policy를 사용하여 정책을 만들고 구현하는 방법을 자세히 살펴보겠습니다.

> [!IMPORTANT]
> 이제 가격 책정 계층에 관계없이 모든 할당에 Azure Policy의 규정 준수 평가가 제공됩니다. 할당에 규정 준수 데이터가 표시되지 않는 경우 구독이 Microsoft.PolicyInsights 리소스 공급자에 등록되었는지 확인하세요.

## <a name="how-is-it-different-from-rbac"></a>RBAC(역할 기반 액세스 제어)와 어떻게 다르나요?

Azure Policy 및 RBAC(역할 기반 액세스 제어)는 몇 가지 주요 차이점이 있습니다. RBAC는 다른 범위에 있는 사용자 작업에 중점을 둡니다. 리소스 그룹에 대한 기여자 역할에 추가되어 해당 리소스 그룹을 변경할 수 있습니다. Azure Policy는 배포 시 기존 리소스의 리소스 속성에 중점을 둡니다. Azure Policy는 리소스의 유형 또는 위치와 같은 속성을 제어합니다. RBAC와 달리 Azure Policy는 기본적으로 허용 및 명시적 거부 시스템입니다.

### <a name="rbac-permissions-in-azure-policy"></a>Azure Policy의 RBAC 사용 권한

Azure Policy에는 여러 권한이 있는데, 다음 두 리소스 공급자에서는 작업으로 알려져 있습니다.

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

여러 기본 제공 역할은 Azure Policy 리소스에 대한 권한을 부여합니다. **리소스 정책 기여자(미리 보기)** 역할은 대부분의 Azure Policy 작업을 포함합니다. **소유자**는 전체 권한을 보유합니다. **기여자**와 **읽기 권한자**는 둘 다 모든 읽기 Azure Policy 작업을 사용할 수 있지만, **기여자**는 수정을 트리거할 수도 있습니다.

기본 제공 역할에 필수 권한이 없는 경우 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 만듭니다.

## <a name="policy-definition"></a>정책 정의 

Azure Policy에서 정책을 만들고 구현하는 과정은 정책 정의 만들기로 시작합니다. 모든 정책 정의에는 정책이 적용되는 조건이 있습니다. 또한 조건이 충족되면 발생하는 정의된 효과가 있습니다.

Azure Policy에는 기본적으로 사용 가능한 여러 가지 기본 제공 정책이 있습니다. 예: 

- **SQL Server 12.0 필요**: 모든 SQL Server가 버전 12.0을 사용하는지 확인합니다. 이 기준에 부합하지 않는 모든 서버를 거부하게 됩니다.
- **허용되는 스토리지 계정 SKU** 배포 중인 스토리지 계정이 SKU 크기 세트 내에 있는지 여부를 확인합니다. 정의된 SKU 크기 세트를 준수하지 않는 모든 스토리지 계정을 거부하게 됩니다.
- **허용되는 리소스 유형** 배포할 수 있는 리소스 유형을 정의합니다. 이 정의된 목록에 속하지 않는 모든 리소스를 거부하게 됩니다.
- **허용되는 위치**: 새 리소스를 사용할 수 있는 위치를 제한합니다. 해당 효과는 지역 규정 준수 요구 사항을 적용하는 데 사용됩니다.
- **허용되는 가상 머신 SKU**: 배포할 수 있는 가상 머신 SKU 세트를 지정합니다.
- **태그 및 기본값 적용**: 배포 요청에 의해 지정되지 않은 경우 필수 태그 및 해당 기본값을 적용합니다.
- **태그 및 해당 값 강제 적용**: 필수 태그 및 해당 값을 리소스에 강제 적용합니다.
- **허용되지 않는 리소스 유형**: 리소스 종류 목록이 배포되지 않도록 합니다.

이러한 정책 정의(기본 제공 및 사용자 지정 정의)를 구현하려면 할당해야 합니다. Azure Portal, PowerShell 또는 Azure CLI를 통해 이러한 정책을 할당할 수 있습니다.

정책 평가는 정책 할당 또는 정책 업데이트와 같은 여러 가지 작업에서 수행됩니다. 전체 목록은 [정책 평가 트리거](./how-to/get-compliance-data.md#evaluation-triggers)를 참조하세요.

정책 정의의 구조에 대한 자세한 내용은 [정책 정의 구조](./concepts/definition-structure.md)를 확인하세요.

## <a name="policy-assignment"></a>정책 할당

정책 할당은 일정 범위 내에서 이루어지도록 할당된 정책 정의입니다. 이 범위는 [관리 그룹](../management-groups/overview.md)에서 리소스 그룹에 이를 수 있습니다. *범위*라는 용어는 정책 정의가 할당된 모든 리소스 그룹, 구독 또는 관리 그룹을 나타냅니다. 정책 할당은 모든 자식 리소스에 의해 상속됩니다. 이 디자인은 리소스 그룹에 적용된 정책이 해당 리소스 그룹의 리소스에도 적용됨을 의미합니다. 그러나 정책 할당에서 하위 범위를 제외할 수 있습니다.

예를 들어 구독 범위에서 네트워킹 리소스를 만들지 못하도록 정책을 할당할 수 있습니다. 해당 구독 내에서 네트워킹 인프라를 대상으로 하는 리소스 그룹을 제외할 수 있습니다. 그런 다음, 네트워킹 리소스 생성을 신뢰하는 사용자에게 이 네트워킹 리소스 그룹에 대한 액세스 권한을 부여합니다.

다른 예에서는 관리 그룹 수준에서 리소스 종류 허용 목록 정책을 할당할 수 있습니다. 그런 다음, 자식 관리 그룹 또는 구독에 직접 관대한 정책(더 많은 리소스 종류 허용)을 할당합니다. 하지만 이 예제는 정책이 명시적 거부 시스템이기 때문에 작동하지 않습니다. 대신 관리 그룹 수준 정책 할당에서 자식 관리 그룹 또는 구독을 제외해야 합니다. 그런 다음, 자식 관리 그룹 또는 구독 수준에 보다 관대한 정책을 할당합니다. 어떤 정책으로 인해 리소스가 거부되는 경우 리소스를 허용하는 유일한 방법은 거부하는 정책을 수정하는 것입니다.

Portal을 통해 정책 정의 및 할당을 설정하는 방법에 대한 자세한 내용은 [Azure 환경에서 규정 비준수 리소스를 식별하는 정책 할당 만들기](assign-policy-portal.md)를 참조하세요. [PowerShell](assign-policy-powershell.md) 및 [Azure CLI](assign-policy-azurecli.md)용 단계도 제공됩니다.

## <a name="policy-parameters"></a>정책 매개 변수

정책 매개 변수는 만들어야 하는 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 더 일반적인 정책 정의를 만들 때는 매개 변수를 정의할 수 있습니다. 그런 다음 다른 시나리오에 대해 해당 정책 정의를 다시 사용할 수 있습니다. 정책 정의를 할당할 때 다른 값을 전달하면 됩니다. 예를 들어 구독에 대해 하나의 위치 집합을 지정합니다.

매개 변수는 정책 정의를 만들 때 정의됩니다. 매개 변수를 정의할 때는 이름과, 필요에 따라 값이 지정됩니다. 예를 들어 *location*이라는 정책에 대한 매개 변수를 정의할 수 있습니다. 그런 다음 정책을 할당할 때 *EastUS* 또는 *WestUS*와 같은 다른 값을 지정할 수 있습니다.

정책 매개 변수에 대한 자세한 내용은 [정의 구조 - 매개 변수](./concepts/definition-structure.md#parameters)를 참조하세요.

## <a name="initiative-definition"></a>이니셔티브 정의

이니셔티브 정의는 가장 중요한 단일 목표를 달성하기 위해 맞춤화된 정책 정의의 컬렉션입니다. 이니셔티브 정의를 사용하면 정책 정의 관리 및 할당을 더 쉽게 수행할 수 있습니다. 정책 집합을 단일 항목으로 그룹화하여 단순화합니다. 예를 들어 이름이 **Azure Security Center에서 모니터링 사용**이면서 목표가 Azure Security Center의 모든 사용 가능한 보안 권장 사항을 모니터링하는 것인 이니셔티브를 만들 수 있습니다.

이 이니셔티브에 따라 다음과 같은 정책 정의가 있게 됩니다.

- **Security Center의 암호화되지 않은 SQL 데이터베이스 모니터링** - 암호화되지 않은 SQL 데이터베이스 및 서버 모니터링
- **Security Center의 OS 취약성 모니터링** – 구성된 기준을 충족하지 않는 서버 모니터링
- **Security Center의 누락된 엔드포인트 보호 모니터링** – 엔드포인트 보호 에이전트가 설치되지 않은 서버 모니터링

## <a name="initiative-assignment"></a>이니셔티브 할당

정책 할당처럼 이니셔티브 할당은 특정 범위에 할당된 이니셔티브 정의입니다.  이니셔티브 할당은 각 범위에 대해 여러 이니셔티브 정의를 만들 필요를 줄입니다. 이 범위 역시 관리 그룹부터 리소스 그룹까지 아우를 수 있습니다.

각 이니셔티브를 서로 다른 범위에 할당할 수 있습니다. 하나의 이니셔티브를 **subscriptionA** 및 **subscriptionB** 둘 다에 할당할 수 있습니다.

## <a name="initiative-parameters"></a>이니셔티브 매개 변수

정책 매개 변수처럼 이니셔티브 매개 변수는 중복을 줄여 이니셔티브 관리를 간소화합니다. 이니셔티브 매개 변수는 이니셔티브 내의 정책 정의에 사용되는 매개 변수입니다.

예를 들어 각각 다른 형식의 매개 변수가 필요한 **policyA** 및 **policyB** 정책 정의를 사용하여 이니셔티브 정의 **initiativeC**가 있는 시나리오를 살펴보겠습니다.

| 정책 | 매개 변수의 이름 |매개 변수 형식   |참고 |
|---|---|---|---|
| policyA | allowedLocations | array  |매개 변수 형식이 배열로 정의되었으므로 이 매개 변수는 값으로 문자열 목록을 예상합니다. |
| policyB | allowedSingleLocation |string |매개 변수 형식이 문자열로 정의되었으므로 이 매개 변수는 값으로 한 단어를 예상합니다. |

이 시나리오에서는 **initiativeC**에 대한 이니셔티브 매개 변수를 정의할 때 세 가지 옵션이 있습니다.

- 이 이니셔티브 내의 정책 정의 매개 변수 사용: 이 예제에서는 *allowedLocations* 및 *allowedSingleLocation*이 **initiativeC**에 대한 이니셔티브 매개 변수가 됩니다.
- 이 이니셔티브 정의 내에서 정책 정의의 매개 변수에 값 제공: 이 예제에서는 위치 목록을 **policyA의 매개 변수 – allowedLocations** 및 **policyB의 매개 변수 – allowedSingleLocation**에 제공합니다. 이 이니셔티브를 할당할 때 값을 제공할 수도 있습니다.
- 이 이니셔티브를 할당할 때 *값* 옵션 목록 제공. 이 이니셔티브를 할당할 때 이니셔티브 내 정책 정의에서 상속된 매개 변수는 이 제공된 목록의 값만 가질 수 있습니다.

이니셔티브 정의에 값 옵션을 만드는 경우 이니셔티브 할당 중에 목록에 속하지 않는 다른 값을 입력할 수 없습니다.

## <a name="maximum-count-of-azure-policy-objects"></a>Azure Policy 개체의 최대 수

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>정책 관리에 대한 권장 사항

다음은 유의해야 할 몇 가지 포인터 및 팁입니다.

- 정책 정의가 환경의 리소스에 미치는 영향을 추적하기 위해 거부 효과 대신 감사 효과로 시작합니다. 이미 애플리케이션 크기를 자동으로 조정하는 스크립트가 있는 경우 거부 효과를 설정하면 기존의 자동화 작업이 방해를 받을 수 있습니다.

- 정의 및 할당을 만들 때는 조직 계층 구조를 고려합니다. 관리 그룹 또는 구독 수준과 같은 상위 수준에서 정의를 만드는 것이 좋습니다. 그런 후에, 다음 하위 수준에서 할당을 만듭니다. 관리 그룹에서 정의를 만드는 경우 할당 범위를 해당 관리 그룹 내의 구독 또는 리소스 그룹으로 축소할 수 있습니다.

- 단일 정책 정의에 대해서도 이니셔티브 정의를 만들고 할당하는 것이 좋습니다.
예를 들어 정책 정의 *policyDefA*가 있고, 이니셔티브 정의 *initiativeDefC* 아래에 만듭니다. 나중에 *policyDefA*와 비슷한 목표로 *policyDefB*에 대한 다른 정책 정의를 만드는 경우 *initiativeDefC* 아래에 추가하고 함께 추적할 수 있습니다.

- 이니셔티브 할당을 만들고 나면, 이니셔티브에 추가한 정책 정의도 해당 이니셔티브 할당의 일부가 됩니다.

- 이니셔티브 할당을 평가할 때 이니셔티브 내의 모든 정책도 평가됩니다. 정책을 개별적으로 평가해야 하는 경우 이니셔티브에 포함하지 않는 것이 좋습니다.

## <a name="video-overview"></a>비디오 개요

Azure Policy의 다음 개요는 Build 2018에서부터 시작됩니다. 슬라이드 또는 비디오 다운로드의 경우 Channel 9에서 [Azure Policy를 통해 Azure 환경 제어](https://channel9.msdn.com/events/Build/2018/THR2030)를 방문하세요.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>다음 단계

이제 Azure Policy 개요와 몇 가지 핵심 개념을 살펴보았으므로 다음과 같은 단계를 권장합니다.

- [포털을 사용하여 정책 정의를 할당합니다](assign-policy-portal.md).
- [Azure CLI를 사용하여 정책 정의를 할당합니다](assign-policy-azurecli.md).
- [PowerShell을 사용하여 정책 정의를 할당합니다](assign-policy-powershell.md).
- [Azure 관리 그룹으로 리소스 구성](..//management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
- Channel 9에서 [Azure Policy를 통해 Azure 환경 제어](https://channel9.msdn.com/events/Build/2018/THR2030)를 봅니다.