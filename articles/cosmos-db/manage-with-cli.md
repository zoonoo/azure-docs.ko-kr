---
title: Azure CLI를 사용하여 Azure Cosmos DB 리소스 관리
description: Azure CLI를 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: d79cae16c109b407840b8565eb80fb87bbed6432
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877550"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos DB 리소스 관리

이 문서에서는 Azure CLI를 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너 관리를 자동화하는 명령에 대해 설명합니다. 또한 컨테이너 처리량 크기 조정 명령도 포함되어 있습니다. 모든 Azure Cosmos DB CLI 명령에 대한 참조 페이지는 [Azure CLI 참조](https://docs.microsoft.com/cli/azure/cosmosdb)에서 제공됩니다. 더 많은 예제는 [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)에서 확인할 수 있습니다. 여기에는 MongoDB, Gremlin, Cassandra 및 Table API에 대한 Cosmos DB 계정, 데이터베이스 및 컨테이너 만들기 및 관리 방법이 포함되어 있습니다.

이 샘플 CLI 스크립트는 Azure Cosmos DB SQL API 계정, 데이터베이스 및 컨테이너를 만듭니다.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

미국 동부 및 서부에서 사용하도록 설정된 SQL API, 세션 일관성 및 다중 마스터를 포함하는 Azure Cosmos DB 계정을 만들려면 Azure CLI 또는 클라우드 셸을 열고 다음 명령을 실행합니다.

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>데이터베이스 만들기

Cosmos DB 데이터베이스를 만들려면 Azure CLI 또는 클라우드 셸을 열고 다음 명령을 실행합니다.

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>컨테이너 만들기

1000 RU/s 및 파티션 키를 포함한 Cosmos DB 컨테이너를 만들려면 Azure CLI 또는 클라우드 셸을 열고 다음 명령을 실행합니다.

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>컨테이너 처리량 변경

Cosmos DB 컨테이너 처리량을 400 RU/s로 변경하려면 Azure CLI 또는 클라우드 셸을 열고 다음 명령을 실행합니다.

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>계정 키 나열

Azure Cosmos DB 계정을 만들면 해당 서비스에서 Azure Cosmos DB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. Azure Cosmos DB에서는 2개의 액세스 키를 제공해서 사용자가 Azure Cosmos DB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다. 읽기 전용 작업을 인증하기 위한 읽기 전용 키도 사용할 수 있습니다. 두 개의 읽기-쓰기 키(기본 및 보조) 및 두 개의 읽기 전용 키(기본 및 보조)가 있습니다. 다음 명령을 실행하여 계정에 대한 키를 가져올 수 있습니다.

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>연결 문자열 나열

Cosmos DB 계정에 애플리케이션을 연결하기 위한 연결 문자열은 다음 명령을 사용하여 검색할 수 있습니다.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>계정 키 다시 생성

저장소 연결을 더욱 안전하게 유지할 수 있도록 정기적으로 Azure Cosmos DB 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 액세스 키 하나를 다시 생성하는 동안 다른 액세스 키를 사용하여 Azure Cosmos DB 계정에 대한 연결을 유지할 수 있습니다.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 다음을 참조하세요.

- [Azure CLI 설치](/cli/azure/install-azure-cli)
- [Azure CLI 참조](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Azure Cosmos DB에 대한 Azure CLI 추가 샘플](cli-samples.md)
