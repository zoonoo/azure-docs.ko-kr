---
title: Azure Resource Manager 템플릿을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트 - Azure RBAC
description: ARM 템플릿(Azure Resource Manager 템플릿) 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 사용자 지정 역할을 만들거나 업데이트하는 방법에 대해 알아봅니다.
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 230a772ba0edb95c2616ede1c4ea9deb4a1b0943
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696620"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>ARM 템플릿을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트

[Azure 기본 제공 역할](built-in-roles.md)이 조직의 특정 요구 사항을 충족하지 않는 경우 [사용자 지정 역할](custom-roles.md)을 만들 수 있습니다. 이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 사용자 지정 역할을 만들거나 업데이트하는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

사용자 지정 역할을 만들려면 역할 이름, 권한 및 역할을 사용할 수 있는 위치를 지정합니다. 이 문서에서는 구독 범위 이하에서 할당될 수 있는 리소스 권한이 있는 _Custom Role - RG Reader_ 라는 역할을 만듭니다.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

사용자 지정 역할을 만들려면 다음이 있어야 합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))

## <a name="review-the-template"></a>템플릿 검토

이 문서에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/create-role-def)에서 가져온 것입니다. 이 템플릿에는 4개의 매개 변수와 1개의 리소스 섹션이 있습니다. 매개 변수는 다음과 같습니다.

- 기본값 `["Microsoft.Resources/subscriptions/resourceGroups/read"]`를 사용하는 작업의 배열입니다.
- 빈 기본값을 사용하는 `notActions`의 배열입니다.
- 기본값이 `Custom Role - RG Reader`인 역할 이름입니다.
- 기본값이 `Subscription Level Deployment of a Role Definition`인 역할 설명입니다.

이 사용자 지정 역할을 할당할 수 있는 범위는 현재 구독으로 설정되어 있습니다.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

템플릿에 정의된 리소스는 다음과 같습니다.

- [Microsoft.Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>템플릿 배포

다음 단계를 따라 위의 템플릿을 배포합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. PowerShell을 위한 Azure Cloud Shell을 엽니다.

1. 다음 스크립트를 복사하여 Cloud Shell에 붙여넣습니다.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. 배포 위치를 지정합니다(예: `centralus`).

1. 사용자 지정 역할의 작업 목록을 `Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read`와 같이 쉼표로 구분된 목록으로 입력합니다.

1. Enter 키를 눌러 `New-AzDeployment` 명령을 실행합니다.

    [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 명령은 템플릿을 배포하여 사용자 지정 역할을 만듭니다.

    다음과 비슷한 내용이 출력됩니다.

    ```azurepowershell-interactive
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

사용자 지정 역할이 생성되었는지 확인하려면 다음 단계를 수행합니다.

1. [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 실행하여 사용자 지정 역할 목록을 가져옵니다.

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    다음과 비슷한 내용이 출력됩니다.

    ```azurepowershell-interactive
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

1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

1. **역할** 탭을 선택합니다.

1. **유형** 목록을 **CustomRole** 로 설정합니다.

1. **Custom Role - RG Reader** 역할이 나열되는지 확인합니다.

   ![Azure Portal의 새 사용자 지정 역할](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 만들 때와 비슷하게 템플릿을 사용하여 기존 사용자 지정 역할을 수정할 수 있습니다. 사용자 지정 역할을 업데이트하려면 업데이트하려는 역할을 지정해야 합니다.

사용자 지정 역할을 업데이트하려면 위의 빠른 시작 템플릿을 다음과 같이 변경해야 합니다.

- 역할 ID를 매개 변수로 포함합니다.
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- 역할 ID 매개 변수를 역할 정의에 포함합니다.

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

다음은 템플릿을 배포하는 방법의 예입니다.

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>리소스 정리

사용자 지정 역할을 제거하려면 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 사용자 지정 역할을 제거합니다.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. **Y** 를 입력하여 사용자 지정 역할을 제거하도록 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 정의 이해](role-definitions.md)
- [빠른 시작: Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](quickstart-role-assignments-template.md)
- [ARM 템플릿 설명서](../azure-resource-manager/templates/index.yml)
