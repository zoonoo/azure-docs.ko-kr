---
title: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 만들기 및 관리
description: Azure Resource Manager 템플릿을 사용 하 여 SQL (Core) API에 대 한 Azure Cosmos DB 만들기 및 구성
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 0cb6e80bafca3bb0bfc339552facae5bd16aced4
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960541"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB SQL (코어) API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너의 관리를 자동화 하는 다양 한 작업을 수행 하는 방법을 설명 합니다. 이 문서에는 SQL API 계정에 대 한 예제가 포함 되어 있습니다. 다른 API 형식 계정에 대 한 예제는 [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)에 대 한 Azure Cosmos DB의 api를 사용 하 여 Azure Resource Manager 템플릿 [사용을 참조](manage-table-with-resource-manager.md) 하세요.

MongoDB, Gremlin, Cassandra 및 Table API에 대 한 Cosmos DB 계정, 데이터베이스 및 컨테이너를 만들고 관리 하는 방법입니다.

## Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기<a id="create-resource"></a>

Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 리소스를 만듭니다. 이 템플릿은 데이터베이스 수준에서 400 r u/s 처리량을 공유 하는 두 개의 컨테이너와 전용 400 r u/초 처리량을 가진 단일 컨테이너를 사용 하 여 Azure Cosmos 계정을 만듭니다. 템플릿을 복사 하 고 아래와 같이 배포 하거나 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) 를 방문 하 여 Azure Portal에서 배포 합니다. 로컬 컴퓨터에 템플릿을 다운로드 하거나 새 템플릿을 만들고 `--template-file` 매개 변수를 사용 하 여 로컬 경로를 지정할 수도 있습니다.

> [!NOTE]
>
> - Azure Cosmos 계정에 위치를 동시에 추가 또는 제거 하 고 다른 속성을 수정할 수 없습니다. 이러한 작업은 별도의 작업으로 수행 해야 합니다.
> - 계정 이름은 소문자와 44 자이 하 여야 합니다.
> - R u/s를 업데이트 하려면 업데이트 된 처리량 속성 값으로 템플릿을 다시 전송 합니다.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> 파티션 키가 많은 컨테이너를 만들려면 이전 템플릿의 `partitionKey` 개체 내에 `"version":2` 속성을 포함 합니다.

### <a name="deploy-via-powershell"></a>PowerShell을 통해 배포

PowerShell을 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 스크립트를 **복사** 하 고 **시도** 를 선택 하 여 Azure Cloud Shell를 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell 대신 로컬로 설치 된 PowerShell 버전을 사용 하도록 선택한 경우 Azure PowerShell 모듈을 [설치](/powershell/azure/install-az-ps) 해야 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다.

### <a name="deploy-via-azure-cli"></a>Azure CLI를 통해 배포

Azure CLI를 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 **시도** 를 선택 하 여 Azure Cloud Shell를 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 명령은 프로 비전 된 후 새로 만든 Azure Cosmos 계정을 보여 줍니다. CloudShell을 사용 하는 대신 로컬로 설치 된 Azure CLI 버전을 사용 하도록 선택 하는 경우 [Azure 명령줄 인터페이스 (CLI)](/cli/azure/) 문서를 참조 하세요.

## 서버 쪽 기능을 사용 하 여 Azure Cosmos DB 컨테이너 만들기<a id="create-sproc"></a>

Azure Resource Manager 템플릿을 사용 하 여 저장 프로시저, 트리거 및 사용자 정의 함수를 사용 하 여 Azure Cosmos DB 컨테이너를 만듭니다. 템플릿을 복사 하 고 아래와 같이 배포 하거나 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) 를 방문 하 여 Azure Portal에서 배포 합니다. 로컬 컴퓨터에 템플릿을 다운로드 하거나 새 템플릿을 만들고 `--template-file` 매개 변수를 사용 하 여 로컬 경로를 지정할 수도 있습니다.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-stored-procedure-template-via-powershell"></a>PowerShell을 통해 저장 프로시저 템플릿 배포

PowerShell을 사용 하 여 리소스 관리자 템플릿을 배포 하려면 스크립트를 **복사** 하 고 **시도** 를 선택 하 여 Azure Cloud Shell를 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell 대신 로컬로 설치 된 PowerShell 버전을 사용 하도록 선택한 경우 Azure PowerShell 모듈을 [설치](/powershell/azure/install-az-ps) 해야 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다.

### <a name="deploy-stored-procedure-template-via-azure-cli"></a>Azure CLI를 통해 저장 프로시저 템플릿 배포

Azure CLI를 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 **시도** 를 선택 하 여 Azure Cloud Shell를 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

- [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
- [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [일반적인 Azure Resource Manager 배포 오류 문제 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)
