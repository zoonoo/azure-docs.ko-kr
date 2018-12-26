---
title: PIM의 적격 할당 및 리소스 표시 유형 - Azure | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 적격인 멤버를 할당하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fb52bc92c86261831d0e8d8e9e863a4863fe8fb9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666892"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>PIM의 적격 할당 및 리소스 표시 유형

Azure 리소스 역할에 대한 PIM(Privileged Identity Management)은 중요한 Azure 리소스가 있는 조직에 대한 향상된 보안을 제공합니다. 리소스 관리자는 PIM을 사용하여 리소스 역할에 멤버를 적격으로 할당할 수 있습니다. 다음 섹션에서 Azure 리소스 역할에 대한 다른 할당 형식 및 할당 상태에 대해 자세히 알아봅니다. 

## <a name="assignment-types"></a>할당 형식

Azure 리소스에서 PIM은 두 개의 고유한 할당 형식을 제공합니다.

- 적격
- Active

적격 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 멤버가 필요합니다. 작업은 Multi-Factor Authentication 검사를 성공하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

활성 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

## <a name="assignment-duration"></a>할당 기간

리소스 관리자는 역할에 대해 PIM 설정을 구성할 때 각 할당 형식에 대한 두 가지 옵션에서 선택할 수 있습니다. 멤버가 PIM의 역할에 할당된 경우 이러한 옵션은 최대 기본 기간이 됩니다. 

관리자는 이러한 할당 형식 중 하나를 선택할 수 있습니다.

- 영구 적격 할당 허용
- 영구 활성 할당 허용

또는 관리자는 이러한 할당 형식 중 하나를 선택할 수 있습니다.

- 적격 할당 만료 기준 시간
- 활성 할당 만료 기준 시간

리소스 관리자가 **영구 적격 할당 허용** 또는 **영구 활성 할당 허용**을 선택하는 경우 리소스에 멤버를 할당하는 모든 관리자는 영구 멤버 자격을 할당할 수 있습니다.

리소스 관리자가 **적격 할당 만료 기준 시간** 또는 **활성 할당 만료 기준 시간**을 선택하는 경우 리소스 관리자는 모든 할당에 지정된 시작 및 종료 날짜를 포함하도록 요구하여 할당 수명 주기를 제어합니다.

> [!NOTE] 
> 리소스 관리자는 지정된 종료 날짜가 있는 모든 할당을 갱신할 수 있습니다. 또한 멤버는 [할당을 확장 또는 갱신](pim-resource-roles-renew-extend.md)하도록 셀프 서비스 요청을 시작할 수 있습니다.


## <a name="assignment-states"></a>할당 상태

Azure 리소스에서 PIM에는 **내 역할**, **역할** 및 PIM의 **멤버** 보기에서 **활성 역할** 탭에 표시되는 두 개의 고유한 할당 상태가 있습니다. 상태는 다음과 같습니다.

- 할당됨
- 활성화됨

**활성 역할**에 나열된 멤버 자격을 볼 때 **STATE** 열의 값을 사용하여 **할당됨**이 활성인 사용자와 적격 할당이 **활성화됨**이고 현재 활성인 사용자 간의 차이점을 구분할 수 있습니다.

## <a name="azure-resource-role-approval-workflow"></a>Azure 리소스 역할 승인 워크플로

관리자는 Azure 리소스 역할에 대한 PIM의 승인 워크플로를 통해 중요한 리소스에 대한 액세스 권한을 추가로 보호하거나 제한할 수 있습니다. 즉, 관리자는 역할 할당을 활성화하기 위해 승인을 요구할 수 있습니다.

리소스 계층의 개념은 Azure 리소스 역할에 고유한 개념입니다. 이 계층을 사용하면 부모 리소스 개체에서 부모 컨테이너 내의 모든 자식 리소스로 역할 할당을 상속할 수 있습니다. 

예를 들어, 리소스 관리자인 Bob이 PIM을 사용하여 Alice를 Contoso 구독의 소유자 역할에 적격 멤버로 할당합니다. 이 할당에서 Alice는 Contoso 구독 내 모든 리소스 그룹 컨테이너의 적격 소유자입니다. 또한 Alice는 구독의 각 리소스 그룹 내에서 가상 머신과 같은 모든 리소스의 적격 소유자이기도 합니다.

Contoso 구독에 Fabrikam Test, Fabrikam Dev 및 Fabrikam Prod라는 세 개의 리소스 그룹이 있다고 가정하겠습니다. 이러한 각 리소스 그룹에는 단일 가상 머신이 포함됩니다.

PIM 설정은 리소스의 각 역할에 대해 구성됩니다. 할당과 달리 이 설정은 상속되지 않고 리소스 역할에 엄격히 적용됩니다.

계속 같은 예제로 설명하면, Bob은 PIM을 사용하여 Contoso 구독 소유자 역할의 모든 멤버에 활성화를 위한 승인 절차를 요청합니다. Fabrikam Prod 리소스 그룹의 리소스를 보호하기 위해 Bob은 또한 이 리소스의 소유자 역할 멤버에 대해 승인을 요청합니다. Fabrikam Test 및 Dev Fabrikam의 소유자 역할은 활성화를 위한 승인이 필요하지 않습니다.

Alice가 Contoso 구독에 대해 소유자 역할의 활성화를 요청하는 경우 승인자는 Alice의 역할이 활성 상태가 되기 전에 해당 요청을 승인하거나 거부해야 합니다. Alice가 Fabrikam Prod 리소스 그룹으로 [활성화 범위를 지정](pim-resource-roles-activate-your-roles.md)하려는 경우에도 승인자는 이 요청을 승인하거나 거부해야 합니다. 그러나 Alice가 Fabrikam Test 또는 Fabrikam Dev로 활성화 범위를 지정하려는 경우 승인이 필요하지 않습니다.

승인 워크플로는 역할의 일부 멤버에게만 필요할 수 있습니다. Azure 구독에서 실행될 애플리케이션 개발을 지원하기 위해 조직이 계약 직원 몇명을 채용하는 시나리오를 가정합니다. 리소스 관리자로서 사용자는 직원이 승인을 요청하지 않고도 적격 액세스 권한을 갖기를 원하지만, 계약 직원은 반드시 승인을 요청해야 합니다. 계약 직원에 대해서만 승인 워크플로를 구성하려면 직원에게 할당된 역할과 동일한 권한으로 사용자 지정 역할을 만들 수 있습니다. 사용자 정의 역할을 활성화하려면 승인이 필요할 수 있습니다. [사용자 지정 역할을 자세히 알아보세요](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
