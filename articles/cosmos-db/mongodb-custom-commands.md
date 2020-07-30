---
title: MongoDB에 대 한 Azure Cosmos DB API의 데이터를 관리 하기 위한 MongoDB 확장 명령
description: 이 문서에서는 MongoDB 확장 명령을 사용 하 여 Azure Cosmos DB의 MongoDB API에 저장 된 데이터를 관리 하는 방법을 설명 합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: lbosq
ms.custom: devx-track-javascript
ms.openlocfilehash: 7b0ac1e301705b24d706638deb3ee0a15d49c87b
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87415094"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 확장 명령을 사용 하 여 Azure Cosmos DB의 MongoDB API에 저장 된 데이터를 관리 합니다. 

다음 문서에는 Azure Cosmos DB의 MongoDB API에만 적용 되는 사용자 지정 작업 명령이 포함 되어 있습니다. 이러한 명령을 사용 하 여 [Azure Cosmos DB 용량 모델과](databases-containers-items.md)관련 된 데이터베이스 리소스를 만들고 가져올 수 있습니다.

MongoDB에 대 한 Azure Cosmos DB API를 사용 하 여 MongoDB 앱에 대 한 투자를 유지 하면서 전역 배포, 자동 분할, 고가용성, 대기 시간 보장, 자동, 미사용 암호화, 백업 등의 Cosmos DB 이점을 누릴 수 있습니다. 오픈 소스 [MongoDB 클라이언트 드라이버](https://docs.mongodb.org/ecosystem/drivers)를 사용 하 여 Azure Cosmos DB의 MongoDB API와 통신할 수 있습니다. MongoDB에 대 한 Azure Cosmos DB API를 사용 하면 [MongoDB 유선 프로토콜](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)을 준수 함으로써 기존 클라이언트 드라이버를 사용할 수 있습니다.

## <a name="mongodb-protocol-support"></a>MongoDB 프로토콜 지원

MongoDB에 대 한 Azure Cosmos DB API는 MongoDB server 버전 3.2 및 3.6와 호환 됩니다. 자세한 내용은 [지원 되는 기능 및 구문](mongodb-feature-support.md) 을 참조 하세요. 

다음 확장 명령은 데이터베이스 요청을 통해 Azure Cosmos DB 특정 리소스를 만들고 수정 하는 기능을 제공 합니다.

* [데이터베이스 만들기](#create-database)
* [데이터베이스 업데이트](#update-database)
* [데이터베이스 가져오기](#get-database)
* [컬렉션 만들기](#create-collection)
* [컬렉션 업데이트](#update-collection)
* [컬렉션 가져오기](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>데이터베이스 만들기

Create database extension 명령은 새 MongoDB 데이터베이스를 만듭니다. 데이터베이스 이름은 명령에 의해 설정 된 데이터베이스 컨텍스트에서 사용할 수 있습니다 `use database` . 다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
| `customAction`   |  `string`  |   사용자 지정 명령의 이름은 "CreateDatabase" 여야 합니다.      |
| `offerThroughput` | `int`  | 데이터베이스에 대해 설정한 프로 비전 된 처리량입니다. 이 매개 변수는 선택 사항입니다. |
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md)에 필요 합니다. 이 개체에는 자동 크기 조정 용량 모드와 관련 된 설정이 포함 됩니다. `maxThroughput`컬렉션이 동적으로 증가 하는 가장 많은 요청 단위를 설명 하는 값을 설정할 수 있습니다. |

### <a name="output"></a>출력

명령이 성공 하면 다음 응답이 반환 됩니다.

```javascript
{ "ok" : 1 }
```

출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예제

#### <a name="create-a-database"></a>데이터베이스 만들기

모든 기본값을 사용 하는 라는 데이터베이스를 만들려면 `"test"` 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

이 명령은 데이터베이스 수준 처리량을 사용 하지 않고 데이터베이스를 만듭니다. 이는이 데이터베이스 내의 컬렉션이 사용 해야 하는 처리량을 지정 해야 함을 의미 합니다.

#### <a name="create-a-database-with-throughput"></a>처리량이 있는 데이터베이스 만들기

라는 데이터베이스를 만들고 `"test"` 1000 RUs의 [데이터베이스 수준](set-throughput.md#set-throughput-on-a-database) 프로 비전 된 처리량을 지정 하려면 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

그러면 데이터베이스가 생성 되 고 그에 대 한 처리량이 설정 됩니다. 컬렉션을 [특정 처리량 수준](set-throughput.md#set-throughput-on-a-database-and-a-container)으로 만들지 않는 한이 데이터베이스 내의 모든 컬렉션은 집합 처리량을 공유 합니다.

#### <a name="create-a-database-with-autoscale-throughput"></a>자동 크기 조정 처리량을 사용 하 여 데이터베이스 만들기

라는 데이터베이스를 만들고 `"test"` [데이터베이스 수준](set-throughput.md#set-throughput-on-a-database)에서 자동 크기 조정 최대 처리량을 2만 r u/초로 지정 하려면 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a>데이터베이스 업데이트

데이터베이스 확장 업데이트 명령은 지정 된 데이터베이스와 연결 된 속성을 업데이트 합니다. 다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
| `customAction`    |    `string`     |   사용자 지정 명령의 이름입니다. "UpdateDatabase" 여야 합니다.      |
|  `offerThroughput`   |  `int`       |     데이터베이스에서 [데이터베이스 수준 처리량](set-throughput.md#set-throughput-on-a-database) 을 사용 하는 경우 데이터베이스에 대해 설정 하려는 새로운 프로 비전 된 처리량  |
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md)에 필요 합니다. 이 개체에는 자동 크기 조정 용량 모드와 관련 된 설정이 포함 됩니다. `maxThroughput`데이터베이스를 동적으로 증가 시킬 가장 높은 요청 단위를 설명 하는 값을 설정할 수 있습니다. |

이 명령은 세션의 컨텍스트에 지정 된 데이터베이스를 사용 합니다. 이는 명령에 사용 된 데이터베이스입니다 `use <database>` . 지금은이 명령을 사용 하 여 데이터베이스 이름을 변경할 수 없습니다.

### <a name="output"></a>출력

명령이 성공 하면 다음 응답이 반환 됩니다.

```javascript
{ "ok" : 1 }
```

출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예제

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>데이터베이스와 연결 된 프로 비전 된 처리량 업데이트

이름이 1200 RUs 인 데이터베이스의 프로 비전 된 처리량을 업데이트 하려면 `"test"` 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>데이터베이스와 연결 된 자동 크기 조정 처리량 업데이트

이름이 2만 RUs 인 데이터베이스의 프로 비전 된 처리량을 업데이트 `"test"` 하거나 [자동 크기 조정 처리량 수준](provision-throughput-autoscale.md)으로 변환 하려면 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a>데이터베이스 가져오기

데이터베이스 확장 가져오기 명령은 데이터베이스 개체를 반환 합니다. 데이터베이스 이름은 명령이 실행 되는 데이터베이스 컨텍스트에서 사용 됩니다.

```javascript
{
  customAction: "GetDatabase"
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   사용자 지정 명령의 이름입니다. "GetDatabase" 여야 합니다.|
        
### <a name="output"></a>출력

명령이 성공 하면 응답에 다음 필드를 포함 하는 문서가 포함 됩니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |   `int`     |   응답 상태입니다. 1 = = 성공. 0 = = 실패      |
| `database`    |    `string`        |   데이터베이스의 이름입니다.      |
|   `provisionedThroughput`  |    `int`      |    데이터베이스에서 [수동 데이터베이스 수준 처리량](set-throughput.md#set-throughput-on-a-database) 을 사용 하는 경우 데이터베이스에 설정 된 프로 비전 된 처리량     |
| `autoScaleSettings` | `Object` | 이 개체는 [자동 크기 조정 모드](provision-throughput-autoscale.md)를 사용 하는 경우 데이터베이스와 연결 된 용량 매개 변수를 포함 합니다. 이 `maxThroughput` 값은 데이터베이스를 동적으로 증가 시킬 수 있는 가장 높은 수준의 요청 단위를 설명 합니다. |

명령이 실패 하면 기본 사용자 지정 명령 응답이 반환 됩니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예제

#### <a name="get-the-database"></a>데이터베이스 가져오기

이라는 데이터베이스에 대 한 데이터베이스 개체를 가져오려면 `"test"` 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

데이터베이스에 연결 된 처리량이 없으면 출력은 다음과 같습니다.

```javascript
{ "database" : "test", "ok" : 1 }
```

데이터베이스의 [데이터베이스 수준 수동 처리량](set-throughput.md#set-throughput-on-a-database) 이 데이터베이스에 연결 된 경우 출력에는 다음 값이 표시 됩니다 `provisionedThroughput` .

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

데이터베이스에 연결 된 [데이터베이스 수준 자동 크기 조정 처리량](provision-throughput-autoscale.md) 이 있는 경우 출력에는 데이터베이스에 `provisionedThroughput` 대 한 최소 r u/s를 설명 하는와 `autoScaleSettings` 데이터베이스의 `maxThroughput` 최대 u r e/s를 설명 하는를 포함 하는 개체가 표시 됩니다.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a>컬렉션 만들기

컬렉션 확장 만들기 명령은 새 MongoDB 컬렉션을 만듭니다. 데이터베이스 이름은 명령에 의해 설정 된 데이터베이스 컨텍스트에서 사용 됩니다 `use database` . CreateCollection 명령의 형식은 다음과 같습니다.

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

| **필드** | **형식** | **필수** | **설명** |
|---------|---------|---------|---------|
| `customAction` | `string` | 필수 | 사용자 지정 명령의 이름입니다. "CreateCollection" 이어야 합니다.|
| `collection` | `string` | 필수 | 컬렉션의 이름입니다. 특수 문자나 공백은 허용 되지 않습니다.|
| `offerThroughput` | `int` | 선택 사항 | 데이터베이스에 설정할 프로 비전 된 처리량입니다. 이 매개 변수를 지정 하지 않으면 기본값은 최소, 400 r u/초입니다. * 1만 r u/초를 초과 하는 처리량을 지정 하려면 `shardKey` 매개 변수가 필요 합니다.|
| `shardKey` | `string` | 처리량이 많은 컬렉션에 필요 합니다. | 분할 된 collection에 대 한 분할 키의 경로입니다. 이 매개 변수는에서 1만 이상/s를 설정 하는 경우에 필요 `offerThroughput` 합니다.  지정 된 경우에는 삽입 된 모든 문서에이 키와 값이 필요 합니다. |
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md) 에 필요 | 이 개체에는 자동 크기 조정 용량 모드와 관련 된 설정이 포함 됩니다. `maxThroughput`컬렉션이 동적으로 증가 하는 가장 많은 요청 단위를 설명 하는 값을 설정할 수 있습니다. |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환 합니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예제

#### <a name="create-a-collection-with-the-minimum-configuration"></a>최소 구성을 사용 하 여 컬렉션 만들기

이름 및 기본값을 사용 하 여 새 컬렉션을 만들려면 `"testCollection"` 다음 명령을 사용 합니다. 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

그러면 400 r u/s를 포함 하는 새로운 고정 된 unsharded 컬렉션 및 `_id` 자동으로 생성 된 필드의 인덱스가 생성 됩니다. 이 유형의 구성은 함수를 통해 새 컬렉션을 만들 때도 적용 됩니다 `insert()` . 예를 들면 다음과 같습니다. 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Unsharded 컬렉션 만들기

1000 RUs의 이름 및 프로 비전 된 처리량을 사용 하 여 unsharded 컬렉션을 만들려면 `"testCollection"` 다음 명령을 사용 합니다. 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

분할 `offerThroughput` 키를 지정할 필요 없이 최대 1만 r u/초를 사용 하 여 컬렉션을 만들 수 있습니다. 처리량이 큰 컬렉션은 다음 섹션을 확인 하세요.

#### <a name="create-a-sharded-collection"></a>분할 된 컬렉션 만들기

11000 RUs의 이름 및 프로 비전 된 처리량과 "분할 된" 속성을 사용 하 여 컬렉션을 만들려면 `"testCollection"` `shardkey` 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

이제이 명령에 `shardKey` 는에 지정 된 1만 r u/초 이상의 매개 변수가 필요 합니다 `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Unsharded 자동 크기 조정 컬렉션 만들기

`'testCollection'` [자동 크기 조정 처리량 용량](provision-throughput-autoscale.md) 을 4000 r u/s로 설정 하는 이라는 unsharded 컬렉션을 만들려면 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

값의 경우 분할 `autoScaleSettings.maxThroughput` 키 없이 4000 r u/초에서 1만 r u/초 사이의 범위를 지정할 수 있습니다. 크기 자동 조정 처리량을 높이려면 매개 변수를 지정 해야 `shardKey` 합니다.

#### <a name="create-a-sharded-autoscale-collection"></a>분할 된 자동 크기 조정 컬렉션 만들기

이라는 분할 키를 사용 하 여 라는 분할 된 컬렉션을 만들고 `'testCollection'` `'a.b'` [자동 크기 조정 처리량 용량](provision-throughput-autoscale.md) 을 2만 r u/s로 설정한 상태에서 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a>컬렉션 업데이트

컬렉션 확장 업데이트 명령은 지정 된 컬렉션과 연결 된 속성을 업데이트 합니다.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   사용자 지정 명령의 이름입니다. "UpdateCollection" 이어야 합니다.      |
|  `collection`   |   `string`      |   컬렉션의 이름입니다.       |
| `offerThroughput` | `int` |   컬렉션에 설정할 프로 비전 된 처리량입니다.|
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md)에 필요 합니다. 이 개체에는 자동 크기 조정 용량 모드와 관련 된 설정이 포함 됩니다. 이 `maxThroughput` 값은 컬렉션이 동적으로 증가 하는 최대 요청 단위를 설명 합니다. |

## <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환 합니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예제

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>컬렉션과 연결 된 프로 비전 된 처리량 업데이트

이름이 1200 RUs 인 컬렉션의 프로 비전 된 처리량을 업데이트 하려면 `"testCollection"` 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>컬렉션 가져오기

컬렉션 가져오기 사용자 지정 명령은 컬렉션 개체를 반환 합니다.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
| `customAction`    |   `string`      |   사용자 지정 명령의 이름입니다. "GetCollection" 이어야 합니다.      |
| `collection`    |    `string`     |    컬렉션의 이름입니다.     |

### <a name="output"></a>출력

명령이 성공 하면 응답에 다음 필드를 포함 하는 문서가 포함 됩니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 = = 성공. 0 = = 실패      |
| `database`    |    `string`     |   데이터베이스의 이름입니다.      |
| `collection`    |    `string`     |    컬렉션의 이름입니다.     |
|  `shardKeyDefinition`   |   `document`      |  분할 키로 사용 되는 인덱스 사양 문서입니다. 선택적 응답 매개 변수입니다.       |
|  `provisionedThroughput`   |   `int`      |    컬렉션에 설정할 프로 비전 된 처리량입니다. 선택적 응답 매개 변수입니다.     |
| `autoScaleSettings` | `Object` | 이 개체는 [자동 크기 조정 모드](provision-throughput-autoscale.md)를 사용 하는 경우 데이터베이스와 연결 된 용량 매개 변수를 포함 합니다. 이 `maxThroughput` 값은 컬렉션이 동적으로 증가 하는 최대 요청 단위를 설명 합니다. |

명령이 실패 하면 기본 사용자 지정 명령 응답이 반환 됩니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예제

#### <a name="get-the-collection"></a>컬렉션 가져오기

라는 컬렉션에 대 한 컬렉션 개체를 가져오려면 `"testCollection"` 다음 명령을 사용 합니다.

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

컬렉션에 연결 된 처리량 용량이 있는 경우 값이 포함 되 `provisionedThroughput` 고 출력은 다음과 같습니다.

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

컬렉션에 연결 된 자동 크기 조정 처리량이 있는 경우 `autoScaleSettings` `maxThroughput` 컬렉션이 동적으로 증가 하는 최대 처리량을 정의 하는 매개 변수를 포함 하는 개체를 포함 합니다. 또한 `provisionedThroughput` 컬렉션에 요청이 없는 경우이 컬렉션이 감소 하는 최소 처리량을 정의 하는 값도 포함 합니다. 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

자동 크기 조정 모드 또는 수동에서 컬렉션이 [데이터베이스 수준 처리량](set-throughput.md#set-throughput-on-a-database)을 공유 하는 경우 출력은 다음과 같습니다.

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>사용자 지정 명령의 기본 출력

지정 하지 않으면 사용자 지정 응답에 다음 필드를 포함 하는 문서가 포함 됩니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 = = 성공. 0 = = 실패      |
| `code`    |   `int`      |   명령이 실패 한 경우에만 반환 됩니다 (예: ok = = 0). MongoDB 오류 코드를 포함 합니다. 선택적 응답 매개 변수입니다.      |
|  `errMsg`   |  `string`      |    명령이 실패 한 경우에만 반환 됩니다 (예: ok = = 0). 사용자에 게 친숙 한 오류 메시지를 포함 합니다. 선택적 응답 매개 변수입니다.      |

예를 들면 다음과 같습니다.

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>다음 단계

다음으로 진행 하 여 다음 Azure Cosmos DB 개념을 알아볼 수 있습니다. 

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
