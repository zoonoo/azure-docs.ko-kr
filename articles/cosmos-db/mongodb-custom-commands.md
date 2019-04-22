---
title: MongoDB에 대 한 Azure Cosmos DB API에 저장 된 데이터를 관리 하는 MongoDB 확장 명령
description: 이 문서에서는 MongoDB 확장 명령을 사용 하 여 MongoDB에 대 한 Azure Cosmos DB API에 저장 된 데이터를 관리 하는 방법을 설명 합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: aef77f121f20d867c8ec5e764d8c9639c961713d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876891"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 확장 명령을 사용 하 여 MongoDB에 대 한 Azure Cosmos DB API에 저장 된 데이터를 관리 합니다. 

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. 오픈 소스 중 하나를 사용 하 여 MongoDB에 대 한 Azure Cosmos DB API와 통신할 수 [MongoDB 클라이언트 드라이버가](https://docs.mongodb.org/ecosystem/drivers)합니다. Azure Cosmos DB의 MongoDB API을 준수 함으로써 기존 클라이언트 드라이버를 사용 하도록 설정 합니다 [MongoDB 유선 프로토콜](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)합니다.

Azure Cosmos DB API에 MongoDB를 사용 하 여 Cosmos DB 전역 배포, 자동 분할, 가용성이 높고, 대기 시간 보장, 자동, 백업, 미사용 데이터 암호화와 같은 혜택을 누릴 수 있습니다 및 많은 자세한 투자를 유지 합니다. MongoDB 앱.

## <a name="mongodb-protocol-support"></a>MongoDB 프로토콜 지원

기본적으로 Azure Cosmos DB API for MongoDB에 대 한 자세한 내용은 server 버전 3.2, MongoDB와 호환 볼 [지원 되는 기능 및 구문](mongodb-feature-support.md)합니다. 기능 또는 MongoDB 버전 3.4에에서 추가 하는 쿼리 연산자를 Azure Cosmos DB의 MongoDB API에서 미리 보기로 현재 사용할 수 있습니다. 다음 확장 명령을 MongoDB에 대 한 Azure Cosmos DB API에서 저장 된 데이터에 대 한 CRUD 작업을 수행 하는 경우 Azure Cosmos DB의 특정 기능을 지원 합니다.

* [데이터베이스 만들기](#create-database)
* [데이터베이스 업데이트](#update-database)
* [데이터베이스 가져오기](#get-database)
* [컬렉션 만들기](#create-collection)
* [컬렉션 업데이트](#update-collection)
* [컬렉션을 가져옵니다.](#get-collection)

## <a id="create-database"></a> 데이터베이스 만들기

Create database 확장 명령을 새 MongoDB 데이터베이스를 만듭니다. 데이터베이스 이름은 명령이 실행 되는 데이터베이스 컨텍스트에서 사용 됩니다. CreateDatabase 명령의 형식은 다음과 같습니다.

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

다음 표에서 명령에 매개 변수를 설명 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
| customAction   |  문자열  |   이름, 사용자 지정 명령 "CreateDatabase" 여야 합니다.      |
| offerThroughput | ssNoversion  | 데이터베이스에 설정 하는 프로 비전 된 처리량입니다. 이 매개 변수는 선택 사항입니다. |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 참조를 [기본 출력](#default-output) 출력 매개 변수 사용자 지정 명령입니다.

### <a name="examples"></a>예

**데이터베이스 만들기**

"Test" 라는 데이터베이스를 만들려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**처리량을 사용 하 여 데이터베이스 만들기**

1000 Ru의 프로 비전 된 처리량 및 "test" 라는 데이터베이스를 만들려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> 데이터베이스 업데이트

업데이트 데이터베이스 확장 명령을 지정 된 데이터베이스에 연결 된 속성을 업데이트 합니다. 현재 "offerThroughput" 속성을 업데이트할 수 있습니다.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

다음 표에서 명령에 매개 변수를 설명 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
| customAction    |    문자열     |   사용자 지정 명령 이름입니다. "UpdateDatabase" 여야 합니다.      |
|  offerThroughput   |  ssNoversion       |     데이터베이스에 설정 하려는 새 프로 비전 된 처리량입니다.    |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 참조를 [기본 출력](#default-output) 출력 매개 변수 사용자 지정 명령입니다.

### <a name="examples"></a>예

**Update database에 연결 된 프로 비전된 된 처리량**

데이터베이스의 프로 비전된 된 처리량을 1200 Ru에 이름이 "test"로 업데이트 하려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> 데이터베이스 가져오기

Get database 확장 명령을 데이터베이스 개체를 반환 합니다. 데이터베이스 이름은 명령이 실행 되는 데이터베이스 컨텍스트에서 사용 됩니다.

```
{
  customAction: "GetDatabase"
}
```

다음 표에서 명령에 매개 변수를 설명 합니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  customAction   |   문자열      |   사용자 지정 명령 이름입니다. "GetDatabase" 여야 합니다.|
        
### <a name="output"></a>출력

명령이 성공 하면 다음 필드를 사용 하 여 문서를 응답에 포함 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |   `int`     |   응답의 상태입니다. 1 성공 = =. 0 == failure.      |
| `database`    |    `string`        |   데이터베이스 이름      |
|   `provisionedThroughput`  |    `int`      |    데이터베이스에 설정 된 프로 비전 된 처리량입니다. 이 선택적 응답 매개 변수입니다.     |

명령이 실패 하는 경우 기본 사용자 지정 명령 응답이 반환 됩니다. 참조를 [기본 출력](#default-output) 출력 매개 변수 사용자 지정 명령입니다.

### <a name="examples"></a>예

**데이터베이스를 가져오려면**

"Test" 라는 데이터베이스에 대 한 데이터베이스 개체를 가져오려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> 컬렉션 만들기

Create 컬렉션 확장 명령에는 새 MongoDB 컬렉션을 만듭니다. 데이터베이스 이름은 명령이 실행 되는 데이터베이스 컨텍스트에서 사용 됩니다. CreateCollection 명령의 형식은 다음과 같습니다.

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

다음 표에서 명령에 매개 변수를 설명 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
| customAction    | 문자열 | 사용자 지정 명령 이름입니다. "CreateDatabase" 여야 합니다.     |
| collection      | 문자열 | 컬렉션의 이름                                   |
| offerThroughput | ssNoversion    | 데이터베이스에 설정 하려면 프로 비전 된 처리량입니다. 이 선택적 매개 변수 |
| shardKey        | 문자열 | 분할 된 컬렉션을 만드는 분할 된 데이터베이스 키 경로입니다. 이 선택적 매개 변수 |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 참조를 [기본 출력](#default-output) 출력 매개 변수 사용자 지정 명령입니다.

### <a name="examples"></a>예

**Unsharded 컬렉션 만들기**

1000 Ru의 프로 비전 된 처리량 및 testCollection"이름"을 사용 하 여 unsharded 컬렉션을 만들려면 다음 명령을 사용 합니다. 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**분할 된 컬렉션 만들기**

1000 Ru의 프로 비전 된 처리량 및 testCollection"이름"을 사용 하 여 분할 된 컬렉션을 만들려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> 컬렉션 업데이트

업데이트 컬렉션 확장 명령을 지정 된 컬렉션과 관련 된 속성을 업데이트 합니다.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

다음 표에서 명령에 매개 변수를 설명 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  customAction   |   문자열      |   사용자 지정 명령 이름입니다. "UpdateCollection" 여야 합니다.      |
|  collection   |   문자열      |   컬렉션의 이름입니다.       |
| offerThroughput   |ssNoversion|   프로 비전 된 처리량 컬렉션에 설정할 수 있습니다.|

## <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환합니다. 참조를 [기본 출력](#default-output) 출력 매개 변수 사용자 지정 명령입니다.

### <a name="examples"></a>예

**컬렉션과 연결 된 프로 비전된 된 처리량을 업데이트 합니다.**

1200 Ru를 testCollection"이름"을 사용 하 여 컬렉션의 프로 비전된 된 처리량으로 업데이트 하려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> 컬렉션을 가져옵니다.

Get 컬렉션 사용자 지정 명령 컬렉션 개체를 반환합니다.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

다음 표에서 명령에 매개 변수를 설명 합니다.


|**필드**|**형식** |**설명** |
|---------|---------|---------|
| customAction    |   문자열      |   사용자 지정 명령 이름입니다. "GetCollection" 여야 합니다.      |
| collection    |    문자열     |    컬렉션의 이름입니다.     |

### <a name="output"></a>출력

응답에 다음 필드를 사용 하 여 문서를 포함 명령이 성공


|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답의 상태입니다. 1 성공 = =. 0 == failure.      |
| `database`    |    `string`     |   데이터베이스 이름      |
| `collection`    |    `string`     |    컬렉션의 이름입니다.     |
|  `shardKeyDefinition`   |   `document`      |  인덱스 사양 문서를 분할 키로 사용 합니다. 이 선택적 응답 매개 변수입니다.       |
|  `provisionedThroughput`   |   `int`      |    프로 비전 된 처리량 컬렉션에 설정할 수 있습니다. 이 선택적 응답 매개 변수입니다.     |

명령이 실패 하는 경우 기본 사용자 지정 명령 응답이 반환 됩니다. 참조를 [기본 출력](#default-output) 출력 매개 변수 사용자 지정 명령입니다.

### <a name="examples"></a>예

**컬렉션을 가져옵니다.**

"TestCollection" 라는 컬렉션에 대 한 컬렉션 개체를 가져오려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> 사용자 지정 명령의 기본 출력

지정 하지 않으면 사용자 지정 응답을 다음 필드를 사용 하 여 문서를 포함 합니다.

|**필드**|**형식** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답의 상태입니다. 1 성공 = =. 0 == failure.      |
| `code`    |   `int`      |   명령이 실패 하는 경우에 반환 (즉, 정상: = = 0). MongoDB 오류 코드를 포함합니다. 이 선택적 응답 매개 변수입니다.      |
|  `errMsg`   |  `string`      |    명령이 실패 하는 경우에 반환 (즉, 정상: = = 0). 친숙 한 오류 메시지를 포함 합니다. 이 선택적 응답 매개 변수입니다.      |

## <a name="next-steps"></a>다음 단계

그런 다음 같은 Azure Cosmos DB 개념을 진행할 수 있습니다. 

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)