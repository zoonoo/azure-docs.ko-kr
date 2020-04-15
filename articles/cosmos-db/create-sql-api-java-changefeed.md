---
title: 자습서 - 변경 피드를 사용하는 엔드투엔드 비동기 Java SQL API 애플리케이션 샘플
description: 이 자습서는 변경 피드를 사용하여 컨테이너의 구체화된 뷰를 유지하면서 Azure Cosmos DB 컨테이너에 문서를 삽입하는 간단한 Java SQL API 애플리케이션을 안내합니다.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587220"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>자습서 - 변경 피드를 사용하는 엔드투엔드 비동기 Java SQL API 애플리케이션 샘플

이 자습서 가이드는 변경 피드를 사용하여 컨테이너의 구체화된 뷰를 유지하면서 Azure Cosmos DB 컨테이너에 문서를 삽입하는 간단한 Java SQL API 애플리케이션을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 개인용 컴퓨터

* Azure Cosmos DB 계정의 URI 및 키

* Maven

* Java 8

## <a name="background"></a>배경

Azure Cosmos DB 변경 피드는 문서 삽입에 대한 응답으로 작업을 트리거하는 이벤트 구동 인터페이스를 제공하며, 많은 용도로 사용됩니다. 예를 들어, 읽기와 쓰기가 많은 애플리케이션에서 변경 피드의 주요 용도는 문서를 수집할 때 컨테이너에 대한 실시간 **구체화된 뷰**를 만드는 것입니다. 구체화된 뷰 컨테이너는 동일한 데이터를 보유하지만 효율적인 읽기를 위해 분할되어 애플리케이션의 읽기 및 쓰기를 효율적으로 만듭니다.

변경 피드 이벤트 관리 작업은 SDK에 내장된 변경 피드 프로세서 라이브러리에서 주로 처리됩니다. 이 라이브러리는 필요한 경우 다수의 작업자에게 변경 피드 이벤트를 배포할 수 있을 만큼 강력합니다. 변경 피드 라이브러리에 콜백을 제공하기만 하면 됩니다.

이 간단한 예제는 구체화된 뷰에서 문서를 만들고 삭제하는 단일 작업자가 있는 변경 피드 프로세서 라이브러리를 보여줍니다.

## <a name="setup"></a>설치 프로그램

아직 수행하지 않은 경우 앱 예제 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> 이 빠른 시작은 Java SDK 4.0 또는 Java SDK 3.7.0을 사용하여 수행할 수 있습니다. **Java SDK 3.7.0을 사용하려면 터미널에 ```git checkout SDK3.7.0```을 입력합니다**. 그렇지 않으면 ```master``` 분기를 유지합니다. 그러면 Java SDK 4.0이 기본적으로 사용됩니다.

리포지토리 디렉터리에서 터미널을 엽니다. 다음을 실행하여 앱을 빌드합니다.

```bash
mvn clean package
```

## <a name="walkthrough"></a>연습

1. 첫 번째 확인으로, Azure Cosmos DB 계정이 있어야 합니다. 브라우저에서 **Azure Portal**을 열고 Azure Cosmos DB 계정으로 이동하여 왼쪽 창에서 **Data Explorer**로 이동합니다.

    ![Azure Cosmos DB 계정](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. 다음 명령을 사용하여 터미널에서 앱을 실행합니다.

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. 다음 내용이 보이면 Enter 키를 누른 다음,

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    브라우저에서 Azure Portal Data Explorer로 돌아갑니다. **GroceryStoreDatabase** 데이터베이스가 빈 컨테이너 세 개와 함께 추가된 것을 볼 수 있습니다. 

    * **InventoryContainer** - UUID인 ```id``` 항목으로 분할되어 있는 예제 식료품점의 인벤토리 레코드입니다.
    * **InventoryContainer-pktype** - ```type``` 항목에 대한 쿼리에 최적화된 인벤토리 레코드의 구체화된 뷰입니다.
    * **InventoryContainer-leases** - 변경 피드에는 임대 컨테이너가 항상 필요하며, 임대는 변경 피드를 읽는 앱의 진행 상황을 추적합니다.


    ![빈 컨테이너](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. 이제 터미널에 프롬프트가 보입니다.

    ```bash
    Press enter to start creating the materialized view...
    ```

    Enter 키를 누릅니다. 이제 다음 코드 블록이 실행되고 다른 스레드에서 변경 피드 프로세서가 초기화됩니다. 


    **Java SDK 4.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    **Java SDK 3.7.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```

    ```"SampleHost_1"```은 변경 피드 프로세서 작업자의 이름입니다. ```changeFeedProcessorInstance.start()```는 변경 피드 프로세서를 실제로 시작합니다.

    브라우저에서 Azure Portal Data Explorer로 돌아갑니다. **InventoryContainer-leases** 컨테이너에서 **항목**을 클릭하여 콘텐츠를 봅니다. 변경 피드 프로세서가 임대 컨테이너를 채운 것이 보입니다. 즉, 프로세서가 ```SampleHost_1``` 작업자에게 **InventoryContainer**의 일부 파티션에 대한 임대를 할당했습니다.

    ![임대](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. 터미널에서 Enter를 다시 누릅니다. 그러면 **InventoryContainer**에 문서 10개가 삽입되도록 트리거됩니다. 각 문서 삽입은 변경 피드에 JSON으로 나타납니다. 다음 콜백 코드는 JSON 문서를 구체화된 뷰로 미러링하여 이러한 이벤트를 처리합니다.

    **Java SDK 4.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. 코드가 5~10초 동안 실행되도록 합니다. 그런 다음 Azure Portal Data Explorer로 돌아가서 **InventoryContainer> 항목**으로 이동합니다. 인벤토리 컨테이너에 항목이 삽입되는 것이 보입니다. 파티션 키(```id```)에 유의합니다.

    ![피드 컨테이너](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. 이제 Data Explorer에서 **InventoryContainer-pktype > 항목**으로 이동합니다. 이것은 구체화된 뷰이며, 이 컨테이너의 항목은 피드 변경에 의해 프로그래밍 방식으로 삽입되었기 때문에 **InventoryContainer**를 미러링합니다. 파티션 키(```type```)에 유의합니다. 이 구체화된 뷰는 ```type```에 대한 쿼리 필터링에 최적화되어 있는데, ```id```으로 분할되어 있기 때문에 **InventoryContainer**에는 비효율적입니다.

    ![구체화된 뷰](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. 단일 ```upsertItem()``` 호출을 사용하여 **InventoryContainer**와 **InventoryContainer-pktype**에서 문서를 삭제하겠습니다. 먼저 Azure Portal Data Explorer를 살펴봅니다. ```/type == "plums"```인 문서를 삭제하겠습니다. 아래에 빨간색으로 둘러싸여 있습니다

    ![구체화된 뷰](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Enter 키를 다시 눌러서 예제 코드에서 ```deleteDocument()``` 함수를 호출합니다. 아래에 표시된 이 함수는 문서 TTL(Time to Live)을 5초로 설정하는 ```/ttl == 5```를 사용하여 새 버전의 문서를 upsert합니다. 
    
    **Java SDK 4.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    **Java SDK 3.7.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    변경 피드 ```feedPollDelay```는 100ms로 설정됩니다. 따라서 변경 피드가 이 업데이트에 거의 즉시 응답하고 위에 보이는 ```updateInventoryTypeMaterializedView()```를 호출합니다. 마지막 함수 호출은 **InventoryContainer-pktype**에 TTL이 5초인 새 문서를 upsert합니다.

    이렇게 하면 약 5초 후에 문서가 만료되고 두 컨테이너에서 삭제됩니다.

    변경 피드는 항목 삽입 또는 업데이트 시에만 이벤트를 발생시키고 항목 삭제 시에는 그렇지 않기 때문에 이 절차가 필요합니다.

1. Enter를 한 번 더 눌러서 프로그램을 닫고 리소스를 정리합니다.
