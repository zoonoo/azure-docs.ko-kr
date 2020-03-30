---
title: 프로그래밍 방식으로 Azure 구독 만들기
description: 프로그래밍 방식으로 추가 Azure 구독을 만드는 방법에 대해 알아봅니다.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460401"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>프로그래밍 방식으로 Azure 구독 만들기(미리 보기)

[EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)및 [Microsoft 고객 계약(MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) 또는 [MPA(Microsoft 파트너 계약)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) 청구 계정을 보유한 Azure 고객은 프로그래밍 방식으로 구독을 만들 수 있습니다. 이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

프로그래밍 방식으로 Azure 구독을 만들 때 해당 구독은 Microsoft 또는 공인 리셀러로부터 Azure 서비스를 획득한 계약의 적용을 받습니다. 자세한 내용은 [Microsoft Azure 법적 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>EA 결제 계정에 대한 구독 만들기

다음 섹션의 정보를 사용하여 EA 구독을 만듭니다.

### <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 등록 계정에 소유자 역할이 있어야 합니다. 역할을 얻는 방법에는 두 가지가 있습니다.

* 등록의 엔터프라이즈 관리자는 [계정 소유자(로그인](https://ea.azure.com/helpdocs/addNewAccount) 필수)를 만들 수 있으므로 등록 계정의 소유자가 될 수 있습니다.

* 등록 계정의 기존 소유자가 [액세스 권한을 부여](grant-access-to-create-subscription.md)할 수 있습니다. 마찬가지로 서비스 주체를 사용하여 EA 구독을 만들려면 [해당 서비스 주체에게 구독을 만들 수 있는 권한을 부여해야](grant-access-to-create-subscription.md)합니다.

### <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

계정 소유자와 연결된 등록 계정에 추가된 후 Azure는 계정 간 관계를 사용하여 구독 요금을 청구할 위치를 결정합니다. 계정에서 생성된 모든 구독은 계정이 있는 EA 등록에 청구됩니다. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다.

다음 명령을 실행하려면 기본적으로 구독이 생성되는 디렉터리인 계정 소유자의 *홈 디렉터리*에 로그인해야 합니다.

### <a name="rest"></a>[나머지](#tab/rest)

액세스할 수 있는 모든 등록 계정을 나열하도록 요청합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API 응답에는 액세스할 수 있는 모든 등록 계정이 나열됩니다.

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

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. `name` 해당 계정을 복사합니다. 예를 들어 등록 계정으로 구독을 SignUpEngineering@contoso.com 만들려는 경우 를 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```복사합니다. 이 식별자는 등록 계정의 개체 ID입니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 `enrollmentAccountObjectId`붙여넣습니다.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[Azure 클라우드 셸을](https://shell.azure.com/) 열고 PowerShell을 선택합니다.

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet을 사용하여 액세스 권한이 있는 모든 등록 계정을 나열합니다.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure는 액세스할 수 있는 등록 계정 목록으로 응답합니다.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. `ObjectId` 해당 계정을 복사합니다. 예를 들어 등록 계정으로 구독을 SignUpEngineering@contoso.com 만들려는 경우 를 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```복사합니다. 다음 단계에서 을 로 사용할 수 있도록 이 개체 `enrollmentAccountObjectId`ID를 어딘가에 붙여넣습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 명령을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다.

```azurecli-interactive
az billing enrollment-account list
```

Azure는 액세스할 수 있는 등록 계정 목록으로 응답합니다.

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

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. `name` 해당 계정을 복사합니다. 예를 들어 등록 계정으로 구독을 SignUpEngineering@contoso.com 만들려는 경우 를 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```복사합니다. 이 식별자는 등록 계정의 개체 ID입니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 `enrollmentAccountObjectId`붙여넣습니다.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기

다음 예제에서는 이전 단계에서 선택한 등록 계정에서 *개발자 팀 구독이라는* 구독을 만듭니다. 구독 제안은 *MS-AZR-0017P(일반* Microsoft 기업 계약)입니다. 또한 구독에 대한 RBAC 소유자로 두 명의 사용자를 선택적으로 추가합니다.

### <a name="rest"></a>[나머지](#tab/rest)

`<enrollmentAccountObjectId>`를 첫 번째 단계(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다. 소유자를 지정하려면 사용자 개체 [를 얻는 방법을](grant-access-to-create-subscription.md#userObjectId)알아봅니다.

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

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 예      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offerType`   | yes      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `owners`      | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |

응답에서 모니터링에 대한 `subscriptionOperation` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionOperation` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

먼저 을 실행하여 `Install-Module Az.Subscription -AllowPrerelease`이 미리 보기 모듈을 설치합니다. `-AllowPrerelease`가 작동하는지 확인하려면 [Get PowerShellGet Module](/powershell/scripting/gallery/installing-psget)에서 최신 버전의 PowerShellGet을 설치합니다.

아래의 [New-AzSubscription](/powershell/module/az.subscription) 명령을 실행하여 `ObjectId` 첫 번째 단계()에서```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```수집된 것으로 대체합니다. `<enrollmentAccountObjectId>` 소유자를 지정하려면 사용자 개체 [를 얻는 방법을](grant-access-to-create-subscription.md#userObjectId)알아봅니다.

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 예      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `OfferType`   | yes      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `EnrollmentAccountObjectId`      | yes       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `Get-AzEnrollmentAccount`에서 가져온 GUID입니다. |
| `OwnerObjectId`      | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `OwnerSignInName`    | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다.|
| `OwnerApplicationId` | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.|

모든 매개 변수의 전체 목록을 보려면 [New-AzSubscription](/powershell/module/az.subscription)을 참조하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 을 실행하여 `az extension add --name subscription`이 미리 보기 확장을 설치합니다.

az [계정 만들기](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) 명령을 아래 `<enrollmentAccountObjectId>` 실행 `name` 하 고 첫 번째```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```단계 ()에서 복사 한 것으로 대체 합니다. 소유자를 지정하려면 사용자 개체 [를 얻는 방법을](grant-access-to-create-subscription.md#userObjectId)알아봅니다.

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 예      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offer-type`   | yes      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `enrollment-account-object-id`      | yes       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `az billing enrollment-account list`에서 가져온 GUID입니다. |
| `owner-object-id`      | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `owner-upn`    | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다.|
| `owner-spn` | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.|

모든 매개 변수의 전체 목록을 보려면 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)를 참조하세요.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- 이 API를 사용하여 Azure 엔터프라이즈 구독을 만들 수 있습니다.
- 등록 계정당 구독은 500개로 제한됩니다. 그런 다음 Azure 포털에서만 계정에 대한 더 많은 구독을 만들 수 있습니다. API를 통해 더 많은 구독을 만들려면 다른 등록 계정을 만듭니다.
- 계정 소유자는 아니지만 RBAC를 통해 등록 계정에 추가된 사용자는 Azure Portal에서 구독을 만들 수 없습니다.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.


## <a name="create-subscriptions-for-an-mca-account"></a>MCA 계정에 대한 구독 만들기

### <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 청구 프로필 또는 청구 계정의 송장 섹션 또는 소유자 또는 기여자 역할에 소유자, 기여자 또는 Azure 구독 작성자 역할이 있어야 합니다. 자세한 내용은 [구독 청구 역할 및 작업](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks)을 참조하세요.

아래 표시된 예제는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-billing-accounts-that-you-have-access-to"></a>액세스할 수 있는 결제 계정 찾기

모든 청구 계정을 나열하려면 아래요청을 합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 응답에는 액세스 권한이 있는 청구 계정이 나열됩니다.

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
속성을 `displayName` 사용하여 구독을 만들 려는 청구 계정을 식별합니다. 계정의 동의 유형이 *MicrosoftCustomerAgreement입니다.* 계정의 `name` 복사를 합니다.  예를 들어 `Contoso` 청구 계정에 대한 구독을 만들려면 을 복사합니다. `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="find-invoice-sections-to-create-subscriptions"></a>구독을 만들 송장 섹션 찾기

구독 요금은 청구 프로필의 인보이스 섹션에 표시됩니다. 다음 API를 사용하여 Azure 구독을 만들 수 있는 권한이 있는 송장 섹션 및 청구 프로필 목록을 가져옵니다.

`<billingAccountName>`를 첫 번째 단계(```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API 응답에는 구독을 만들 수 있는 모든 송장 섹션과 해당 청구 프로필이 나열됩니다.

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

속성을 `invoiceSectionDisplayName` 사용하여 구독을 만들 송장 섹션을 식별합니다. `invoiceSectionId`송장 `billingProfileId` 섹션의 에서 `skuId` 와 하나를 복사합니다. 예를 들어 송장 섹션에 `Microsoft Azure plan` 대한 `Development` 형식의 구독을 만들려면 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`을 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` 복사합니다. `0001` 다음 단계에서 사용할 수 있도록 이러한 값을 어딘가에 붙여 넣습니다.

### <a name="create-a-subscription-for-an-invoice-section"></a>송장 섹션에 대한 구독 만들기

다음 예제에서는 *개발* 송장 섹션에 대 한 *Microsoft Azure 계획* 형식의 개발자 팀 *구독이라는 구독을* 만듭니다. 구독은 *Contoso 금융의* 청구 프로필로 청구되며 송장의 *개발* 섹션에 표시됩니다.

다음 요청을 하고 두 `<invoiceSectionId>` 번째 `invoiceSectionId` 단계()에서```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```복사된 것으로 교체합니다. API의 요청 매개 `billingProfileId` 변수에서 두 번째 단계에서 `skuId` 복사한 및 를 전달해야 합니다. 소유자를 지정하려면 사용자 개체 [를 얻는 방법을](grant-access-to-create-subscription.md#userObjectId)알아봅니다.

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

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | yes      | String | 구독의 표시 이름입니다.|
| `billingProfileId`   | yes      | String | 구독 요금에 대해 청구되는 청구 프로필의 ID입니다.  |
| `skuId` | yes      | String | Azure 계획의 유형을 결정하는 sku ID입니다. |
| `owners`      | 예       | String | 구독을 만들 때 구독에서 RBAC 소유자로 추가하려는 사용자 또는 서비스 주체의 개체 ID입니다.  |
| `costCenter` | 예      | String | 구독과 연결된 비용 센터입니다. 사용 CSV 파일에 표시됩니다. |
| `managementGroupId` | 예      | String | 구독이 추가될 관리 그룹의 ID입니다. 관리 그룹 목록을 얻으려면 [관리 그룹 - 목록 API를](/rest/api/resources/managementgroups/list)참조하십시오. API에서 관리 그룹의 ID를 사용합니다. |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>MPA 청구 계정에 대한 구독 만들기

### <a name="prerequisites"></a>사전 요구 사항

청구 계정에 대한 구독을 만들려면 조직의 클라우드 솔루션 공급자 계정에 글로벌 관리자 또는 관리자 에이전트 역할이 있어야 합니다. 자세한 내용은 [파트너 센터 - 사용자 역할 및 사용 권한 할당을 참조하세요.](https://docs.microsoft.com/partner-center/permissions-overview)

아래 표시된 예제는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>액세스할 수 있는 결제 계정 찾기

아래에서 액세스 권한이 있는 모든 청구 계정을 나열하도록 요청합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 응답에는 청구 계정이 나열됩니다.

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
속성을 `displayName` 사용하여 구독을 만들 려는 청구 계정을 식별합니다. 계정의 동의 유형은 *Microsoft파트너 계약입니다.* 계정의 `name` 복사본을 복사합니다. 예를 들어 `Contoso` 청구 계정에 대한 구독을 만들려면 을 복사합니다. `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="find-customers-that-have-azure-plans"></a>Azure 요금제가 있는 고객 찾기

다음 요청을 만들고 첫 `<billingAccountName>` 번째 `name` 단계()에서```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```복사된 고객으로 교체하여 Azure 구독을 만들 수 있는 청구 계정의 모든 고객을 나열합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API 응답은 Azure 요금제와 함께 청구 계정의 고객을 나열합니다. 이러한 고객에 대한 구독을 만들 수 있습니다.

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

속성을 `displayName` 사용하여 구독을 만들 고객을 식별합니다. 고객을 `id` 위해 복사합니다. 예를 들어 에 대한 `Fabrikam toys`구독을 만들려면 을 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`복사합니다. 이 값을 다음 단계에서 사용할 수 있도록 어딘가에 붙여 넣습니다.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>간접 공급자의 경우 선택 사항: 고객을 위한 리셀러 받기

CSP 2계층 모델의 간접 공급자인 경우 고객을 위한 구독을 만드는 동안 리셀러를 지정할 수 있습니다.

다음 요청을 하여 두 `<customerId>` 번째 `id` 단계()에서```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```복사된 것으로 교체하여 고객이 사용할 수 있는 모든 리셀러를 나열합니다.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API 응답은 고객의 리셀러를 나열합니다.

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
속성을 `description` 사용하여 구독과 연결될 리셀러를 식별합니다. 리셀러의 `resellerId` 복사본을 복사합니다. 예를 들어 연결하려는 경우 `Wingtip`을 복사합니다. `3xxxxx` 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="create-a-subscription-for-a-customer"></a>고객에 대한 구독 만들기

다음 예제는 *Fabrikam 장난감에* 대한 *Dev Team 구독이라는* 구독을 만들고 *Wingtip* 리셀러를 구독에 연결합니다. T

다음 요청을 하고 두 `<customerId>` 번째 `id` 단계()에서```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```복사된 것으로 교체합니다. API의 요청 매개 변수에서 두 번째 단계에서 복사된 선택적 *resellerId를* 전달합니다.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | yes      | String | 구독의 표시 이름입니다.|
| `skuId` | yes      | String | Azure 계획의 sku ID입니다. 사용 *0001* 유형 의 구독에 대 한 Microsoft Azure 계획 |
| `resellerId`      | 예       | String | 구독과 연결될 리셀러의 MPN ID입니다.  |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

## <a name="next-steps"></a>다음 단계

* .NET을 사용하여 EA(기업 계약) 구독을 만드는 예제는 [GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)의 샘플 코드를 참조하십시오.
* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독을 관리하는 방법에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성을 참조하세요.](../../governance/management-groups/overview.md)
