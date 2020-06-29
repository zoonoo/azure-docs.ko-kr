---
title: Azure Resource Manager 템플릿을 사용하여 SQL 풀 만들기
description: Azure Resource Manager 템플릿을 사용하여 Azure Synapse Analytics SQL 풀을 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: bc9ed138926375fcb01d51ed866bd2d2f46c1c60
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977813"
---
# <a name="create-an-azure-synapse-analytics-sql-pool-by-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Azure Synapse Analytics SQL 풀 만들기

이 템플릿은 투명한 데이터 암호화가 활성화된 Azure Synapse Analytics SQL 풀을 만듭니다. Synapse SQL 풀은 Azure Synapse에서 일반적으로 사용할 수 있는 엔터프라이즈 데이터 웨어하우징 기능을 나타냅니다.


<!-- The second paragraph must be the following include file. You might need to change the file path of the include file depending on your content structure. This include is a paragraph that consistently introduces ARM concepts before doing a deployment and includes all our desired links to ARM content.-->

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

<!-- If your service has prerequisites you can include the free account link in that section. -->

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

<!-- If there aren't any prerequisites, just place "None" in the section. -->
없음

## <a name="create-an-azure-synapse-analytics-sql-pool"></a>Azure Synapse Analytics SQL 풀 만들기

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### <a name="review-the-template"></a>템플릿 검토

<!-- The first sentence must be the following sentence. Use a link to the quickstart gallery that begins with https://azure.microsoft.com/resources/templates/.  -->

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates)에서 나온 것입니다.

<!-- After the first sentence, add a JSON code fence that links to the quickstart template. Customers have provided feedback that they prefer to see the whole template. We recommend you include the entire template in your article. If your template is too long to show in the quickstart (more than 250 lines), you can instead add a sentence that says - `The template for this article is too long to show here. To view the template, see [azuredeploy.json](link to template's raw output)`.

The syntax for the code fence is: -->

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

<!-- For visibility, use highlight for the template's "resources": section. -->

<!-- After the JSON code fence, a list of each resourceType from the JSON must exist with a link to the template reference starting with /azure/templates. List the resourceType links in the same order as in the template.

For example:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): create an Azure key vault.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): create an key vault secret.

The URL usually appears as, for example, https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/loadBalancers for loadbalancer of Microsoft.Network. Remove the API version from the URL so that the URL redirects to the latest version.
-->

<!-- List additional quickstart templates. For example: [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).
Notice the resourceType and sort elements in the URL.
-->

### <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 이 템플릿은 Synapse SQL 풀을 만듭니다.
 [![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)
1. 다음 값을 입력하거나 업데이트합니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기**를 선택하고, 리소스 그룹의 고유한 이름을 입력하고, **확인**을 선택합니다. 새 리소스 그룹은 리소스 정리를 용이하게 합니다.
    * **지역**: 지역을 선택합니다.  예: **미국 중부**
    * **SQL Server 이름**: 기본 이름을 적용하거나 SQL Server 이름의 이름을 입력합니다.
    * **SQL 관리자 로그인**: SQL Server의 관리자 사용자 이름을 입력합니다.
    * **SQL 관리자 암호**: SQL Server의 관리자 암호를 입력합니다.
    * **데이터 웨어하우스 이름**: SQL 풀 이름을 입력합니다.
    * **투명한 데이터 암호화**: 기본값인 사용을 적용합니다. 
    * **서비스 수준 목표**: 기본값인 DW400c를 적용합니다.
    * **위치**: 리소스 그룹의 기본 위치를 적용합니다.
    * **검토 및 만들기**: 선택합니다.
    * **만들기**: 선택합니다.
<!--
<a href="https%3A%2F%2Fgithub.com%2FAzure%2Fazure-quickstart-templates%2Ftree%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create" target="_blank">
<img src="../../media/template-deployments/deploy-to-azure.svg"/>
</a>
-->

<!--
 One of the following options must be included:

  - **CLI**: In an Azure CLI Interactive code fence must contain **az deployment group create**. For example:

    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri
    echo "Press [ENTER] to continue ..." &&
    read
    ```

  - **PowerShell**: In an Azure PowerShell Interactive code fence must contain **New-AzResourceGroupDeployment**. For example:

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

  - **Portal**: Use a button with description **Deploy to Azure**, and the shared image ../media/template-deployments/deploy-to-azure.svg. The template link starts with https://portal.azure.com/#create/Microsoft.Template/uri/.
  
    ```markdown
    [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)
    ```

    The shared button image is in [GitHub](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/media/template-deployments/deploy-to-azure.svg). To find more information about this deployment option, see [Use a deployment button to deploy templates from GitHub repository](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button).
 -->

## <a name="review-deployed-resources"></a>배포된 리소스 검토

<!-- You can also use the title "Validate the deployment". -->

<!-- Include a portal screenshot of the resources or use interactive Azure CLI and Azure PowerShell commands to show the deployed resources. -->

Azure Portal을 사용하여 배포된 리소스를 확인하거나 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 배포된 리소스를 나열할 수 있습니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---
## <a name="clean-up-resources"></a>리소스 정리

<!-- Include a paragraph that explains how to delete unneeded resources. Add a portal screenshot or use interactive Azure CLI and Azure PowerShell commands to clean up the resources. -->


더 이상 필요하지 않은 경우 Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

<!--

Choose Azure CLI, Azure PowerShell, or Azure portal to delete the resource group.

Here are the samples for Azure CLI and Azure PowerShell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

-->

## <a name="next-steps"></a>다음 단계

<!-- You can either make the next steps similar to the next steps in your other quickstarts, or point users to the following tutorial.

If you want to include links to more information about the service, it's acceptable to use a paragraph and bullet points.
-->

이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 Azure Synapse Analytics 풀을 만들고 배포의 유효성을 검사했습니다. Azure Synapse Analytics 및 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Synapse Analytics 개요](sql-data-warehouse-overview-what-is.md) 읽기
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- [첫 번째 Azure Resource Manager 템플릿을 만들고 배포](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
