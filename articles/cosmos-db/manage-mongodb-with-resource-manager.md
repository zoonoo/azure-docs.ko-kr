---
title: MongoDB 용 Azure Cosmos DB API에 대 한 리소스 관리자 템플릿
description: Azure Resource Manager 템플릿을 사용 하 여 MongoDB 용 Azure Cosmos DB API를 만들고 구성 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: d6f916002f949f78e4854903940f342261a109ff
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791224"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB MongoDB API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 MongoDB API, 데이터베이스 및 컬렉션에 대 한 Azure Cosmos DB 계정을 배포 하 고 관리 하는 방법에 대해 알아봅니다.

이 문서에는 Azure Cosmos DB의 MongoDB 용 API에 대 한 예제만 있습니다. 다른 API 형식 계정에 대 한 예제를 찾으려면 Azure Cosmos DB의 API를 사용 하 여 [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [테이블](manage-table-with-resource-manager.md) 아티클에 대 한 Azure Resource Manager 템플릿 사용을 참조 하세요.

> [!IMPORTANT]
>
> * 계정 이름은 44 자 (모두 소문자)로 제한 됩니다.
> * 처리량 값을 변경 하려면 업데이트 된 r u/s를 사용 하 여 템플릿을 다시 배포 합니다.
> * Azure Cosmos 계정에 위치를 추가 하거나 제거 하는 경우 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행 해야 합니다.

아래 Azure Cosmos DB 리소스를 만들려면 다음 예제 템플릿을 새 json 파일에 복사 합니다. 필요에 따라 다른 이름 및 값을 사용 하 여 동일한 리소스의 여러 인스턴스를 배포할 때 사용할 매개 변수 json 파일을 만들 수 있습니다. , [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 및 [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)를 비롯 한 Azure Resource Manager 템플릿을 배포할 수 있는 여러 가지 방법이 있습니다.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>자동 크기 조정 프로 비전 된 처리량을 제공 하는 MongoDB 용 Azure Cosmos 계정

이 템플릿은 데이터베이스 수준에서 자동 크기 조정 처리량을 공유 하는 두 개의 컬렉션을 사용 하 여 MongoDB API 용 Azure Cosmos 계정 (3.2 또는 3.6)을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포 하는 경우에도 사용할 수 있습니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>표준 프로 비전 된 처리량이 있는 MongoDB 용 Azure Cosmos 계정

이 템플릿은 데이터베이스 수준에서 400 r u/s 표준 (수동) 처리량을 공유 하는 두 개의 컬렉션을 사용 하 여 MongoDB API 용 Azure Cosmos 계정 (3.2 또는 3.6)을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포 하는 경우에도 사용할 수 있습니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
* [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [일반적인 Azure Resource Manager 배포 오류 문제 해결](../azure-resource-manager/templates/common-deployment-errors.md)
