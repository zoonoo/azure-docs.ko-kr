---
title: mongoimport 및 mongorestorestore를 사용하여 MongoDB 데이터를 Azure Cosmos DB로 마이그레이션
description: mongoimport와 mongorestore를 사용하여 데이터를 Cosmos DB로 가져오는 방법을 알아봅니다.
keywords: mongoimport, mongorestore
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: rimman
ms.custom: mvc
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 4cd30c7981cd6807113729292db403a80cbddef0
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793753"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>MongoDB 데이터를 Azure Cosmos DB로 마이그레이션

 이 자습서에서는 MongoDB에 저장된 데이터를 Cosmos DB의 MongoDB API를 사용하도록 구성된 Azure Cosmos DB로 마이그레이션하는 방법에 대한 지침을 제공합니다. MongoDB에서 데이터를 가져와서 Azure Cosmos DB SQL API에 사용하려는 경우 [데이터 마이그레이션 도구](import-data.md)를 사용하여 데이터를 가져와야 합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 마이그레이션 계획 준비
> * mongoimport를 사용하여 데이터 마이그레이션
> * mongorestore를 사용하여 데이터 마이그레이션

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

마이그레이션을 시작하기 전에 다음 필수 조건을 검토하고 완료합니다.

### <a name="plan-for-the-migration"></a>마이그레이션 계획

이 섹션에서는 데이터 마이그레이션을 계획하는 방법에 대해 설명합니다. RU 요금을 추정하고, 머신에서 클라우드 서비스까지의 대기 시간을 결정하며, 일괄 처리 크기와 삽입 작업자의 수를 계산합니다.


#### <a name="pre-create-and-scale-your-collections"></a>컬렉션 미리 만들기 및 크기 조정

mongoimport 또는 mongorestore를 사용하여 마이그레이션하기 전에 [Azure Portal](https://portal.azure.com) 또는 MongoDB 드라이버 및 도구에서 모든 컬렉션을 미리 만듭니다. 

[Azure Portal](https://portal.azure.com)에서 마이그레이션에 대한 컬렉션 처리량을 늘립니다. 처리량이 높을수록 속도 제한을 방지하고 마이그레이션 시간을 줄일 수 있습니다. 마이그레이션 후 즉시 처리량을 줄여 비용을 절감할 수 있습니다.

컬렉션 수준에서 처리량을 프로비전하는 것 외에도, 프로비전된 처리량을 공유할 컬렉션 세트에 대해 데이터베이스 수준에서 처리량을 프로비전할 수 있습니다. 데이터베이스와 컬렉션을 미리 만들고 공유 처리량 데이터베이스의 각 컬렉션에 대한 분할 키를 정의해야 합니다.

기본 설정 도구, 드라이버 또는 SDK를 사용하여 분할된 컬렉션을 만들 수 있습니다. 이 예제에서는 Mongo Shell을 사용하여 분할된 컬렉션을 만듭니다.

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
이 명령은 다음 결과를 반환합니다.

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>단일 문서 쓰기에 대해 대략적인 RU 요금 계산

MongoDB 셸에서 Cosmos DB의 MongoDB API를 사용하도록 구성된 Cosmos 계정에 연결합니다. 지침은 [Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md)에서 찾을 수 있습니다.

다음으로, 샘플 문서 중 하나를 사용하여 샘플 삽입 명령을 실행합니다.
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
`db.runCommand({getLastRequestStatistics: 1})`명령을 실행합니다.

다음 출력과 같은 응답을 받습니다.
     
```bash
globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "insert",
    "RequestCharge": 10,
    "RequestDurationInMilliSeconds": NumberLong(50)
}
```
        
요청 요금을 기록해 둡니다.
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>머신에서 Cosmos DB까지의 대기 시간 확인
    
`setVerboseShell(true)` 명령을 사용하여 MongoDB 셸에서 자세한 로깅을 사용하도록 설정합니다.
    
`db.coll.find().limit(1)` 명령을 사용하여 데이터베이스에 대해 기본 쿼리를 실행합니다.

다음 출력과 같은 응답을 받습니다.

```bash
Fetched 1 record(s) in 100(ms)
```
        
마이그레이션을 실행하기 전에 삽입된 문서를 제거하여 중복된 문서가 없도록 합니다. `db.coll.remove({})` 명령을 사용하여 문서를 제거할 수 있습니다.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>batchSize 및 numInsertionWorkers 속성에 대한 대략적인 값 계산

**batchSize** 속성의 경우 "머신에서 Cosmos DB까지의 대기 시간 확인" 섹션에서 수행한 대로 프로비전된 총 처리량(RU/초)을 단일 문서 쓰기에 사용된 RU로 나눕니다. 계산된 값이 24보다 작거나 같으면 해당 숫자를 속성 값으로 사용합니다. 계산된 값이 24보다 크면 속성 값을 24로 설정합니다.
    
**numInsertionWorkers** 속성 값을 구하려면 다음 수식을 사용합니다.

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

다음 값을 사용하여 **numInsertionWorkers** 속성 값을 계산할 수 있습니다.

| 자산 | 값 |
|--------|-----|
| **batchSize** | 24 |
| 프로비전된 RU 수 | 10000 |
| 대기 시간 | 0.100s |
| 사용된 RU 수 | 10RU |
| **numInsertionWorkers** | (10,000개 RU x 0.100초) / (24 x 10개 RU) = **4.1666** |

**monogoimport** 마이그레이션 명령을 실행합니다. 명령 매개변수는 이 문서의 뒷부분에서 설명합니다.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

또한 **monogorestore** 명령을 사용할 수도 있습니다. 모든 컬렉션의 처리량이 이전 계산에서 사용된 RU 수 이상으로 설정되었는지 확인합니다.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>필수 조건 완료

마이그레이션이 계획되면 다음 단계를 수행합니다. 

* **샘플 데이터 가져오기**: 마이그레이션을 시작하기 전에 몇 가지 샘플 데이터가 있는지 확인합니다. 

* **처리량 증가**: 데이터 마이그레이션 기간은 개별 컬렉션 또는 데이터베이스에 프로비전하는 처리량에 따라 달라집니다. 대량 데이터 마이그레이션의 경우 처리량을 늘려야 합니다. 마이그레이션이 완료되면 비용을 절약하기 위해 처리량을 줄입니다. 

* **SSL 사용**:  Cosmos DB에는 엄격한 보안 요구 사항과 표준이 있습니다. Cosmos 계정과 상호 작용하는 경우 SSL을 사용하도록 설정해야 합니다. 이 문서의 절차에는 mongoimport 및 mongorestore 명령에서 SSL을 사용하도록 설정하는 방법이 포함되어 있습니다.

* **Cosmos DB 리소스 만들기**: 마이그레이션을 시작하기 전에 Azure Portal에서 모든 컬렉션을 미리 만듭니다. 데이터베이스 수준의 프로비전 처리량이 있는 Cosmos 계정으로 마이그레이션하는 경우 컬렉션을 만들 때 파티션 키를 제공해야 합니다.

* **연결 문자열 가져오기**: [Azure Portal](https://portal.azure.com)의 왼쪽 영역에서 **Azure Cosmos DB** 항목을 선택합니다. **구독** 아래에서 계정 이름을 선택합니다. **연결 문자열** 아래에서 **연결 문자열**을 선택합니다. 포털의 오른쪽에는 계정에 연결하는 데 필요한 정보가 표시됩니다.

    ![연결 문자열 정보](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>mongoimport 사용

데이터를 Cosmos 계정으로 가져오려면 다음 템플릿을 사용합니다.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

\<your_hostname>, \<your_username> 및 \<your_password> 매개 변수를 계정의 특정 값으로 바꿉니다. 다음 예제에서는 **sampleDB**를 \<your_database>의 값으로 사용하고 **sampleColl**을 \<your_collection>의 값으로 사용합니다.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>mongorestore 사용

Cosmos DB의 MongoDB API로 구성된 Cosmos 계정에 데이터를 복원하려면 다음 템플릿을 사용하여 가져오기를 실행합니다.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

\<your_hostname>, \<your_username> 및 \<your_password> 매개 변수를 계정의 특정 값으로 바꿉니다. 다음 예제에서는 **./dump/dumps/dump-2016-12-07**을 \<path_to_backup>의 값으로 사용합니다.

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 리소스 그룹, Cosmos 계정 및 모든 관련 리소스를 삭제해도 됩니다. 다음 단계를 사용하여 리소스 그룹을 삭제합니다.

1. Cosmos 계정을 만든 리소스 그룹으로 이동합니다.
1. **리소스 그룹 삭제**를 선택합니다.
1. 삭제할 리소스 그룹의 이름을 확인하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 MongoDB API를 사용하여 데이터를 쿼리하는 방법에 대해 알아보려면 다음 자습서로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [MongoDB 데이터를 쿼리하는 방법](../cosmos-db/tutorial-query-mongodb.md)
