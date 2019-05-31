---
title: Azure CLI를 사용하여 Azure Cosmos DB 리소스 관리
description: Azure CLI를 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 144515fef9da714ab80f15bb39757ed2283c6dd0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243371"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos 리소스 관리

다음 가이드에서는 Azure Cosmos DB 계정, 데이터베이스 및 Azure CLI를 사용 하 여 컨테이너 관리를 자동화 하는 일반적인 명령 설명 합니다. 모든 Azure Cosmos DB CLI 명령에 대한 참조 페이지는 [Azure CLI 참조](https://docs.microsoft.com/cli/azure/cosmosdb)에서 제공됩니다. 더 많은 예제는 [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)에서 확인할 수 있습니다. 여기에는 MongoDB, Gremlin, Cassandra 및 Table API에 대한 Cosmos DB 계정, 데이터베이스 및 컨테이너 만들기 및 관리 방법이 포함되어 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

SQL API, 미국 동부 및 미국 서 부 지역에서 세션 일관성을 사용 하 여 Azure Cosmos DB 계정을 만들려면 다음 명령을 실행 합니다.

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Azure Cosmos 계정 이름은 소문자 여야 합니다.

## <a name="create-a-database"></a>데이터베이스 만들기

Cosmos DB 데이터베이스를 만들려면 다음 명령을 실행 합니다.

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>컨테이너 만들기

RU/s 400이 고 파티션 키를 사용 하 여 Cosmos DB 컨테이너를 만들려면 다음 명령을 실행 합니다.

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>컨테이너 처리량 변경

Cosmos DB 컨테이너의 처리량 1000 RU/s를으로 변경 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>계정 키 나열

Cosmos 계정의 키를 가져오려면 다음 명령을 실행 합니다.

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>연결 문자열 나열

Cosmos 계정의 연결 문자열을 가져오려면 다음 명령을 실행 합니다.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>계정 키 다시 생성

Cosmos 계정의 새 기본 키를 다시 생성 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 다음을 참조하세요.

- [Azure CLI 설치](/cli/azure/install-azure-cli)
- [Azure CLI 참조](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Azure Cosmos DB에 대한 Azure CLI 추가 샘플](cli-samples.md)
