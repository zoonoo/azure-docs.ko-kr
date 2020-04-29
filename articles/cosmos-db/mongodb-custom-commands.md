---
title: MongoDB에 대 한 Azure Cosmos DB API의 데이터를 관리 하기 위한 MongoDB 확장 명령
description: 이 문서에서는 MongoDB 확장 명령을 사용 하 여 Azure Cosmos DB의 MongoDB API에 저장 된 데이터를 관리 하는 방법을 설명 합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583574"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 확장 명령을 사용 하 여 Azure Cosmos DB의 MongoDB API에 저장 된 데이터를 관리 합니다. 

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. 오픈 소스 [MongoDB 클라이언트 드라이버](https://docs.mongodb.org/ecosystem/drivers)를 사용 하 여 Azure Cosmos DB의 MongoDB API와 통신할 수 있습니다. MongoDB에 대 한 Azure Cosmos DB API를 사용 하면 [MongoDB 유선 프로토콜](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)을 준수 함으로써 기존 클라이언트 드라이버를 사용할 수 있습니다.

MongoDB에 대 한 Azure Cosmos DB API를 사용 하 여 MongoDB 앱에 대 한 투자를 유지 하면서 전역 배포, 자동 분할, 고가용성, 대기 시간 보장, 자동, 미사용 암호화, 백업 등의 Cosmos DB 이점을 누릴 수 있습니다.

## <a name="mongodb-protocol-support"></a>MongoDB 프로토콜 지원

기본적으로 MongoDB에 대 한 Azure Cosmos DB API는 MongoDB server 버전 3.2와 호환 됩니다. 자세한 내용은 [지원 되는 기능 및 구문](mongodb-feature-support.md)을 참조 하세요. MongoDB 버전 3.4에 추가 된 기능 또는 쿼리 연산자는 현재 MongoDB API의 미리 Azure Cosmos DB 보기로 제공 됩니다. 다음 확장 명령은 MongoDB 용 Azure Cosmos DB의 API에 저장 된 데이터에 대 한 CRUD 작업을 수행할 때 특정 Azure Cosmos DB 기능을 지원 합니다.

* [데이터베이스 만들기](#create-database)
* [데이터베이스 업데이트](#update-database)
* [데이터베이스 가져오기](#get-database)
* [컬렉션 만들기](#create-collection)
* [컬렉션 업데이트](#update-collection)
* [컬렉션 가져오기](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>데이터베이스 만들기

Create database extension 명령은 새 MongoDB 데이터베이스를 만듭니다. 데이터베이스 이름은 명령이 실행 되는 데이터베이스 컨텍스트에서 사용 됩니다. CreateDatabase 명령의 형식은 다음과 같습니다.

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
| customAction   |  string  |   사용자 지정 명령의 이름은 "CreateDatabase" 여야 합니다.      |
| offerThroughput | int  | 데이터베이스에 대해 설정한 프로 비전 된 처리량입니다. 이 매개 변수는 선택 사항입니다. |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환 합니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예

**데이터베이스 만들기**

"Test" 라는 데이터베이스를 만들려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**처리량이 있는 데이터베이스 만들기**

"Test" 라는 데이터베이스와 1000 RUs의 프로 비전 된 처리량을 만들려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>데이터베이스 업데이트

데이터베이스 확장 업데이트 명령은 지정 된 데이터베이스와 연결 된 속성을 업데이트 합니다. 현재 "offerThroughput" 속성만 업데이트할 수 있습니다.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
| customAction    |    string     |   사용자 지정 명령의 이름입니다. "UpdateDatabase" 여야 합니다.      |
|  offerThroughput   |  int       |     데이터베이스에 대해 설정 하려는 새로운 프로 비전 된 처리량입니다.    |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환 합니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예

**데이터베이스와 연결 된 프로 비전 된 처리량 업데이트**

이름이 "test" 인 데이터베이스의 프로 비전 된 처리량을 1200 RUs로 업데이트 하려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>데이터베이스 가져오기

데이터베이스 확장 가져오기 명령은 데이터베이스 개체를 반환 합니다. 데이터베이스 이름은 명령이 실행 되는 데이터베이스 컨텍스트에서 사용 됩니다.

```
{
  customAction: "GetDatabase"
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.


|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  customAction   |   string      |   사용자 지정 명령의 이름입니다. "GetDatabase" 여야 합니다.|
        
### <a name="output"></a>출력

명령이 성공 하면 응답에 다음 필드를 포함 하는 문서가 포함 됩니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  `ok`   |   `int`     |   응답 상태입니다. 1 = = 성공. 0 = = 실패      |
| `database`    |    `string`        |   데이터베이스의 이름입니다.      |
|   `provisionedThroughput`  |    `int`      |    데이터베이스에 설정 된 프로 비전 된 처리량입니다. 선택적 응답 매개 변수입니다.     |

명령이 실패 하면 기본 사용자 지정 명령 응답이 반환 됩니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예

**데이터베이스 가져오기**

"Test" 라는 데이터베이스에 대 한 데이터베이스 개체를 가져오려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>컬렉션 만들기

컬렉션 확장 만들기 명령은 새 MongoDB 컬렉션을 만듭니다. 데이터베이스 이름은 명령이 실행 되는 데이터베이스 컨텍스트에서 사용 됩니다. CreateCollection 명령의 형식은 다음과 같습니다.

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

| **필드** | **Type** | **필수** | **설명** |
|---------|---------|---------|---------|
| customAction | string | 필수 | 사용자 지정 명령의 이름입니다. "CreateCollection" 이어야 합니다.|
| collection | string | 필수 | 컬렉션의 이름입니다. 특수 문자는 허용 되지 않습니다.|
| offerThroughput | int | 필드 | 데이터베이스에 설정할 프로 비전 된 처리량입니다. 이 매개 변수를 지정 하지 않으면 기본값은 최소, 400 r u/초입니다. * 1만 r u/초를 초과 하는 처리량 `shardKey` 을 지정 하려면 매개 변수가 필요 합니다.|
| shardKey | string | 필드 | 분할 된 collection에 대 한 분할 키의 경로입니다. 이 매개 변수는에서 1만 이상/s를 설정 하는 경우 `offerThroughput`에 필요 합니다.  지정 된 경우에는 삽입 된 모든 문서에이 값이 필요 합니다. |

### <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환 합니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예

**Unsharded 컬렉션 만들기**

이름이 "testCollection"이 고 프로 비전 된 처리량이 1000 RUs 인 unsharded 컬렉션을 만들려면 다음 명령을 사용 합니다. 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**분할 된 컬렉션 만들기**

이름이 "testCollection"이 고 프로 비전 된 처리량이 1000 RUs 인 분할 된 컬렉션을 만들고 shardkey 속성 "a. b"를 사용 하려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>컬렉션 업데이트

컬렉션 확장 업데이트 명령은 지정 된 컬렉션과 연결 된 속성을 업데이트 합니다.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  customAction   |   string      |   사용자 지정 명령의 이름입니다. "UpdateCollection" 이어야 합니다.      |
|  collection   |   string      |   컬렉션의 이름입니다.       |
| offerThroughput   |int|   컬렉션에 설정할 프로 비전 된 처리량입니다.|

## <a name="output"></a>출력

기본 사용자 지정 명령 응답을 반환 합니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예

**컬렉션과 연결 된 프로 비전 된 처리량 업데이트**

이름이 "testCollection" 인 컬렉션의 프로 비전 된 처리량을 1200 RUs로 업데이트 하려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>컬렉션 가져오기

컬렉션 가져오기 사용자 지정 명령은 컬렉션 개체를 반환 합니다.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

다음 표에서는 명령 내의 매개 변수에 대해 설명 합니다.


|**필드**|**Type** |**설명** |
|---------|---------|---------|
| customAction    |   string      |   사용자 지정 명령의 이름입니다. "GetCollection" 이어야 합니다.      |
| collection    |    string     |    컬렉션의 이름입니다.     |

### <a name="output"></a>출력

명령이 성공 하면 응답에 다음 필드를 포함 하는 문서가 포함 됩니다.


|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 = = 성공. 0 = = 실패      |
| `database`    |    `string`     |   데이터베이스의 이름입니다.      |
| `collection`    |    `string`     |    컬렉션의 이름입니다.     |
|  `shardKeyDefinition`   |   `document`      |  분할 키로 사용 되는 인덱스 사양 문서입니다. 선택적 응답 매개 변수입니다.       |
|  `provisionedThroughput`   |   `int`      |    컬렉션에 설정할 프로 비전 된 처리량입니다. 선택적 응답 매개 변수입니다.     |

명령이 실패 하면 기본 사용자 지정 명령 응답이 반환 됩니다. 출력의 매개 변수에 대 한 사용자 지정 명령의 [기본 출력](#default-output) 을 참조 하세요.

### <a name="examples"></a>예

**컬렉션 가져오기**

"TestCollection" 이라는 컬렉션에 대 한 컬렉션 개체를 가져오려면 다음 명령을 사용 합니다.

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>사용자 지정 명령의 기본 출력

지정 하지 않으면 사용자 지정 응답에 다음 필드를 포함 하는 문서가 포함 됩니다.

|**필드**|**Type** |**설명** |
|---------|---------|---------|
|  `ok`   |    `int`     |   응답 상태입니다. 1 = = 성공. 0 = = 실패      |
| `code`    |   `int`      |   명령이 실패 한 경우에만 반환 됩니다 (예: ok = = 0). MongoDB 오류 코드를 포함 합니다. 선택적 응답 매개 변수입니다.      |
|  `errMsg`   |  `string`      |    명령이 실패 한 경우에만 반환 됩니다 (예: ok = = 0). 사용자에 게 친숙 한 오류 메시지를 포함 합니다. 선택적 응답 매개 변수입니다.      |

## <a name="next-steps"></a>다음 단계

다음으로 진행 하 여 다음 Azure Cosmos DB 개념을 알아볼 수 있습니다. 

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
