---
title: 테넌트 ID를 찾는 방법 - Azure Active Directory
description: 기존 Azure 구독에 대한 Azure Active Directory 테넌트 ID를 찾는 방법에 대한 지침입니다.
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
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a6bba9a1fc7974d324696e88e059c1d44924dac
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108016692"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Azure Active Directory 테넌트 ID를 찾는 방법

Azure 구독은 Azure AD(Azure Active Directory)와 트러스트 관계가 있습니다. Azure AD는 구독에 대한 사용자, 서비스, 디바이스를 인증하는 데 신뢰할 수 있습니다. 각 구독에는 연결된 테넌트 ID가 있으며, 구독에 대한 테넌트 ID를 찾을 수 있는 몇 가지 방법이 있습니다.

## <a name="find-tenant-id-through-the-azure-portal"></a>Azure Portal을 통해 테넌트 ID 찾기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
 
1. **Azure Active Directory** 를 선택합니다.

1. **속성** 을 선택합니다.

1. 그런 다음, **테넌트 ID** 필드로 스크롤합니다. 테넌트 ID가 상자에 있습니다.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory - 속성 - 테넌트 ID - 테넌트 ID 필드":::

## <a name="find-tenant-id-with-powershell"></a>PowerShell을 사용하여 테넌트 ID 찾기

프로그래밍 방식으로 테넌트를 찾을 수도 있습니다. Azure PowerShell을 사용하여 테넌트 ID를 찾으려면 cmdlet `Get-AzTenant`을 사용합니다.

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
자세한 내용은 [Get-AzTenant](/powershell/module/az.accounts/get-aztenant)에 대한 이 Azure PowerShell cmdlet 참조를 참조하세요.


## <a name="find-tenant-id-with-cli"></a>CLI를 사용하여 테넌트 ID 찾기
명령줄 인터페이스를 사용하여 테넌트 ID를 찾으려면 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/)를 사용하여 이 작업을 수행할 수 있습니다. 

Azure CLI의 경우 다음 예와 같이 **az login**, **az account list** 또는 **az account tenant list** 명령 중 하나를 사용합니다. 각 명령의 출력의 각 구독에 대한 **tenantId** 속성을 확인합니다.

```azurecli-interactive
az login
az account list
az account tenant list
```

자세한 내용은 [az login](/cli/azure/reference-index#az_login) 명령 참조, [az account](/cli/azure/account) 명령 참조 또는 [az account tenant](/cli/azure/account/tenant) 명령 참조에서 확인하세요.


Microsoft 365 CLI의 경우 다음 예와 같이 **tenant id** cmdlet을 사용합니다.
 
```cli
m365 tenant id get
```

자세한 내용은 Microsoft 365 [tenant id get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) 명령 참조를 참조하세요.


## <a name="next-steps"></a>다음 단계

- 새 Azure AD 테넌트를 만들려면 [빠른 시작: Azure Active Directory에 액세스하여 새 테넌트 만들기](active-directory-access-create-new-tenant.md)를 참조하세요.

- 새 구독을 Azure Active Directory 테넌트에 추가하는 방법을 알아보려면 [Azure 구독을 Azure Active Directory 테넌트에 연결 또는 추가하는 방법](active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

- 개체 ID를 찾는 방법에 대해 알아보려면 [사용자 개체 ID 찾기](/partner-center/find-ids-and-domain-names#find-the-user-object-id)를 참조하세요.
