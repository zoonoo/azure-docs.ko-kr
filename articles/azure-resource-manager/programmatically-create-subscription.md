---
title: 프로그래밍 방식으로 Azure 엔터프라이즈 구독 만들기 | Microsoft Docs
description: 프로그래밍 방식으로 추가 Azure 엔터프라이즈 또는 Enterprise 개발/테스트 구독을 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 90823eded03f298dd912735fb0170fd8002328f3
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715893"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>프로그래밍 방식으로 Azure 엔터프라이즈 구독 만들기(미리 보기)

[EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)의 Azure 사용자로서 프로그래밍 방식으로 EA(MS-AZR-0017P) 및 EA 개발/테스트(MS-AZR-0148P) 구독을 만들 수 있습니다. 이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

이 API에서 Azure 구독을 만든 경우 해당 구독은 Microsoft 또는 공인된 대리점에서 Microsoft Azure 서비스를 가져온 계약에 의해 제어됩니다. 자세한 내용은 [Microsoft Azure 법적 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

구독을 만들려는 등록 계정에 대한 소유자 또는 기여자 역할이 있어야 합니다. 이 역할을 얻는 두 가지 방법이 있습니다.

* 등록 관리자가 [계정 소유자로 만들어줄 수 있습니다](https://ea.azure.com/helpdocs/addNewAccount)(로그인 필요). 이렇게 하면 등록 계정의 소유자가 됩니다. 수동으로 초기 구독을 만들려면 수신한 초대 이메일의 지침을 따릅니다. 다음 단계로 진행하기 전에 계정 소유권을 확인하고 초기 EA 구독을 수동으로 만듭니다. 등록에 계정을 추가하는 것만으로는 부족합니다.

* 등록 계정의 기존 소유자가 [액세스 권한을 부여](grant-access-to-create-subscription.md)할 수 있습니다. 마찬가지로, EA 구독을 만들 서비스 주체를 사용하려는 경우 [해당 서비스 주체에게 구독을 만들 수 있는 권한을 부여](grant-access-to-create-subscription.md)해야 합니다.

## <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

계정 소유자로 Azure EA 등록에 추가된 후 Azure는 계정-등록 관계를 사용하여 구독 요금을 청구할 위치를 결정합니다. 계정에서 만든 모든 구독은 계정이 있는 EA 등록으로 청구됩니다. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다. 

다음 명령을 실행하려면 기본적으로 구독이 생성되는 디렉터리인 계정 소유자의 *홈 디렉터리*에 로그인해야 합니다.

# <a name="resttabrest"></a>[REST](#tab/rest)

모든 등록 계정을 나열하도록 요청합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure는 액세스할 수 있는 모든 등록 계정의 목록으로 응답합니다.

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[Get-AzureRmEnrollmentAccount 명령](/powershell/module/azurerm.billing/get-azurermenrollmentaccount)을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure는 계정의 개체 ID 및 이메일 주소 목록으로 응답합니다.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 명령을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다.

```azurecli-interactive 
az billing enrollment-account list
```

Azure는 계정의 개체 ID 및 이메일 주소 목록으로 응답합니다.

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
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 다음 단계에서 구독을 만드는 데 사용하는 `enrollmentAccount` 값으로 `id`를 사용합니다.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기 

다음 예제에서는 *개발 팀 구독*이라는 구독을 만드는 요청을 만들고 구독 제안은 *MS-AZR-0017P*(일반 EA)입니다. 등록 계정은 SignUpEngineering@contoso.com에 대한 등록 계정인 `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(자리 표시자 값, 이 값은 GUID)입니다. 또한 구독에 대한 RBAC 소유자로 두 명의 사용자를 선택적으로 추가합니다.

# <a name="resttabrest"></a>[REST](#tab/rest)

요청 경로에 있는 `enrollmentAccount`의 `id`를 사용하여 구독을 만듭니다.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| 요소 이름  | 필수 | 형식   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 아니요      | 문자열 | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offerType`   | yes      | 문자열 | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `owners`      | 아니요       | 문자열 | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |

응답에서 모니터링에 대한 `subscriptionOperation` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionOperation` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

이 미리 보기 모듈을 사용하려면 `Install-Module AzureRM.Subscription -AllowPrerelease`를 먼저 실행하여 설치합니다. `-AllowPrerelease`가 작동하는지 확인하려면 [Get PowerShellGet Module](/powershell/gallery/installing-psget)에서 최신 버전의 PowerShellGet을 설치합니다.

`enrollmentAccount` 개체 ID와 함께 [New-AzureRmSubscription](/powershell/module/azurerm.subscription)을 `EnrollmentAccountObjectId` 매개 변수로 사용하여 새 구독을 만듭니다. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| 요소 이름  | 필수 | 형식   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 아니요      | 문자열 | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `OfferType`   | yes      | 문자열 | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `EnrollmentAccountObjectId`      | yes       | 문자열 | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `Get-AzureRmEnrollmentAccount`에서 가져온 GUID입니다. |
| `OwnerObjectId`      | 아니요       | 문자열 | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `OwnerSignInName`    | 아니요       | 문자열 | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다.|
| `OwnerApplicationId` | 아니요       | 문자열 | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.| 

모든 매개 변수의 전체 목록을 보려면 [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview)을 참조하세요.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

이 미리 보기 확장을 사용하려면 `az extension add --name subscription`을 먼저 실행하여 설치합니다.

`enrollmentAccount` 개체 ID와 함께 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)를 `enrollment-account-object-id` 매개 변수로 사용하여 새 구독을 만듭니다.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 요소 이름  | 필수 | 형식   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 아니요      | 문자열 | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offer-type`   | yes      | 문자열 | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `enrollment-account-object-id`      | yes       | 문자열 | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `az billing enrollment-account list`에서 가져온 GUID입니다. |
| `owner-object-id`      | 아니요       | 문자열 | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `owner-upn`    | 아니요       | 문자열 | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다.|
| `owner-spn` | 아니요       | 문자열 | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.| 

모든 매개 변수의 전체 목록을 보려면 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)를 참조하세요.

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- 이 API를 사용하여 Azure 엔터프라이즈 구독을 만들 수 있습니다.
- 계정당 50개의 구독 제한이 있습니다. 그 후 구독은 계정 센터를 사용하여 만들 수 있습니다.
- 계정에 하나 이상의 EA 또는 EA 개발/테스트 구독이 있어야 합니다. 즉, 계정 소유자는 한 번 이상 수동 등록을 완료했습니다.
- 계정 소유자가 아니지만 RBAC를 통해 등록 계정에 추가된 사용자는 계정 센터를 사용하여 구독을 만들 수 없습니다.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* .NET을 사용하여 구독 만들기에 대한 예제는 [GitHub의 샘플 코드](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)를 참조하세요.
* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독 관리에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](management-groups-overview.md)을 참조하세요.
