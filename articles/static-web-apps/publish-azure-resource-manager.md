---
title: '자습서: ARM 템플릿을 사용하여 Azure Static Web Apps 게시'
description: Static Web Apps에 대한 ARM 템플릿 만들기 및 배포
services: static-web-apps
author: petender
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: petender
ms.openlocfilehash: 95b2bd71b59a8ef14274928428624fe52e923fe1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968899"
---
# <a name="tutorial-publish-azure-static-web-apps-using-an-arm-template"></a>자습서: ARM 템플릿을 사용하여 Azure Static Web Apps 게시

이 문서에서는 [ARM 템플릿](../azure-resource-manager/templates/overview.md)(Azure Resource Manager 템플릿)을 사용하여 [Azure Static Web Apps](./overview.md)를 배포하는 방법을 보여줍니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> - Azure Static Web Apps에 대한 ARM 템플릿 만들기
> - ARM 템플릿을 배포하여 Azure Static Web App 인스턴스 만들기

## <a name="prerequisites"></a>필수 구성 요소

- **활성 Azure 계정:** 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- **GitHub 계정:** 계정이 없는 경우 [체험 GitHub 계정을 만들](https://github.com) 수 있습니다.
- **ARM 템플릿용 편집기:** 템플릿을 검토하고 편집하려면 JSON 편집기가 필요합니다. [Azure Resource Manager 도구 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)이 포함된 Visual Studio Code ARM 템플릿을 편집하는 데 적합합니다. Visual Studio Code를 설치하고 구성하는 방법에 대한 지침은 [빠른 시작: Visual Studio Code를 사용하여 ARM 템플릿 만들기](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.

- **Azure CLI 또는 Azure PowerShell**: ARM 템플릿을 배포하려면 명령줄 도구가 필요합니다. 설치 지침은 다음을 참조하세요.
  - [Windows OS에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)
  - [Linux OS에 Azure CLI 설치](/cli/azure/install-azure-cli-linux)
  - [macOS에 Azure CLI 설치](/cli/azure/install-azure-cli-macos)
  - [Azure PowerShell 설치](/powershell/azure/install-az-ps)

## <a name="create-a-github-personal-access-token"></a>GitHub 개인용 액세스 토큰 만들기

ARM 템플릿의 필수 매개 변수 중 하나는 `repositoryToken`이며, 이를 통해 ARM 배포 프로세스가 정적 사이트 소스 코드를 보유하고 있는 GitHub 리포지토리와 상호 작용할 수 있습니다. 

1. GitHub 계정 프로필(오른쪽 위 모서리에 있음)에서 **설정** 을 선택합니다.

1. **개발자 설정** 을 선택합니다.

1. **개인용 액세스 토큰** 을 선택합니다.

1. **새 토큰 생성** 을 선택합니다.

1. _Note_ 필드에 이 토큰의 이름을 입력합니다(예: *myfirstswadeployment*).

1. **리포지토리, 워크플로, 쓰기: 패키지** 등의 *범위* 를 지정합니다.

1. **토큰 생성** 을 선택합니다.

1. 토큰 값을 복사하여 나중에 사용할 수 있도록 텍스트 편집기에 붙여넣습니다.

> [!IMPORTANT]
> 이 토큰을 복사하여 안전한 곳에 보관해야 합니다. 이 토큰을 [Azure KeyVault](../azure-resource-manager/templates/template-tutorial-use-key-vault.md)에 저장하고 ARM 템플릿에서 액세스하는 것이 좋습니다.

## <a name="create-a-github-repo"></a>GitHub 리포지토리 만들기

이 문서에서는 GitHub 템플릿 리포지토리를 사용하여 쉽게 시작할 수 있습니다. 이 템플릿에는 Azure Static Web Apps를 통해 배포하는 데 사용되는 스타터 앱이 있습니다.

1. 다음 위치로 이동하여 새 리포지토리를 만듭니다.
    1. [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate)

1. 리포지토리 이름을 **myfirstswadeployment** 로 지정

    > [!NOTE]
    > Azure Static Web Apps에는 웹앱을 만들기 위한 하나 이상의 HTML 파일이 필요합니다. 이 단계에서 만드는 리포지토리에는 단일 _index.html_ 파일이 포함됩니다.

1. **템플릿에서 리포지토리 만들기** 를 선택합니다.

    :::image type="content" source="./media/getting-started/create-template.png" alt-text="템플릿에서 리포지토리 만들기":::

## <a name="create-the-arm-template"></a>ARM 템플릿 만들기

필수 구성 요소가 준비되면 다음 단계는 ARM 배포 템플릿 파일을 정의하는 것입니다.

1. ARM 템플릿을 저장할 새 폴더를 만듭니다.

1. 새 파일을 만들고, 이름을 **azuredeploy.json** 으로 지정합니다.

1. 다음 ARM 템플릿 조각을 _azuredeploy.js_ 에 붙여넣습니다.

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "type": "string"
                },
                "location": {
                    "type": "string"
                },
                "sku": {
                    "type": "string"
                },
                "skucode": {
                    "type": "string"
                },
                "repositoryUrl": {
                    "type": "string"
                },
                "branch": {
                    "type": "string"
                },
                "repositoryToken": {
                    "type": "securestring"
                },
                "appLocation": {
                    "type": "string"
                },
                "apiLocation": {
                    "type": "string"
                },
                "appArtifactLocation": {
                    "type": "string"
                },
                "resourceTags": {
                    "type": "object"
                }
            },
            "resources": [
                {
                    "apiVersion": "2019-12-01-preview",
                    "name": "[parameters('name')]",
                    "type": "Microsoft.Web/staticSites",
                    "location": "[parameters('location')]",
                    "tags": "[parameters('resourceTags')]",
                    "properties": {
                        "repositoryUrl": "[parameters('repositoryUrl')]",
                        "branch": "[parameters('branch')]",
                        "repositoryToken": "[parameters('repositoryToken')]",
                        "buildProperties": {
                            "appLocation": "[parameters('appLocation')]",
                            "apiLocation": "[parameters('apiLocation')]",
                            "appArtifactLocation": "[parameters('appArtifactLocation')]"
                        }
                    },
                    "sku": {
                        "Tier": "[parameters('sku')]",
                        "Name": "[parameters('skuCode')]"
                    }
                }
            ]
        }

    ```

1. 새 파일을 만들고, 이름을 **azuredeploy.parameters.json** 으로 지정합니다.

1. 다음 ARM 템플릿 조각을 _azuredeploy.parameters.json_ 에 붙여넣습니다.

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "value": "myfirstswadeployment"
                },
                "location": {
                "type": "string",
                "defaultValue": "Central US"
                },   
                "sku": {
                    "value": "Free"
                },
                "skucode": {
                    "value": "Free"
                },
                "repositoryUrl": {
                    "value": "https://github.com/<YOUR-GITHUB-USER-NAME>/<YOUR-GITHUB-REPOSITORY-NAME>"
                },
                "branch": {
                    "value": "main"
                },
                "repositoryToken": {
                    "value": "<YOUR-GITHUB-PAT>" 
                },
                "appLocation": {
                    "value": "/"
                },
                "apiLocation": {
                    "value": ""
                },
                "appArtifactLocation": {
                    "value": "public"
                },
                "resourceTags": {
                    "value": {
                        "Environment": "Development",
                        "Project": "Testing SWA with ARM",
                        "ApplicationName": "myfirstswadeployment"
                    }
                }
            }
        }
    ```

1. 다음 매개 변수를 업데이트합니다.

    | 매개 변수 | 필요한 값 |
    | --- | --- |
    | `repositoryUrl` | Static Web Apps GitHub 리포지토리에 URL을 제공합니다. |
    | `repositoryToken` | GitHub PAT 토큰을 제공합니다. |

1. 다음 단계에서 배포를 실행하기 전에 업데이트를 저장합니다.

## <a name="running-the-deployment"></a>배포 실행

템플릿을 배포하려면 Azure CLI 또는 Azure PowerShell이 필요합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

템플릿을 배포하려면 Azure CLI 또는 Azure PowerShell에 로그인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

---

Azure 구독이 여러 개 있는 경우 사용할 구독을 선택합니다. `<SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>`을 사용자의 구독 정보로 바꿉니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

---

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

템플릿을 배포하는 경우 관련 리소스가 포함된 그룹을 지정합니다. 배포 명령을 실행하기 전에 먼저 Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 만듭니다.

> [!NOTE]
> 이 문서의 CLI 예제는 Bash 셸에 대해 작성되었습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName="myfirstswadeployRG"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = "myfirstswadeployRG"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

---

## <a name="deploy-template"></a>템플릿 배포

이러한 배포 옵션 중 하나를 사용하여 템플릿을 배포합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file <PATH-TO-AZUREDEPLOY.JSON> \
  --parameters <PATH-TO-AZUREDEPLOY.PARAMETERS.JSON> \
  --verbose
```

Azure CLI를 사용하여 템플릿을 배포하는 방법에 대해 자세히 알아보려면 [ARM 템플릿과 Azure CLI를 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$templateparameterFile = Read-Host -Prompt "Enter the template parameter file path and file name"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -TemplateParameterFile $templateparameterfile `
  -verbose
```

Azure PowerShell을 사용하여 템플릿을 배포하는 방법에 대해 자세히 알아보려면 [ARM 템플릿과 Azure PowerShell을 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

---

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹을 삭제하여 배포된 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [정적 웹앱 구성](./configuration.md)