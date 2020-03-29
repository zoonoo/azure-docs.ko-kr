---
title: Azure Cosmos DB에서만 데이터 작업에 대한 사용자 액세스 제한
description: Azure Cosmos DB에서만 데이터 작업에 대한 액세스를 제한하는 방법에 대해 알아봅니다.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980375"
---
# <a name="restrict-user-access-to-data-operations-only"></a>데이터 작업에 대해서만 사용자 액세스 제한

Azure Cosmos DB에는 데이터베이스 서비스와의 상호 작용을 인증하는 두 가지 방법이 있습니다.
- Azure 포털과 상호 작용할 때 Azure Active Directory ID를 사용하여
- API 및 SDK에서 호출을 발행할 때 Azure Cosmos DB [키](secure-access-to-data.md#master-keys) 또는 [리소스 토큰을](secure-access-to-data.md#resource-tokens) 사용합니다.

각 인증 방법은 서로 다른 작업 집합에 대한 ![액세스를 제공하며 일부 중복: 인증 유형별 작업 분할](./media/how-to-restrict-user-data/operations.png)

일부 시나리오에서는 조직의 일부 사용자가 CRUD 요청 및 쿼리만 데이터 작업을 수행하도록 제한할 수 있습니다. 일반적으로 리소스를 만들거나 삭제하거나 작업 중인 컨테이너의 프로비저닝된 처리량을 변경할 필요가 없는 개발자의 경우그렇습니다.

다음 단계를 적용하여 액세스를 제한할 수 있습니다.
1. 액세스를 제한하려는 사용자에 대한 사용자 지정 Azure Active Directory 역할 만들기 사용자 지정 Active Directory 역할에는 Azure Cosmos DB의 [세분화된 작업을](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)사용하여 작업에 대한 세부 액세스 수준이 있어야 합니다.
1. 키를 사용하여 비데이터 작업의 실행을 허용하지 않습니다. 이러한 작업을 Azure Resource Manager 호출로만 제한하여 이 작업을 수행할 수 있습니다.

이 문서의 다음 섹션에서는 이러한 단계를 수행하는 방법을 보여 주며 이 문서에서는 이러한 단계를 수행하는 방법을 보여 주며 이 문서에서는 이러한 단계를 수행하는 방법을 보여 준다.

> [!NOTE]
> 다음 섹션에서 명령을 실행하려면 수정하려는 구독에 Azure PowerShell 모듈 3.0.0 이상과 [Azure 소유자 역할을](../role-based-access-control/built-in-roles.md#owner) 설치해야 합니다.

다음 섹션의 PowerShell 스크립트에서 다음 자리 표시자를 사용자 환경과 관련된 값으로 대체합니다.
- `$MySubscriptionId`- 권한을 제한하려는 Azure Cosmos 계정이 포함된 구독 ID입니다. 예: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`
- `$MyResourceGroupName`- Azure Cosmos 계정을 포함하는 리소스 그룹입니다. 예: `myresourcegroup`
- `$MyAzureCosmosDBAccountName`- Azure 코스모스 계정의 이름입니다. 예: `mycosmosdbsaccount`
- `$MyUserName`- 액세스를 제한하려는 사용자의 로그인 ()username@domain 예: `cosmosdbuser@contoso.com`

## <a name="select-your-azure-subscription"></a>Azure 구독 선택

Azure PowerShell 명령을 수행하려면 로그인하여 명령을 실행하기 위해 구독을 선택해야 합니다.

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>사용자 지정 Azure 활성 디렉터리 역할 만들기

다음 스크립트는 Azure Cosmos 계정에 대한 "키 전용" 액세스 권한이 있는 Azure Active Directory 역할 할당을 만듭니다. 역할은 [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md) 과 Azure [Cosmos DB에 대한 세분화된 작업을](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)기반으로 합니다. 이러한 역할 및 작업은 `Microsoft.DocumentDB` Azure Active 디렉터리 네임스페이스의 일부입니다.

1. 먼저 다음 내용으로 명명된 `AzureCosmosKeyOnlyAccess.json` JSON 문서를 만듭니다.

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

## <a name="disallow-the-execution-of-non-data-operations"></a>비데이터 작업 실행 허용 금지

다음 명령은 키를 사용하여 다음을 수행할 수 있는 기능을 제거합니다.
- 리소스 생성, 수정 또는 삭제
- 업데이트 컨테이너 설정(인덱싱 정책, 처리량 등 포함).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>다음 단계

- [코스모스 DB의 역할 기반 액세스 제어에](role-based-access-control.md) 대해 자세히 알아보기
- [코스모스 DB의 데이터에 대한 보안 액세스 개요](secure-access-to-data.md) 보기
