---
title: Azure Advisor에 대 한 사용 권한
description: 관리자 권한 및 연기 또는 권장 사항을 해제할 구독을 구성 하는 기능을 차단할 수 있습니다 하는 방법입니다.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467615"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor에 대 한 사용 권한

Azure Advisor의 구독 및 Azure 리소스 구성과 사용량에 따라 권장 사항을 제공 합니다. 관리자가 사용 하는 [기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 제공한 [역할 기반 Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) 권장 사항 및 관리자의 기능에 대 한 액세스를 관리할 수 있습니다. 

## <a name="roles-and-their-access"></a>역할 및 액세스

다음 표에서 역할 및 관리자의 액세스를 정의 합니다.

| **역할** | **권장 사항 보기** | **규칙 편집** | **구독 구성 편집** | **리소스 그룹 구성 편집**| **해제 하 고 권장 사항 연기**|
|---|:---:|:---:|:---:|:---:|:---:|
|구독 소유자|**X**|**X**|**X**|**X**|**X**|
|구독 참가자|**X**|**X**|**X**|**X**|**X**|
|구독 읽기 권한자|**X**|--|--|--|--|
|리소스 그룹 소유자|**X**|--|--|**X**|**X**|
|리소스 그룹 참가자|**X**|--|--|**X**|**X**|
|리소스 그룹 판독기|**X**|--|--|--|--|
|리소스 소유자|**X**|--|--|--|**X**|
|리소스 참가자|**X**|--|--|--|**X**|
|리소스 판독기|**X**|--|--|--|--|

> [!NOTE]
> 권장 사항 보기에 대 한 액세스에 대 한 권장 구성의 영향을 받는 리소스에 종속 됩니다.

## <a name="permissions-and-unavailable-actions"></a>사용 권한 및 사용할 수 없는 작업

적절 한 권한이 부족 한 Advisor에서 작업을 수행 하는 기능을 차단할 수 있습니다. 다음은 몇 가지 일반적인 문제입니다.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>구독 또는 리소스 그룹을 구성할 수 없습니다.

Advisor에서 구독 또는 리소스 그룹을 구성 하려고 할 때 포함 하거나 제외 하는 옵션은 사용 되지 않음을 표시 될 수 있습니다. 이 상태는 리소스 그룹 또는 구독에 대 한 권한이 있을 정도로 했음을 나타냅니다. 이 문제를 해결 하려면 하는 방법 [사용자 액세스 권한을 부여할](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)합니다.

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>연기 하거나 권장 사항을 해제할 수 없습니다.

연기 또는 권장 사항을 해제 하려고 할 때 오류가 표시 되 면 충분 한 권한이 없을 수 있습니다. 했는지는 적어도 연기 하거나 해제 하는 권장 사항의 영향을 받는 리소스에 대 한 참가자 액세스. 이 문제를 해결 하려면 하는 방법 [사용자 액세스 권한을 부여할](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 관리자 권한을 사용자에 RBAC를 사용 하는 방법 및 일반적인 문제를 해결 하는 방법의 개요를 제공 했습니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure Advisor란?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Azure Advisor 시작](https://docs.microsoft.com/azure/advisor/advisor-get-started)
