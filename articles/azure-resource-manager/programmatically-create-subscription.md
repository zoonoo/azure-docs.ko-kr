---
title: Programmatically create Azure subscriptions
description: Learn how to create additional Azure subscriptions programmatically.
author: amberb
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: f6c7ff0ade741b3a7e9552147dbac34fc131e622
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224204"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programmatically create Azure subscriptions (preview)

Azure customers with an [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) or [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) billing account can create subscriptions programmatically. 이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

When you create an Azure subscription programmatically, that subscription is governed by the agreement under which you obtained Azure services from Microsoft or an authorized reseller. 자세한 내용은 [Microsoft Azure 법적 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Create subscriptions for an EA billing account

### <a name="prerequisites"></a>전제 조건

You must have an Owner role on an Enrollment Account to create a subscription. There are two ways to get the role:

* The Enterprise Administrator of your enrollment can [make you an Account Owner](https://ea.azure.com/helpdocs/addNewAccount) (sign in required) which makes you an Owner of the Enrollment Account.

* 등록 계정의 기존 소유자가 [액세스 권한을 부여](grant-access-to-create-subscription.md)할 수 있습니다. Similarly, if you want to use a service principal to create an EA subscription, you must [grant that service principal the ability to create subscriptions](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

After you're added to an Enrollment Account associated to an Account Owner, Azure uses the account-to-enrollment relationship to determine where to bill the subscription charges. All subscriptions created under the account are billed to the EA enrollment that the account is in. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다. 

다음 명령을 실행하려면 기본적으로 구독이 생성되는 디렉터리인 계정 소유자의 *홈 디렉터리*에 로그인해야 합니다.

### <a name="resttabrest"></a>[REST](#tab/rest)

Request to list all enrollment accounts you have access to:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

The API response lists all enrollment accounts you have access to:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. Copy the `name` of that account. For example, if you wanted to create subscriptions under the SignUpEngineering@contoso.com enrollment account, you'd copy ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. This identifier is the object ID of the enrollment account. Paste this value somewhere so that you can use it in the next step as `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Open [Azure Cloud Shell](https://shell.azure.com/) and select PowerShell.

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet을 사용하여 액세스 권한이 있는 모든 등록 계정을 나열합니다.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure responds with a list of enrollment accounts you have access to:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. Copy the `ObjectId` of that account. For example, if you wanted to create subscriptions under the SignUpEngineering@contoso.com enrollment account, you'd copy ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Paste this object ID somewhere so that you can use it in the next step as the `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 명령을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다.

```azurecli-interactive 
az billing enrollment-account list
```

Azure responds with a list of enrollment accounts you have access to:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. Copy the `name` of that account. For example, if you wanted to create subscriptions under the SignUpEngineering@contoso.com enrollment account, you'd copy ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. This identifier is the object ID of the enrollment account. Paste this value somewhere so that you can use it in the next step as `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기

The following example creates a subscription named *Dev Team Subscription* in the enrollment account selected in the previous step. The subscription offer is *MS-AZR-0017P* (regular Microsoft Enterprise Agreement). 또한 구독에 대한 RBAC 소유자로 두 명의 사용자를 선택적으로 추가합니다.

### <a name="resttabrest"></a>[REST](#tab/rest)

`<enrollmentAccountObjectId>`를 첫 번째 단계(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다. If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 요소 이름  | 필수 | Type   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 아닙니다.      | string | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offerType`   | yes      | string | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `owners`      | 아닙니다.       | string | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |

응답에서 모니터링에 대한 `subscriptionOperation` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionOperation` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

First, install this preview module by running `Install-Module Az.Subscription -AllowPrerelease`. `-AllowPrerelease`가 작동하는지 확인하려면 [Get PowerShellGet Module](/powershell/scripting/gallery/installing-psget)에서 최신 버전의 PowerShellGet을 설치합니다.

Run the [New-AzSubscription](/powershell/module/az.subscription) command below, replacing `<enrollmentAccountObjectId>` with the `ObjectId` collected in the first step (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 요소 이름  | 필수 | Type   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 아닙니다.      | string | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `OfferType`   | yes      | string | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `EnrollmentAccountObjectId`      | yes       | string | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `Get-AzEnrollmentAccount`에서 가져온 GUID입니다. |
| `OwnerObjectId`      | 아닙니다.       | string | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `OwnerSignInName`    | 아닙니다.       | string | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다.|
| `OwnerApplicationId` | 아닙니다.       | string | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.| 

모든 매개 변수의 전체 목록을 보려면 [New-AzSubscription](/powershell/module/az.subscription)을 참조하세요.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

First, install this preview extension by running `az extension add --name subscription`.

Run the [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) command below, replacing `<enrollmentAccountObjectId>` with the `name` you copied in the first step (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 요소 이름  | 필수 | Type   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 아닙니다.      | string | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offer-type`   | yes      | string | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `enrollment-account-object-id`      | yes       | string | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `az billing enrollment-account list`에서 가져온 GUID입니다. |
| `owner-object-id`      | 아닙니다.       | string | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `owner-upn`    | 아닙니다.       | string | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다.|
| `owner-spn` | 아닙니다.       | string | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.| 

모든 매개 변수의 전체 목록을 보려면 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)를 참조하세요.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- 이 API를 사용하여 Azure 엔터프라이즈 구독을 만들 수 있습니다.
- There's a limit of 200 subscriptions per enrollment account. After that, more subscriptions for the account can only be created in the Azure portal. If you want to create more subscriptions through the API, create another enrollment account.
- Users who aren't Account Owners, but were added to an enrollment account via RBAC, can't create subscriptions in the Azure portal.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.


## <a name="create-subscriptions-for-an-mca-account"></a>Create subscriptions for an MCA account

### <a name="prerequisites"></a>전제 조건

You must have an owner, contributor, or Azure subscription creator role on an invoice section or owner or contributor role on a billing profile or a billing account to create subscriptions. 자세한 내용은 [구독 청구 역할 및 작업](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)을 참조하세요.

The example shown below use REST APIs. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Find billing accounts that you have access to 

Make the request below to list all the billing accounts.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
The API response lists the billing accounts that you have access to.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use the `displayName` property to identify the billing account for which you want to create subscriptions. Ensure, the agreeementType of the account is *MicrosoftCustomerAgreement*. Copy the `name` of the account.  For example, if you want to create a subscription for the `Contoso` billing account, you'd copy `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Find invoice sections to create subscriptions

The charges for your subscription appear on a section of a billing profile's invoice. Use the following API to get the list of invoice sections and billing profiles on which you have permission to create Azure subscriptions.

`<billingAccountName>`를 첫 번째 단계(```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
The API response lists all the invoice sections and their billing profiles on which you have access to create subscriptions:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}
    
```

Use the `invoiceSectionDisplayName` property to identify the invoice section for which you want to create subscriptions. Copy the `invoiceSectionId`, `billingProfileId` and one of the `skuId` for the invoice section. For example, if you want to create a subscription of type `Microsoft Azure plan` for `Development` invoice section, you'd copy `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` , and `0001`. Paste these values somewhere so that you can use them in the next step.

### <a name="create-a-subscription-for-an-invoice-section"></a>Create a subscription for an invoice section

The following example creates a subscription named *Dev Team subscription* of type *Microsoft Azure Plan* for the *Development* invoice section. The subscription will be billed to the *Contoso finance's* billing profile and appear on the *Development* section of its invoice. 

Make the following request, replacing `<invoiceSectionId>` with the `invoiceSectionId` copied from the second step (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). You'd need to pass the `billingProfileId` and `skuId` copied from the second step in the request parameters of the API. If you'd like to specify owners, learn [how to get user object IDs](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| 요소 이름  | 필수 | Type   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | yes      | string | 구독의 표시 이름입니다.|
| `billingProfileId`   | yes      | string | The ID of the billing profile that will be billed for the subscription's charges.  |
| `skuId` | yes      | string | The sku ID that determines the type of Azure plan. |
| `owners`      | 아닙니다.       | string | The Object ID of any user or service principal that you'd like to add as an RBAC Owner on the subscription when it's created.  |
| `costCenter` | 아닙니다.      | string | The cost center associated with the subscription. It shows up in the usage csv file. |
| `managementGroupId` | 아닙니다.      | string | The ID of the management group to which the subscription will be added. To get the list of management groups, see [Management Groups - List API](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Use the ID of a management group from the API. |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Create subscriptions for an MPA billing account

### <a name="prerequisites"></a>전제 조건

You must have a Global Admin or  Admin Agent role in your organization's cloud solution provider account to create subscription for your billing account. For more information, see [Partner Center - Assign users roles and permissions](https://docs.microsoft.com/partner-center/permissions-overview).

The example shown below use REST APIs. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Find the billing accounts that you have access to 

Make the request below to list all billing accounts that you have access to.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
The API response list the billing accounts.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use the `displayName` property to identify the billing account for which you want to create subscriptions. Ensure, the agreeementType of the account is *MicrosoftPartnerAgreement*. Copy the `name` for the account. For example, if you want to create a subscription for the `Contoso` billing account, you'd copy `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="find-customers-that-have-azure-plans"></a>Find customers that have Azure plans

Make the following request, replacing `<billingAccountName>` with the `name` copied from the first step (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) to list all customers in the billing account for whom you can create Azure subscriptions. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
The API response lists the customers in the billing account with Azure plans. You can create subscriptions for these customers.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}
    
```

Use the `displayName` property to identify the customer for which you want to create subscriptions. Copy the `id` for the customer. For example, if you want to create a subscription for `Fabrikam toys`, you'd copy `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Paste this value somewhere to use it in the subsequent steps.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Optional for Indirect providers: Get the resellers for a customer

If you're an Indirect provider in the CSP two-tier model, you can specify a reseller while creating subscriptions for customers. 

Make the following request, replacing `<customerId>` with the `id` copied from the second step (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) to list all resellers that are available for a customer.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
The API response lists the resellers for the customer:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Use the `description` property to identify the reseller who will be associated with the subscription. Copy the `resellerId` for the reseller. For example, if you want to associate `Wingtip`, you'd copy `3xxxxx`. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="create-a-subscription-for-a-customer"></a>Create a subscription for a customer

The following example creates a subscription named *Dev Team subscription*  for *Fabrikam toys* and associate *Wingtip* reseller to the subscription. 조

`<customerId>`를 첫 번째 단계(```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다. Pass the optional *resellerId* copied from the second step in the request parameters of the API. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| 요소 이름  | 필수 | Type   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | yes      | string | 구독의 표시 이름입니다.|
| `skuId` | yes      | string | The sku ID of the Azure plan. Use *0001* for subscriptions of type Microsoft Azure Plan |
| `resellerId`      | 아닙니다.       | string | The MPN ID of the reseller who will be associated with the subscription.  |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

## <a name="next-steps"></a>다음 단계

* For an example on creating an Enterprise Agreement (EA) subscription using .NET, see [sample code on GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독 관리에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](management-groups-overview.md)을 참조하세요.
