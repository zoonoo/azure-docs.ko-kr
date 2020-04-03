---
title: MongoDB 확장 명령 은 몽고 DB에 대한 Azure 코스모스 DB의 API에서 데이터를 관리할 수 있습니다.
description: 이 문서에서는 MongoDB 에 대한 Azure Cosmos DB의 API에 저장된 데이터를 관리하기 위해 MongoDB 확장 명령을 사용하는 방법을 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583574"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 확장 명령을 사용하여 MongoDB용 Azure Cosmos DB의 API에 저장된 데이터를 관리합니다. 

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. 오픈 소스 [MongoDB 클라이언트 드라이버를](https://docs.mongodb.org/ecosystem/drivers)사용하여 MongoDB에 대한 Azure Cosmos DB의 API와 통신할 수 있습니다. MongoDB용 Azure Cosmos DB의 API를 사용하면 [MongoDB 와이어 프로토콜을](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)준수하여 기존 클라이언트 드라이버를 사용할 수 있습니다.

MongoDB에 대한 Azure Cosmos DB의 API를 사용하면 MongoDB 앱에 대한 투자를 유지하면서 글로벌 배포, 자동 샤딩, 고가용성, 대기 시간 보장, 자동, 미사용 암호화, 백업 등과 같은 Cosmos DB의 이점을 누릴 수 있습니다.

## <a name="mongodb-protocol-support"></a>MongoDB 프로토콜 지원

기본적으로 MongoDB용 Azure Cosmos DB의 API는 MongoDB 서버 버전 3.2와 호환되며 자세한 내용은 [지원되는 기능 및 구문을](mongodb-feature-support.md)참조하십시오. MongoDB 버전 3.4에 추가된 기능 또는 쿼리 연산자는 현재 MongoDB용 Azure Cosmos DB의 API에서 미리 보기로 사용할 수 있습니다. 다음 확장 명령은 MongoDB에 대한 Azure Cosmos DB의 API에 저장된 데이터에 대해 CRUD 작업을 수행할 때 특정 Azure Cosmos DB 기능을 지원합니다.

* [데이터베이스 만들기](#create-database)
* [데이터베이스 업데이트](#update-database)
* [데이터베이스 받기](#get-database)
* [컬렉션 만들기](#create-collection)
* [컬렉션 업데이트](#update-collection)
* [컬렉션 받기](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>데이터베이스 만들기

데이터베이스 확장 만들기 명령은 새 MongoDB 데이터베이스를 만듭니다. 데이터베이스 이름은 명령이 실행되는 데이터베이스 컨텍스트에서 사용됩니다. CreateDatabase 명령의 형식은 다음과 같습니다.

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

다음 표는 명령 내의 매개 변수에 대해 설명합니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
| customAction   |  문자열  |   사용자 지정 명령의 이름은 "CreateDatabase"여야 합니다.      |
| 제공Throughput | int  | 데이터베이스에 설정한 프로비전된 처리량입니다. 이 매개 변수는 선택 사항입니다. |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본](#default-output) 출력을 참조하십시오.

### <a name="examples"></a>예

**데이터베이스 만들기**

"test"라는 데이터베이스를 만들려면 다음 명령을 사용합니다.

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**처리량이 있는 데이터베이스 만들기**

"test"라는 데이터베이스와 1000R의 프로비전된 처리량을 만들려면 다음 명령을 사용하십시오.

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>데이터베이스 업데이트

업데이트 데이터베이스 확장 명령은 지정된 데이터베이스와 연결된 속성을 업데이트합니다. 현재는 "offerThroughput" 속성만 업데이트할 수 있습니다.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

다음 표는 명령 내의 매개 변수에 대해 설명합니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
| customAction    |    문자열     |   사용자 지정 명령의 이름입니다. "업데이트데이터베이스"여야 합니다.      |
|  제공Throughput   |  int       |     데이터베이스에 설정하려는 새 프로비전된 처리량입니다.    |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본](#default-output) 출력을 참조하십시오.

### <a name="examples"></a>예

**데이터베이스와 연결된 프로비저닝된 처리량 업데이트**

데이터베이스의 프로비저닝된 처리량을 이름 "test"를 1200 R로 업데이트하려면 다음 명령을 사용합니다.

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>데이터베이스 받기

get 데이터베이스 확장 명령은 데이터베이스 개체를 반환합니다. 데이터베이스 이름은 명령이 실행되는 데이터베이스 컨텍스트에서 사용됩니다.

```
{
  customAction: "GetDatabase"
}
```

다음 표는 명령 내의 매개 변수에 대해 설명합니다.


|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  customAction   |   문자열      |   사용자 지정 명령의 이름입니다. "GetDatabase"여야 합니다.|
        
### <a name="output"></a>출력

명령이 성공하면 응답에는 다음 필드가 있는 문서가 포함됩니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  `ok`   |   `int`     |   응답 상태입니다. 1 == 성공. 0 == 실패.      |
| `database`    |    `string`        |   데이터베이스의 이름입니다.      |
|   `provisionedThroughput`  |    `int`      |    데이터베이스에 설정된 프로비전된 처리량입니다. 선택적 응답 매개 변수입니다.     |

명령이 실패하면 기본 사용자 지정 명령 응답이 반환됩니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본](#default-output) 출력을 참조하십시오.

### <a name="examples"></a>예

**데이터베이스 받기**

"test"라는 데이터베이스의 데이터베이스 개체를 얻으려면 다음 명령을 사용합니다.

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>컬렉션 만들기

컬렉션 확장 만들기 명령을 작성 하면 새 MongoDB 컬렉션을 만듭니다. 데이터베이스 이름은 명령이 실행되는 데이터베이스 컨텍스트에서 사용됩니다. CreateCollection 명령의 형식은 다음과 같습니다.

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

다음 표는 명령 내의 매개 변수에 대해 설명합니다.

| **필드** | **Type** | **필수** | **설명** |
|---------|---------|---------|---------|
| customAction | 문자열 | 필수 | 사용자 지정 명령의 이름입니다. "CreateCollection"이어야 합니다.|
| collection | 문자열 | 필수 | 컬렉션의 이름입니다. 특수 문자는 허용되지 않습니다.|
| 제공Throughput | int | 옵션* | 데이터베이스에 설정할 프로비전된 처리량입니다. 이 매개 변수가 제공되지 않으면 기본값인 최소 400RU/s로 설정됩니다. * 10,000 RU/s를 초과하는 처리량을 지정하려면 매개 변수가 `shardKey` 필요합니다.|
| 샤드키 | 문자열 | 옵션* | 샤드 컬렉션의 샤드 키로 가는 경로입니다. 에서 10,000개 이상의 RU/s를 설정하는 `offerThroughput`경우 이 매개 변수가 필요합니다.  이 값을 지정하면 삽입된 모든 문서에 이 값이 필요합니다. |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본](#default-output) 출력을 참조하십시오.

### <a name="examples"></a>예

**방해받지 않은 컬렉션 만들기**

"testCollection" 이름과 프로비전된 처리량 1000루션으로 하드되지 않은 컬렉션을 만들려면 다음 명령을 사용하십시오. 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**샤드 컬렉션 만들기**

"testCollection" 이름과 프로비전된 처리량 1000루션, 샤드키 속성 "a.b"로 샤드 컬렉션을 만들려면 다음 명령을 사용합니다.

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>컬렉션 업데이트

업데이트 컬렉션 확장 명령은 지정된 컬렉션과 연결된 속성을 업데이트합니다.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

다음 표는 명령 내의 매개 변수에 대해 설명합니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  customAction   |   문자열      |   사용자 지정 명령의 이름입니다. "업데이트 컬렉션"이어야 합니다.      |
|  collection   |   문자열      |   컬렉션의 이름입니다.       |
| 제공Throughput   |int|   컬렉션에 설정할 프로비전된 처리량입니다.|

## <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본](#default-output) 출력을 참조하십시오.

### <a name="examples"></a>예

**컬렉션과 연결된 프로비저닝된 처리량 업데이트**

컬렉션의 프로비저닝된 처리량을 "testCollection" 이름으로 1200R로 업데이트하려면 다음 명령을 사용합니다.

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>컬렉션 받기

get 컬렉션 사용자 지정 명령은 컬렉션 개체를 반환합니다.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

다음 표는 명령 내의 매개 변수에 대해 설명합니다.


|**필드**|**Type** |**설명** |
|---------|---------|---------|
| customAction    |   문자열      |   사용자 지정 명령의 이름입니다. "GetCollection"이어야 합니다.      |
| collection    |    문자열     |    컬렉션의 이름입니다.     |

### <a name="output"></a>출력

명령이 성공하면 응답에 다음 필드가 있는 문서가 포함되어 있습니다.


|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 == 성공. 0 == 실패.      |
| `database`    |    `string`     |   데이터베이스의 이름입니다.      |
| `collection`    |    `string`     |    컬렉션의 이름입니다.     |
|  `shardKeyDefinition`   |   `document`      |  샤드 키로 사용되는 인덱스 사양 문서입니다. 선택적 응답 매개 변수입니다.       |
|  `provisionedThroughput`   |   `int`      |    컬렉션에 설정할 프로비전된 처리량입니다. 선택적 응답 매개 변수입니다.     |

명령이 실패하면 기본 사용자 지정 명령 응답이 반환됩니다. 출력의 매개 변수에 대한 사용자 지정 명령의 [기본](#default-output) 출력을 참조하십시오.

### <a name="examples"></a>예

**컬렉션 받기**

"testCollection"이라는 컬렉션에 대한 컬렉션 개체를 얻으려면 다음 명령을 사용합니다.

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>사용자 지정 명령의 기본 출력

지정하지 않으면 사용자 지정 응답에는 다음 필드가 있는 문서가 포함됩니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 == 성공. 0 == 실패.      |
| `code`    |   `int`      |   명령이 실패했을 때만 반환됩니다(예: 확인 == 0). MongoDB 오류 코드가 포함되어 있습니다. 선택적 응답 매개 변수입니다.      |
|  `errMsg`   |  `string`      |    명령이 실패했을 때만 반환됩니다(예: 확인 == 0). 사용자 친화적인 오류 메시지가 포함되어 있습니다. 선택적 응답 매개 변수입니다.      |

## <a name="next-steps"></a>다음 단계

다음으로 다음 Azure 코스모스 DB 개념을 배울 수 있습니다. 

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
