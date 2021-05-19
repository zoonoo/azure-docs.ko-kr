---
title: Azure CLI를 사용하여 Azure Cosmos DB에서 지속적인 백업 및 특정 시점 복원을 구성합니다.
description: 계정에 지속적인 백업을 프로비전하고 Azure CLI를 사용하여 데이터를 복원하는 방법을 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377332"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>지속적인 백업 및 특정 시점 복원(미리 보기) 구성 및 관리 - Azure CLI 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대한 특정 시점 복원 기능(지속적인 백업 모드)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB의 특정 시점 복원 기능(미리 보기)을 사용하면 컨테이너 내에서 실수로 인한 변경을 복구하거나, 삭제된 계정, 데이터베이스 또는 컨테이너를 복원하거나, 백업이 있던 모든 지역으로 복원할 수 있습니다. 지속적인 백업 모드를 사용하면 지난 30일 이내의 모든 시점으로 복원할 수 있습니다.

이 문서에서는 계정에 지속적인 백업을 프로비전하고 Azure CLI를 사용하여 데이터를 복원하는 방법을 설명합니다.

## <a name="install-azure-cli"></a><a id="install"></a>Azure CLI 설치

1. 최신 버전의 Azure CLI 설치

   * 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 2.17.1 이상 버전을 설치합니다.
   * CLI를 이미 설치한 경우 `az upgrade` 명령을 실행하여 최신 버전으로 업데이트합니다. 이 명령은 2.11 이상 CLI 버전에서만 작동합니다. 이전 버전인 경우 위의 링크를 사용하여 최신 버전을 설치하세요.

1. `cosmosdb-preview` CLI 확장 설치

   * 특정 시점 복원 명령은 `cosmosdb-preview` 확장 아래에서 사용할 수 있습니다.
   * 이 확장은 다음 `az extension add --name cosmosdb-preview` 명령을 실행하여 설치할 수 있습니다.
   * 이 확장은 `az extension remove --name cosmosdb-preview` 명령을 실행하여 제거할 수 있습니다.

1. 로그인하고 구독 선택

   * `az login` 명령을 사용하여 Azure 계정에 로그인합니다.
   * `az account set -s <subscriptionguid>` 명령을 사용하여 필요한 구독을 선택합니다.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>SQL API 계정에 지속적인 백업 프로비전

SQL API 계정에 지속적인 백업을 프로비전하려면 일반 프로비전 명령과 함께 추가 인수 `--backup-policy-type Continuous`를 전달해야 합니다. 다음 명령은 미국 서부 지역에서 *myrg* 리소스 그룹 아래에 지속적인 백업 정책을 사용하여 생성된 `pitracct2`라는 단일 지역 쓰기 계정의 예입니다.

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api">MongoDB용 Azure Cosmos DB API 계정에 지속적인 백업 프로비전</a>

다음 명령은 미국 서부 지역에서 *myrg* 리소스 그룹 아래에 지속적인 백업 정책을 사용하여 생성된 `pitracct3`라는 단일 지역 쓰기 계정의 예를 보여 줍니다.

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>CLI를 사용하여 복원 작업 트리거

복원을 트리거하는 가장 간단한 방법은 대상 계정의 이름, 원본 계정, 위치, 리소스 그룹, 타임스탬프(UTC) 및 필요에 따라 데이터베이스와 컨테이너 이름을 사용하여 복원 명령을 실행하는 것입니다. 다음은 복원 작업을 트리거하는 몇 가지 예입니다.

1. 기존 계정에서 복원하여 새 Azure Cosmos DB 계정을 만듭니다.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. 기존 데이터베이스 계정에서 선택한 데이터베이스 및 컨테이너만 복원하여 새 Azure Cosmos DB 계정을 만듭니다.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API에 대해 복원 가능한 리소스 열거

아래에 설명된 열거 명령은 다양한 타임스탬프에서 복원에 사용할 수 있는 리소스를 검색하는 데 유용합니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다.

**현재 구독에서 복원할 수 있는 모든 계정 나열**

다음 CLI 명령을 실행하여 현재 구독에서 복원할 수 있는 모든 계정을 나열합니다.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

응답에는 복원할 수 있는 모든 데이터베이스 계정(라이브 및 삭제된 계정 모두)과 복원할 수 있는 지역이 포함됩니다.

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

계정의 `CreationTime` 또는 `DeletionTime`와 마찬가지로 지역에도 `CreationTime` 또는 `DeletionTime`가 있습니다. 이러한 시간을 사용하여 올바른 지역 및 해당 지역으로 복원할 유효한 시간 범위를 선택할 수 있습니다.

**라이브 데이터베이스 계정에서 모든 버전의 데이터베이스를 나열**

모든 버전의 데이터베이스를 나열하면 데이터베이스의 실제 존재 시간을 알 수 없는 시나리오에서 올바른 데이터베이스를 선택할 수 있습니다.

다음 CLI 명령을 실행하여 모든 버전의 데이터베이스를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `instanceId` 및 `location` 매개 변수는 `az cosmosdb restorable-database-account list` 명령 응답의 `name` 및 `location` 속성에서 가져옵니다. instanceId 특성은 복원되는 원본 데이터베이스 계정의 속성이기도 합니다.

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

이제 이 명령 출력에는 데이터베이스가 만들어지고 삭제된 시간이 표시됩니다.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**라이브 데이터베이스 계정의 데이터베이스에서 모든 SQL 컨테이너 버전을 나열**

다음 명령을 사용하여 모든 버전의 SQL 컨테이너를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `databaseRid` 매개 변수는 복원하려는 데이터베이스의 `ResourceId`입니다. `az cosmosdb sql restorable-database list` 명령의 응답에 포함된 `ownerResourceid` 특성의 값입니다.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

이 명령 출력에는 이 데이터베이스 내의 모든 컨테이너에 대해 수행된 작업의 목록이 포함되어 있습니다.

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너 찾기**

다음 명령을 사용하여 특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너의 목록을 가져옵니다. 이 명령은 라이브 계정에서만 작동합니다.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>MongoDB API 계정에 대해 복원 가능한 리소스 열거

아래에 설명된 열거 명령은 다양한 타임스탬프에서 복원에 사용할 수 있는 리소스를 검색하는 데 유용합니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다. SQL API와 마찬가지로 `az cosmosdb` 명령을 사용할 수 있지만 `sql` 대신 `mongodb`를 매개 변수로 사용해야 합니다. 이러한 명령은 라이브 계정에서만 작동합니다.

**라이브 데이터베이스 계정에서 모든 버전의 MongoDB 데이터베이스를 나열**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**라이브 데이터베이스 계정의 데이터베이스에서 모든 버전의 MongoDB 컬렉션을 나열**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**특정 타임스탬프 및 지역에서 복원에 사용할 수 있는 MongoDB 데이터베이스 계정의 모든 리소스를 나열**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md) 또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용하여 지속적인 백업을 구성하고 관리합니다.
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한 관리](continuous-backup-restore-permissions.md)
