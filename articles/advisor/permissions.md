---
title: Azure Advisor의 사용 권한
description: Advisor 권한 및 구독을 구성 하는 기능을 차단 하는 방법 및 권장 사항을 연기 또는 해제할 수 있습니다.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 3c7b42372ae6de5c2f519b69aec8a154a2f35a82
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927388"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor의 사용 권한

Azure Advisor은 Azure 리소스 및 구독의 사용량 및 구성에 따라 권장 사항을 제공 합니다. Advisor는 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md) 에서 제공 하는 [기본 제공 역할](../role-based-access-control/built-in-roles.md) 을 사용 하 여 권장 사항 및 Advisor 기능에 대 한 액세스를 관리 합니다. 

## <a name="roles-and-their-access"></a>역할 및 해당 액세스

다음 표에서는 Advisor 내에서 역할 및 액세스 권한을 정의 합니다.

| **역할** | **권장 사항 보기** | **규칙 편집** | **구독 구성 편집** | **리소스 그룹 구성 편집**| **권장 구성 해제 및 연기**|
|---|:---:|:---:|:---:|:---:|:---:|
|구독 소유자|**X**|**X**|**X**|**X**|**X**|
|구독 참가자|**X**|**X**|**X**|**X**|**X**|
|구독 판독기|**X**|--|--|--|--|
|리소스 그룹 소유자|**X**|--|--|**X**|**X**|
|리소스 그룹 참가자|**X**|--|--|**X**|**X**|
|리소스 그룹 판독기|**X**|--|--|--|--|
|리소스 소유자|**X**|--|--|--|**X**|
|리소스 참여자|**X**|--|--|--|**X**|
|리소스 판독기|**X**|--|--|--|--|

> [!NOTE]
> 권장 사항 보기에 대 한 액세스는 권장 사항의 영향을 받는 리소스에 대 한 액세스에 따라 달라 집니다.

## <a name="permissions-and-unavailable-actions"></a>사용 권한 및 사용 불가능 한 작업

적절 한 권한이 없으면 Advisor에서 작업을 수행 하는 기능을 차단할 수 있습니다. 다음은 몇 가지 일반적인 문제입니다.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>구독 또는 리소스 그룹을 구성할 수 없습니다.

Advisor에서 구독 또는 리소스 그룹을 구성 하려고 하면 포함 하거나 제외 하는 옵션이 사용 하지 않도록 설정 된 것을 볼 수 있습니다. 이 상태는 해당 리소스 그룹 또는 구독에 대 한 충분 한 수준의 권한이 없음을 나타냅니다. 이 문제를 해결 하려면 사용자에 게 [액세스 권한을 부여](../role-based-access-control/quickstart-assign-role-user-portal.md)하는 방법을 알아보세요.

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>권장 사항을 연기 하거나 해제할 수 없습니다.

권장 사항을 연기 하거나 해제 하려고 할 때 오류가 표시 되 면 충분 한 권한이 없을 수 있습니다. 연기 하거나 해제 하려는 권장 구성의 영향을 받는 리소스에 대 한 참가자 이상의 액세스 권한이 있는지 확인 합니다. 이 문제를 해결 하려면 사용자에 게 [액세스 권한을 부여](../role-based-access-control/quickstart-assign-role-user-portal.md)하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Advisor를 사용 하 여 사용자 권한을 제어 하는 방법 및 일반적인 문제를 해결 하는 방법에 대 한 개요를 제공 했습니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure Advisor란?](./advisor-overview.md)
- [Azure Advisor 시작](./advisor-get-started.md)
