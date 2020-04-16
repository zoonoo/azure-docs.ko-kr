---
title: 리소스 관리자 템플릿을 사용하여 Azure Cosmos DB 생성 및 관리
description: Azure 리소스 관리자 템플릿을 사용하여 SQL(코어) API에 대한 Azure 코스모스 DB 생성 및 구성
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390899"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿을 사용 하 고 Azure 코스모스 DB SQL (코어) API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너의 관리를 자동화하는 방법에 대해 알아봅니다.

이 문서에서는 SQL API 계정에 대한 Azure 리소스 관리자 템플릿 예제만 보여 주며 있습니다. [카산드라,](manage-cassandra-with-resource-manager.md) [그렘린,](manage-gremlin-with-resource-manager.md) [몽고DB](manage-mongodb-with-resource-manager.md)및 [테이블](manage-table-with-resource-manager.md) API에 대한 템플릿 예제도 찾을 수 있습니다.

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Azure 코스모스 계정, 데이터베이스 및 컨테이너 만들기

다음 Azure 리소스 관리자 템플릿은 다음과 같은 Azure Cosmos 계정을 만듭니다.

* 데이터베이스 수준에서 요청된 단위(RU/s) 처리량을 400개 공유하는 두 개의 컨테이너입니다.
* 전용 400 RU/s 처리량이 있는 하나의 컨테이너.

Azure Cosmos DB 리소스를 만들려면 다음 예제 템플릿을 복사하고 [PowerShell](#deploy-via-powershell) 또는 [Azure CLI를](#deploy-via-azure-cli)통해 설명된 대로 배포합니다.

* 선택적으로 Azure 빠른 [시작 갤러리를](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) 방문하여 Azure 포털에서 템플릿을 배포할 수 있습니다.
* 템플릿을 로컬 컴퓨터에 다운로드하거나 새 템플릿을 만들고 매개 변수를 `--template-file` 사용하여 로컬 경로를 지정할 수도 있습니다.

> [!IMPORTANT]
>
> * Azure Cosmos 계정에 위치를 추가하거나 제거하면 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행해야 합니다.
> * 계정 이름은 44자로 제한되며 모든 소문자로 제한됩니다.
> * 처리량 값을 변경하려면 업데이트된 RU/s를 사용하여 템플릿을 다시 제출합니다.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> 큰 파티션 키가 있는 컨테이너를 만들려면 이전 `"version":2` 템플릿을 `partitionKey` 수정하여 개체 내에 속성을 포함합니다.

### <a name="deploy-via-powershell"></a>파워쉘을 통한 배포

PowerShell을 사용하여 Azure 리소스 관리자 템플릿을 배포하려면 다음을 수행합니다.

1. 스크립트를 **복사합니다.**
2. Azure 클라우드 셸을 열려면 **시도를** 선택합니다.
3. Azure 클라우드 셸 창을 마우스 오른쪽 단추로 클릭한 다음 **붙여넣기를**선택합니다.

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

Azure 클라우드 셸 대신 로컬로 설치된 PowerShell 버전으로 템플릿을 배포하도록 선택할 수 있습니다. [Azure PowerShell 모듈을 설치해야](/powershell/azure/install-az-ps)합니다. 실행하여 `Get-Module -ListAvailable Az` 필요한 버전을 찾습니다.

### <a name="deploy-via-azure-cli"></a>Azure CLI를 통한 배포

Azure CLI를 사용하여 Azure 리소스 관리자 템플릿을 배포하려면 다음을 수행합니다.

1. 스크립트를 **복사합니다.**
2. Azure 클라우드 셸을 열려면 **시도를** 선택합니다.
3. Azure 클라우드 셸 창을 마우스 오른쪽 단추로 클릭한 다음 **붙여넣기를**선택합니다.

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

이 `az cosmosdb show` 명령은 프로비전된 후 새로 생성된 Azure Cosmos 계정을 표시합니다. Azure Cloud Shell 대신 로컬로 설치된 Azure CLI 버전으로 템플릿을 배포하도록 선택할 수 있습니다. 자세한 내용은 [CLI(Azure 명령줄 인터페이스)](/cli/azure/) 문서를 참조하십시오.

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>서버 측 기능을 사용하여 Azure Cosmos DB 컨테이너 만들기

Azure 리소스 관리자 템플릿을 사용하여 저장 프로시저, 트리거 및 사용자 정의 함수가 있는 Azure Cosmos DB 컨테이너를 만들 수 있습니다.

다음 예제 템플릿을 복사하여 PowerShell 또는 [Azure CLI를](#deploy-with-azure-cli)사용하면 설명된 대로 [배포할](#deploy-with-powershell) 수 있습니다.

* 선택적으로 [Azure 빠른 시작 갤러리를](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) 방문하여 Azure 포털에서 템플릿을 배포할 수 있습니다.
* 템플릿을 로컬 컴퓨터에 다운로드하거나 새 템플릿을 만들고 매개 변수를 `--template-file` 사용하여 로컬 경로를 지정할 수도 있습니다.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포

PowerShell을 사용하여 Azure 리소스 관리자 템플릿을 배포하려면 다음을 수행합니다.

1. 스크립트를 **복사합니다.**
1. Azure 클라우드 셸을 열려면 **시도를** 선택합니다.
1. Azure 클라우드 셸 창을 마우스 오른쪽 단추로 클릭한 다음 **붙여넣기를 선택합니다.**

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

Azure 클라우드 셸 대신 로컬로 설치된 PowerShell 버전으로 템플릿을 배포하도록 선택할 수 있습니다. [Azure PowerShell 모듈을 설치해야](/powershell/azure/install-az-ps)합니다. 실행하여 `Get-Module -ListAvailable Az` 필요한 버전을 찾습니다.

### <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI를 사용하여 Azure 리소스 관리자 템플릿을 배포하려면 다음을 수행합니다.

1. 스크립트를 **복사합니다.**
2. Azure 클라우드 셸을 열려면 **시도를** 선택합니다.
3. Azure 클라우드 셸 창을 마우스 오른쪽 단추로 클릭한 다음 **붙여넣기를**선택합니다.

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

* [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
* [Azure 코스모스 DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
* [Azure 코스모스 DB 퀵스타트 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [일반적인 Azure 리소스 관리자 배포 오류 문제 해결](../azure-resource-manager/templates/common-deployment-errors.md)
