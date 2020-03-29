---
title: Azure 어드바이저의 권한
description: 어드바이저 권한 및 구독을 구성하거나 권장 사항을 연기하거나 해제하는 기능을 차단할 수 있는 방법.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422310"
---
# <a name="permissions-in-azure-advisor"></a>Azure 어드바이저의 권한

Azure Advisor는 Azure 리소스 및 구독의 사용 및 구성에 따라 권장 사항을 제공합니다. Advisor는 [RBAC(역할 기반 액세스 제어)에서](https://docs.microsoft.com/azure/role-based-access-control/overview) 제공하는 [기본 제공 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 사용하여 권장 사항 및 Advisor 기능에 대한 액세스를 관리합니다. 

## <a name="roles-and-their-access"></a>역할 및 액세스 권한

다음 표는 Advisor 내에서 역할과 액세스 권한을 정의합니다.

| **Role** | **권장 사항 보기** | **규칙 편집** | **구독 구성 편집** | **리소스 그룹 구성 편집**| **권장 사항 해제 및 연기**|
|---|:---:|:---:|:---:|:---:|:---:|
|구독 소유자|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|
|구독 참가자|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|
|구독 리더|**Ⅹ**|--|--|--|--|
|리소스 그룹 소유자|**Ⅹ**|--|--|**Ⅹ**|**Ⅹ**|
|리소스 그룹 기고자|**Ⅹ**|--|--|**Ⅹ**|**Ⅹ**|
|리소스 그룹 판독기|**Ⅹ**|--|--|--|--|
|리소스 소유자|**Ⅹ**|--|--|--|**Ⅹ**|
|리소스 기여자|**Ⅹ**|--|--|--|**Ⅹ**|
|리소스 판독기|**Ⅹ**|--|--|--|--|

> [!NOTE]
> 참조 권장 사항에 대한 액세스는 권장 사항의 영향을 받는 리소스에 대한 액세스에 따라 달라집니다.

## <a name="permissions-and-unavailable-actions"></a>권한 및 사용할 수 없는 작업

적절한 권한이 부족하면 Advisor에서 작업을 수행하는 기능이 차단될 수 있습니다. 다음은 몇 가지 일반적인 문제입니다.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>구독 또는 리소스 그룹을 구성할 수 없음

Advisor에서 구독 또는 리소스 그룹을 구성하려고 하면 포함 또는 제외 옵션이 비활성화된 것으로 표시될 수 있습니다. 이 상태는 해당 리소스 그룹 또는 구독에 대한 충분한 권한 수준이 아님을 나타냅니다. 이 문제를 해결하려면 [사용자에게 액세스 권한을 부여하는](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)방법을 알아봅니다.

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>권장 사항을 연기하거나 해제할 수 없음

권장 사항을 연기하거나 해제하려고 할 때 오류가 발생하면 충분한 사용 권한이 없을 수 있습니다. 연기하거나 해제하는 권장 사항의 영향을 받는 리소스에 적어도 기여자 액세스 권한이 있는지 확인합니다. 이 문제를 해결하려면 [사용자에게 액세스 권한을 부여하는](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Advisor가 RBAC를 사용하여 사용자 권한을 제어하는 방법과 일반적인 문제를 해결하는 방법에 대한 개요를 제공했습니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure Advisor란?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Azure 어드바이저 시작](https://docs.microsoft.com/azure/advisor/advisor-get-started)
