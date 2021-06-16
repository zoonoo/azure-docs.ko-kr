---
title: Azure Cosmos DB를 사용하여 데이터 작업에 대한 사용자 액세스 제한
description: Azure Cosmos DB를 사용하여 데이터 작업에 대한 사용자 액세스를 제한하는 방법 알아보기
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3d79c043446fbf124ca851f40e85cd10c962a73b
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110662143"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터 작업에 대한 사용자 액세스 제한
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB에서는 데이터베이스 서비스와의 상호 작용을 인증하는 두 가지 방법이 있습니다.

- Azure Portal와 상호 작용할 때 Azure Active Directory ID 사용
- API 및 SDK에서 호출을 실행하는 경우 Azure Cosmos DB [키](database-security.md#primary-keys) 또는 [리소스 토큰](secure-access-to-data.md#resource-tokens) 사용.

각 인증 방법은 일부 겹침이 있는 다음과 같은 여러 작업 집합에 대한 액세스를 제공합니다.

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="인증 유형별 작업 분할" border="false":::

일부 시나리오에서는 조직의 일부 사용자가 데이터 작업(CRUD 요청 및 쿼리)만 수행하도록 제한할 수 있습니다. 이는 일반적으로 리소스를 만들거나 삭제하고 또는 작업 중인 컨테이너의 프로비전된 처리량을 변경할 필요가 없는 개발자에 해당하는 사례입니다.

다음 단계를 적용하여 액세스를 제한할 수 있습니다.
1. 액세스를 제한하려는 사용자에 대한 사용자 지정 Azure Active Directory 역할을 만듭니다. 사용자 지정 Active Directory 역할은 Azure Cosmos DB의 [세분화된 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)을 사용하여 작업에 대한 세분화된 액세스 수준이 있어야 합니다.
1. 키를 사용하여 비 데이터 작업의 실행을 허용하지 않습니다. 이러한 작업을 Azure Resource Manager 호출로만 제한하여 이를 달성할 수 있습니다.

이 문서의 다음 섹션에서 이러한 단계를 수행하는 방법을 보여 줍니다.

> [!NOTE]
> 다음 섹션의 명령을 실행하기 위해서는 수정하려는 구독에 대한 [Azure 소유자 역할](../role-based-access-control/built-in-roles.md#owner) 뿐만 아니라 Azure PowerShell Module 3.0.0 이상을 설치해야 합니다.

다음 섹션의 PowerShell 스크립트에서 다음 자리 표시자를 사용자 환경에 해당하는 값으로 대체합니다.
- `$MySubscriptionId` - 사용 권한을 제한하려는 Azure Cosmos 계정을 포함하는 구독 ID. 예: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`
- `$MyResourceGroupName` - Azure Cosmos 계정을 포함하는 리소스 그룹. 예: `myresourcegroup`
- `$MyAzureCosmosDBAccountName` - Azure Cosmos DB 계정의 이름. 예: `mycosmosdbsaccount`
- `$MyUserName` -  액세스를 제한하려는 사용자의 로그인(username@domain). 예: `cosmosdbuser@contoso.com`

## <a name="select-your-azure-subscription"></a>Azure 구독 선택

Azure PowerShell 명령을 실행하려면 로그인하고 구독을 선택하여 명령을 실행해야 합니다.

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>사용자 지정 Azure Active Directory 역할 만들기

다음 스크립트는 Azure Cosmos 계정에 대한 "키 전용" 액세스 권한이 있는 Azure Active Directory 역할 할당을 만듭니다. 이 역할은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md) 및 [Azure Cosmos DB에 대한 세부적인 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)을 기반으로 합니다. 이러한 역할 및 작업은 `Microsoft.DocumentDB` Azure Active Directory 네임스페이스의 일부입니다.

1. 먼저 다음과 같은 콘텐츠를 포함하는 `AzureCosmosKeyOnlyAccess.json`이라는 JSON 문서를 만듭니다.

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. 다음 명령을 실행하여 역할 할당을 만들고 사용자에게 할당합니다.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>비 데이터 작업 실행 허용 안 함

다음 명령은 키를 사용하여 다음을 수행하는 기능을 제거합니다.
- 리소스 만들기, 수정 또는 삭제
- 컨테이너 설정 업데이트(인덱싱 정책, 처리량 등 포함)

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>다음 단계

- [Cosmos DB의 역할 기반 액세스 제어](role-based-access-control.md)에 대한 자세한 정보
- [Cosmos DB의 데이터에 대한 보안 액세스](secure-access-to-data.md) 개요 살펴보기
