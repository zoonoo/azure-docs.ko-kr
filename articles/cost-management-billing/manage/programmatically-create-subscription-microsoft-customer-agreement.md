---
title: 최신 API를 사용하여 프로그래밍 방식으로 Microsoft 고객 계약에 대한 Azure 구독 만들기
description: 최신 버전의 REST API, Azure CLI 및 Azure PowerShell을 사용하여 프로그래밍 방식으로 Microsoft 고객 계약에 대한 Azure 구독을 만드는 방법을 알아봅니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2b0eaef7940b84dbd1e1325b5ae3bfb7b65dcef6
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200566"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>최신 API를 사용하여 프로그래밍 방식으로 Microsoft 고객 계약에 대한 Azure 구독 만들기

이 문서는 최신 API 버전을 사용하여 프로그래밍 방식으로 Microsoft 고객 계약에 대한 Azure 구독을 만드는 데 도움이 됩니다. 이전 미리 보기 버전을 여전히 사용 중인 경우 [미리 보기 API를 사용하여 프로그래밍 방식으로 Azure 구독 만들기](programmatically-create-subscription-preview.md)를 참조하세요. 

이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

프로그래밍 방식으로 Azure 구독을 만든 경우 해당 구독은 Microsoft 또는 공인된 재판매인으로부터 Azure 서비스를 구입한 계약이 적용됩니다. 자세한 내용은 [Microsoft Azure 법률 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 청구서 섹션에 대한 소유자, 기여자 또는 Azure 구독 생성자 역할이 있거나 청구 프로필에 대한 소유자나 기여자 역할 또는 청구 계정이 있어야 합니다. 자세한 내용은 [구독 청구 역할 및 작업](understand-mca-roles.md#subscription-billing-roles-and-tasks)을 참조하세요.

Microsoft 고객 계약 계정에 대한 액세스 권한이 있는지 여부를 모르는 경우 [Microsoft 고객 계약에 대한 액세스 권한 확인](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement)을 참조하세요.

다음 예제에서는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

## <a name="find-billing-accounts-that-you-have-access-to"></a>액세스 권한이 있는 청구 계정 찾기

모든 청구 계정을 나열하는 다음 요청을 만듭니다.

### <a name="rest"></a>[REST (영문)](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
액세스 권한이 있는 청구 계정이 API 응답에 나열됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

`displayName` 속성을 사용하여 구독을 생성하려는 청구 계정을 식별합니다. 계정의 agreementType이 *MicrosoftCustomerAgreement* 인지 확인합니다. 계정의 `name`을 복사합니다.  예를 들어 청구 계정 `Contoso`에 대한 구독을 만들려면 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>구독을 만들 청구 프로필 및 청구서 섹션 찾기

구독에 대한 요금은 청구 프로필 청구서의 섹션에 표시됩니다. 다음 API를 사용하여 Azure 구독을 만들 권한이 있는 청구 프로필 및 청구서 섹션 목록을 가져옵니다.

먼저 액세스 권한이 있는 청구 계정의 청구 프로필 목록을 가져옵니다.

### <a name="rest"></a>[REST (영문)](#tab/rest-getBillingProfiles)
```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
구독을 만들 액세스 권한이 있는 모든 청구 프로필이 API 응답에 나열됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 다음에 청구 프로필 아래의 청구서 섹션을 확인할 수 있도록 `id`를 복사합니다. 예를 들어 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx`를 복사하고 다음 API를 호출합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>응답

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

`id` 속성을 사용하여 구독을 만들 청구서 섹션을 식별할 수 있습니다. 전체 문자열을 복사합니다. 예: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx` 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-an-invoice-section"></a>청구서 섹션에 대한 구독 만들기

다음 예제에서는 *Development* 청구서 섹션에 대한 *Dev Team subscription* 이라는 구독을 만듭니다. 이 구독은 *Contoso 청구 프로필* 로 요금이 청구되며 청구서의 *Development* 섹션에 표시됩니다. 이전 단계에서 복사한 청구 범위 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`를 사용합니다. 

### <a name="rest"></a>[REST (영문)](#tab/rest-MCA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>요청 본문

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```

### <a name="response"></a>응답

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MCA)

`New-AzSubscriptionAlias` cmdlet을 포함하는 최신 버전의 모듈을 설치하려면 `Install-Module Az.Subscription`을 실행합니다. 최신 버전의 PowerShellGet을 설치하려면 [PowerShellGet 모듈 가져오기](/powershell/scripting/gallery/installing-psget)를 참조하세요.

다음 [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) 명령 및 청구 범위 `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`를 실행합니다. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
```

명령의 응답에 subscriptionId가 포함됩니다.

```azurepowershell
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MCA)

먼저 `az extension add --name account` 및 `az extension add --name alias`를 실행하여 확장을 설치합니다.

[az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create) 명령을 실행합니다.

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
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

## <a name="next-steps"></a>다음 단계

* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 여러 구독을 관리하는 방법에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.
