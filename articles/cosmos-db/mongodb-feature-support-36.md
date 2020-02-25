---
title: Azure Cosmos DB의 API for MongoDB(3.6 버전) 지원 기능 및 구문
description: Azure Cosmos DB의 API for MongoDB(3.6 버전) 지원 기능 및 구문에 대해 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: cde731f9d9e673446bc4d08117004b028db2a7f9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462465"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB의 API for MongoDB(3.6 버전): 지원되는 기능 및 구문

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. 오픈 소스 MongoDB 클라이언트 [드라이버](https://docs.mongodb.org/ecosystem/drivers)를 사용하여 Azure Cosmos DB의 API for MongoDB와 통신할 수 있습니다. Azure Cosmos DB의 API for MongoDB를 사용하면 MongoDB [유선 프로토콜](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)에 따라 기존 클라이언트 드라이버를 사용할 수 있습니다.

Azure Cosmos DB의 API for MongoDB를 사용하면 Cosmos DB가 제공하는 모든 엔터프라이즈 기능과 함께 익숙한 MongoDB의 이점을 누릴 수 있습니다. 이러한 기능에는 [글로벌 배포](distribute-data-globally.md), [자동 분할](partition-data.md), 가용성 및 대기 시간 보장, 미사용 암호화, 백업 등이 포함됩니다.

## <a name="protocol-support"></a>프로토콜 지원

Azure Cosmos DB의 API for MongoDB는 새 계정의 경우 기본적으로 MongoDB 서버 버전 **3.6**과 호환됩니다. 지원되는 연산자 및 제한 사항이나 예외는 다음과 같습니다. 이러한 프로토콜을 인식하는 모든 클라이언트 드라이버는 Azure Cosmos DB의 API for MongoDB에 연결할 수 있습니다. Azure Cosmos DB의 API for MongoDB 계정을 사용하는 경우 3.6 버전의 계정에는 `*.mongo.cosmos.azure.com` 형식의 엔드포인트가 있지만 3.2 버전의 계정에는 `*.documents.azure.com` 형식의 엔드포인트가 있습니다.

## <a name="query-language-support"></a>쿼리 언어 지원

Azure Cosmos DB의 API for MongoDB는 MongoDB 쿼리 언어 구문을 포괄적으로 지원합니다. 아래에서 현재 지원되는 연산, 연산자, 단계, 명령 및 옵션에 대한 자세한 목록을 찾을 수 있습니다.

## <a name="database-commands"></a>데이터베이스 명령

Azure Cosmos DB의 API for MongoDB는 다음과 같은 데이터베이스 명령을 지원합니다.

### <a name="query-and-write-operation-commands"></a>쿼리 및 쓰기 작업 명령

|명령  |지원됨 |
|---------|---------|
|delete | yes |
|찾기 | yes     |
|findAndModify | yes  |
|getLastError|   yes |
|getMore  |  yes  |
|getPrevError | 예  |
|insert  |   yes  |
|parallelCollectionScan  | yes   |
|resetError |   예  |
|update  |   yes  |
|[스트림 변경](mongodb-change-streams.md)  |  yes  |
|GridFS |   yes  |

### <a name="authentication-commands"></a>인증 명령

|명령  |지원됨 |
|---------|---------|
|authenticate    |   yes      |
|logout    |      yes   |
|getnonce   |    yes     |


### <a name="administration-commands"></a>관리 명령

|명령  |지원됨 |
|---------|---------|
|닫힌 컬렉션   |   예      |
|cloneCollectionAsCapped     |   예      |
|collMod     |   예      |
|collMod: expireAfterSeconds   |   예      |
|convertToCapped   |  예       |
|copydb     |  예       |
|create   |    yes     |
|createIndexes     |  yes       |
|currentOp     |  yes       |
|drop     |   yes      |
|dropDatabase     |  yes       |
|dropIndexes     |   yes      |
|filemd5    |   yes      |
|killCursors    |  yes       |
|killOp     |   예      |
|listCollections     |  yes       |
|listDatabases     |  yes       |
|listIndexes     |  yes       |
|reIndex     |    yes     |
|renameCollection     |    예     |
|connectionStatus    |     예    |

### <a name="diagnostics-commands"></a>진단 명령

|명령  |지원됨 |
|---------|---------|
|buildInfo       |   yes      |
|collStats    |  yes       |
|connPoolStats     |  예       |
|connectionStatus     |  예       |
|dataSize     |   예      |
|dbHash    |    예     |
|dbStats     |   yes      |
|설명     | 예        |
|설명: executionStats     |     예    |
|기능     |    예     |
|hostInfo     |   예      |
|listDatabases       |   yes      |
|listCommands     |  예       |
|프로파일러     |  예       |
|serverStatus     |  예       |
|top     |    예     |
|whatsmyuri     |   yes      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>집계 파이프라인</a>

### <a name="aggregation-commands"></a>집계 명령

|명령  |지원됨 |
|---------|---------|
|aggregate |   yes  |
|count     |   yes  |
|distinct  | yes |
|mapReduce | 예 |

### <a name="aggregation-stages"></a>집계 단계

|명령  |지원됨 |
|---------|---------|
|$collStats |예|
|$project   |yes|
|$match |yes|
|$redact|   yes|
|$limit |yes|
|$skip  |yes|
|$unwind|   yes|
|$group |   yes|
|$sample|       yes|
|$sort  |yes|
|$geoNear|  예|
|$lookup    |   yes|
|$out       |yes|
|$indexStats|       예|
|$facet |예|
|$bucket|   예|
|$bucketAuto|   예|
|$sortByCount|  yes|
|$addFields |yes|
|$replaceRoot|  yes|
|$count |yes|
|$currentOp|    예|
|$listLocalSessions |예|
|$listSessions  |예|
|$graphLookup   |예|

### <a name="boolean-expressions"></a>부울 식

|명령  |지원됨 |
|---------|---------|
|$and| yes|
|$or|yes|
|$not|yes|

### <a name="set-expressions"></a>식 설정

|명령  |지원됨 |
|---------|---------|
| $setEquals | yes|
|$setIntersection|yes|
| $setUnion|yes|
| $setDifference|yes|
| $setIsSubset|yes|
| $anyElementTrue|yes|
| $allElementsTrue|yes|

### <a name="comparison-expressions"></a>비교 식

|명령  |지원됨 |
|---------|---------|
|$cmp     |  yes       |
|$eq|   yes| 
|$gt |  yes| 
|$gte|  yes| 
|$lt    |yes|
|$lte|  yes| 
|$ne    |   yes| 
|$in    |   yes| 
|$nin   |   yes| 

### <a name="arithmetic-expressions"></a>산술 식

|명령  |지원됨 |
|---------|---------|
|$abs |  yes       |
| $add |  yes       |
| $ceil |  yes       |
| $divide |  yes       |
| $exp |  yes       |
| $floor |  yes       |
| $ln |  yes       |
| $log |  yes       |
| $log10 |  yes       |
| $mod |  yes       |
| $multiply |  yes       |
| $pow |  yes       |
| $sqrt |  yes       |
| $subtract |  yes       |
| $trunc |  yes       |

### <a name="string-expressions"></a>문자열 식

|명령  |지원됨 |
|---------|---------|
|$concat |  yes       |
| $indexOfBytes|  yes       |
| $indexOfCP|  yes       |
| $split|  yes       |
| $strLenBytes|  yes       |
| $strLenCP|  yes       |
| $strcasecmp|  yes       |
| $substr|  yes       |
| $substrBytes|  yes       |
| $substrCP|  yes       |
| $toLower|  yes       |
| $toUpper|  yes       |

### <a name="text-search-operator"></a>텍스트 검색 연산자

|명령  |지원됨 |
|---------|---------|
| $meta | 예|

### <a name="array-expressions"></a>배열 식

|명령  |지원됨 |
|---------|---------|
|$arrayElemAt   |   yes|
|$arrayToObject|    yes|
|$concatArrays  |   yes|
|$filter    |   yes|
|$indexOfArray  |yes|
|$isArray   |   yes|
|$objectToArray |yes|
|$range |yes|
|$reverseArray  |   yes|
|$reduce|   yes|
|$size  |   yes|
|$slice |   yes|
|$zip   |   yes|
|$in    |   yes|

### <a name="variable-operators"></a>변수 연산자

|명령  |지원됨 |
|---------|---------|
|$map   |예|
|$let   |yes|

### <a name="system-variables"></a>시스템 변수

|명령  |지원됨 |
|---------|---------|
|$$CURRENT| yes|
|$$DESCEND|     yes|
|$$KEEP     |yes|
|$$PRUNE    |   yes|
|$$REMOVE   |yes|
|$$ROOT     |yes|

### <a name="literal-operator"></a>리터럴 연산자

|명령  |지원됨 |
|---------|---------|
|$literal   |yes|

### <a name="date-expressions"></a>날짜 식

|명령  |지원됨 |
|---------|---------|
|$dayOfYear |yes    |
|$dayOfMonth|   yes |
|$dayOfWeek |yes    |
|$year  |yes    |
|$month |yes|   
|$week  |yes    |
|$hour  |yes    |
|$minute|   yes|    
|$second    |yes    |
|$millisecond|  yes|    
|$dateToString  |yes    |
|$isoDayOfWeek  |yes    |
|$isoWeek   |yes    |
|$dateFromParts|    예| 
|$dateToParts   |예 |
|$dateFromString|   예|
|$isoWeekYear   |yes    |

### <a name="conditional-expressions"></a>조건부 식

|명령  |지원됨 |
|---------|---------|
| $cond| yes|
| $ifNull| yes|
| $switch |yes|

### <a name="data-type-operator"></a>데이터 형식 연산자

|명령  |지원됨 |
|---------|---------|
| $type| yes|

### <a name="accumulator-expressions"></a>누적기 식

|명령  |지원됨 |
|---------|---------|
|$sum   |yes    |
|$avg   |yes    |
|$first|    yes|
|$last  |yes    |
|$max   |yes    |
|$min   |yes    |
|$push| yes|
|$addToSet| yes|
|$stdDevPop|    예  |
|$stdDevSamp|   예|

### <a name="merge-operator"></a>Merge 연산자

|명령  |지원됨 |
|---------|---------|
| $mergeObjects | yes|

## <a name="data-types"></a>데이터 형식

|명령  |지원됨 |
|---------|---------|
|Double |yes    |
|String |yes    |
|Object |yes    |
|Array  |yes    |
|이진 데이터    |yes|   
|ObjectId   |yes    |
|부울    |yes    |
|Date   |yes    |
|Null   |yes    |
|32비트 정수(int)   |yes    |
|타임스탬프  |yes    |
|64비트 정수(long)  |yes    |
|MinKey |yes    |
|MaxKey |yes    |
|Decimal128 |yes|   
|정규식 |yes|
|JavaScript |yes|
|JavaScript(범위 포함)|   yes |
|정의되지 않음  |yes    |

## <a name="indexes-and-index-properties"></a>인덱스 및 인덱스 속성

### <a name="indexes"></a>인덱스

|명령  |지원됨 |
|---------|---------|
|단일 필드 인덱스 |yes    |
|복합 인덱스 |yes    |
|Multikey 인덱스 |yes    |
|텍스트 인덱스 |예|
|2dsphere   |yes    |
|2d 인덱스   |예 |
|해시된 인덱스   | yes|

### <a name="index-properties"></a>인덱스 속성

|명령  |지원됨 |
|---------|---------|
|TTL|   yes |
|고유한 |yes|
|부분|   예|
|대/소문자 구분 안 함   |예|
|스파스 |예 |
|배경|    yes |

## <a name="operators"></a>연산자

### <a name="logical-operators"></a>논리 연산자

|명령  |지원됨 |
|---------|---------|
|$or    |   yes|
|$and   |   yes|
|$not   |   yes|
|$nor   |   yes| 

### <a name="element-operators"></a>요소 연산자

|명령  |지원됨 |
|---------|---------|
|$exists|   yes|
|$type  |   yes|

### <a name="evaluation-query-operators"></a>평가 쿼리 연산자

|명령  |지원됨 |
|---------|---------|
|$expr  |   예|
|$jsonSchema    |   예|
|$mod   |   yes|
|$regex |   yes|
|$text  | 아니요(지원되지 않습니다. 대신 $regex를 사용합니다.)| 
|$where |예| 

$regex 쿼리에서 왼쪽에 고정된 식은 인덱스 검색을 허용합니다. 그러나 ‘i’ 한정자(대/소문자 구분 없음) 및 ‘m’ 한정자(여러 줄)를 사용하면 모든 식에서 컬렉션 검사가 수행됩니다.

‘$’ 또는 ‘|’을 포함해야 하는 경우 두 개 이상의 정규식 쿼리를 만드는 것이 가장 좋습니다. 예를 들어, 다음 원래 쿼리가 ```find({x:{$regex: /^abc$/})```인 경우 이를 다음과 같이 수정해야 합니다.

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```입니다.

첫 번째 부분은 인덱스를 사용하여 ^abc로 시작하는 문서로 검색을 제한하며, 두 번째 부분은 정확한 항목의 일치를 확인합니다. 막대 연산자 ‘|’는 “or” 함수 역할을 하고, ```find({x:{$regex: /^abc|^def/})``` 쿼리는 ‘x’ 필드에 “abc” 또는 “def”로 시작하는 값이 있는 문서를 일치하는 항목으로 검색합니다. 인덱스를 사용하려면 쿼리를 $or 연산자 ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```에 의해 조인된 두 개의 서로 다른 쿼리로 나누는 것이 좋습니다.

### <a name="array-operators"></a>배열 연산자

|명령  |지원됨 | 
|---------|---------|
| $all | yes| 
| $elemMatch | yes| 
| $size | yes | 

### <a name="comment-operator"></a>설명 연산자

|명령  |지원됨 | 
|---------|---------|
$comment |yes| 

### <a name="projection-operators"></a>프로젝션 연산자

|명령  |지원됨 |
|---------|---------|
|$elemMatch |yes|
|$meta| 예|
|$slice | yes|

### <a name="update-operators"></a>업데이트 연산자

#### <a name="field-update-operators"></a>필드 업데이트 연산자

|명령  |지원됨 |
|---------|---------|
|$inc   |   yes|
|$mul   |   yes|
|$rename    |   yes|
|$setOnInsert|  yes|
|$set   |yes|
|$unset| yes|
|$min   |yes|
|$max   |yes|
|$currentDate   | yes|

#### <a name="array-update-operators"></a>배열 업데이트 연산자

|명령  |지원됨 |
|---------|---------|
|$  |yes|
|$[]|   yes|
|$[<identifier>]|   yes|
|$addToSet  |yes|
|$pop   |yes|
|$pullAll|  yes|
|$pull  |yes|
|$push  |yes|
|$pushAll| yes|


#### <a name="update-modifiers"></a>한정자 업데이트

|명령  |지원됨 |
|---------|---------|
|$each  |   yes|
|$slice |yes|
|$sort  |yes|
|$position  |yes|

#### <a name="bitwise-update-operator"></a>비트 업데이트 연산자

|명령  |지원됨 |
|---------|---------|
| $bit  |   yes|    
|$bitsAllSet    |   예|
|$bitsAnySet    |   예|
|$bitsAllClear  |예|
|$bitsAnyClear  |예|

### <a name="geospatial-operators"></a>지리 공간적 연산자

연산자 | 지원됨| 
--- | --- |
$geoWithin | yes |
$geoIntersects | yes | 
$near |  yes |
$nearSphere |  yes |
$geometry |  yes |
$minDistance | yes |
$maxDistance | yes |
$center | yes |
$centerSphere | yes |
$box | yes |
$polygon |  yes |

## <a name="cursor-methods"></a>커서 메서드

|명령  |지원됨 |
|---------|---------|
|cursor.batchSize() |   yes|
|cursor.close() |yes|
|cursor.isClosed()|     yes|
|cursor.collation()|    예|
|cursor.comment()   |yes|
|cursor.count() |yes|
|cursor.explain()|  예|
|cursor.forEach()   |yes|
|cursor.hasNext()   |yes|
|cursor.hint()  |yes|
|cursor.isExhausted()|  yes|
|cursor.itcount()   |yes|
|cursor.limit() |yes|
|cursor.map()   |yes|
|cursor.maxScan()   |yes|
|cursor.maxTimeMS()|    yes|
|cursor.max()   |yes|
|cursor.min()   |yes|
|cursor.next()| yes|
|cursor.noCursorTimeout()   |예|
|cursor.objsLeftInBatch()   |yes|
|cursor.pretty()|   yes|
|cursor.readConcern()|  yes|
|cursor.readPref()      |yes|
|cursor.returnKey() |예|
|cursor.showRecordId()| 예|
|cursor.size()  |Nes|
|cursor.skip()  |yes|
|cursor.sort()  |   yes|
|cursor.tailable()| 예|
|cursor.toArray()   |yes|

## <a name="sort-operations"></a>정렬 작업

`findOneAndUpdate` 작업을 사용할 경우 단일 필드에 대한 정렬 작업이 지원되지만 여러 필드에 대한 정렬 작업은 지원되지 않습니다.

## <a name="unique-indexes"></a>고유 인덱스

고유 인덱스를 사용하면 컬렉션의 모든 문서에서 특정 필드에 중복된 값이 나타나지 않으며, 이는 기본 “_id” 키에서 고유성이 유지되는 방식과 유사합니다. '고유' 제약 조건을 포함하여 createIndex 명령을 사용하여 Cosmos DB에서 사용자 지정 인덱스를 만들 수 있습니다.

## <a name="time-to-live-ttl"></a>TTL(Time to Live)

Cosmos DB는 문서의 타임스탬프를 기반으로 TTL(Time to Live)을 지원합니다. [Azure Portal](https://portal.azure.com)로 이동하여 컬렉션에 TTL을 사용하도록 설정할 수 있습니다.

## <a name="user-and-role-management"></a>사용자 및 역할 관리

Cosmos DB는 아직 사용자 및 역할을 지원하지 않습니다. 그러나 Cosmos DB는 [Azure Portal](https://portal.azure.com)(연결 문자열 페이지)을 통해 가져올 수 있는 RBAC(역할 기반 액세스 제어)와 읽기-쓰기 및 읽기 전용 암호/키를 지원합니다.

## <a name="replication"></a>복제

Cosmos DB는 가장 낮은 계층에서 자동 네이티브 복제를 지원합니다. 이 논리는 또한 짧은 대기 시간, 글로벌 복제를 달성하기 위해 확장됩니다. Cosmos DB는 수동 복제 명령을 지원하지 않습니다.

## <a name="write-concern"></a>쓰기 문제

일부 애플리케이션은 쓰기 작업 중에 필요한 응답 수를 지정하는 [쓰기 문제](https://docs.mongodb.com/manual/reference/write-concern/)를 사용합니다. Cosmos DB가 백그라운드에서 복제를 처리하는 방식 때문에 모든 쓰기는 자동으로 쿼럼(기본값)입니다. 클라이언트 코드를 통해 지정된 모든 쓰기 문제는 무시됩니다. [일관성 수준을 사용하여 가용성 및 성능 최대화](consistency-levels.md)에서 자세히 알아보세요.

## <a name="sharding"></a>분할

Azure Cosmos DB는 서버 측 자동 분할을 지원합니다. 분할 만들기, 배치 및 분산을 자동으로 관리합니다. Azure Cosmos DB는 수동 분할 명령을 지원하지 않으므로 addShard, balancerStart, moveChunk 등과 같은 명령을 호출할 필요가 없습니다. 컨테이너를 만들거나 데이터를 쿼리하는 동안 분할 키만 지정하면 됩니다.

## <a name="sessions"></a>세션

Azure Cosmos DB는 아직 서버 쪽 세션 명령을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Mongo 3.6 버전 기능](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)을 확인하세요.
- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.

<sup>참고: 이 문서에서는 MongoDB 데이터베이스와 유선 프로토콜 호환성을 제공하는 Azure Cosmos DB 기능을 설명합니다. Microsoft는 이 서비스를 제공하기 위해 MongoDB 데이터베이스를 실행하지 않습니다. Azure Cosmos DB는 MongoDB, Inc.와 관련이 없습니다.</sup>
