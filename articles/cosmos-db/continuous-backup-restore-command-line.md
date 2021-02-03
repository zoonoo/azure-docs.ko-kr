---
title: Azure CLI를 사용 하 여 Azure Cosmos DB에서 연속 백업 및 특정 시점 복원을 구성 합니다.
description: 연속 백업으로 계정을 프로 비전 하 고 Azure CLI를 사용 하 여 데이터를 복원 하는 방법을 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 735e85683bc72cb8d0ddc0d18d16f10f72ba7641
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527473"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-cli"></a>연속 백업 및 지정 시간 복원 구성 및 관리-Azure CLI 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB의 지정 시간 복원 기능을 사용 하면 삭제 된 계정, 데이터베이스 또는 컨테이너를 복원 하거나 백업이 있던 모든 지역으로 복원 하기 위해 컨테이너 내에서 실수로 인 한 변경 으로부터 복구 하는 데 도움이 됩니다. 연속 백업 모드를 사용 하면 지난 30 일 이내에 모든 시점으로 복원할 수 있습니다.

이 문서에서는 연속 백업으로 계정을 프로 비전 하 고 Azure CLI를 사용 하 여 데이터를 복원 하는 방법을 설명 합니다.

## <a name="install-azure-cli"></a><a id="install"></a>Azure CLI 설치

1. 최신 버전의 Azure CLI 설치

   * 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 2.17.1 보다 높은 버전을 설치 합니다.
   * CLI를 이미 설치한 경우에 `az upgrade` 는 명령을 실행 하 여 최신 버전으로 업데이트 합니다. 이 명령은 2.11 보다 높은 CLI 버전 에서만 작동 합니다. 이전 버전이 있는 경우 위의 링크를 사용 하 여 최신 버전을 설치 합니다.

1. `cosmosdb-preview`CLI 확장을 설치 합니다.

   * 지정 시간 복원 명령은 확장 아래에서 사용할 수 있습니다 `cosmosdb-preview` .
   * 다음 명령을 실행 하 여이 확장을 설치할 수 있습니다. `az extension add --name cosmosdb-preview`
   * 다음 명령을 실행 하 여이 확장을 제거할 수 있습니다. `az extension remove --name cosmosdb-preview`

1. 로그인하고 구독 선택

   * 명령을 사용 하 여 Azure 계정에 로그인 `az login` 합니다.
   * 명령을 사용 하 여 필요한 구독을 선택 `az account set -s <subscriptionguid>` 합니다.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>연속 백업을 사용 하 여 SQL API 계정 프로 비전

연속 백업을 사용 하 여 SQL API 계정을 프로 비전 하려면 `--backup-policy-type Continuous` 일반 프로 비전 명령과 함께 추가 인수를 전달 해야 합니다. 다음 명령은 `pitracct2` "myrg" 리소스 그룹의 "미국 서 부" 지역에서 생성 된 연속 백업 정책으로 명명 된 단일 지역 쓰기 계정의 예입니다.

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>연속 백업을 사용 하 여 MongoDB 용 Azure Cosmos DB API 계정 프로 비전

다음 명령은 `pitracct3` "myrg" 리소스 그룹에서 "미국 서 부" 영역을 만든 연속 백업 정책으로 명명 된 단일 지역 쓰기 계정의 예를 보여 줍니다.

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

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>CLI를 사용 하 여 복원 작업 트리거

복원을 트리거하는 가장 간단한 방법은 대상 계정의 이름, 원본 계정, 위치, 리소스 그룹, 타임 스탬프 (UTC) 및 필요에 따라 데이터베이스와 컨테이너 이름을 사용 하 여 복원 명령을 실행 하는 것입니다. 다음은 복원 작업을 트리거하는 몇 가지 예입니다.

1. 기존 계정에서 복원 하 여 새 Azure Cosmos DB 계정을 만듭니다.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. 기존 데이터베이스 계정에서 선택한 데이터베이스 및 컨테이너만 복원 하 여 새 Azure Cosmos DB 계정을 만듭니다.

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

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API에 대 한 복원 가능한 리소스 열거

아래에 설명 된 열거 명령은 다양 한 타임 스탬프에서 복원에 사용할 수 있는 리소스를 검색 하는 데 도움이 됩니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대 한 주요 이벤트의 피드를 제공 합니다.

**현재 구독에서 복원할 수 있는 모든 계정 나열**

다음 CLI 명령을 실행 하 여 현재 구독에서 복원할 수 있는 모든 계정을 나열 합니다.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

응답에는 복원할 수 있는 모든 데이터베이스 계정 (라이브 및 삭제)과 복원할 수 있는 지역이 포함 됩니다.

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

계정에 대 한 "CreationTime" 또는 "DeletionTime"와 마찬가지로 해당 지역에 대 한 "CreationTime" 또는 "DeletionTime"도 있습니다. 이러한 시간을 사용 하 여 해당 지역으로 복원할 올바른 지역 및 유효한 시간 범위를 선택할 수 있습니다.

**라이브 데이터베이스 계정에 있는 모든 버전의 데이터베이스 나열**

데이터베이스의 모든 버전을 나열 하면 데이터베이스의 실제 존재 시간을 알 수 없는 경우 올바른 데이터베이스를 선택할 수 있습니다.

다음 CLI 명령을 실행 하 여 모든 버전의 데이터베이스를 나열 합니다. 이 명령은 live 계정 에서만 작동 합니다. "InstanceId" 및 "location" 매개 변수는 명령 응답의 "name" 및 "location" 속성에서 가져옵니다 `az cosmosdb restorable-database-account list` . InstanceId 특성은 복원 되는 원본 데이터베이스 계정의 속성 이기도 합니다.

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

이제이 명령 출력에는 데이터베이스를 만들고 삭제 하는 시간을 표시 합니다.

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

**라이브 데이터베이스 계정에 있는 데이터베이스의 모든 버전의 SQL 컨테이너를 나열 합니다.**

다음 명령을 사용 하 여 모든 버전의 SQL 컨테이너를 나열 합니다. 이 명령은 live 계정 에서만 작동 합니다. "DatabaseRid" 매개 변수는 복원 하려는 데이터베이스의 "ResourceId"입니다. 이 값은 명령의 응답에서 찾은 "소유자 Resourceid" 특성의 값입니다 `az cosmosdb sql restorable-database list` .

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

이 명령 출력은이 데이터베이스 내의 모든 컨테이너에 대해 수행 된 작업 목록을 포함 합니다.

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

**지정 된 타임 스탬프에서 복원할 수 있는 데이터베이스나 컨테이너를 찾습니다.**

다음 명령을 사용 하 여 지정 된 타임 스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너 목록을 가져옵니다. 이 명령은 live 계정 에서만 작동 합니다.

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

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>MongoDB API 계정에 대 한 복원 가능한 리소스 열거

아래에 설명 된 열거 명령은 다양 한 타임 스탬프에서 복원에 사용할 수 있는 리소스를 검색 하는 데 도움이 됩니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대 한 주요 이벤트의 피드를 제공 합니다. SQL API와 마찬가지로 명령에는 " `az cosmosdb` sql" 대신 "mongodb"를 매개 변수로 사용할 수 있습니다. 이러한 명령은 라이브 계정에 대해서만 작동 합니다.

**라이브 데이터베이스 계정에 있는 mongodb 데이터베이스의 모든 버전을 나열 합니다.**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**라이브 데이터베이스 계정에 있는 데이터베이스의 mongodb 컬렉션의 모든 버전을 나열 합니다.**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**지정 된 타임 스탬프 및 지역에서 복원에 사용할 수 있는 mongodb 데이터베이스 계정의 모든 리소스를 나열 합니다.**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md)또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용 하 여 연속 백업을 구성 하 고 관리 합니다.
* [연속 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
* 연속 백업 모드를 사용 하 여 데이터를 복원 하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md) 합니다.
