---
title: MongoDB의 Azure Cosmos DB API용 Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용하여 MongoDB의 Azure Cosmos DB API를 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 1fe2c96e3c469e41e12d64f27a6a3b3d16a1174d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684783"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB MongoDB API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 MongoDB API용 Azure Cosmos DB 계정, 데이터베이스 및 컬렉션을 배포 및 관리하는 방법을 알아봅니다.

이 문서에는 MongoDB용 Azure Cosmos DB API의 예제만 있으며, 다른 API 형식 계정의 예제를 찾으려면 [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [Table](manage-table-with-resource-manager.md)의 Azure Cosmos DB API에서 Azure Resource Manager 템플릿 사용 문서를 참조하세요.

> [!IMPORTANT]
>
> * 계정 이름은 44자(모두 소문자)로 제한됩니다.
> * 처리량 값을 변경하려면 업데이트된 RU/s로 템플릿을 다시 배포합니다.
> * Azure Cosmos 계정에 위치를 추가하거나 제거하면 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행해야 합니다.

아래의 Azure Cosmos DB 리소스를 만들려면 다음 예제 템플릿을 새 json 파일에 복사합니다. 필요에 따라 이름 및 값이 다른 동일한 리소스의 여러 인스턴스를 배포할 때 매개 변수 json 파일을 만들 수 있습니다. [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 및 [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)를 비롯한 Azure Resource Manager 템플릿을 배포할 수 있는 여러 가지 방법이 있습니다.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>자동 크기 조정 프로비저닝된 처리량을 포함하는 MongoDB용 Azure Cosmos 계정

이 템플릿은 데이터베이스 수준에서 자동 크기 조정 처리량을 공유하는 두 개의 컬렉션을 사용하여 MongoDB API(3.2 또는 3.6)용 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>표준 프로비저닝된 처리량을 포함하는 MongoDB용 Azure Cosmos 계정

이 템플릿은 데이터베이스 수준에서 400 RU/초 표준(수동) 처리량을 공유하는 두 개의 컬렉션을 사용하여 MongoDB API(3.2 또는 3.6)용 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
* [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [일반적인 Azure Resource Manager 배포 오류 문제 해결](../azure-resource-manager/templates/common-deployment-errors.md)
