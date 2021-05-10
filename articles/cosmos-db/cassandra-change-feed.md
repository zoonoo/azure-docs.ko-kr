---
title: Azure Cosmos DB API for Cassandra의 변경 피드
description: Azure Cosmos DB API for Cassandra에서 변경 피드를 사용하여 데이터에 대한 변경 내용을 가져오는 방법을 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 10f037dddcce43a1e023982af816660bd325d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605096"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Azure Cosmos DB API for Cassandra의 변경 피드
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB API for Cassandra의 [변경 피드](change-feed.md) 지원은 CQL(Cassandra Query Language)의 쿼리 조건자를 통해 사용할 수 있습니다. 이러한 조건자 조건을 사용하여 변경 피드 API를 쿼리할 수 있습니다. 애플리케이션은 CQL에 필요한 기본 키(파티션 키라고도 함)를 사용하여 테이블에 대한 변경 내용을 가져올 수 있습니다. 그런 후 결과에 따라 추가 작업을 수행할 수 있습니다. 테이블의 행 변경 내용은 수정 시간 순서 및 파티션 키당 정렬 순서대로 캡처됩니다.

다음 예에서는 .NET을 사용하여 Cassandra API Keyspace 테이블의 모든 행에 대한 변경 피드를 가져오는 방법을 보여 줍니다. 조건자 COSMOS_CHANGEFEED_START_TIME()은 지정된 시작 시간(이 경우 현재 날짜/시간)부터 변경 피드의 항목을 쿼리하기 위해 CQL 내에서 직접 사용됩니다. [여기](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)에서는 C#에 대한 전체 샘플, [여기](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java)에서는 Java에 대한 전체 샘플을 다운로드할 수 있습니다.

각 반복에서 페이징 상태를 사용하여 마지막 요소 변경 내용을 읽은 후 쿼리가 다시 시작됩니다. Keyspace에서 테이블에 대한 새 변경 내용의 연속 스트림을 볼 수 있습니다. 삽입되거나 업데이트되는 행에 대한 변경 내용이 표시됩니다. Cassandra API의 변경 피드를 사용한 삭제 작업에 대한 감시는 현재 지원되지 않습니다.

# <a name="java"></a>[Java](#tab/java)

```java
    Session cassandraSession = utils.getSession();

    try {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
        LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
        String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
            + dtf.format(now)+ "'";
        
        byte[] token=null; 
        System.out.println(query); 
        while(true)
        {
            SimpleStatement st=new  SimpleStatement(query);
            st.setFetchSize(100);
            if(token!=null)
                st.setPagingStateUnsafe(token);
            
            ResultSet result=cassandraSession.execute(st) ;
            token=result.getExecutionInfo().getPagingState().toBytes();
            
            for(Row row:result)
            {
                System.out.println(row.getString("user_name"));
            }
        }
    } finally {
        utils.close();
        LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
    }
```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

기본 키를 기준으로 단일 행에 대한 변경 내용을 가져오려면 쿼리에 기본 키를 추가하면 됩니다. 다음 예에서는 "user_id = 1"인 행의 변경 내용을 추적하는 방법을 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                       + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>현재 제한 사항

Cassandra API에서 변경 피드를 사용하는 경우 다음과 같은 제한이 적용될 수 있습니다.

* 현재 삽입 및 업데이트가 지원됩니다. 삭제 작업은 아직 지원되지 않습니다. 이 문제를 해결하려면 삭제할 행에 소프트 마커를 추가하면 됩니다. 예를 들어, "deleted"라는 행에 필드를 추가하고 이를 "true"로 설정합니다.
* 마지막 업데이트는 핵심 SQL API와 동일하게 유지되며 엔터티에 대한 중간 업데이트는 사용할 수 없습니다.


## <a name="error-handling"></a>오류 처리

Cassandra API에서 변경 피드를 사용할 때 지원되는 오류 코드와 메시지는 다음과 같습니다.

* **HTTP 오류 코드 429** - 변경 피드의 속도가 제한되면 빈 페이지를 반환합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB Cassandra API 리소스 관리](./templates-samples-cassandra.md)