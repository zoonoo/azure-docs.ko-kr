---
title: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB를 만들고 설정 합니다.
description: Azure Resource Manager 템플릿을 사용 하 여 만들기 및 Azure Cosmos DB SQL API (코어) 구성
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mjbrown
ms.openlocfilehash: 5683fd072961c7793d8f4bbeb9ecc16a93dd7373
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242599"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB SQL (코어) API 리소스를 관리 합니다.

## Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기 <a id="create-resource"></a>

Azure Resource Manager 템플릿을 사용 하는 Azure Cosmos DB 리소스를 만듭니다. 이 템플릿은 데이터베이스 수준에서 400 RU/s 처리량을 공유 하는 두 개의 컨테이너를 사용 하 여 Azure Cosmos 계정이 만들어집니다. 서식 파일을 복사 하 고 아래와 같이 배포 하거나 방문 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) 및 Azure portal에서 배포 합니다. 있습니다 수 또한 템플릿을 로컬 컴퓨터에 다운로드 또는 새 템플릿 만들기 및 로컬 경로를 지정 합니다 `--template-file` 매개 변수입니다.

> [!NOTE]
> 현재 Resource Manager 템플릿을 사용 하 여 사용자 정의 Functions(UDFs), 저장된 프로시저 및 트리거를 배포할 수 없습니다. 

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>PowerShell을 통해 배포

PowerShell을 사용 하 여 Resource Manager 템플릿을 배포 하려면 **복사본** 선택한 스크립트 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud shell에서 로컬에 설치 된 버전 대신 PowerShell 사용 하려는 경우 필요가 [설치](/powershell/azure/install-az-ps) Azure PowerShell 모듈. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다.

### <a name="deploy-via-azure-cli"></a>Azure CLI를 통해 배포

Azure CLI를 사용 하 여 Resource Manager 템플릿을 배포 하려면 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 명령은 프로 비전 된 후 새로 만든된 Azure Cosmos 계정을 보여 줍니다. Cloud Shell을 사용 하는 대신 로컬에 설치 된 버전의 Azure CLI를 사용 하려는 경우 [Azure 명령줄 인터페이스 (CLI)](/cli/azure/) 문서.

## 데이터베이스에 대 한 처리량 (RU/s)를 업데이트 합니다. <a id="database-ru-update"></a>

다음 템플릿은 데이터베이스의 처리량을 업데이트 합니다. 서식 파일을 복사 하 고 아래와 같이 배포 하거나 방문 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) 및 Azure portal에서 배포 합니다. 있습니다 수 또한 템플릿을 로컬 컴퓨터에 다운로드 또는 새 템플릿 만들기 및 로컬 경로를 지정 합니다 `--template-file` 매개 변수입니다.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>PowerShell 통해 데이터베이스 템플릿 배포

PowerShell을 사용 하 여 Resource Manager 템플릿을 배포 하려면 **복사본** 선택한 스크립트 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Azure CLI를 통해 데이터베이스 템플릿 배포

Azure CLI를 사용 하 여 Resource Manager 템플릿을 배포 하려면 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## 컨테이너의 처리량 (RU/s)를 업데이트 합니다. <a id="container-ru-update"></a>

다음 템플릿 컨테이너의 처리량을 업데이트 합니다. 서식 파일을 복사 하 고 아래와 같이 배포 하거나 방문 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) 및 Azure portal에서 배포 합니다. 있습니다 수 또한 템플릿을 로컬 컴퓨터에 다운로드 또는 새 템플릿 만들기 및 로컬 경로를 지정 합니다 `--template-file` 매개 변수입니다.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>PowerShell 통해 컨테이너 템플릿 배포

PowerShell을 사용 하 여 Resource Manager 템플릿을 배포 하려면 **복사본** 선택한 스크립트 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Azure CLI를 통해 컨테이너 템플릿 배포

Azure CLI를 사용 하 여 Resource Manager 템플릿을 배포 하려면 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

- [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
- [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [일반적인 Azure Resource Manager 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)