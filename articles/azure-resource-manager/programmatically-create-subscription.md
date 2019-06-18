---
title: 프로그래밍 방식으로 Azure 엔터프라이즈 구독 만들기 | Microsoft Docs
description: 프로그래밍 방식으로 추가 Azure 엔터프라이즈 또는 Enterprise 개발/테스트 구독을 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 7985451eb2bb5e9fd4fbcfb3d2fcf35149122c15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796073"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>프로그래밍 방식으로 Azure 엔터프라이즈 구독 만들기(미리 보기)

[EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)의 Azure 사용자로서 프로그래밍 방식으로 EA(MS-AZR-0017P) 및 EA 개발/테스트(MS-AZR-0148P) 구독을 만들 수 있습니다. 이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

이 API에서 Azure 구독을 만든 경우 해당 구독은 Microsoft 또는 공인된 대리점에서 Microsoft Azure 서비스를 가져온 계약에 의해 제어됩니다. 자세한 내용은 [Microsoft Azure 법적 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

아래에서 구독을 생성 하려면 등록 계정에는 소유자 역할이 있어야 합니다. 이 역할을 얻는 두 가지 방법이 있습니다.

* 등록 관리자 수 [계정 소유자는 해야](https://ea.azure.com/helpdocs/addNewAccount) (로그인 필요)를 통해 등록 계정의 소유자입니다. 수동으로 초기 구독을 만들려면 수신한 초대 이메일의 지침을 따릅니다. 다음 단계로 진행하기 전에 계정 소유권을 확인하고 초기 EA 구독을 수동으로 만듭니다. 등록에 계정을 추가하는 것만으로는 부족합니다.

* 등록 계정의 기존 소유자가 [액세스 권한을 부여](grant-access-to-create-subscription.md)할 수 있습니다. 마찬가지로, EA 구독을 만들 서비스 주체를 사용하려는 경우 [해당 서비스 주체에게 구독을 만들 수 있는 권한을 부여](grant-access-to-create-subscription.md)해야 합니다.

## <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

계정 소유자로 Azure EA 등록에 추가된 후 Azure는 계정-등록 관계를 사용하여 구독 요금을 청구할 위치를 결정합니다. 계정에서 만든 모든 구독은 계정이 있는 EA 등록으로 청구됩니다. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다. 

다음 명령을 실행하려면 기본적으로 구독이 생성되는 디렉터리인 계정 소유자의 *홈 디렉터리*에 로그인해야 합니다.

## <a name="resttabrest"></a>[REST (영문)](#tab/rest)

요청에 대 한 액세스를 해야 하는 모든 등록 계정을 나열 하려면:

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

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 복사를 `name` 해당 계정의 합니다. 예를 들어 아래에서 구독을 만든다면 합니다 SignUpEngineering@contoso.com 등록 계정에 복사 하는 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```합니다. 등록 계정의 개체 ID입니다. 다음 단계에서 사용할 수 있도록 특정 위치에이 값을 붙여 넣습니다 `enrollmentAccountObjectId`합니다.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

오픈 [Azure Cloud Shell](https://shell.azure.com/) 고 PowerShell을 선택 합니다.

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet을 사용하여 액세스 권한이 있는 모든 등록 계정을 나열합니다.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure 등록 계정에 액세스할 수 있는 목록을 사용 하 여 응답 합니다.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 복사를 `ObjectId` 해당 계정의 합니다. 예를 들어 아래에서 구독을 만든다면 합니다 SignUpEngineering@contoso.com 등록 계정에 복사 하는 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```합니다. 다음 단계에서 사용할 수 있도록 특정 위치에이 개체 ID를 붙여 넣습니다는 `enrollmentAccountObjectId`합니다.

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 명령을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다.

```azurecli-interactive 
az billing enrollment-account list
```

Azure 등록 계정에 액세스할 수 있는 목록을 사용 하 여 응답 합니다.

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

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 복사를 `name` 해당 계정의 합니다. 예를 들어 아래에서 구독을 만든다면 합니다 SignUpEngineering@contoso.com 등록 계정에 복사 하는 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```합니다. 등록 계정의 개체 ID입니다. 다음 단계에서 사용할 수 있도록 특정 위치에이 값을 붙여 넣습니다 `enrollmentAccountObjectId`합니다.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기

다음 예제에서는 라는 구독을 만듭니다 *개발 팀 구독* 이전 단계에서 선택한 등록 계정에 있습니다. 구독 제품은 *-0017 Ms-azr* (일반 Microsoft 기업 계약). 또한 구독에 대한 RBAC 소유자로 두 명의 사용자를 선택적으로 추가합니다.

# <a name="resttabrest"></a>[REST (영문)](#tab/rest)

다음 요청을 확인, 교체 `<enrollmentAccountObjectId>` 사용 하 여 합니다 `name` 첫 번째 단계에서 복사한 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). 소유자를 지정 하려는 경우에 대해 알아봅니다 [사용자 개체 Id를 가져오는 방법을](grant-access-to-create-subscription.md#userObjectId)합니다.

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

| 요소 이름  | 필수 | 형식   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 아니요      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offerType`   | 예      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `owners`      | 아닙니다.       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |

응답에서 모니터링에 대한 `subscriptionOperation` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionOperation` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

먼저 실행 하 여이 미리 보기 모듈을 설치 `Install-Module Az.Subscription -AllowPrerelease`합니다. `-AllowPrerelease`가 작동하는지 확인하려면 [Get PowerShellGet Module](/powershell/gallery/installing-psget)에서 최신 버전의 PowerShellGet을 설치합니다.

실행 합니다 [새로 만들기-AzSubscription](/powershell/module/az.subscription) 대체 아래 명령을 `<enrollmentAccountObjectId>` 사용 하 여를 `ObjectId` 첫 번째 단계에서 수집한 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). 소유자를 지정 하려는 경우에 대해 알아봅니다 [사용자 개체 Id를 가져오는 방법을](grant-access-to-create-subscription.md#userObjectId)합니다.

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 요소 이름  | 필수 | 형식   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 아니요      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `OfferType`   | 예      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `EnrollmentAccountObjectId`      | 예       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `Get-AzEnrollmentAccount`에서 가져온 GUID입니다. |
| `OwnerObjectId`      | 아닙니다.       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `OwnerSignInName`    | 아닙니다.       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다.|
| `OwnerApplicationId` | 아닙니다.       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.| 

모든 매개 변수의 전체 목록을 보려면 [New-AzSubscription](/powershell/module/az.subscription.preview)을 참조하세요.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 실행 하 여이 미리 보기 확장을 설치 `az extension add --name subscription`합니다.

실행 합니다 [az 계정 만들기](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) 대체 아래 명령을 `<enrollmentAccountObjectId>` 사용 하 여 합니다 `name` 첫 번째 단계에서 복사한 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). 소유자를 지정 하려는 경우에 대해 알아봅니다 [사용자 개체 Id를 가져오는 방법을](grant-access-to-create-subscription.md#userObjectId)합니다.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 요소 이름  | 필수 | 형식   | 설명                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 아니요      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offer-type`   | 예      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `enrollment-account-object-id`      | 예       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `az billing enrollment-account list`에서 가져온 GUID입니다. |
| `owner-object-id`      | 아닙니다.       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `owner-upn`    | 아닙니다.       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다.|
| `owner-spn` | 아닙니다.       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.| 

모든 매개 변수의 전체 목록을 보려면 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)를 참조하세요.

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- 이 API를 사용하여 Azure 엔터프라이즈 구독을 만들 수 있습니다.
- 등록 계정당 50 구독의 초기 제한은 없지만 할 수 있습니다 [지원 요청을 만드는](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 200으로 제한을 늘립니다. 그 후 구독은 계정 센터를 통해 만들만 있습니다.
- 계정에 하나 이상의 EA 또는 EA 개발/테스트 구독이 있어야 합니다. 즉, 계정 소유자는 한 번 이상 수동 등록을 완료했습니다.
- 계정 소유자가 아니지만 RBAC를 통해 등록 계정에 추가된 사용자는 계정 센터를 사용하여 구독을 만들 수 없습니다.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* .NET을 사용하여 구독 만들기에 대한 예제는 [GitHub의 샘플 코드](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)를 참조하세요.
* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독 관리에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](management-groups-overview.md)을 참조하세요.
