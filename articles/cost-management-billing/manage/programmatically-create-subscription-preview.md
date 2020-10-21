---
title: 미리 보기 API를 사용하여 프로그래밍 방식으로 Azure 구독 만들기
description: 미리 보기 버전의 REST API, Azure CLI 및 Azure PowerShell을 사용하여 추가 Azure 구독을 프로그래밍 방식으로 만드는 방법을 알아봅니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c63733a66a2bb4e320a24649dfe82eac259e79ae
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131108"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>미리 보기 API를 사용하여 프로그래밍 방식으로 Azure 구독 만들기

이 문서는 이전 미리 보기 API를 사용하여 Azure 구독을 프로그래밍 방식으로 만드는 데 도움이 됩니다. [최신 API 버전](programmatically-create-subscription.md)이 릴리스되었습니다. 최신 버전을 사용하지 않으려면 이 문서의 정보를 사용할 수 있습니다. 이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

다음 계약 유형의 청구 계정이 있는 Azure 고객은 구독을 프로그래밍 방식으로 만들 수 있습니다.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [MCA(Microsoft 고객 계약)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [MPA(Microsoft 파트너 계약)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement)

Azure 구독을 프로그래밍 방식으로 만드는 경우 해당 구독에는 Microsoft 또는 공인 재판매인으로부터 Azure 서비스를 구입한 계약이 적용됩니다. 자세한 내용은 [Microsoft Azure 법적 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>EA 청구 계정에 대한 구독 만들기

다음 섹션의 정보를 사용하여 EA 구독을 만들 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 등록 계정에 대한 소유자 역할이 있어야 합니다. 역할을 얻는 방법은 두 가지입니다.

* 등록의 엔터프라이즈 관리자는 사용자를 등록 계정의 소유자가 되는 [계정 소유자](https://ea.azure.com/helpdocs/addNewAccount)(로그인 필요)로 만들 수 있습니다.
* 등록 계정의 기존 소유자가 [액세스 권한을 부여](grant-access-to-create-subscription.md)할 수 있습니다. 마찬가지로, 서비스 주체를 사용하여 EA 구독을 만들려면 [해당 서비스 주체에게 구독을 만들 수 있는 권한을 부여](grant-access-to-create-subscription.md)해야 합니다.

### <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

계정 소유자와 연결된 등록 계정에 귀하가 추가되면 Azure는 계정-등록 관계를 사용하여 구독 요금을 청구할 위치를 결정합니다. 계정에서 만든 모든 구독은 계정이 있는 EA 등록에 청구됩니다. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다.

다음 명령을 실행하려면 구독이 기본적으로 만들어지는 디렉터리인 계정 소유자의 *홈 디렉터리*에 로그인해야 합니다.

### <a name="rest"></a>[REST (영문)](#tab/rest)

액세스 권한이 있는 모든 등록 계정을 나열하도록 요청합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

액세스 권한이 있는 모든 등록 계정이 API 응답에 나열됩니다.

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

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 해당 계정의 `name`을 복사합니다. 예를 들어 구독을 SignUpEngineering@contoso.com 등록 계정 아래에 만들고, ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사합니다. 이 식별자는 등록 계정의 개체 ID입니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Azure Cloud Shell](https://shell.azure.com/)을 열고 PowerShell을 선택합니다.

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet을 사용하여 액세스 권한이 있는 모든 등록 계정을 나열합니다.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

액세스 권한이 있는 등록 계정 목록이 Azure 응답에 표시됩니다.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 해당 계정의 `ObjectId`을 복사합니다. 예를 들어 구독을 SignUpEngineering@contoso.com 등록 계정 아래에 만들려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사합니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록 이 개체 ID를 어딘가에 붙여넣습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](/cli/azure/billing) 명령을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다.

```azurecli-interactive
az billing enrollment-account list
```

액세스 권한이 있는 등록 계정 목록이 Azure 응답에 표시됩니다.

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

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 해당 계정의 `name`을 복사합니다. 예를 들어 구독을 SignUpEngineering@contoso.com 등록 계정 아래에 만들려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사합니다. 이 식별자는 등록 계정의 개체 ID입니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기

다음 예에서는 이전 단계에서 선택한 등록 계정에 *Dev Team Subscription*이라는 구독을 만듭니다. 구독 제안은 *MS-AZR-0017P*(일반 Microsoft 기업계약)입니다. 또한 구독에 대한 Azure RBAC 소유자로 두 명의 사용자를 선택적으로 추가합니다.

### <a name="rest"></a>[REST (영문)](#tab/rest)

`<enrollmentAccountObjectId>`를 첫 번째 단계(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다. 소유자를 지정하려면 [사용자 개체 ID를 가져오는 방법](grant-access-to-create-subscription.md#userObjectId)을 참조하세요.

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
| `displayName` | 예      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offerType`   | 예      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `owners`      | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가하려는 사용자의 개체 ID입니다.  |

응답에서 헤더 `Location`의 일부로 구독 만들기 작업의 상태를 쿼리할 수 있는 URL이 반환됩니다. 구독 만들기가 완료되면 `Location` URL의 GET은 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다. 자세한 내용은 [구독 API 설명서](/rest/api/subscription/)를 참조하세요.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`New-AzSubscription` cmdlet을 포함하는 최신 버전의 모듈을 설치하려면 `Install-Module Az.Subscription`을 실행합니다. 최신 버전의 PowerShellGet을 설치하려면 [PowerShellGet 모듈 가져오기](/powershell/scripting/gallery/installing-psget)를 참조하세요.

아래 [New-AzSubscription](/powershell/module/az.subscription) 명령에서 `<enrollmentAccountObjectId>`를 첫 번째 단계에서 수집한 `ObjectId`(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)로 바꿔서 실행합니다. 소유자를 지정하려면 [사용자 개체 ID를 가져오는 방법](grant-access-to-create-subscription.md#userObjectId)을 참조하세요.

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 요소 이름  | 필수 | Type   | 설명 |
|---------------|----------|--------|----|
| `Name` | 예      | String | 구독의 표시 이름입니다. 지정하지 않으면 제안의 이름(예: *Microsoft Azure 엔터프라이즈*)으로 설정됩니다. |
| `OfferType`   | 예      | String | 구독 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `EnrollmentAccountObjectId`      | 예       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 값은 `Get-AzEnrollmentAccount`에서 가져오는 GUID입니다. |
| `OwnerObjectId`      | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가할 사용자의 개체 ID입니다.  |
| `OwnerSignInName`    | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가할 사용자의 이메일 주소입니다. `OwnerObjectId` 대신 매개 변수를 사용할 수 있습니다.|
| `OwnerApplicationId` | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가할 서비스 주체의 애플리케이션 ID입니다. `OwnerObjectId` 대신 매개 변수를 사용할 수 있습니다. 매개 변수를 사용하는 경우 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true)이 서비스 주체에 있어야 합니다.|

모든 매개 변수의 전체 목록을 보려면 [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription)을 참조하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 `az extension add --name subscription`을 실행하여 미리 보기 확장을 설치합니다.

아래 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) 명령에서 `<enrollmentAccountObjectId>`를 첫 번째 단계에서 복사한 `name`(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)으로 바꿔서 실행합니다. 소유자를 지정하려면 [사용자 개체 ID를 가져오는 방법](grant-access-to-create-subscription.md#userObjectId)을 참조하세요.

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 요소 이름  | 필수 | Type   | 설명 |
|---------------|----------|--------|------------|
| `display-name` | 예      | String | 구독의 표시 이름입니다. 지정하지 않으면 제안의 이름(예: *Microsoft Azure 엔터프라이즈*)으로 설정됩니다.|
| `offer-type`   | 예      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `enrollment-account-object-id`      | 예       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 값은 `az billing enrollment-account list`에서 가져오는 GUID입니다. |
| `owner-object-id`      | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가할 사용자의 개체 ID입니다.  |
| `owner-upn`    | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가할 사용자의 이메일 주소입니다. `owner-object-id` 대신 매개 변수를 사용할 수 있습니다.|
| `owner-spn` | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가할 서비스 주체의 애플리케이션 ID입니다. `owner-object-id` 대신 매개 변수를 사용할 수 있습니다. 매개 변수를 사용하는 경우 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true)이 서비스 주체에 있어야 합니다.|

모든 매개 변수의 전체 목록을 보려면 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true)를 참조하세요.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- Azure 엔터프라이즈 구독만 API를 사용하여 만들 수 있습니다.
- 등록 계정당 구독 수는 2000개로 제한됩니다. 그 후에는 Azure Portal에서만 계정에 대해 더 많은 구독을 만들 수 있습니다. API를 통해 더 많은 구독을 만들려면 또 다른 등록 계정을 만드세요.
- 계정 소유자가 아니지만 Azure RBAC를 사용하여 등록 계정에 추가된 사용자는 Azure Portal에서 구독을 만들 수 없습니다.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.


## <a name="create-subscriptions-for-an-mca-account"></a>MCA 계정에 대한 구독 만들기

다음 섹션의 정보를 사용하여 MCA 계정에 대한 구독을 만듭니다.

### <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 청구서 섹션에 대한 소유자, 기여자 또는 Azure 구독 생성자 역할이 있거나 청구 프로필에 대한 소유자나 기여자 역할 또는 청구 계정이 있어야 합니다. 자세한 내용은 [구독 청구 역할 및 작업](understand-mca-roles.md#subscription-billing-roles-and-tasks)을 참조하세요.

다음 예제에서는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-billing-accounts-that-you-have-access-to"></a>액세스 권한이 있는 청구 계정 찾기

다음 요청을 수행하여 모든 청구 계정을 나열합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
액세스 권한이 있는 청구 계정이 API 응답에 나열됩니다.

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
`displayName` 속성을 사용하여 구독을 생성하려는 청구 계정을 식별합니다. 계정의 agreementType이 *MicrosoftCustomerAgreement*인지 확인합니다. 계정의 `name`을 복사합니다.  예를 들어 `Contoso` 청구 계정에 대한 구독을 만들려면 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="find-invoice-sections-to-create-subscriptions"></a>구독을 만들 청구서 섹션 찾기

구독에 대한 요금은 청구 프로필 청구서의 섹션에 표시됩니다. 다음 API를 사용하여 Azure 구독을 만들 수 있는 권한이 있는 청구서 섹션 및 청구 프로필 목록을 가져옵니다.

`<billingAccountName>`를 첫 번째 단계(```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

구독을 만들 수 있는 액세스 권한이 있는 모든 청구서 섹션과 해당 청구 프로필이 API 응답에 나열됩니다.

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

`invoiceSectionDisplayName` 속성을 사용하여 구독을 만들 청구서 섹션을 식별할 수 있습니다. `invoiceSectionId`, `billingProfileId` 및 청구서 섹션에 대한 `skuId` 중 하나를 복사합니다. 예를 들어 `Development` 청구서 섹션에 대한 `Microsoft Azure plan` 형식의 구독을 만들려면 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` 및 `0001`을 복사합니다. 다음 단계에서 사용할 수 있도록 이러한 값을 어딘가에 붙여넣습니다.

### <a name="create-a-subscription-for-an-invoice-section"></a>청구서 섹션에 대한 구독 만들기

다음 예에서는 *Development* 청구서 섹션에 대해 *Microsoft Azure Plan* 형식의 *Dev Team subscription*이라는 구독을 만듭니다. 이 구독은 *Contoso finance*의 청구 프로필로 청구되며, 해당 청구서의 *Development* 섹션에 표시됩니다.

`<invoiceSectionId>`를 두 번째 단계(```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```)에서 복사한 `invoiceSectionId`로 바꿔서 다음 요청을 수행합니다. API의 요청 매개 변수의 두 번째 단계에서 복사한 `billingProfileId` 및 `skuId`를 전달합니다. 소유자를 지정하려면 [사용자 개체 ID를 가져오는 방법](grant-access-to-create-subscription.md#userObjectId)을 참조하세요.

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
| `displayName` | 예      | String | 구독의 표시 이름입니다.|
| `billingProfileId`   | 예      | String | 구독 요금으로 청구되는 청구 프로필의 ID입니다.  |
| `skuId` | 예      | String | Azure 플랜의 형식을 결정하는 SKU ID입니다. |
| `owners`      | 예       | String | 구독을 만들 때 Azure RBAC 소유자로 추가할 사용자 또는 서비스 주체의 개체 ID입니다.  |
| `costCenter` | 예      | String | 구독과 연결된 비용 센터입니다. 이는 사용량 CSV 파일에 표시됩니다. |
| `managementGroupId` | 예      | String | 구독이 추가될 관리 그룹의 ID입니다. 관리 그룹 목록을 가져오려면 [관리 그룹 - 목록 API](/rest/api/resources/managementgroups/list)를 참조하세요. API에서 관리 그룹의 ID를 사용합니다. |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체에서 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>MPA 청구 계정에 대한 구독 만들기

다음 섹션의 정보를 사용하여 MPA 청구 계정에 대한 구독을 만듭니다.

### <a name="prerequisites"></a>필수 구성 요소

청구 계정에 대한 구독을 만들려면 조직의 클라우드 솔루션 공급자 계정에 전역 관리자 또는 관리 에이전트 역할이 있어야 합니다. 자세한 내용은 [파트너 센터 - 사용자 역할 및 사용 권한 할당](/partner-center/permissions-overview)을 참조하세요.

다음 예제에서는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>액세스 권한이 있는 청구 계정 찾기

아래 요청을 통해 액세스 권한이 있는 모든 청구 계정을 나열합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

API 응답에서 청구 계정을 나열합니다.

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
`displayName` 속성을 사용하여 구독을 생성하려는 청구 계정을 식별합니다. 계정의 agreementType이 *MicrosoftPartnerAgreement*인지 확인합니다. 계정의 `name`을 복사합니다. 예를 들어 `Contoso` 청구 계정에 대한 구독을 만들려면 `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="find-customers-that-have-azure-plans"></a>Azure 플랜이 있는 고객 찾기

`<billingAccountName>`을 첫 번째 단계(```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)에서 복사한 `name`으로 바꿔서 다음 요청을 수행하고, Azure 구독을 만들 수 있는 청구 계정의 모든 고객을 나열합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Azure 플랜이 있는 청구 계정의 고객이 API 응답에 나열됩니다. 고객에 대한 구독을 만들 수 있습니다.

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

`displayName` 속성을 사용하여 구독을 만들 고객을 식별할 수 있습니다. 고객의 `id`를 검색합니다. 예를 들어 `Fabrikam toys`에 대한 구독을 만들려면 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`를 복사합니다. 이후 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>간접 공급자를 위한 선택 사항: 고객을 위한 재판매인 확보

CSP 2계층 모델의 간접 공급자인 경우, 고객에 대한 구독을 만드는 동안 재판매인을 지정할 수 있습니다.

`<customerId>`을 두 번째 단계(```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행하고 고객이 사용할 수 있는 재판매인을 모두 나열합니다.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
고객을 위한 재판매인이 API 응답에 나열됩니다.

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

`description` 속성을 사용하여 구독과 연결할 재판매인을 식별합니다. 재판매인의 `resellerId`를 검색합니다. 예를 들어 `Wingtip`을 연결하려면 `3xxxxx`를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="create-a-subscription-for-a-customer"></a>고객을 위한 구독 만들기

다음 예제에서는 *Fabrikam toys*에 대한 *Dev Team subscription*이라는 구독을 만들고, *Wingtip* 재판매인을 구독에 연결합니다. 

`<customerId>`를 두 번째 단계(```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다. API 요청 매개 변수의 두 번째 단계에서 복사한 선택적 *resellerId*를 전달합니다.

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
| `displayName` | 예      | String | 구독의 표시 이름입니다.|
| `skuId` | 예      | String | Azure 플랜의 sku ID입니다. Microsoft Azure 플랜 유형의 구독에 *0001*을 사용합니다. |
| `resellerId`      | 예       | String | 구독과 연결될 재판매인의 MPN ID입니다.  |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체에서 `subscriptionLink` 개체를 반환합니다. 구독 ID가 있습니다.

## <a name="next-steps"></a>다음 단계

- .NET을 사용하여 EA(기업계약) 구독을 만드는 방법에 대한 예제는 [GitHub의 샘플 코드](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)를 참조하세요.
* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독을 관리하는 방법에 대한 자세한 내용은 [Azure 관리 그룹을 사용하여 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.