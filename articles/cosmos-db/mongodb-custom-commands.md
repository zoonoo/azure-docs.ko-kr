---
title: Azure Cosmos DB의 API for MongoDB에서 데이터를 관리하기 위한 MongoDB 확장 명령
description: 이 문서에서는 MongoDB 확장 명령을 사용하여 Azure Cosmos DB의 API for MongoDB에 저장된 데이터를 관리하는 방법을 설명합니다.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: deba6696eb71287902fa3970ed2d83d0b09ac08d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658489"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 확장 명령을 사용하여 Azure Cosmos DB의 API for MongoDB에 저장된 데이터 관리 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

다음 문서에는 Azure Cosmos DB의 API for MongoDB에만 적용되는 사용자 지정 작업 명령이 포함되어 있습니다. 이 명령을 사용하여 [Azure Cosmos DB 용량 모델](account-databases-containers-items.md)에만 적용되는 데이터베이스 리소스를 만들고 가져올 수 있습니다.

Azure Cosmos DB의 API for MongoDB를 사용하면 글로벌 배포, 자동 분할, 고가용성, 대기 시간 보장, 저장 데이터 암호화 등을 비롯한 Cosmos DB의 이점을 누리면서도 MongoDB 앱에 대한 투자는 그대로 유지할 수 있습니다. 오픈 소스 [MongoDB 클라이언트 드라이버](https://docs.mongodb.org/ecosystem/drivers)를 사용하여 Azure Cosmos DB의 API for MongoDB와 통신할 수 있습니다. Azure Cosmos DB의 API for MongoDB를 사용하면 MongoDB [유선 프로토콜](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)에 따라 기존 클라이언트 드라이버를 사용할 수 있습니다.

## <a name="mongodb-protocol-support"></a>MongoDB 프로토콜 지원

Azure Cosmos DB의 API for MongoDB는 기본적으로 MongoDB 서버 버전 4.0, 3.6 및 3.2와 호환됩니다. 자세한 내용은 [4.0](mongodb-feature-support-40.md), [3.6](mongodb-feature-support-36.md) 및 [3.2](mongodb-feature-support.md) 문서의 기능과 구문을 참조하세요. 

다음 확장 명령은 데이터베이스 요청을 통해 Azure Cosmos DB 특정 리소스를 만들고 수정하는 기능을 제공합니다.

* [데이터베이스 만들기](#create-database)
* [데이터베이스 업데이트](#update-database)
* [데이터베이스 가져오기](#get-database)
* [컬렉션 만들기](#create-collection)
* [컬렉션 업데이트](#update-collection)
* [컬렉션 가져오기](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> 데이터베이스 만들기

데이터베이스 만들기 확장 명령은 새 MongoDB 데이터베이스를 만듭니다. 데이터베이스 이름은 `use database` 명령으로 설정한 데이터베이스 컨텍스트에서 사용할 수 있습니다. 다음 표에서는 명령 안의 매개 변수에 대해 설명합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
| `customAction`   |  `string`  |   사용자 지정 명령의 이름으로, "CreateDatabase"여야 합니다.      |
| `offerThroughput` | `int`  | 데이터베이스에 설정한 프로비전된 처리량입니다. 이 매개 변수는 선택 사항입니다. |
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md)에 필요합니다. 이 개체에는 자동 크기 조정 용량 모드와 관련된 설정이 포함됩니다. 컬렉션이 동적으로 증가하는 요청 단위의 최대 크기를 설명하는 `maxThroughput` 값을 설정할 수 있습니다. |

### <a name="output"></a>출력

명령이 성공하면 다음 응답이 반환됩니다.

```javascript
{ "ok" : 1 }
```

출력의 매개 변수에 대한 사용자 지정 명령의 [기본 출력](#default-output)을 참조하세요.

### <a name="examples"></a>예제

#### <a name="create-a-database"></a>데이터베이스 만들기

모든 기본값을 사용하는 `"test"`라는 데이터베이스를 만들려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

이 명령은 데이터베이스 수준 처리량 없이 데이터베이스를 만듭니다. 즉, 이 데이터베이스 내의 컬렉션은 사용이 필요한 처리량의 크기를 지정해야 합니다.

#### <a name="create-a-database-with-throughput"></a>처리량을 사용하여 데이터베이스 만들기

이름이 `"test"`인 데이터베이스를 만들고 [데이터베이스 수준의](set-throughput.md#set-throughput-on-a-database) 프로비전된 처리량을 1000RU로 지정하려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

그러면 데이터베이스가 만들어지고 처리량이 그렇게 설정됩니다. 컬렉션을 [특정 처리량 수준](set-throughput.md#set-throughput-on-a-database-and-a-container)으로 만들지 않는 한 이 데이터베이스 내의 모든 컬렉션은 설정된 처리량을 공유합니다.

#### <a name="create-a-database-with-autoscale-throughput"></a>자동 크기 조정 처리량으로 데이터베이스 만들기

이름이 `"test"`인 데이터베이스를 만들고 [데이터베이스 수준](set-throughput.md#set-throughput-on-a-database)에서 자동 크기 조정 최대 처리량을 20,000RU/s로 지정하려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> 데이터베이스 업데이트

데이터베이스 업데이트 확장 명령은 지정된 데이터베이스에 연결된 속성을 업데이트합니다. 프로비전된 처리량과 자동 크기 조정 간에 데이터베이스를 변경하는 것은 Azure Portal에서만 지원됩니다. 다음 표에서는 명령 안의 매개 변수에 대해 설명합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
| `customAction`    |    `string`     |   사용자 지정 명령의 이름입니다. "UpdateDatabase"여야 합니다.      |
|  `offerThroughput`   |  `int`       |     데이터베이스에서 [데이터베이스 수준 처리량](set-throughput.md#set-throughput-on-a-database)을 사용하는 경우 데이터베이스에 대해 설정하려는 새로운 프로비전된 처리량입니다.  |
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md)에 필요합니다. 이 개체에는 자동 크기 조정 용량 모드와 관련된 설정이 포함됩니다. 데이터베이스가 동적으로 증가하는 요청 단위의 최대 크기를 설명하는 `maxThroughput` 값을 설정할 수 있습니다. |

이 명령은 세션의 컨텍스트에서 지정된 데이터베이스를 사용합니다. 이는 `use <database>` 명령에 사용된 데이터베이스입니다. 현재는 이 명령을 사용하여 데이터베이스 이름을 변경할 수 없습니다.

### <a name="output"></a>출력

명령이 성공하면 다음 응답이 반환됩니다.

```javascript
{ "ok" : 1 }
```

출력의 매개 변수에 대한 사용자 지정 명령의 [기본 출력](#default-output)을 참조하세요.

### <a name="examples"></a>예제

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>데이터베이스 연결을 통해 프로비전된 처리량 업데이트

이름이 `"test"`인 데이터베이스의 프로비전된 처리량을 1200 RU로 업데이트하려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>데이터베이스 연결을 통해 자동 크기 조정 처리량 업데이트

이름이 `"test"`인 데이터베이스의 프로비전된 처리량을 20,000RU로 업데이트하거나 [자동 크기 조정 처리량](provision-throughput-autoscale.md) 수준으로 변환하려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> 데이터베이스 가져오기

데이터베이스 가져오기 확장 명령은 데이터베이스 개체를 반환합니다. 데이터베이스 이름은 명령이 실행되는 데이터베이스 컨텍스트에서 사용됩니다.

```javascript
{
  customAction: "GetDatabase"
}
```

다음 표에서는 명령 안의 매개 변수에 대해 설명합니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   사용자 지정 명령의 이름입니다. "GetDatabase"여야 합니다.|
        
### <a name="output"></a>출력

명령이 성공하면 응답에 다음 필드가 있는 문서가 포함됩니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |   `int`     |   응답 상태입니다. 1 = 성공 0 = 실패      |
| `database`    |    `string`        |   데이터베이스의 이름입니다.      |
|   `provisionedThroughput`  |    `int`      |    데이터베이스가 [데이터베이스 수준 처리량](set-throughput.md#set-throughput-on-a-database)을 사용하는 경우 데이터베이스에 설정된 프로비전된 처리량입니다.     |
| `autoScaleSettings` | `Object` | 이 개체는 [자동 크기 조정 모드](provision-throughput-autoscale.md)를 사용하는 경우 데이터베이스에 연결된 용량 매개 변수를 포함합니다. `maxThroughput` 값은 데이터베이스가 동적으로 증가하는 요청 단위의 최대 크기를 설명합니다. |

명령이 실패하면 기본 사용자 지정 명령 응답이 반환됩니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본 출력](#default-output)을 참조하세요.

### <a name="examples"></a>예제

#### <a name="get-the-database"></a>데이터베이스 가져오기

이름이 `"test"`인 데이터베이스에 대한 데이터베이스 개체를 가져오려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

데이터베이스에 연결된 처리량이 없으면 출력은 다음과 같습니다.

```javascript
{ "database" : "test", "ok" : 1 }
```

데이터베이스에 연결된 [데이터베이스 수준 수동 처리량](set-throughput.md#set-throughput-on-a-database)이 있는 경우 출력에는 다음과 같이 `provisionedThroughput` 값이 표시됩니다.

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

데이터베이스에 연결된 [데이터베이스 수준 자동 크기 조정 처리량](provision-throughput-autoscale.md)이 있다면 출력에는 데이터베이스에 대한 최소 RU/s를 설명하는 `provisionedThroughput`과, 데이터베이스의 최대 RU/s를 설명하는 `maxThroughput`을 포함하는 `autoScaleSettings` 개체가 표시됩니다.

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

## <a name="create-collection"></a><a id="create-collection"></a> 컬렉션 만들기

컬렉션 만들기 확장 명령은 새 MongoDB 컬렉션을 만듭니다. 데이터베이스 이름은 `use database` 명령으로 설정된 데이터베이스 컨텍스트에서 사용됩니다. CreateCollection 명령의 형식은 다음과 같습니다.

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" to use Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

다음 표에서는 명령 안의 매개 변수에 대해 설명합니다.

| **필드** | **유형** | **필수** | **설명** |
|---------|---------|---------|---------|
| `customAction` | `string` | 필수 | 사용자 지정 명령의 이름입니다. "CreateCollection"이어야 합니다.|
| `collection` | `string` | 필수 | 컬렉션의 이름입니다. 특수 문자나 공백은 허용되지 않습니다.|
| `offerThroughput` | `int` | 선택 사항 | 데이터베이스에 설정할 프로비전된 처리량입니다. 이 매개 변수를 지정하지 않으면 기본값은 최솟값인 400RU/s입니다. * 10,000RU/s가 넘는 처리량을 지정하려면 `shardKey` 매개 변수가 필요합니다.|
| `shardKey` | `string` | 처리량이 많은 컬렉션에 필요 | 분할된 컬렉션에 대한 분할 키의 경로입니다. 이 매개 변수는 `offerThroughput`에서 10,000RU/s를 초과하여 설정하는 경우에 필요합니다.  지정할 경우 삽입된 모든 문서에 이 키와 값이 필요합니다. |
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md)에 필요합니다. | 이 개체에는 자동 크기 조정 용량 모드와 관련된 설정이 포함됩니다. 컬렉션이 동적으로 증가하는 요청 단위의 최대 크기를 설명하는 `maxThroughput` 값을 설정할 수 있습니다. |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본 출력](#default-output)을 참조하세요.

### <a name="examples"></a>예제

#### <a name="create-a-collection-with-the-minimum-configuration"></a>최소 구성으로 컬렉션 만들기

이름 `"testCollection"` 및 기본값을 사용하여 새 컬렉션을 만들려면 다음 명령을 사용합니다. 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

그러면 400RU/s의 분할되지 않은 새 고정 컬렉션이 생기고, `_id` 필드에 대한 인덱스가 자동 생성됩니다. 이 구성 형식은 `insert()` 함수를 통해 새 컬렉션을 만들 때도 적용됩니다. 예: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>분할되지 않은 컬렉션 만들기

이름이 `"testCollection"`이고 프로비전된 처리량이 1000RU인 분할되지 않은 컬렉션을 만들려면 다음 명령을 사용합니다. 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

분할 키를 지정하지 않고 최대 10,000RU/s인 컬렉션을 `offerThroughput`으로 만들 수 있습니다. 처리량이 더 큰 컬렉션은 다음 섹션을 확인하세요.

#### <a name="create-a-sharded-collection"></a>분할된 컬렉션 만들기

이름이 `"testCollection"`이고 프로비전된 처리량 11,000RU, `shardkey` 속성 "a.b"로 분할된 컬렉션을 만들려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

`offerThroughput`에 지정된 10,000RU/s보다 더 크기 때문에 이제 이 명령에 `shardKey` 매개 변수가 필요합니다.

#### <a name="create-an-unsharded-autoscale-collection"></a>분할되지 않은 자동 크기 조정 컬렉션 만들기

4,000RU/s로 설정된 [자동 크기 조정 처리량 용량](provision-throughput-autoscale.md)을 사용하는 `'testCollection'`이라는 이름의 분할되지 않은 컬렉션을 만들려면 다음 명령어를 사용합니다.

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

`autoScaleSettings.maxThroughput` 값의 경우 분할 키 없이 4,000~10,000RU/s 범위에서 지정할 수 있습니다. 자동 크기 조정 처리량을 높이려면 `shardKey` 매개 변수를 지정해야 합니다.

#### <a name="create-a-sharded-autoscale-collection"></a>분할된 자동 크기 조정 컬렉션 만들기

`'a.b'`라는 분할 키를 사용하고 20,000RU/s로 설정된 [자동 크기 조정 처리량 용량](provision-throughput-autoscale.md)을 사용하는 `'testCollection'`라는 분할된 컬렉션을 만들려면 다음 명령어를 사용합니다.

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> 컬렉션 업데이트

컬렉션 업데이트 확장 명령은 지정된 컬렉션에 연결된 속성을 업데이트합니다. 프로비전된 처리량과 자동 크기 조정 간에 컬렉션을 변경하는 것은 Azure Portal에서만 지원됩니다.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" if using Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting. Changing between Autoscale and Provisioned throughput is only supported in the Azure Portal.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

다음 표에서는 명령 안의 매개 변수에 대해 설명합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   사용자 지정 명령의 이름입니다. "UpdateCollection"이어야 합니다.      |
|  `collection`   |   `string`      |   컬렉션의 이름입니다.       |
| `offerThroughput` | `int` |   컬렉션에 설정할 프로비전된 처리량입니다.|
| `autoScaleSettings` | `Object` | [자동 크기 조정 모드](provision-throughput-autoscale.md)에 필요합니다. 이 개체에는 자동 크기 조정 용량 모드와 관련된 설정이 포함됩니다. `maxThroughput` 값은 컬렉션이 동적으로 증가하는 요청 단위의 최대 크기를 설명합니다. |

## <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본 출력](#default-output)을 참조하세요.

### <a name="examples"></a>예제

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>컬렉션 연결을 통해 프로비전된 처리량 업데이트

이름이 `"testCollection"`인 컬렉션의 프로비전된 처리량을 1200RU로 업데이트하려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> 컬렉션 가져오기

컬렉션 사용자 지정 가져오기 명령은 컬렉션 개체를 반환합니다.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

다음 표에서는 명령 안의 매개 변수에 대해 설명합니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
| `customAction`    |   `string`      |   사용자 지정 명령의 이름입니다. "GetCollection"이어야 합니다.      |
| `collection`    |    `string`     |    컬렉션의 이름입니다.     |

### <a name="output"></a>출력

명령이 성공하면 응답에 다음 필드가 있는 문서가 포함됩니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 = 성공 0 = 실패      |
| `database`    |    `string`     |   데이터베이스의 이름입니다.      |
| `collection`    |    `string`     |    컬렉션의 이름입니다.     |
|  `shardKeyDefinition`   |   `document`      |  분할 키로 사용되는 인덱스 사양 문서입니다. 선택적 응답 매개 변수입니다.       |
|  `provisionedThroughput`   |   `int`      |    컬렉션에 설정할 프로비전된 처리량입니다. 선택적 응답 매개 변수입니다.     |
| `autoScaleSettings` | `Object` | 이 개체는 [자동 크기 조정 모드](provision-throughput-autoscale.md)를 사용하는 경우 데이터베이스에 연결된 용량 매개 변수를 포함합니다. `maxThroughput` 값은 컬렉션이 동적으로 증가하는 요청 단위의 최대 크기를 설명합니다. |

명령이 실패하면 기본 사용자 지정 명령 응답이 반환됩니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본 출력](#default-output)을 참조하세요.

### <a name="examples"></a>예제

#### <a name="get-the-collection"></a>컬렉션 가져오기

이름이 `"testCollection"`인 컬렉션에 대해 컬렉션 개체를 가져오려면 다음 명령을 사용합니다.

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

컬렉션에 연결된 처리량 용량이 있는 경우 이 값이 `provisionedThroughput` 값을 포함하며 출력은 다음과 같습니다.

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

컬렉션에 연결된 자동 크기 조정 처리량이 있는 경우, 여기에 `maxThroughput` 매개 변수가 있는 `autoScaleSettings` 개체가 포함되어 컬렉션이 동적으로 증가할 수 있는 최대 처리량을 정의합니다. 또한 컬렉션에 요청이 없는 경우 이 컬렉션이 감소하는 최소 처리량을 정의하는 `provisionedThroughput` 값도 포함됩니다. 

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

컬렉션이 [데이터베이스 수준 처리량](set-throughput.md#set-throughput-on-a-database)을 공유하는 경우 자동 크기 조정 모드 또는 수동 모두에서 출력은 다음과 같습니다.

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


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> 사용자 지정 명령의 기본 출력

지정하지 않으면 사용자 지정 응답에 다음 필드가 있는 문서가 포함됩니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 = 성공 0 = 실패      |
| `code`    |   `int`      |   명령이 실패했을 때만 반환됩니다(즉, ok == 0). MongoDB 오류 코드를 포함합니다. 선택적 응답 매개 변수입니다.      |
|  `errMsg`   |  `string`      |    명령이 실패했을 때만 반환됩니다(즉, ok == 0). 사용자가 알기 쉬운 오류 메시지를 포함합니다. 선택적 응답 매개 변수입니다.      |

다음은 그 예입니다.

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>다음 단계

이제 다음 Azure Cosmos DB 개념을 알아볼 수 있습니다. 

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
