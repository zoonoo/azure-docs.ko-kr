---
title: Azure Resource Manager 템플릿을 사용 하 여 Azure 사용자 지정 역할 만들기-Azure RBAC
description: Azure Resource Manager 템플릿 및 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure 사용자 지정 역할을 만드는 방법에 대해 알아봅니다.
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85398042"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure 사용자 지정 역할 만들기

[Azure 기본 제공 역할이](built-in-roles.md) 조직의 특정 요구를 충족 하지 않는 경우 [사용자 고유의 사용자 지정 역할](custom-roles.md)을 만들 수 있습니다. 이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 사용자 지정 역할을 만드는 방법을 설명 합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>사전 요구 사항

사용자 지정 역할을 만들려면 다음이 있어야 합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 역할 이름, 사용 권한 및 역할을 사용할 수 있는 위치를 지정 합니다. 이 문서에서는 구독 범위 이하로 할당 될 수 있는 리소스 권한이 있는 "사용자 지정 역할-RG 판독기" 라는 역할을 만듭니다.

### <a name="review-the-template"></a>템플릿 검토

이 문서에 사용 된 템플릿은 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def)에 있습니다. 템플릿에는 네 개의 매개 변수 및 리소스 섹션이 있습니다. 네 가지 매개 변수는 다음과 같습니다.

- 기본값인 ["Microsoft .Resources/subscription/resourceGroups/read"]를 사용 하는 작업의 배열입니다.
- 기본값이 비어 있는 notActions의 배열입니다.
- "사용자 지정 역할-RG 판독기"의 기본값을 사용 하는 역할 이름
- 역할 설명의 기본값은 "역할 정의의 구독 수준 배포"입니다.

템플릿에 정의된 리소스는 다음과 같습니다.

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

이 사용자 지정 역할을 할당할 수 있는 범위가 현재 구독으로 설정 되어 있습니다.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>템플릿 배포

이전 템플릿을 배포 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. PowerShell을 위한 Azure Cloud Shell을 엽니다.

1. 다음 스크립트를 복사하여 Cloud Shell에 붙여넣습니다.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. 배포 위치 (예: *centralus*)를 입력 합니다.

1. 사용자 지정 역할에 대 한 작업 목록을 *Microsoft .resources/resources/read, Microsoft .resources/subscription/resourceGroups/read*와 같이 쉼표로 구분 된 목록으로 입력 합니다.

1. 필요한 경우 Enter 키를 눌러 New-AzDeployment 명령을 실행 합니다.

    [AzDeployment](/powershell/module/az.resources/new-azdeployment) 명령은 템플릿을 배포 하 여 사용자 지정 역할을 만듭니다.

    다음과 비슷한 내용이 출력됩니다.

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>배포된 리소스 검토

사용자 지정 역할이 생성 되었는지 확인 하려면 다음 단계를 수행 합니다.

1. [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 실행 하 여 사용자 지정 역할을 나열 합니다.

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    다음과 비슷한 결과가 나타나야 합니다.

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Azure Portal에서 구독을 엽니다.

1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 클릭합니다.

1. **역할** 탭을 클릭 합니다.

1. **유형** 목록을 **customrole**로 설정 합니다.

1. **사용자 지정 역할-RG 판독기** 역할이 나열 되는지 확인 합니다.

   ![Azure Portal의 새 사용자 지정 역할](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>리소스 정리

사용자 지정 역할을 제거 하려면 다음 단계를 수행 합니다.

1. 다음 명령을 실행 하 여 사용자 지정 역할을 제거 합니다.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. **Y** 를 입력 하 여 사용자 지정 역할을 제거할 것임을 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 정의 이해](role-definitions.md)
- [빠른 시작: Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 추가](quickstart-role-assignments-template.md)
- [ARM 템플릿 설명서](../azure-resource-manager/templates/index.yml)
