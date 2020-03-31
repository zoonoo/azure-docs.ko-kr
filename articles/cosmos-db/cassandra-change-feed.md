---
title: 카산드라에 대한 Azure 코스모스 DB API의 피드 변경
description: Cassandra용 Azure Cosmos DB API에서 변경 피드를 사용하여 데이터를 변경하는 방법을 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694624"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>카산드라에 대한 Azure 코스모스 DB API의 피드 변경

카산드라에 대한 Azure Cosmos DB API의 [변경 피드](change-feed.md) 지원은 CQL(카산드라 쿼리 언어)의 쿼리 조건자에서 사용할 수 있습니다. 이러한 조건자 조건을 사용하여 변경 피드 API를 쿼리할 수 있습니다. 응용 프로그램은 CQL에서 요구되는 기본 키(파티션 키라고도 함)를 사용하여 테이블에 대한 변경 내용을 얻을 수 있습니다. 그런 다음 결과에 따라 추가 작업을 수행할 수 있습니다. 테이블의 행에 대한 변경 내용은 수정 시간의 순서로 캡처되며 파티션 키당 정렬 순서가 보장됩니다.

다음 예제에서는 .NET을 사용하여 Cassandra API 키스페이스 테이블의 모든 행에 대한 변경 피드를 얻는 방법을 보여 주습니다. 조건자 COSMOS_CHANGEFEED_START_TIME()는 CQL 내에서 지정된 시작 시간(이 경우 현재 datetime)에서 변경 피드의 항목을 쿼리하는 데 직접 사용됩니다. 여기에서 전체 샘플을 다운로드할 수 [있습니다.](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)

각 반복에서 페이징 상태를 사용하여 마지막 지점 변경 시 쿼리가 다시 시작됩니다. 키스페이스에서 테이블에 대한 새로운 변경 사항이 연속된 스트림을 볼 수 있습니다. 삽입되거나 업데이트된 행에 대한 변경 내용이 표시됩니다. Cassandra API에서 변경 피드를 사용하여 삭제 작업을 감시하는 것은 현재 지원되지 않습니다. 

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

기본 키로 단일 행에 대한 변경 내용을 얻으려면 쿼리에 기본 키를 추가할 수 있습니다. 다음 예제에서는 "user_id = 1" 행의 변경 내용을 추적하는 방법을 보여 주며

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>현재 제한 사항

Cassandra API를 사용하여 변경 피드를 사용할 때는 다음과 같은 제한 사항이 적용됩니다.

* 삽입 및 업데이트는 현재 지원됩니다. 삭제 작업은 아직 지원되지 않습니다. 해결 방법을 사용하면 삭제되는 행에 소프트 마커를 추가할 수 있습니다. 예를 들어 행에 "deleted"라는 필드를 추가하고 "true"로 설정합니다.
* 마지막 업데이트는 핵심 SQL API와 마찬가지로 유지되며 엔터티에 대한 중간 업데이트는 사용할 수 없습니다.


## <a name="error-handling"></a>오류 처리

Cassandra API에서 변경 피드를 사용할 때 다음과 같은 오류 코드 및 메시지가 지원됩니다.

* **HTTP 오류 코드 429** - 변경 피드의 속도가 제한되면 빈 페이지를 반환합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 관리자 템플릿을 사용하여 Azure 코스모스 DB 카산드라 API 리소스 관리](manage-cassandra-with-resource-manager.md)
