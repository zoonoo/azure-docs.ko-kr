---
title: Azure 코스모스 DB 테이블 API에 대한 리소스 관리자 템플릿
description: Azure 리소스 관리자 템플릿을 사용하여 Azure Cosmos DB 테이블 API를 만들고 구성합니다.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246709"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿을 사용하여 Azure Cosmos DB 테이블 API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너관리를 자동화하기 위해 다양한 작업을 수행하는 방법에 대해 설명합니다. 이 문서에는 다른 API 형식 계정에 대한 예제를 찾기 위해 테이블 API 계정에 대한 예제만 있습니다. [Cassandra](manage-cassandra-with-resource-manager.md) [Gremlin](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md) [SQL](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>Azure 코스모스 계정 및 테이블 만들기<a id="create-resource"></a>

Azure 리소스 관리자 템플릿을 사용하여 Azure Cosmos DB 리소스를 만듭니다. 이 템플릿은 400 RU/s 처리량에서 하나의 테이블이 있는 테이블 API에 대한 Azure Cosmos 계정을 만듭니다. 아래 와 같이 템플릿을 복사하여 배포하거나 [Azure Quickstart 갤러리를](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) 방문하여 Azure 포털에서 배포합니다. 템플릿을 로컬 컴퓨터에 다운로드하거나 새 템플릿을 만들고 매개 변수를 `--template-file` 사용하여 로컬 경로를 지정할 수도 있습니다.

> [!NOTE]
> 계정 이름은 소문자여야 하며 문자는 44자 이상이어야 합니다.
> RU/s를 업데이트하려면 업데이트된 처리량 속성 값으로 템플릿을 다시 제출합니다.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>파워쉘을 통한 배포

PowerShell을 사용하여 리소스 관리자 템플릿을 배포하려면 스크립트를 **복사하고** Azure 클라우드 셸을 여는 **시도를** 선택합니다. 스크립트를 붙여 넣은 다음 셸을 마우스 오른쪽 단추로 클릭한 다음 **붙여넣기를 선택합니다.**

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Azure 클라우드 셸 대신 로컬로 설치된 PowerShell 버전을 사용하도록 선택한 경우 Azure PowerShell 모듈을 [설치해야](/powershell/azure/install-az-ps) 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다.

### <a name="deploy-via-the-azure-cli"></a>Azure CLI를 통한 배포

Azure CLI를 사용하여 Azure 리소스 관리자 템플릿을 배포하려면 스크립트를 **복사하고** Azure 클라우드 셸을 **열려고 시도를** 선택합니다. 스크립트를 붙여 넣은 다음 셸을 마우스 오른쪽 단추로 클릭한 다음 **붙여넣기를 선택합니다.**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

이 `az cosmosdb show` 명령은 프로비전된 후 새로 만든 Azure Cosmos 계정을 표시합니다. 클라우드 셸을 사용하는 대신 로컬로 설치된 Azure CLI 버전을 사용하도록 선택한 경우 [Azure CLI](/cli/azure/) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

- [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
- [Azure 코스모스 DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
- [Azure 코스모스 DB 퀵스타트 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [일반적인 Azure 리소스 관리자 배포 오류 문제 해결](../azure-resource-manager/templates/common-deployment-errors.md)