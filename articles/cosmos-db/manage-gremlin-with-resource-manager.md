---
title: Azure Cosmos DB Gremlin API에 대 한 azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿 만들기 및 Azure Cosmos DB Gremlin API를 구성 하려면 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 9f62399e3a1ef2a4ceaa8bdf64196bdb634fb4b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968892"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB Gremlin API 리소스 관리

## MongoDB 계정, 데이터베이스 및 컬렉션에 대 한 Azure Cosmos DB API 만들기 <a id="create-resource"></a>

Azure Resource Manager 템플릿을 사용 하는 Azure Cosmos DB 리소스를 만듭니다. 이 템플릿은 데이터베이스 수준에서 400 RU/s 처리량을 공유 하는 두 개의 그래프를 사용 하 여 Gremlin API에 대 한 Azure Cosmos 계정이 만들어집니다. 서식 파일을 복사 하 고 아래와 같이 배포 하거나 방문 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) 및 Azure portal에서 배포 합니다. 있습니다 수 또한 템플릿을 로컬 컴퓨터에 다운로드 또는 새 템플릿 만들기 및 로컬 경로를 지정 합니다 `--template-file` 매개 변수입니다.

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI를 사용 하 여 Resource Manager 템플릿을 배포 하려면 **복사본** 선택한 스크립트 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 명령은 프로 비전 된 후 새로 만든된 Azure Cosmos 계정을 보여 줍니다. Cloud Shell을 사용 하는 대신 로컬에 설치 된 버전의 Azure CLI를 사용 하려는 경우 [Azure 명령줄 인터페이스 (CLI)](/cli/azure/) 문서.

## 데이터베이스에 대 한 처리량 (RU/s)를 업데이트 합니다. <a id="database-ru-update"></a>

다음 템플릿은 데이터베이스의 처리량을 업데이트 합니다. 서식 파일을 복사 하 고 아래와 같이 배포 하거나 방문 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-database-ru-update/) 및 Azure portal에서 배포 합니다. 있습니다 수 또한 템플릿을 로컬 컴퓨터에 다운로드 또는 새 템플릿 만들기 및 로컬 경로를 지정 합니다 `--template-file` 매개 변수입니다.

[!code-json[cosmosdb-gremlin-database-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Azure CLI를 통해 데이터베이스 템플릿 배포

Azure CLI를 사용 하 여 Resource Manager 템플릿을 배포 하려면 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## 그래프의 처리량 (RU/s)를 업데이트 합니다. <a id="graph-ru-update"></a>

다음 템플릿은 그래프의 처리량을 업데이트 합니다. 서식 파일을 복사 하 고 아래와 같이 배포 하거나 방문 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-graph-ru-update/) 및 Azure portal에서 배포 합니다. 있습니다 수 또한 템플릿을 로컬 컴퓨터에 다운로드 또는 새 템플릿 만들기 및 로컬 경로를 지정 합니다 `--template-file` 매개 변수입니다.

[!code-json[cosmosdb-gremlin-graph-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json)]

### <a name="deploy-graph-template-via-azure-cli"></a>Azure CLI를 통해 그래프 템플릿 배포

Azure CLI를 사용 하 여 Resource Manager 템플릿을 배포 하려면 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName graphName=$graphName throughput=$throughput
```

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

- [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
- [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [일반적인 Azure Resource Manager 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)