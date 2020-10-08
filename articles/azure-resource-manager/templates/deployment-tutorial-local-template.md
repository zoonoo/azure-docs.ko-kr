---
title: 자습서 - 로컬 Azure Resource Manager 템플릿 배포
description: 로컬 컴퓨터에서 Azure Resource Manager 템플릿을 배포하는 방법 알아보기
ms.date: 05/20/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: fe13376ced428713703f2bd5cf33941129dec1d9
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611625"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>자습서: 로컬 Azure Resource Manager 템플릿 배포

로컬 머신에서 Azure Resource Manager 템플릿을 배포하는 방법을 알아봅니다. 완료하는 데 **8분** 정도 걸립니다.

이 자습서는 시리즈의 첫 번째 자습서입니다. 시리즈를 진행하면서 연결된 템플릿을 만들어서 템플릿을 모듈화하고, 연결된 계정을 스토리지 계정에 저장하고, SAS 토큰을 사용하여 연결된 템플릿을 보호하고, 템플릿을 배포하는 DevOp 파이프라인을 만드는 방법을 알아볼 수 있습니다. 이 시리즈에서는 템플릿 배포에 중점을 둡니다.  템플릿 개발을 알아보려면 [초보자를 위한 자습서](./template-tutorial-create-first-template.md)를 참조하세요.

## <a name="get-tools"></a>도구 가져오기

먼저 템플릿을 배포하는 데 필요한 도구가 있는지 확인합니다.

### <a name="command-line-deployment"></a>명령줄 배포

템플릿을 배포하려면 Azure PowerShell 또는 Azure CLI가 필요합니다. 설치 지침은 다음을 참조하세요.

- [Azure PowerShell 설치](/powershell/azure/install-az-ps)
- [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)
- [Linux에 Azure CLI 설치](/cli/azure/install-azure-cli-linux)

Azure PowerShell 또는 Azure CLI가 설치되면 처음으로 로그인해야 합니다. 도움을 받으려면 [로그인 - PowerShell](/powershell/azure/install-az-ps#sign-in) 또는 [로그인 - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)를 참조하세요.

### <a name="editor-optional"></a>편집기(선택 사항)

템플릿은 JSON 파일입니다. 템플릿을 검토/편집하려면 적합한 JSON 편집기가 필요합니다. Resource Manager 도구 확장이 있는 Visual Studio Code를 사용하는 것이 좋습니다. 이러한 도구를 설치해야 하는 경우 [빠른 시작: Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.

## <a name="review-template"></a>템플릿 검토

템플릿은 스토리지 계정, App Service 계획 및 웹앱을 배포합니다. 템플릿을 만드는 데 관심이 있으면 [빠른 시작 템플릿에 대한 자습서](template-tutorial-quickstart-template.md)를 진행하면 됩니다. 단, 이 자습서를 완료하는 데는 필요하지 않습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다. 이름은 고유해야 합니다. 템플릿에서 스토리지 계정 이름은 "store"가 추가된 프로젝트 이름이며, 프로젝트 이름은 3-11자 사이여야 합니다. 따라서 프로젝트 이름은 스토리지 계정 이름 요구 사항을 충족해야 하며 11자 미만이어야 합니다.

확장명이 .json인 템플릿(예: azuredeploy.json)의 복사본을 로컬 컴퓨터에 저장합니다. 이 템플릿은 자습서의 뒷부분에서 배포합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure PowerShell/Azure CLI를 사용하여 템플릿을 배포하기 시작하려면 Azure 자격 증명으로 로그인합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Azure 구독이 여러 개 있으면 사용할 구독을 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>리소스 그룹 만들기

템플릿을 배포하는 경우 리소스를 포함할 리소스 그룹을 지정합니다. 배포 명령을 실행하기 전에 먼저 Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 만듭니다. Azure PowerShell과 Azure CLI 중에서 선택하려면 다음 코드 섹션에서 해당 탭을 선택합니다. 이 문서의 CLI 예제는 Bash 셸에 대해 작성되었습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>템플릿 배포

하나 또는 두 가지 배포 옵션을 사용하여 템플릿을 배포합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Azure PowerShell을 사용하여 템플릿을 배포하는 방법에 대해 자세히 알아보려면 [Resource Manager 템플릿과 Azure PowerShell을 사용하여 리소스 배포](./deploy-powershell.md)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Azure CLI를 사용하여 템플릿을 배포하는 방법에 대해 자세히 알아보려면 [Resource Manager 템플릿과 Azure CLI를 사용하여 리소스 배포](./deploy-cli.md)를 참조하세요.

---

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹을 삭제하여 배포된 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

로컬 템플릿을 배포하는 방법을 알아보았습니다. 다음 자습서에서는 템플릿을 기본 템플릿과 연결된 템플릿으로 분리하고 연결된 템플릿을 저장하고 보호하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [연결된 템플릿 배포](./deployment-tutorial-linked-template.md)
