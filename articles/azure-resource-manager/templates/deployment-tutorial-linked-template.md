---
title: 자습서 - 연결된 템플릿 배포
description: 연결된 템플릿을 배포하는 방법을 알아봅니다.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63383f810b6f5643bad9feb86360745cd2f9b000
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501136"
---
# <a name="tutorial-deploy-a-linked-template"></a>자습서: 연결된 템플릿 배포

[이전 자습서](./deployment-tutorial-local-template.md)에서 로컬 컴퓨터에 저장된 템플릿을 배포하는 방법을 알아보았습니다. 복잡한 솔루션을 배포하기 위해 템플릿을 여러 템플릿으로 분할하고 기본 템플릿을 통해 이러한 템플릿을 배포할 수 있습니다. 이 자습서에서는 연결된 템플릿에 대한 참조가 포함된 기본 템플릿을 배포하는 방법에 대해 알아봅니다. 기본 템플릿이 배포되면 연결된 템플릿의 배포가 트리거됩니다. 또한 SAS 토큰을 사용하여 연결된 템플릿을 저장하고 보호하는 방법에 대해서도 알아봅니다. 완료하는 데 **12분** 정도 걸립니다.

## <a name="prerequisites"></a>사전 요구 사항

이전 자습서를 완료하는 것이 좋지만 필수는 아닙니다.

## <a name="review-template"></a>템플릿 검토

이전 자습서에서는 스토리지 계정, App Service 계획 및 웹앱을 만드는 템플릿을 배포합니다. 사용된 템플릿은 다음과 같습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>연결된 템플릿 만들기

스토리지 계정 리소스를 연결된 템플릿으로 구분할 수 있습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

다음 템플릿은 기본 템플릿입니다.  강조 표시된 **Microsoft.Resources/deployments** 개체는 연결된 템플릿을 호출하는 방법을 보여 줍니다. 연결된 템플릿은 로컬 파일 또는 로컬 네트워크에서만 사용할 수 있는 파일로 저장할 수 없습니다. *http* 또는 *https* 중 하나를 포함하는 URI 값만 제공할 수 있습니다. Resource Manager에서 템플릿에 액세스할 수 있어야 합니다. 한 가지 옵션은 연결된 템플릿을 스토리지 계정에 배치하고 해당 항목의 URI를 사용하는 것입니다. URI는 매개 변수를 사용하여 템플릿에 전달됩니다. 강조 표시된 매개 변수 정의를 참조하세요.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

확장명이 .json인 기본 템플릿(예: azuredeploy.json)의 복사본을 로컬 컴퓨터에 저장합니다. 연결된 템플릿의 복사본은 저장할 필요가 없습니다.  연결된 템플릿이 GitHub 리포지토리에서 스토리지 계정으로 복사됩니다.

## <a name="store-the-linked-template"></a>연결된 템플릿 저장

다음 PowerShell 스크립트는 스토리지 계정을 만들고, 컨테이너를 만들고, 연결된 템플릿을 GitHub 리포지토리에서 컨테이너로 복사합니다. 연결된 템플릿의 복사본이 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json)에 저장됩니다.

Cloud Shell을 열려면 **사용해 보세요**를 선택하고, PowerShell 스크립트를 복사하려면 **복사**를 선택하고, 마우스 오른쪽 단추로 셸 창을 클릭하여 스크립트를 붙여넣습니다.

> [!IMPORTANT]
> Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다. 이름은 고유해야 합니다. 템플릿에서 스토리지 계정 이름은 "store"가 추가된 프로젝트 이름이며, 프로젝트 이름은 3-11자 사이여야 합니다. 따라서 프로젝트 이름은 스토리지 계정 이름 요구 사항을 충족해야 하며 11자 미만이어야 합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>템플릿 배포

스토리지 계정에 프라이빗 템플릿을 배포하려면 SAS 토큰을 생성하고 해당 템플릿의 URI에 포함합니다. 배포를 완료할 만큼 충분한 여유를 두고 만료 기간을 설정합니다. 템플릿이 포함된 Blob은 계정 소유자만 액세스할 수 있습니다. 그러나 blob용 SAS 토큰을 생성하면 해당 blob은 해당 URI를 가진 사람이면 누구나 액세스할 수 있습니다. 다른 사용자가 URI를 가로채는 경우, 그 사용자가 템플릿에 액세스할 수 있습니다. SAS 토큰은 템플릿에 대한 액세스를 제한하는 좋은 방법이지만, 암호와 같은 중요한 데이터를 템플릿에 직접 포함하지 않아야 합니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](./deployment-tutorial-local-template.md#create-resource-group)를 참조하세요.

> [!NOTE]
> 아래 Azure CLI 코드에서 date 매개 변수 -d는 macOS의 잘못된 인수입니다. 따라서 macOS 사용자는 macOS의 터미널에서 현재 시간에 2시간을 추가하려면-v+2H를 사용해야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹을 삭제하여 배포된 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

연결된 템플릿을 배포하는 방법을 알아보았습니다. 다음 자습서에서 템플릿을 배포하는 DevOp 파이프라인을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [파이프라인을 만듭니다.](./deployment-tutorial-pipeline.md)
