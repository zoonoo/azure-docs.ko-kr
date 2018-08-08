---
title: Azure 관리 그룹으로 리소스 구성 | Microsoft Docs
description: 관리 그룹 및 사용 방법에 대해 알아봅니다.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/31/2018
ms.author: rithorn
ms.openlocfilehash: 146ded37dbf517528af23574cd5b9325f4b5f9d0
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358772"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Azure 관리 그룹으로 리소스 구성

조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다. "관리 그룹"이라는 컨테이너에 구독을 구성하고 거버넌스 조건을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 조건을 자동으로 상속합니다. 관리 그룹은 어떤 형식의 구독을 사용하든 관계 없이 대규모의 엔터프라이즈급 관리를 제공합니다.

예를 들어, VM(가상 머신) 생성에 사용 가능한 지역을 제한하는 정책을 관리 그룹에 적용할 수 있습니다. 이 정책은 해당 지역에만 VM을 만들 수 있도록 허용하는 방식으로 그 지역에 속한 모든 관리 그룹, 구독 및 리소스에 적용됩니다.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>관리 그룹 및 구독의 계층 구조

리소스를 통합 정책 및 액세스 관리를 위한 계층 구조로 구성하는 유연한 관리 그룹 및 구독 구조를 만들 수 있습니다.
다음 다이어그램은 관리 그룹을 사용하여 거버넌스용 계층을 만드는 예를 보여줍니다.

![트리](media/management-groups/MG_overview.png)

이 예제처럼 계층 구조를 만들면 정책을 적용할 수 있습니다. 예를 들어 VM 위치를 "인프라 팀 관리 그룹"의 미국 서부 지역으로 제한하는 정책을 적용하여 내부 규정 및 보안 정책을 준수할 수 있습니다. 이 정책은 해당 관리 그룹의 두 EA 구독으로 상속되어 해당 구독의 모든 VM에 적용됩니다. 이 정책은 관리 그룹에서 구독으로 상속되므로, 거버넌스 향상을 위해 리소스 또는 구독 소유자가 이 보안 정책을 변경할 수 없습니다.

관리 그룹을 사용할 만한 또 다른 시나리오는 여러 구독에 대한 사용자 액세스 제공입니다.  여러 구독을 해당 관리 그룹으로 이동하면 관리 그룹에 RBAC 할당을 만들 수 있게 되고, 이 할당은 모든 구독에 대한 액세스를 상속합니다.  여러 구독에 RBAC 할당을 스크립팅할 필요 없이, 관리 그룹에 하나만 할당하면 사용자가 필요한 모든 항목에 액세스할 수 있습니다.

### <a name="important-facts-about-management-groups"></a>관리 그룹에 대한 중요 한 사실

- 단일 디렉터리에서 지원할 수 있는 관리 그룹 수는 10,000개입니다.
- 관리 그룹 트리에서 지원할 수 있는 최대 깊이 수준은 6입니다.
  - 이 제한에는 루트 수준 또는 구독 수준이 포함되지 않습니다.
- 각 관리 그룹 및 구독은 하나의 부모만 지원할 수 있습니다.
- 각 관리 그룹은 여러 자식을 가질 수 있습니다.
- 모든 구독 및 관리 그룹은 각 디렉터리에서 단일 계층 내에 포함됩니다. 미리 보기 중 예외는 [루트 관리 그룹에 대한 중요한 사실](#important-facts-about-the-root-management-group)을 참조하세요.

## <a name="root-management-group-for-each-directory"></a>각 디렉터리에 대한 루트 관리 그룹

각 디렉터리에는 "루트" 관리 그룹이라고 하는 단일 최상위 관리 그룹이 부여됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 루트 관리 그룹은 전역 정책 및 RBAC 할당을 디렉터리 수준에서 적용하는 것을 허용합니다. 디렉터리 관리자는 처음에 이 루트 그룹의 소유자가 되도록 [자신을 승격해야 합니다](../role-based-access-control/elevate-access-global-admin.md). 관리자는 그룹의 소유자가 되면 계층 구조를 관리할 다른 디렉터리 사용자 또는 그룹에 모든 RBAC 역할을 할당할 수 있습니다.  

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

>[!NOTE]
>디렉터리가 2018/6/25 이전에 관리 그룹 서비스를 사용하여 시작되면 해당 디렉터리는 계층 구조에서 모든 구독을 사용하여 설정되지 않을 수 있습니다. 관리 그룹 팀은 2018년 7~8월 내에 해당 날짜 이전의 공개 미리 보기 상태인 관리 그룹을 사용하여 시작된 각 디렉터리를 소급해서 업데이트하고 있습니다. 디렉터리의 모든 구독은 이전 루트 관리 그룹 아래에 자식 요소를 만듭니다.  
>
>이 소급 프로세스에 대한 질문이 있으면 managementgroups@microsoft.com에 문의하세요.  
  
## <a name="initial-setup-of-management-groups"></a>관리 그룹의 초기 설치

사용자가 관리 그룹을 사용하기 시작하는 경우 발생하는 초기 설치 프로세스가 있습니다. 첫 번째 단계는 루트 관리 그룹을 디렉터리에 만드는 것입니다. 이 그룹을 만들면 디렉터리에 있는 모든 기존 구독이 루트 관리 그룹의 자식 요소를 만듭니다.  이 프로세스는 디렉터리 내에 하나의 관리 그룹 계층 구조가 있도록 했기 때문에 발생합니다.  디렉터리 내의 단일 계층 구조를 사용하면 관리 고객은 디렉터리 내의 다른 고객이 무시할 수 없는 전역 액세스 및 정책을 적용할 수 있습니다. 루트에 할당된 모든 항목은 디렉터리 내에 하나의 계층을 보유하여 디렉터리 내의 모든 관리 그룹, 구독, 리소스 그룹 및 리소스 간에 적용됩니다.  

> [!IMPORTANT]
> 루트 관리 그룹에서 사용자 액세스 할당 또는 정책 할당은 **디렉터리 내의 모든 리소스에 적용됩니다**. 이로 인해 모든 고객은 이 범위에서 정의된 항목을 가져야 하는 필요성을 평가해야 합니다.  사용자 액세스 및 정책 할당은 이 범위에만 "있어야" 합니다.  
  
## <a name="management-group-access"></a>관리 그룹 액세스

Azure 관리 그룹은 모든 리소스 액세스 및 역할 정의를 위한 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 지원합니다. 이러한 권한은 해당 계층 구조에 있는 자식 리소스에 상속됩니다. 기본 제공 RBAC 역할은 리소스에 대한 계층 구조를 상속하는 관리 그룹에 할당될 수 있습니다.  예를 들어, RBAC 역할 VM 기여자는 관리 그룹에 할당될 수 있습니다. 이 역할은 관리 그룹에 대한 조치를 취하지 않지만 해당 관리 그룹에서 모든 VM에 상속됩니다.  

다음 차트에서는 관리 그룹에 대한 역할 및 지원되는 작업 목록을 보여줍니다.

| RBAC 역할 이름             | 생성 | 이름 바꾸기 | 이동 | 삭제 | 액세스 권한 할당 | 정책 할당 | 읽기  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|소유자                       | X      | X      | X    | X      | X             |               | X     |
|참가자                 | X      | X      | X    | X      |               |               | X     |
|MG 기여자*             | X      | X      | X    | X      |               |               | X     |
|판독기                      |        |        |      |        |               |               | X     |
|MG 읽기 권한자*                  |        |        |      |        |               |               | X     |
|리소스 정책 참가자 |        |        |      |        |               | X             |       |
|사용자 액세스 관리자   |        |        |      |        | X             |               |       |

*: MG 기여자 및 MG 읽기 권한자만 사용자가 관리 그룹 범위에서 이러한 작업을 수행하도록 허용합니다.  

### <a name="custom-rbac-role-definition-and-assignment"></a>사용자 지정 RBAC 역할 정의 및 할당

사용자 지정 RBAC 역할은 현재 관리 그룹에서 지원되지 않습니다.  [관리 그룹 피드백 포럼](https://aka.ms/mgfeedback)을 참조하여 이 항목의 상태를 봅니다.

## <a name="next-steps"></a>다음 단계

관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [관리 그룹을 만들어 Azure 리소스 구성](management-groups-create.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](management-groups-manage.md)
- [Azure PowerShell 모듈 설치](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [REST API 사양 검토](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Azure CLI 확장 설치](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
