---
title: 테 넌 트 ID를 찾는 방법-Azure Active Directory
description: 기존 Azure 구독에 대 한 테 넌 트 ID를 찾고 Azure Active Directory 하는 방법에 대 한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbf23a32fdf5515402e18f70fe032a1a589dbcbe
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424258"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Azure Active Directory 테 넌 트 ID를 찾는 방법

Azure 구독에는 Azure Active Directory (Azure AD)와의 트러스트 관계가 있습니다. Azure AD는 구독에 대 한 사용자, 서비스 및 장치를 인증 하는 데 신뢰할 수 있습니다. 각 구독에는 연결 된 테 넌 트 ID가 있으며, 구독에 대 한 테 넌 트 ID를 찾을 수 있는 몇 가지 방법이 있습니다.

## <a name="find-tenant-id-through-the-azure-portal"></a>Azure Portal를 통해 테 넌 트 ID 찾기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
 
1. **Azure Active Directory** 를 선택합니다.

1. **속성** 을 선택합니다.

1. 그런 다음, **테 넌 트 ID** 필드로 스크롤합니다. 테 넌 트 ID가 상자에 있습니다.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-테 넌 트 ID-테 넌 트 ID 필드":::

## <a name="find-tenant-id-with-powershell"></a>PowerShell을 사용 하 여 테 넌 트 ID 찾기

프로그래밍 방식으로 테 넌 트를 찾을 수도 있습니다. Azure PowerShell를 사용 하 여 테 넌 트 ID를 찾으려면 cmdlet을 사용 합니다 `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
자세한 내용은 [AzTenant](/powershell/module/az.accounts/get-aztenant)에 대 한이 Azure PowerShell cmdlet 참조를 참조 하세요.


## <a name="find-tenant-id-with-cli"></a>CLI를 사용 하 여 테 넌 트 ID 찾기
명령줄 인터페이스를 사용 하 여 테 넌 트 ID를 찾으려면 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/)를 사용 하 여이 작업을 수행할 수 있습니다. 

Azure CLI의 경우 다음 예제와 같이 **az login** , **az account list** 또는 **az account tenant list** 명령 중 하나를 사용 합니다. 각 명령의 출력에서 각 구독에 대 한 **tenantId** 속성이 있는지 확인 합니다.

```azurecli-interactive
az login
az account list
az account tenant list
```

자세한 내용은 [az login](/cli/azure/reference-index#az_login) command reference, [az account](/cli/azure/ext/account/account) command reference 또는 [az account tenant](/cli/azure/ext/account/account/tenant) command reference를 참조 하세요.


CLI Microsoft 365의 경우 다음 예제와 같이 cmdlet **테 넌 트 id** 를 사용 합니다.
 
```cli
m365 tenant id get
```

자세한 내용은 Microsoft 365 [테 넌 트 id 가져오기](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) 명령 참조를 참조 하세요.


## <a name="next-steps"></a>다음 단계

- 새 Azure AD 테 넌 트를 만들려면 [빠른 시작: Azure Active Directory에서 새 테 넌 트 만들기](active-directory-access-create-new-tenant.md)를 참조 하세요.

- 테 넌 트에 구독을 연결 하거나 추가 하는 방법을 알아보려면 [Azure Active Directory 테 넌 트에 Azure 구독 연결 또는 추가](active-directory-how-subscriptions-associated-directory.md)를 참조 하세요.

- 개체 ID를 찾는 방법에 대해 알아보려면 [사용자 개체 Id 찾기](https://docs.microsoft.com/partner-center/find-ids-and-domain-names#find-the-user-object-id)를 참조 하세요.
