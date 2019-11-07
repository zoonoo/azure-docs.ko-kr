---
title: Azure Cosmos DB Table API에 대 한 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB Table API를 만들고 구성 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: aa169ed7001ad858a2a0373f9d5bbbe770a2a727
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604473"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB Table API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너의 관리를 자동화 하는 다양 한 작업을 수행 하는 방법을 설명 합니다. 이 문서에는 Table API 계정에 대 한 예제가 포함 되어 있습니다. 다른 API 형식 계정에 대 한 예제를 찾으려면 Azure Cosmos DB의 API를 사용 하 여 [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md) 아티클에 대 한 리소스 관리자 템플릿 사용을 참조 하세요.

## Azure Cosmos 계정 및 테이블 만들기<a id="create-resource"></a>

Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 리소스를 만듭니다. 이 템플릿은 400 r u/초 처리량에 한 테이블을 포함 하는 Table API에 대 한 Azure Cosmos 계정을 만듭니다. 템플릿을 복사 하 고 아래와 같이 배포 하거나 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) 를 방문 하 여 Azure Portal에서 배포 합니다. 로컬 컴퓨터에 템플릿을 다운로드 하거나 새 템플릿을 만들고 `--template-file` 매개 변수를 사용 하 여 로컬 경로를 지정할 수도 있습니다.

> [!NOTE]
> 계정 이름은 소문자 여야 하 고 31 자 < 합니다.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>PowerShell을 통해 배포

PowerShell을 사용 하 여 리소스 관리자 템플릿을 배포 하려면 스크립트를 **복사** 하 고 **체험** 을 선택 하 여 Azure Cloud shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

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

Azure Cloud shell 대신 로컬로 설치 된 PowerShell 버전을 사용 하도록 선택 하는 경우 Azure PowerShell 모듈을 [설치](/powershell/azure/install-az-ps) 해야 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다.

### <a name="deploy-via-azure-cli"></a>Azure CLI를 통해 배포

Azure CLI를 사용 하 여 리소스 관리자 템플릿을 배포 하려면 스크립트를 **복사** 하 고 **체험** 을 선택 하 여 Azure Cloud shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

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

`az cosmosdb show` 명령은 프로 비전 된 후 새로 만든 Azure Cosmos 계정을 보여 줍니다. CloudShell을 사용 하는 대신 로컬로 설치 된 Azure CLI 버전을 사용 하도록 선택 하는 경우 [Azure 명령줄 인터페이스 (CLI)](/cli/azure/) 문서를 참조 하세요.

## 테이블의 업데이트 처리량 (r u/초)<a id="table-ru-update"></a>

다음 템플릿은 테이블의 처리량을 업데이트 합니다. 템플릿을 복사 하 고 아래와 같이 배포 하거나 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) 를 방문 하 여 Azure Portal에서 배포 합니다. 로컬 컴퓨터에 템플릿을 다운로드 하거나 새 템플릿을 만들고 `--template-file` 매개 변수를 사용 하 여 로컬 경로를 지정할 수도 있습니다.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>PowerShell을 통해 테이블 처리량 배포

PowerShell을 사용 하 여 리소스 관리자 템플릿을 배포 하려면 스크립트를 **복사** 하 고 **체험** 을 선택 하 여 Azure Cloud shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Azure CLI를 통해 테이블 템플릿 배포

Azure CLI를 사용 하 여 리소스 관리자 템플릿을 배포 하려면 **시도** 를 선택 하 여 Azure Cloud shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

- [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
- [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [일반적인 Azure Resource Manager 배포 오류 문제 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)