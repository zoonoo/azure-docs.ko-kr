---
title: Azure 관리 그룹으로 리소스 구성
description: 관리 그룹 및 사용 방법에 대해 알아봅니다.
author: rthorn17
manager: rithorn
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rithorn
ms.topic: overview
ms.openlocfilehash: ea34296e170d18a1d5636c50e7cae316b1d97948
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584608"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Azure 관리 그룹으로 리소스 구성

조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다. "관리 그룹"이라는 컨테이너에 구독을 구성하고 거버넌스 조건을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 조건을 자동으로 상속합니다. 관리 그룹은 어떤 형식의 구독을 사용하든 관계 없이 대규모의 엔터프라이즈급 관리를 제공합니다.

예를 들어, VM(가상 머신) 생성에 사용 가능한 지역을 제한하는 정책을 관리 그룹에 적용할 수 있습니다. 이 정책은 해당 지역에만 VM을 만들 수 있도록 허용하는 방식으로 그 지역에 속한 모든 관리 그룹, 구독 및 리소스에 적용됩니다.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>관리 그룹 및 구독의 계층 구조

리소스를 통합 정책 및 액세스 관리를 위한 계층 구조로 구성하는 유연한 관리 그룹 및 구독 구조를 만들 수 있습니다. 다음 다이어그램은 관리 그룹을 사용하여 거버넌스용 계층을 만드는 예를 보여줍니다.

![트리](./media/tree.png)

정책을 적용할 수 있도록 계층을 만듭니다(예: VM 위치를 "인프라 팀 관리 그룹" 그룹의 미국 서부 지역으로 제한). 이 정책은 해당 관리 그룹의 두 EA 구독으로 상속되어 해당 구독의 모든 VM에 적용됩니다. 거버넌스 향상을 위해 리소스 또는 구독 소유자가 이 보안 정책을 변경할 수 없습니다.

관리 그룹을 사용하는 또 다른 시나리오는 여러 구독에 대한 사용자 액세스를 제공하는 것입니다. 해당 관리 그룹에서 여러 구독을 이동하면 관리 그룹에 하나의 RBAC([역할 기반 액세스 제어](../../role-based-access-control/overview.md)) 할당을 만들 수 있습니다. 그러면 모든 구독에 대한 액세스를 상속하게 됩니다.
관리 그룹에 하나만 할당하면 여러 구독에 RBAC를 스크립팅하지 않고 사용자가 필요한 모든 항목에 액세스할 수 있습니다.

### <a name="important-facts-about-management-groups"></a>관리 그룹에 대한 중요 한 사실

- 단일 디렉터리에서 지원할 수 있는 관리 그룹 수는 10,000개입니다.
- 관리 그룹 트리에서 지원할 수 있는 최대 깊이 수준은 6입니다.
  - 이 제한에는 루트 수준 또는 구독 수준이 포함되지 않습니다.
- 각 관리 그룹 및 구독은 하나의 부모만 지원할 수 있습니다.
- 각 관리 그룹에는 여러 자식 요소가 있을 수 있습니다.
- 모든 구독 및 관리 그룹은 각 디렉터리의 단일 계층 내에 위치합니다. 미리 보기 중 예외는 [루트 관리 그룹에 대한 중요한 사실](#important-facts-about-the-root-management-group)을 참조하세요.

## <a name="root-management-group-for-each-directory"></a>각 디렉터리에 대한 루트 관리 그룹

각 디렉터리에는 "루트" 관리 그룹이라고 하는 단일 최상위 관리 그룹이 부여됩니다.
이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 루트 관리 그룹은 전역 정책 및 RBAC 할당을 디렉터리 수준에서 적용하는 것을 허용합니다. 디렉터리 관리자는 처음에 이 루트 그룹의 소유자가 되도록 [자신을 승격해야 합니다](../../role-based-access-control/elevate-access-global-admin.md). 관리자는 그룹의 소유자가 되면 계층 구조를 관리할 다른 디렉터리 사용자 또는 그룹에 모든 RBAC 역할을 할당할 수 있습니다.

### <a name="important-facts-about-the-root-management-group"></a>루트 관리 그룹에 대한 중요한 사실

- 기본적으로 루트 관리 그룹의 이름 및 ID가 제공됩니다. 표시 이름은 언제든지 Azure Portal 내에서 다른 이름을 표시하도록 업데이트할 수 있습니다.
  - 이름은 "테넌트 루트 그룹"입니다.
  - ID는 Azure Active Directory ID입니다.
- 루트 관리 그룹은 다른 관리 그룹과는 다르게 이동하거나 삭제할 수 없습니다.  
- 모든 구독 및 관리 그룹은 디렉터리 내의 한 루트 관리 그룹까지 접을 수 있습니다.
  - 디렉터리의 모든 리소스는 전역 관리를 위해 루트 관리 그룹까지 접을 수 있습니다.
  - 새 구독이 생성될 때 자동으로 루트 관리 그룹에 기본값이 지정되지 않습니다.
- 모든 Azure 고객은 루트 관리 그룹을 볼 수 있지만 일부 고객에게는 해당 루트 관리 그룹을 관리하는 액세스 권한이 없습니다.
  - 구독에 대한 액세스 권한이 있는 모든 사용자는 계층 구조에 있는 해당 구독의 컨텍스트를 볼 수 있습니다.  
  - 사용자에게는 루트 관리 그룹에 대한 기본 액세스 권한이 부여되지 않습니다. 디렉터리 전역 관리자는 액세스 권한을 스스로 높일 수 있는 유일한 사용자입니다.  액세스 권한이 있다면 디렉터리 관리자는 RBAC 역할을 관리할 다른 사용자에게 할당할 수 있습니다.  

> [!IMPORTANT]
> 루트 관리 그룹에서 사용자 액세스 할당 또는 정책 할당은 **디렉터리 내의 모든 리소스에 적용됩니다**.
> 이로 인해 모든 고객은 이 범위에서 정의된 항목을 가져야 하는 필요성을 평가해야 합니다.
> 사용자 액세스 및 정책 할당은 이 범위에만 "있어야" 합니다.  

## <a name="initial-setup-of-management-groups"></a>관리 그룹의 초기 설치

사용자가 관리 그룹을 사용하기 시작하는 경우 발생하는 초기 설치 프로세스가 있습니다. 첫 번째 단계는 루트 관리 그룹을 디렉터리에 만드는 것입니다. 이 그룹을 만들면 디렉터리에 있는 모든 기존 구독이 루트 관리 그룹의 자식 요소를 만듭니다. 이 프로세스는 디렉터리 내에 하나의 관리 그룹 계층 구조가 있도록 했기 때문에 발생합니다. 디렉터리 내의 단일 계층 구조를 사용하면 관리 고객은 디렉터리 내의 다른 고객이 무시할 수 없는 전역 액세스 및 정책을 적용할 수 있습니다. 루트에 할당된 모든 항목은 디렉터리 내에 하나의 계층을 보유하여 디렉터리 내의 모든 관리 그룹, 구독, 리소스 그룹 및 리소스 간에 적용됩니다.

## <a name="trouble-seeing-all-subscriptions"></a>모든 구독 표시 관련 문제

(2018년 6월 25일) 이전에 미리 보기 초기 단계의 관리 그룹을 사용하여 시작된 몇몇 디렉터리에는 모든 구독이 계층 구조에 적용되지 않은 경우 문제가 발생할 수 있습니다.  계층 구조에 구독을 적용하는 프로세스는 디렉터리의 루트 관리 그룹에 대한 역할 또는 정책 할당이 완료된 후에 구현되었습니다.

### <a name="how-to-resolve-the-issue"></a>문제 해결 방법

이 문제를 해결하기 위해 수행할 수 있는 두 가지 옵션이 있습니다.

1. 루트 관리 그룹에서 모든 역할 및 정책 할당 제거
    1. 서비스는 루트 관리 그룹에서 모든 정책 및 역할 할당을 제거한 후 다음 야간 주기에 모든 구독을 계층 구조에 다시 채웁니다.  이 프로세스는 모든 테넌트 구독에 액세스 권한이 우연히 지정되거나 정책이 할당되지 않도록 합니다.
    1. 서비스에 영향을 주지 않고 이 프로세스를 수행하는 가장 좋은 방법은 루트 관리 그룹에서 한 수준 아래에 역할 또는 정책 할당을 적용하는 것입니다. 그런 다음, 루트 범위에서 모든 할당을 제거할 수 있습니다.
1. API를 직접 호출하여 다시 채우기 프로세스 시작
    1. 디렉터리의 모든 고객은 *TenantBackfillStatusRequest* 또는 *StartTenantBackfillRequest* API를 호출할 수 있습니다. 호출된 StartTenantBackfillRequest API는 모든 구독을 계층 구조로 이동하는 초기 설정 프로세스를 시작합니다. 이 프로세스는 또한 모든 새 구독을 루트 관리 그룹의 자식으로 적용하기 시작합니다. 이 프로세스는 루트 수준에서 할당을 변경하지 않고 수행될 수 있습니다. API를 호출하면 루트의 정책 또는 액세스 할당이 모든 구독에 적용될 수 있어도 괜찮습니다.

이 다시 채우기 프로세스에 대한 질문이 있는 경우 managementgroups@microsoft.com에 문의하세요.  
  
## <a name="management-group-access"></a>관리 그룹 액세스

Azure 관리 그룹은 모든 리소스 액세스 및 역할 정의를 위한 [Azure RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 지원합니다.
이러한 권한은 해당 계층 구조에 있는 자식 리소스에 상속됩니다. 기본 제공 RBAC 역할은 리소스에 대한 계층 구조를 상속하는 관리 그룹에 할당될 수 있습니다.
예를 들어, RBAC 역할 VM 기여자는 관리 그룹에 할당될 수 있습니다. 이 역할은 관리 그룹에 대한 조치를 취하지 않지만 해당 관리 그룹에서 모든 VM에 상속됩니다.

다음 차트에서는 관리 그룹에 대한 역할 및 지원되는 작업 목록을 보여줍니다.

| RBAC 역할 이름             | 생성 | 이름 바꾸기 | 이동 | 삭제 | 액세스 권한 할당 | 정책 할당 | 읽기  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|소유자                       | X      | X      | X    | X      | X             | X             | X     |
|참가자                 | X      | X      | X    | X      |               |               | X     |
|MG 기여자*             | X      | X      | X    | X      |               |               | X     |
|판독기                      |        |        |      |        |               |               | X     |
|MG 읽기 권한자*                  |        |        |      |        |               |               | X     |
|리소스 정책 참가자 |        |        |      |        |               | X             |       |
|사용자 액세스 관리자   |        |        |      |        | X             |               |       |

*: MG 기여자 및 MG 읽기 권한자는 사용자가 관리 그룹 범위에서 이러한 작업만 수행하도록 허용합니다.  

### <a name="custom-rbac-role-definition-and-assignment"></a>사용자 지정 RBAC 역할 정의 및 할당

사용자 지정 RBAC 역할은 현재 관리 그룹에서 지원되지 않습니다. [관리 그룹 피드백 포럼](https://aka.ms/mgfeedback)을 참조하여 이 항목의 상태를 봅니다.

## <a name="audit-management-groups-using-activity-logs"></a>활동 로그를 사용하여 관리 그룹 감사

이 API를 통해 관리 그룹을 추적하려면 [테넌트 활동 로그 API](/rest/api/monitor/tenantactivitylogs)를 사용합니다. 현재 PowerShell, CLI 또는 Azure Portal을 사용하여 관리 그룹 작업을 추적할 수 없습니다.

1. Azure AD 테넌트의 테넌트 관리자로 [액세스의 권한을 상승한](../../role-based-access-control/elevate-access-global-admin.md) 다음, `/providers/microsoft.insights/eventtypes/management` 범위에 대해 감사 사용자에게 읽기 역할을 할당합니다.
1. 감사 사용자로 [테넌트 활동 로그 API](/rest/api/monitor/tenantactivitylogs)를 호출하여 관리 그룹 작업을 확인합니다. 모든 관리 그룹 작업에 대해 **Microsoft.Management** 리소스 공급자로 필터링하려고 합니다.  예제:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Management'"
```

> [!NOTE]
> 명령줄에서 이 API를 편리하게 호출하려면 [ARMClient](https://github.com/projectkudu/ARMClient)를 시도합니다.

## <a name="next-steps"></a>다음 단계

관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [관리 그룹을 만들어 Azure 리소스 구성](create.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](manage.md)
- [Azure PowerShell 리소스 모듈에서 관리 그룹 검토](https://aka.ms/mgPSdocs)
- [REST API에서 관리 그룹 검토](https://aka.ms/mgAPIdocs)
- [Azure CLI에서 관리 그룹 검토](https://aka.ms/mgclidoc)
