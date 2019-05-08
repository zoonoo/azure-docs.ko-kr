---
title: MongoDB에 대 한 Azure Cosmos DB API에 대 한 azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿 만들기 및 MongoDB에 대 한 Azure Cosmos DB API를 구성 하려면 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: bcf9e0492e58de79efbb16f9ee13adbd0f27b572
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077772"
---
# <a name="create-azure-cosmos-db-api-for-mongodb-resources-from-a-resource-manager-template"></a>Resource Manager 템플릿에서 리소스를 MongoDB에 대 한 Azure Cosmos DB API 만들기

Azure Resource Manager 템플릿을 사용 하는 MongoDB 리소스에 대 한 Azure Cosmos DB API를 만드는 방법에 알아봅니다. 다음 예제에서 MongoDB API에 대 한 Azure Cosmos DB 계정을 만듭니다는 [Azure 빠른 시작 템플릿](https://aka.ms/mongodb-arm-qs)합니다. 이 템플릿은 데이터베이스 수준에서 400 RU/s 처리량을 공유 하는 두 컬렉션을 사용 하 여 MongoDB API에 대 한 Azure Cosmos 계정이 만들어집니다.

다음은 템플릿의 복사본입니다.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

## <a name="deploy-via-azure-cli"></a>Azure CLI를 통해 배포

Azure CLI를 사용 하 여 Resource Manager 템플릿을 배포 하려면 **복사본** 선택한 스크립트 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 명령은 프로 비전 된 후 새로 만든된 Azure Cosmos 계정을 보여 줍니다. Cloud Shell을 사용 하는 대신 로컬에 설치 된 버전의 Azure CLI를 사용 하려는 경우 [Azure 명령줄 인터페이스 (CLI)](/cli/azure/) 문서.

이전 예제에서는 GitHub에 저장 된 템플릿을 참조 합니다. 있습니다 수 또한 템플릿을 로컬 컴퓨터에 다운로드 또는 새 템플릿 만들기 및 로컬 경로를 지정 합니다 `--template-file` 매개 변수입니다.

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

- [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
- [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [일반적인 Azure Resource Manager 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)