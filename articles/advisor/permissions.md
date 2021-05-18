---
title: Azure Advisor의 권한
description: Advisor 권한 및 해당 권한이 구독을 구성하거나 권장 사항을 연기 또는 해제하는 기능을 차단할 수 있는 방법입니다.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91712934"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor의 권한

Azure Advisor는 Azure 리소스 및 구독의 사용량 및 구성에 따라 권장 사항을 제공합니다. Advisor는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)에서 제공하는 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 사용하여 권장 사항 및 Advisor 기능에 대한 액세스를 관리합니다. 

## <a name="roles-and-their-access"></a>역할 및 해당 액세스

다음 표에서는 Advisor 내의 역할 및 해당 액세스 권한을 정의합니다.

| **Role** | **권장 사항 보기** | **규칙 편집** | **구독 구성 편집** | **리소스 그룹 구성 편집**| **권장 사항 해제 및 연기**|
|---|:---:|:---:|:---:|:---:|:---:|
|구독 소유자|**X**|**X**|**X**|**X**|**X**|
|구독 참가자|**X**|**X**|**X**|**X**|**X**|
|구독 읽기 권한자|**X**|--|--|--|--|
|리소스 그룹 소유자|**X**|--|--|**X**|**X**|
|리소스 그룹 기여자|**X**|--|--|**X**|**X**|
|리소스 그룹 읽기 권한자|**X**|--|--|--|--|
|리소스 소유자|**X**|--|--|--|**X**|
|리소스 기여자|**X**|--|--|--|**X**|
|리소스 읽기 권한자|**X**|--|--|--|--|

> [!NOTE]
> 권장 사항 보기 권한은 권장 사항의 영향을 받는 리소스에 대한 액세스 권한에 따라 달라집니다.

## <a name="permissions-and-unavailable-actions"></a>권한 및 사용할 수 없는 작업

적절한 권한이 없으면 Advisor에서 작업을 수행하는 기능이 차단될 수 있습니다. 다음은 몇 가지 일반적인 문제입니다.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>구독 또는 리소스 그룹을 구성할 수 없음

Advisor에서 구독 또는 리소스 그룹을 구성하려고 할 때 포함 또는 제외 옵션이 비활성화된 것을 볼 수도 있습니다. 이 상태는 해당 리소스 그룹 또는 구독에 대한 충분한 수준의 권한이 없음을 나타냅니다. 이슈를 해결하려면 [사용자에게 액세스 권한을 부여](../role-based-access-control/quickstart-assign-role-user-portal.md)하는 방법을 알아보세요.

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>권장 사항을 연기하거나 해제할 수 없음

권장 사항을 연기하거나 해제하려고 할 때 오류가 표시되는 경우 충분한 권한이 없을 수 있습니다. 연기하거나 해제하려는 권장 사항의 영향을 받는 리소스에 대해 기여자 이상의 액세스 권한이 있는지 확인합니다. 이슈를 해결하려면 [사용자에게 액세스 권한을 부여](../role-based-access-control/quickstart-assign-role-user-portal.md)하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Advisor가 Azure RBAC를 사용하여 사용자 권한을 제어하는 방법과 일반적인 이슈를 해결하는 방법을 개괄적으로 설명했습니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure Advisor란?](./advisor-overview.md)
- [Azure Adviser 시작](./advisor-get-started.md)
