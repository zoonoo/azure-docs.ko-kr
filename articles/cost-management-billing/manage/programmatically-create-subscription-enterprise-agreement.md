---
title: 최신 API를 사용하여 프로그래밍 방식으로 Azure 기업계약 구독 만들기
description: 최신 버전의 REST API, Azure CLI 및 Azure PowerShell을 사용하여 프로그래밍 방식으로 Azure 기업계약 구독을 만드는 방법을 알아봅니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0cdd25b2937dd1fb2cc70ef7b1c5a9e9ddaef375
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780606"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>최신 API를 사용하여 프로그래밍 방식으로 Azure 기업계약 구독 만들기

이 문서는 최신 API 버전을 사용하여 EA 청구 계정에 대한 Azure EA(기업계약) 구독을 프로그래밍 방식으로 만드는 데 도움이 됩니다. 이전 미리 보기 버전을 여전히 사용 중인 경우 [미리 보기 API를 사용하여 프로그래밍 방식으로 Azure 구독 만들기](programmatically-create-subscription-preview.md)를 참조하세요. 

이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

프로그래밍 방식으로 Azure 구독을 만든 경우 해당 구독은 Microsoft 또는 공인된 재판매인으로부터 Azure 서비스를 구입한 계약이 적용됩니다. 자세한 내용은 [Microsoft Azure 법률 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 등록 계정에 대한 소유자 역할이 있어야 합니다. 역할을 얻는 방법은 두 가지입니다.

* 등록의 엔터프라이즈 관리자는 귀하를 [계정 소유자로 만들 수](https://ea.azure.com/helpdocs/addNewAccount) 있고(로그인 필요) 그러면 귀하가 등록 계정의 소유자가 됩니다.
* 등록 계정의 기존 소유자가 [액세스 권한을 부여](grant-access-to-create-subscription.md)할 수 있습니다. 마찬가지로, EA 구독을 만들 서비스 주체를 사용하려면 [해당 서비스 주체에게 구독을 만들 수 있는 권한을 부여](grant-access-to-create-subscription.md)해야 합니다.

## <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

계정 소유자와 연결된 등록 계정에 귀하가 추가되면 Azure는 계정-등록 관계를 사용하여 구독 요금을 청구할 위치를 결정합니다. 계정에서 만든 모든 구독은 계정이 있는 EA 등록에 청구됩니다. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다.

다음 명령을 실행하려면 기본적으로 구독이 생성되는 디렉터리인 계정 소유자의 *홈 디렉터리* 에 로그인해야 합니다.

### <a name="rest"></a>[REST (영문)](#tab/rest-getEnrollments)

액세스 권한이 있는 모든 등록 계정을 나열하도록 요청합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

액세스 권한이 있는 모든 등록 계정이 API 응답에 나열됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

청구 범위와 `id`의 값은 동일합니다. 등록 계정의 `id`는 구독 요청이 시작되는 청구 범위입니다. 구독을 만들기 위해 문서의 뒷부분에서 사용하는 필수 매개 변수이므로 ID를 알고 있어야 합니다.

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기

다음 예에서는 이전 단계에서 선택한 등록 계정에 *Dev Team Subscription* 이라는 구독을 만듭니다. 

### <a name="rest"></a>[REST (영문)](#tab/rest-EA)

PUT API를 호출하여 구독 만들기 요청/별칭을 만듭니다.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

요청 본문에서, `enrollmentAccounts` 중 하나의 `billingScope` 및 `id`를 입력합니다.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

`Workload`에 허용되는 값은 `Production` 및 `DevTest`입니다.

#### <a name="response"></a>응답

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

동일한 URL에 대해 GET을 수행하여 요청 상태를 가져올 수 있습니다.

### <a name="request"></a>요청

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>응답

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

진행 중 상태는 `provisioningState` 아래에 `Accepted` 상태로 반환됩니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

`New-AzSubscriptionAlias` cmdlet을 포함하는 최신 버전의 모듈을 설치하려면 `Install-Module Az.Subscription`을 실행합니다. 최신 버전의 PowerShellGet을 설치하려면 [PowerShellGet 모듈 가져오기](/powershell/scripting/gallery/installing-psget)를 참조하세요.

청구 범위 `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`를 사용하여 다음 [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) 명령을 실행합니다. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
```

명령의 응답에 subscriptionId가 포함됩니다.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-EA)

먼저 `az extension add --name account` 및 `az extension add --name alias`를 실행하여 확장을 설치합니다.

다음 [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) 명령을 실행하고 `enrollmentAccounts` 중 하나의 `billing-scope` 및 `id`를 입력합니다. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

명령의 응답에 subscriptionId가 포함됩니다.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- 오직 Azure 엔터프라이즈 구독만이 이 API를 사용하여 생성됩니다.
- 등록 계정당 구독 수는 2000개로 제한됩니다. 그 후에는 Azure Portal에서만 계정에 대해 더 많은 구독을 만들 수 있습니다. API를 통해 더 많은 구독을 만들려면 또 다른 등록 계정을 만드세요. 취소, 삭제 및 전송된 구독은 2000개 제한에 포함됩니다.
- 계정 소유자가 아니지만 Azure RBAC를 통해 등록 계정에 추가된 사용자는 Azure Portal에서 구독을 만들 수 없습니다.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 여러 구독을 관리하는 방법에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.
