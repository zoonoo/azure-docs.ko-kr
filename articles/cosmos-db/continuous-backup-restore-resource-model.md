---
title: Azure Cosmos DB 지정 시간 복원 기능의 리소스 모델입니다.
description: 이 문서에서는 Azure Cosmos DB 지정 시간 복원 기능의 리소스 모델에 대해 설명 합니다. SQL 및 MongoDB 계정에 대 한 Azure Cosmos DB API에서 복원할 수 있는 연속 백업 및 리소스를 지 원하는 매개 변수를 설명 합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 91421b66af441ed2c7e9c8b66c16ee83f489b03e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538507"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Azure Cosmos DB 지정 시간 복원 기능 (미리 보기)에 대 한 리소스 모델
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 특정 시점 복원 기능 (연속 백업 모드)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Cosmos DB 지정 시간 복원 기능 (미리 보기)에 대 한 리소스 모델을 설명 합니다. SQL 및 MongoDB 계정에 대 한 Azure Cosmos DB API에서 복원할 수 있는 연속 백업 및 리소스를 지 원하는 매개 변수를 설명 합니다.

## <a name="database-accounts-resource-model"></a>데이터베이스 계정의 리소스 모델

데이터베이스 계정의 리소스 모델은 새로운 복원 시나리오를 지원 하기 위해 몇 가지 추가 속성으로 업데이트 됩니다. 이러한 속성은 **Backuppolicy, CreateMode 및 RestoreParameters입니다.**

### <a name="backuppolicy"></a>BackupPolicy

"Backuppolicy" 매개 변수 아래에서 "Type" 이라는 계정 수준 백업 정책의 새 속성은 연속 백업 및 지정 시간 복원 기능을 사용 하도록 설정 합니다. 이 모드를 **연속 백업** 이라고 합니다. 공개 미리 보기에서는 계정을 만들 때만이 모드를 설정할 수 있습니다. 사용 하도록 설정 된 후에는이 계정 내에서 만들어진 모든 컨테이너와 데이터베이스에 연속 백업 및 지정 시간 복원 기능이 기본적으로 사용 되도록 설정 됩니다.

> [!NOTE]
> 현재 지정 시간 복원 기능은 공개 미리 보기로 제공 되며 MongoDB 및 SQL 계정에 대 한 Azure Cosmos DB API에 사용할 수 있습니다. 연속 모드를 사용 하 여 계정을 만든 후에는 정기적 모드로 전환할 수 없습니다.

### <a name="createmode"></a>CreateMode

이 속성은 계정을 만든 방법을 나타냅니다. 가능한 값은 "Default" 및 "Restore"입니다. 복원을 수행 하려면이 값을 "Restore"로 설정 하 고 속성에 적절 한 값을 제공 합니다 `RestoreParameters` .

### <a name="restoreparameters"></a>RestoreParameters

리소스에는 `RestoreParameters` 계정 ID, 복원 시간 및 복원 해야 하는 리소스를 비롯 한 복원 작업 세부 정보가 포함 됩니다.

|속성 이름 |설명  |
|---------|---------|
|restoreMode  | 복원 모드는 "PointInTime" 이어야 합니다. |
|restoreSource   |  복원이 시작 되는 원본 계정의 instanceId입니다.       |
|restoreTimestampInUtc  | 계정을 복원 해야 하는 시간 (UTC)입니다. |
|databasesToRestore   | `DatabaseRestoreSource`복원 해야 하는 데이터베이스 및 컨테이너를 지정 하는 개체의 목록입니다. 이 값이 비어 있으면 전체 계정이 복원 됩니다.   |

**Databaserestoreresource** -각 리소스는 단일 데이터베이스와 해당 데이터베이스의 모든 컬렉션을 나타냅니다.

|속성 이름 |설명  |
|---------|---------|
|databaseName | 데이터베이스의 이름 |
| collectionNames| 이 데이터베이스에 있는 컨테이너 목록 |

### <a name="sample-resource"></a>샘플 리소스

다음 JSON은 연속 백업을 사용 하는 예제 데이터베이스 계정 리소스입니다.

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>복원 가능한 리소스

리소스에 대 한 중요 한 정보를 검색 하는 데 사용할 수 있는 새 리소스 및 Api 집합을 사용할 수 있습니다 .이 정보는 복원할 수 있는 위치, 복원할 수 있는 위치 및 이러한 리소스에 대해 키 작업이 수행 된 타임 스탬프입니다.

> [!NOTE]
> 이러한 리소스를 열거 하는 데 사용 되는 모든 API에는 다음 권한이 필요 합니다.
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>복원 가능한 데이터베이스 계정

이 리소스는 복원할 수 있는 데이터베이스 계정 인스턴스를 포함 합니다. 데이터베이스 계정은 삭제 된 계정 이거나 라이브 계정일 수 있습니다. 복원 하려는 원본 데이터베이스 계정을 찾을 수 있는 정보를 포함 합니다.

|속성 이름 |설명  |
|---------|---------|
| ID | 리소스의 고유 식별자입니다. |
| accountName | 전역 데이터베이스 계정 이름입니다. |
| creationTime | 계정을 만든 UTC 시간입니다.  |
| deletionTime | 계정이 삭제 된 UTC 시간입니다.  계정이 활성 상태인 경우에는이 값이 비어 있습니다. |
| apiType | Azure Cosmos DB 계정의 API 유형입니다. |
| restorableLocations | 계정이 있던 위치의 목록입니다. |
| restorableLocations: locationName | 지역 계정의 지역 이름입니다. |
| restorableLocations: 지역 Aldatabaseaccountinstanceei | 지역 계정의 GUID입니다. |
| restorableLocations: creationTime | 지역 계정을 만든 UTC 시간입니다.|
| restorableLocations: deletionTime | 지역 계정이 삭제 된 UTC 시간입니다. 지역 계정이 live 인 경우이 값은 비어 있습니다.|

모든 복원 가능한 계정 목록을 가져오려면 [복원 가능한 Database accounts-list](restorable-database-accounts-list.md) 또는 [복원 가능한 database accounts-location by location](restorable-database-accounts-list-by-location.md) 문서를 참조 하세요.

### <a name="restorable-sql-database"></a>복원 가능한 SQL 데이터베이스

각 리소스는 SQL Database에서 발생 한 만들기 및 삭제와 같은 변형 이벤트의 정보를 포함 합니다. 이 정보는 데이터베이스가 실수로 삭제 된 경우와 해당 이벤트가 발생 한 시기를 확인 해야 하는 경우에 유용할 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
| eventTimestamp | 데이터베이스를 만들거나 삭제할 때의 UTC 시간입니다. |
| ownerId | SQL 데이터베이스의 이름입니다. |
| 소유자 Resourceid | SQL 데이터베이스의 리소스 ID입니다.|
| VM에서 operationType 작업을 | 이 데이터베이스 이벤트의 작업 유형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>만들기: 데이터베이스 만들기 이벤트</li><li>삭제: 데이터베이스 삭제 이벤트</li><li>Replace: 데이터베이스 수정 이벤트</li><li>SystemOperation: 시스템에 의해 트리거되는 데이터베이스 수정 이벤트입니다. 이 이벤트는 사용자가 시작 하지 않습니다.</li></ul> |
| 데이터베이스 |이벤트 당시 SQL 데이터베이스의 속성|

모든 데이터베이스 변경이 목록을 가져오려면 [복원 가능한 Sql Databases-list](restorable-sql-databases-list.md) 문서를 참조 하세요.

### <a name="restorable-sql-container"></a>복원 가능한 SQL 컨테이너

각 리소스는 SQL 컨테이너에서 발생 한 만들기 및 삭제와 같은 변형 이벤트의 정보를 포함 합니다. 이 정보는 컨테이너가 수정 되거나 삭제 된 시나리오와 해당 이벤트가 발생 한 시기를 확인 해야 하는 경우에 유용할 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
| eventTimestamp    | 이 컨테이너 이벤트가 발생 한 UTC 시간입니다.|
| ownerId| SQL 컨테이너의 이름입니다.|
| 소유자 Resourceid   | SQL 컨테이너의 리소스 ID입니다.|
| VM에서 operationType 작업을 | 이 컨테이너 이벤트의 작업 유형입니다. 가능한 값은 다음과 같습니다. <br/><ul><li>만들기: 컨테이너 만들기 이벤트</li><li>삭제: 컨테이너 삭제 이벤트</li><li>Replace: 컨테이너 수정 이벤트</li><li>SystemOperation: 시스템에 의해 트리거되는 컨테이너 수정 이벤트입니다. 이 이벤트는 사용자가 시작 하지 않습니다.</li></ul> |
| container | 이벤트 당시 SQL 컨테이너의 속성입니다.|

동일한 데이터베이스의 모든 컨테이너 변경이 목록을 가져오려면 [복원 가능한 Sql 컨테이너-목록](restorable-sql-containers-list.md) 문서를 참조 하세요.

### <a name="restorable-sql-resources"></a>복원 가능한 SQL 리소스

각 리소스는 단일 데이터베이스와 해당 데이터베이스의 모든 컨테이너를 나타냅니다.

|속성 이름 |설명  |
|---------|---------|
| databaseName  | SQL 데이터베이스의 이름입니다.
| collectionNames   | 이 데이터베이스의 SQL 컨테이너 목록입니다.|

지정 된 타임 스탬프 및 위치의 계정에 존재 하는 SQL database 및 container combo 목록을 가져오려면 [복원 가능한 SQL Resources-list](restorable-sql-resources-list.md) article을 참조 하세요.

### <a name="restorable-mongodb-database"></a>복원 가능한 MongoDB 데이터베이스

각 리소스는 MongoDB 데이터베이스에서 발생 한 만들기 및 삭제와 같은 변형 이벤트의 정보를 포함 합니다. 이 정보는 데이터베이스가 실수로 삭제 된 경우와 해당 이벤트가 발생 했을 때 사용자가 확인 해야 하는 경우에 유용할 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
|eventTimestamp| 이 데이터베이스 이벤트가 발생 한 UTC 시간입니다.|
| ownerId| MongoDB 데이터베이스의 이름입니다. |
| 소유자 Resourceid   | MongoDB 데이터베이스의 리소스 ID입니다. |
| VM에서 operationType 작업을 |   이 데이터베이스 이벤트의 작업 유형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li> 만들기: 데이터베이스 만들기 이벤트</li><li> 삭제: 데이터베이스 삭제 이벤트</li><li> Replace: 데이터베이스 수정 이벤트</li><li> SystemOperation: 시스템에 의해 트리거되는 데이터베이스 수정 이벤트입니다. 이 이벤트는 사용자가 시작 하지 않습니다. </li></ul> |

모든 데이터베이스 변형 목록을 가져오려면 [복원 가능한 Mongodb Databases-list](restorable-mongodb-databases-list.md) 문서를 참조 하세요.

### <a name="restorable-mongodb-collection"></a>복원 가능한 MongoDB 컬렉션

각 리소스는 MongoDB 컬렉션에서 발생 한 만들기 및 삭제와 같은 변형 이벤트의 정보를 포함 합니다. 이 정보는 컬렉션이 수정 또는 삭제 된 경우와 해당 이벤트가 발생 했을 때 사용자가 확인 해야 하는 경우에 유용할 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
| eventTimestamp |이 컬렉션 이벤트가 발생 한 UTC 시간입니다. |
| ownerId| MongoDB 컬렉션의 이름입니다. |
| 소유자 Resourceid   | MongoDB collection의 리소스 ID입니다. |
| VM에서 operationType 작업을 |이 컬렉션 이벤트의 작업 유형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>만들기: 컬렉션 생성 이벤트</li><li>삭제: 컬렉션 삭제 이벤트</li><li>Replace: collection 수정 이벤트</li><li>SystemOperation: 시스템에 의해 트리거되는 컬렉션 수정 이벤트입니다. 이 이벤트는 사용자가 시작 하지 않습니다.</li></ul> |

동일한 데이터베이스의 모든 컨테이너 변경이 목록을 가져오려면 [복원 가능한 Mongodb Collections-list](restorable-mongodb-collections-list.md) 문서를 참조 하세요.

### <a name="restorable-mongodb-resources"></a>복원 가능한 MongoDB 리소스

각 리소스는 단일 데이터베이스와 해당 데이터베이스의 모든 컬렉션을 나타냅니다.

|속성 이름 |설명  |
|---------|---------|
| databaseName  |MongoDB 데이터베이스의 이름입니다. |
| collectionNames | 이 데이터베이스의 MongoDB 컬렉션 목록입니다. |

지정 된 타임 스탬프 및 위치의 계정에 있는 모든 MongoDB 데이터베이스 및 컬렉션 조합의 목록을 가져오려면 [복원 가능한 MongoDB Resources-list](restorable-mongodb-resources-list.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용 하 여 연속 백업을 구성 하 고 관리 합니다.
* 연속 백업 모드를 사용 하 여 데이터를 복원 하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md) 합니다.
