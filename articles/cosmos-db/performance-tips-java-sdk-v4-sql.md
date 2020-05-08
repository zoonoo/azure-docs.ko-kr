---
title: Azure Cosmos DB Java SDK v4에 대 한 성능 팁
description: Java SDK v4에 대 한 Azure Cosmos database 성능 향상을 위한 클라이언트 구성 옵션에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: ce4e4d11ead41ee8cc4a4bd1d85f1fbad2af4b07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982533"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4에 대 한 성능 팁

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Java SDK v2 동기화](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> 이 문서의 성능 팁은 Azure Cosmos DB Java SDK v4 전용입니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 릴리스 정보, [Maven 리포지토리](https://mvnrepository.com/artifact/com.azure/azure-cosmos)및 AZURE COSMOS DB java sdk v4 [문제 해결 가이드](troubleshoot-java-sdk-v4-sql.md) 를 참조 하세요. 현재 v4 보다 이전 버전을 사용 하는 경우 v4로 업그레이드 하는 데 도움이 필요한 경우 [Azure Cosmos DB JAVA SDK v4로 마이그레이션](migrate-java-v4-sdk.md) 가이드를 참조 하세요.
>

Azure Cosmos DB는 보장된 대기 시간 및 처리량으로 매끄럽게 크기가 조정되는 빠르고 유연한 분산 데이터베이스입니다. Azure Cosmos DB를 사용하여 데이터베이스의 크기를 조정하기 위해 주요 아키텍처를 변경하거나 복잡한 코드를 작성할 필요는 없습니다. 규모를 확장 및 축소하는 것은 단일 API 호출 또는 SDK 메서드 호출을 수행하는 것만큼 쉽습니다. 그러나 네트워크 호출을 통해 Azure Cosmos DB에 액세스 하기 때문에 Azure Cosmos DB Java SDK v4를 사용할 때 최대 성능을 얻기 위해 클라이언트 쪽 최적화를 수행할 수 있습니다.

"내 데이터베이스 성능을 향상시키는 방법"을 물으면 다음 옵션을 고려합니다.

## <a name="networking"></a>네트워킹

* **연결 모드: 직접 모드를 사용 합니다.**
<a id="direct-connection"></a>
    
    클라이언트에서 Azure Cosmos DB에 연결 하는 방법은 특히 클라이언트 쪽 대기 시간 측면에서 성능에 중요 한 영향을 미칩니다. *Connectionmode* 는 클라이언트 *connectionmode*를 구성 하는 데 사용할 수 있는 키 구성 설정입니다. Java SDK v4 Azure Cosmos DB의 경우 사용 가능한 두 *Connectionmode*는 다음과 같습니다.  
      
    * [게이트웨이(기본값)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [직접](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    이러한 *Connectionmode*는 기본적으로 요청 하는 경로를 클라이언트 컴퓨터에서 Azure Cosmos DB 백 엔드 파티션으로 이동 합니다. 일반적으로 직접 모드는 최상의 성능을 위해 기본 설정 된 옵션입니다. 클라이언트는 백 엔드를 사용 하지 않고 직접 Azure Cosmos DB 백 엔드에 있는 파티션에 대 한 TCP 연결을 직접 열고 요청을 *직접*보낼 수 있습니다. 이와 대조적으로, 게이트웨이 모드에서 클라이언트에 의해 수행 된 요청은 Azure Cosmos DB 프런트 엔드에서 "게이트웨이" 서버로 라우팅됩니다. 그러면 Azure Cosmos DB 백 엔드에서 적절 한 파티션으로 요청을 팬 합니다. 응용 프로그램이 엄격한 방화벽 제한을 사용 하는 회사 네트워크 내에서 실행 되는 경우 표준 HTTPS 포트 및 단일 끝점을 사용 하기 때문에 게이트웨이 모드를 선택 하는 것이 가장 좋습니다. 그러나 게이트웨이 모드는 데이터를 읽거나 Azure Cosmos DB 쓸 때마다 데이터를 읽거나 쓸 때마다 네트워크 홉 (클라이언트에서 게이트웨이로 및 게이트웨이로)을 추가 합니다. 이로 인해 직접 모드는 네트워크 홉이 적기 때문에 더 나은 성능을 제공 합니다.

    Connectionmode 매개 변수를 사용 하 여 Azure Cosmos DB 클라이언트 인스턴스를 생성 하는 *ConnectionPolicy* 동안 *connectionmode* 가 구성 됩니다.
    
   #### <a name="async"></a>[동기화](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Async API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[동기화](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Sync API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildClient();
    ```

    --- 

<a name="collocate-clients"></a>
* **성능을 위해 동일한 Azure 지역에서 클라이언트 배치**<a id="same-region"></a>

    가능 하면 Azure Cosmos DB를 호출 하는 모든 응용 프로그램을 Azure Cosmos 데이터베이스와 동일한 지역에 저장 합니다. 대략적으로 비교한다면, 동일한 지역 내의 Azure Cosmos DB 호출은 1-2밀리초 내에 완료되지만 미국 서부와 동부 해안 간의 대기 시간은 50밀리초보다 큽니다. 클라이언트에서 Azure 데이터 센터 경계로 요청이 전달되는 경로에 따라 이러한 요청 간 대기 시간은 달라질 수 있습니다. 호출하는 애플리케이션이 프로비전된 Azure Cosmos DB 엔드포인트와 동일한 Azure 지역 내에 있도록 하면 가능한 최저 대기 시간을 얻을 수 있습니다. 사용 가능한 영역 목록은 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

    ![Azure Cosmos DB 연결 정책 그림](./media/performance-tips/same-region.png)

    다중 지역 Azure Cosmos DB 계정과 상호 작용 하는 앱은 요청이 배치 된 지역으로 이동 하도록 [기본 위치]() 를 구성 해야 합니다.

* **낮은 대기 시간을 위해 Azure VM에서 가속 네트워킹을 사용 하도록 설정 합니다.**

성능을 최대화 하려면 지침에 따라 Windows에서 가속 네트워킹을 사용 하도록 설정 하는 것이 좋습니다. [(지침을 보려면 클릭)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) [Azure VM](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 을 사용 하 여 Azure VM을 사용 하도록 설정 하는 것이 좋습니다.

가속화 된 네트워킹을 사용 하지 않으면 Azure VM과 기타 Azure 리소스 사이에서 전송은 IO는 VM과 네트워크 카드 사이에 있는 호스트 및 가상 스위치를 통해 불필요 하 게 라우팅될 수 있습니다. 데이터 경로에 호스트 및 가상 스위치를 인라인으로 두면 통신 채널의 대기 시간과 지터도 증가 하 고, VM에서 CPU 사이클을 도용 합니다. 가속화 된 네트워킹을 사용 하는 경우 VM은 중개자 없이 NIC와 직접 상호 작용 합니다. 호스트 및 가상 스위치에 의해 처리 된 네트워크 정책 세부 정보는 이제 NIC의 하드웨어에서 처리 됩니다. 호스트 및 가상 스위치는 무시 됩니다. 일반적으로 가속 네트워킹을 사용 하는 경우 더 낮은 대기 시간 및 더 높은 처리량 뿐만 아니라 보다 *일관* 된 대기 시간 및 CPU 사용률 감소를 예측할 수 있습니다.

제한 사항: 가속화 된 네트워킹은 VM OS에서 지원 되어야 하며 VM이 중지 되 고 할당 취소 된 경우에만 사용할 수 있습니다. Azure Resource Manager를 사용 하 여 VM을 배포할 수 없습니다.

자세한 내용은 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 및 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 지침을 참조 하세요.

## <a name="sdk-usage"></a>SDK 사용
* **최신 SDK 설치**

    Azure Cosmos DB SDK는 최상의 성능을 제공하기 위해 지속적으로 향상됩니다. [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) 페이지를 참조하여 최신 SDK를 확인하고 향상된 기능을 검토하세요.

* **응용 프로그램 수명 동안 singleton Azure Cosmos DB 클라이언트 사용**

    각 Azure Cosmos DB 클라이언트 인스턴스는 스레드로부터 안전 하며 효율적인 연결 관리와 주소 캐싱을 수행 합니다. Azure Cosmos DB 클라이언트가 효율적으로 연결을 관리 하 고 성능을 향상 시키려면 응용 프로그램의 수명 동안 AppDomain 당 Azure Cosmos DB 클라이언트의 단일 인스턴스를 사용 하는 것이 좋습니다.

   <a id="max-connection"></a>

* **응용 프로그램에 필요한 가장 낮은 일관성 수준 사용**

    *CosmosClient*를 만들 때 명시적으로 설정 하지 않은 경우 사용 되는 기본 일관성은 *Session*입니다. 응용 프로그램 논리에서 *세션* 일관성을 요구 하지 않는 경우 *일관성* 을 *최종*으로 설정 합니다. 참고: Azure Cosmos DB 변경 피드 프로세서를 사용 하는 응용 프로그램에서 최소한 *세션* 일관성을 사용 하는 것이 좋습니다.

* **비동기 API를 사용 하 여 프로 비전 된 처리량 최대화**

    Azure Cosmos DB Java SDK v4는 Sync 및 Async 라는 두 개의 Api를 번들로 묶습니다. 대략적으로 말하면 비동기 API는 SDK 기능을 구현 하는 반면, Sync API는 비동기 API에 대 한 차단 호출을 만드는 씬 래퍼입니다. 이는 비동기 전용 이었던 이전 Azure Cosmos DB Async Java SDK v 2와 동기화 전용으로 완전히 분리 된 이전 Azure Cosmos DB Sync Java SDK v 2와 대조적입니다. 
    
    API 선택은 클라이언트 초기화 중에 결정 됩니다. *CosmosAsyncClient* 는 비동기 api를 지원 하지만 *COSMOSCLIENT* 는 Sync api를 지원 합니다. 
    
    비동기 API는 비차단 IO를 구현 하며, Azure Cosmos DB에 대 한 요청을 실행 하는 경우 최대 처리량을 달성 하는 경우 최적의 선택입니다. 
    
    각 요청에 대 한 응답을 차단 하는 API가 필요 하거나 응용 프로그램의 주요 패러다임 인 경우 Sync API를 사용 하는 것이 적절 한 선택 일 수 있습니다. 예를 들어 마이크로 서비스 응용 프로그램에서 Azure Cosmos DB에 대 한 데이터를 유지할 때 동기화 API를 원할 수 있으며, 제공 된 처리량은 중요 하지 않습니다.  
    
    동기화 API 처리량은 요청 응답 시간이 늘어남에 따라 저하 되지만 비동기 API는 하드웨어의 전체 대역폭 기능을 포화 상태로 만들 수 있습니다. 
    
    지리적 위치는 Sync API를 사용 하는 경우 더 높거나 더 일관 된 처리량을 제공할 수 있습니다 ( [성능에 대해 동일한 Azure 지역에 있는 클라이언트 배치](#collocate-clients)참조). 그러나 여전히 비동기 API realistic 처리량을 초과할 필요는 없습니다.

    일부 사용자는 Java SDK v4 비동기 API Azure Cosmos DB 구현 하는 데 사용 되는 사후 흐름 프레임 워크인 [Project Reactor](https://projectreactor.io/)에 익숙하지 않을 수도 있습니다. 이 문제가 우려 하는 경우 소개 하는 [Reactor 패턴 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) 를 읽고이에 대 한 자세한 내용을 확인 하기 위해 [사후 대응 프로그래밍에 대 한 소개](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) 를 확인 하는 것이 좋습니다. 비동기 인터페이스를 사용 하 여 이미 Azure Cosmos DB를 사용 했으며 사용한 SDK가 async Java SDK v 2 Azure Cosmos DB 된 경우에는 [activex](http://reactivex.io/)/[rxjava](https://github.com/ReactiveX/RxJava) 에 대해 잘 알고 있지만 Project reactivex에서 변경 된 내용이 무엇 인지 알 수 없습니다. 이 경우 [재 행위자 및 RxJava 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 를 살펴보고 알아보기.

    다음 코드 조각에서는 비동기 API 또는 Sync API 작업에 대해 Azure Cosmos DB 클라이언트를 초기화 하는 방법을 보여 줍니다.

    #### <a name="async"></a>[동기화](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-async-client"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Async API

    ```java
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[동기화](#tab/api-sync)
 
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-sync-client"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Sync API

    ```java
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildClient();
    ```    

    ---

* **ConnectionPolicy 튜닝**

    기본적으로 Azure Cosmos DB Java SDK v4를 사용 하는 경우 직접 모드 Cosmos DB 요청은 TCP를 통해 수행 됩니다. 내부적으로 SDK는 특별 한 직접 모드 아키텍처를 사용 하 여 네트워크 리소스를 동적으로 관리 하 고 최적의 성능을 얻을 수 있습니다.

    Java SDK v4 Azure Cosmos DB 대부분의 워크 로드를 사용 하 여 데이터베이스 성능을 향상 시키기 위해 직접 모드를 선택 하는 것이 가장 좋습니다. 

    * ***직접 모드 개요***

        ![직접 모드 아키텍처의 그림](./media/performance-tips-async-java/rntbdtransportclient.png)

        직접 모드에서 사용 되는 클라이언트 쪽 아키텍처를 사용 하면 예측 가능한 네트워크 사용률을 멀티플렉싱 Azure Cosmos DB 복제본에 액세스할 수 있습니다. 위의 다이어그램에서는 직접 모드가 Cosmos DB 백 엔드에서 클라이언트 요청을 복제본으로 라우팅하는 방법을 보여 줍니다. 직접 모드 아키텍처는 DB 복제본 당 클라이언트 쪽에 최대 10 개의 **채널** 을 할당 합니다. 채널은 요청 버퍼가 30 개 요청으로 이루어진 TCP 연결입니다. 복제본에 속하는 채널은 복제본의 **서비스 끝점**에서 필요에 따라 동적으로 할당 됩니다. 사용자가 직접 모드에서 요청을 실행 하는 경우 요청 **클라이언트** 는 파티션 키에 따라 적절 한 서비스 끝점으로 요청을 라우팅합니다. **요청 큐** 는 서비스 끝점 앞에 요청을 버퍼링 합니다.

    * ***직접 모드용 ConnectionPolicy 구성 옵션***

        이러한 구성 설정은 Direct mode SDK 동작을 제어 하는 RNTBD 아키텍처의 동작을 제어 합니다.
        
        첫 번째 단계로 다음과 같은 권장 구성 설정을 사용 합니다. 이러한 *Connectionpolicy* 옵션은 예기치 않은 방식으로 SDK 성능에 영향을 줄 수 있는 고급 구성 설정입니다. 장단점을 이해 하는 데 매우 편안 하 게 생각 하 고 반드시 필요한 경우가 아니면 수정 하지 않는 것이 좋습니다. 이 특정 항목에서 문제가 발생 하는 경우 [Azure Cosmos DB 팀](mailto:CosmosDBPerformanceSupport@service.microsoft.com) 에 문의 하세요.

        참조 데이터베이스로 Azure Cosmos DB를 사용 하는 경우 (즉, 여러 시점 읽기 작업과 몇 가지 쓰기 작업에 데이터베이스를 사용 하는 경우) *idleEndpointTimeout* 를 0 (시간 제한 없음)으로 설정 하는 것이 허용 될 수 있습니다.


        | 구성 옵션       | 기본    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | connectionTimeout          | "PT1M"     |
        | idleChannelTimeout         | PT0S입니다     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | PT5S     |
        | requestTimeout             | "PT1M"     |
        | Request타이머 해상도     | "PT 0.5 S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | PT15S    |

* **분할된 컬렉션에 대한 병렬 쿼리 튜닝**

    Azure Cosmos DB Java SDK v4는 병렬 쿼리를 지원 하므로 분할 된 컬렉션을 병렬로 쿼리할 수 있습니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 사용과 관련 된 [코드 샘플](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) 을 참조 하세요. 병렬 쿼리는 해당 직렬 대응을 통해 쿼리 대기 시간 및 처리량을 개선하기 위해 설계되었습니다.

    * ***튜닝 setMaxDegreeOfParallelism\:***
    
        병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동 합니다. 그러나 개별 분할된 컬렉션의 데이터는 쿼리와 관련하여 순차적으로 가져오기 됩니다. 따라서 setMaxDegreeOfParallelism을 사용하여 파티션 수를 설정하면 다른 모든 시스템 조건을 동일하게 유지하는 동시에 가장 성능이 뛰어난 쿼리를 달성할 수 있는 가능성을 극대화합니다. 파티션 수를 모르는 경우 setMaxDegreeOfParallelism을 사용하여 더 높은 값을 설정할 수 있습니다. 그러면 시스템에서 최소값(사용자가 제공한 입력인 파티션 수)을 최대 병렬 처리 수준으로 선택합니다.

        데이터가 쿼리와 관련하여 모든 파티션에 균등하게 분산되어 있는 경우 병렬 쿼리가 최고의 성능을 발휘한다는 것이 중요합니다. 쿼리에서 반환된 전체 또는 대부분의 데이터가 몇 개의 파티션(최악의 경우 하나의 파티션)에 집중되는 것처럼 분할된 컬렉션이 분할되는 경우 해당 파티션으로 인해 쿼리의 성능에는 장애가 발생합니다.

    * ***튜닝 setMaxBufferedItemCount\:***
    
        병렬 쿼리는 결과의 현재 배치가 클라이언트에서 처리 되는 동안 결과를 프리페치 하도록 설계 되었습니다. 프리페치는 쿼리의 전체 대기 시간 개선 사항에 도움이 됩니다. setMaxBufferedItemCount는 프리페치된 결과의 수를 제한합니다. setMaxBufferedItemCount를 설정하면 반환된 결과의 예상 수(또는 더 높은 수)로 설정하면 쿼리가 프리페치의 최대 이점을 얻을 수 있습니다.

        프리페치는 MaxDegreeOfParallelism에 관계없이 동일한 방식으로 작동하고 여기에는 모든 파티션의 데이터에 대한 단일 버퍼가 있습니다.

* **클라이언트 워크로드 규모 확장**

    높은 처리량 수준을 테스트 하는 경우 클라이언트 응용 프로그램은 CPU 또는 네트워크 사용률에 대 한 컴퓨터 50, 인해 병목 상태가 발생할 수 있습니다. 이 시점에 도달하면 여러 서버에 걸쳐 클라이언트 애플리케이션을 확장하여 Azure Cosmos DB 계정을 계속 추가할 수 있습니다.

    대기 시간을 낮게 유지 하기 위해 지정 된 서버에서 >50% CPU 사용률을 초과 하지 않는 것이 좋습니다.

   <a id="tune-page-size"></a>

* **성능 향상을 위해 쿼리/읽기 피드에 맞게 페이지 크기 조정**

    읽기 피드 기능을 사용 하 여 문서 대량 읽기를 수행 하거나 (예: *Readitems*) SQL 쿼리를 실행 하는 경우 (*queryitems*) 결과 집합이 너무 크면 결과가 분할 된 방식으로 반환 됩니다. 기본적으로, 100개의 항목 또는 1MB 단위(둘 중 먼저 도달하는 단위)로 결과가 반환됩니다.

    응용 프로그램에서 Azure Cosmos DB 쿼리를 실행 하 여 작업을 완료 하기 위해 쿼리 결과의 전체 집합이 필요한 경우 응용 프로그램에서 쿼리를 실행 한다고 가정 합니다. 적용 가능한 모든 결과를 검색 하는 데 필요한 네트워크 왕복 횟수를 줄이기 위해 [x m m-최대 항목 수](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 요청 헤더 필드를 조정 하 여 페이지 크기를 늘릴 수 있습니다. 

    * 단일 파티션 쿼리의 경우 [x-y](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) (페이지 크기에 대 한 제한 없음)를-1로 조정 하면 쿼리 응답 페이지 수를 최소화 하 여 대기 시간을 최대화 합니다. 전체 결과 집합은 단일 페이지에서 반환 되거나 쿼리가 시간 제한 간격 보다 오래 걸리는 경우 가능한 최소 페이지 수로 전체 결과 집합이 반환 됩니다. 이렇게 하면 요청 라운드트립 시간의 배수가 줄어듭니다.
    
    * 파티션 간 쿼리의 경우 [x m m-최대 항목 수](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 필드를-1로 설정 하 고 페이지 크기 제한을 제거 하면 SDK에서 관리할 수 없는 페이지 크기가 과도 하 게 발생할 위험이 있습니다. 교차 파티션 사례에서는 대기 시간을 줄이기 위해 페이지 크기 제한을 크게 증가 하지만 유한 값 (1000)으로 올리지 않는 것이 좋습니다. 
    
    일부 응용 프로그램에서는 전체 쿼리 결과 집합이 필요 하지 않을 수 있습니다. 예를 들어 사용자 인터페이스 또는 응용 프로그램 API가 한 번에 10 개의 결과만 반환 하는 경우와 같이 몇 가지 결과만 표시 해야 하는 경우에는 페이지 크기를 10으로 줄여 읽기 및 쿼리에 사용 되는 처리량을 줄일 수도 있습니다.

    REST 헤더 필드를 직접 수정 하는 대신 *byPage* 메서드의 기본 설정 페이지 크기 인수를 설정할 수도 있습니다. *ByPage* 의 기본 페이지 크기 인수 또는 기본 페이지 크기 [인수는 절대](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 요구 사항이 아니라 페이지 크기에 대 한 상한만 설정 한다는 점에 유의 하세요. 따라서 다양 한 이유로 기본 페이지 크기 보다 작은 반환 페이지가 Azure Cosmos DB 표시 될 수 있습니다. 

* **적절한 스케줄러 사용(이벤트 루프 IO Netty 스레드 도용 방지)**

    Azure Cosmos DB Java SDK의 비동기 기능은 [netty](https://netty.io/) 비 블로킹 IO를 기반으로 합니다. SDK는 IO 작업을 실행하기 위해 고정된 수의 IO netty 이벤트 루프 스레드(시스템에 있는 CPU 코어 수만큼)를 사용합니다. API에서 반환 된 Flux는 공유 IO 이벤트 루프 netty 스레드 중 하나에 대 한 결과를 내보냅니다. 따라서 이 공유 IO 이벤트 루프 netty 스레드를 차단하지 않는 것이 중요합니다. IO 이벤트 루프 netty 스레드에서 CPU 집약적인 작업 또는 차단 작업을 수행하면 교착 상태가 발생하거나 SDK 처리량이 크게 저하될 수 있습니다.

    예를 들어 다음 코드는 이벤트 루프 IO netty 스레드에서 CPU 집약적인 작업을 실행합니다.
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Async API

    ```java
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub.subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    결과에 대해 CPU 집약적인 작업을 수행하려는 경우 결과를 수신한 후에는 이벤트 루프 IO netty 스레드에서 이를 수행하지 않아야 합니다. 대신 사용자 고유의 스케줄러를 제공 하 여 아래와 같이 작업을 실행 하기 위한 고유한 스레드를 제공할 수 있습니다.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Async API

    ```java
    import reactor.core.scheduler.Schedulers;
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub
        .subscribeOn(Schedulers.elastic())
        .subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    작업 유형에 따라 작업에 적절 한 기존 Reactor 스케줄러를 사용 해야 합니다. 여기 [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)를 참조 하세요.

    Java SDK v4 Azure Cosmos DB에 대 한 자세한 내용은 [GitHub의 java monorepo 용 AZURE SDK의 Cosmos DB 디렉터리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)를 참조 하세요.

* **응용 프로그램의 로깅 설정 최적화**

    다양 한 이유로 인해 높은 요청 처리량을 생성 하는 스레드에서 로깅을 추가 하거나 추가 해야 할 수 있습니다. 이 스레드에서 생성 된 요청을 사용 하 여 컨테이너의 프로 비전 된 처리량을 완전히 포화 하는 것이 목표 라면 로깅 최적화를 사용 하면 성능이 크게 향상 될 수 있습니다.

    * ***비동기로 거 구성***

        동기로 거의 대기 시간은 요청 생성 스레드의 전체 대기 시간 계산을 의미 합니다. [Log4j2.xml](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) 와 같은 비동기로 거는 고성능 응용 프로그램 스레드에서 로깅 오버 헤드를 분리 하는 데 권장 됩니다.

    * ***Netty의 로깅 사용 안 함***

        Netty 라이브러리 로깅은 번잡 되며, 추가 CPU 비용이 발생 하지 않도록 하려면 구성의 로그인이 충분 하지 않을 수 있습니다. 디버깅 모드가 아니라면 netty의 로깅을 모두 해제합니다. 따라서 log4j를 사용하여 netty에서 ``org.apache.log4j.Category.callAppenders()``에 의한 추가 CPU 비용이 발생하지 않도록 하려면 코드베이스에 다음 행을 추가합니다.

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **OS Open files 리소스 제한**
 
    일부 Linux 시스템 (예: Red Hat)은 열린 파일 수에 대 한 상한 및 총 연결 수를 포함 합니다. 현재 한도를 보려면 다음을 실행합니다.

    ```bash
    ulimit -a
    ```

    열린 파일(nofile)의 수는 OS에 의해 구성된 연결 풀 크기와 다른 열린 파일을 위한 공간이 충분할 만큼 커야 합니다. 연결 풀 크기를 더 크게 수정할 수 있습니다.

    limits.conf 파일을 엽니다.

    ```bash
    vim /etc/security/limits.conf
    ```
    
    다음 줄을 추가/수정합니다.

    ```
    * - nofile 100000
    ```

* **시점 쓰기에서 파티션 키를 지정 합니다.**

    Point write의 성능을 향상 시키려면 아래와 같이 point write API 호출에서 항목 파티션 키를 지정 합니다.

    #### <a name="async"></a>[동기화](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-good-async"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Async API

    ```java
    asyncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions()).block();
    ```

    #### <a name="sync"></a>[동기화](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-good-sync"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Sync API

    ```java
    syncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions());
    ```

    ---

    아래와 같이 항목 인스턴스만 제공 하지 않습니다.

    #### <a name="async"></a>[동기화](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-bad-async"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Async API

    ```java
    asyncContainer.createItem(item).block();
    ```

    #### <a name="sync"></a>[동기화](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-bad-sync"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Sync API

    ```java
    syncContainer.createItem(item);
    ```

    ---

    후자는 지원 되지만 응용 프로그램에 대기 시간이 추가 됩니다. SDK는 항목을 구문 분석 하 고 파티션 키를 추출 해야 합니다.

## <a name="indexing-policy"></a>인덱싱 정책
 
* **더 빠른 쓰기를 위해 인덱싱에서 사용 하지 않는 경로 제외**

    Azure Cosmos DB의 인덱싱 정책을 통해 인덱싱 경로(setIncludedPaths 및 setExcludedPaths)를 활용하여 인덱싱에 포함하거나 제외할 문서 경로를 지정할 수 있습니다. 인덱싱 비용이 인덱싱된 고유 경로 수와 직접 관련이 있기 때문에, 인덱싱 경로를 사용하면 사전에 알려진 쿼리 패턴의 시나리오에 대해 쓰기 성능을 향상시키고 인덱스 스토리지를 낮출 수 있습니다. 예를 들어 다음 코드는 "*" 와일드 카드를 사용 하 여 인덱싱에서 문서의 전체 섹션 (하위 트리 라고도 함)을 제외 하는 방법을 보여 줍니다.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven:: azure-cosmos)
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    자세한 내용은 [Azure Cosmos DB 인덱싱 정책](indexing-policies.md)을 참조하세요.

## <a name="throughput"></a>처리량
<a id="measure-rus"></a>

* **낮은 요청 단위/초 사용량 측정 및 튜닝**

    Azure Cosmos DB는 관계형 쿼리와 계층형 쿼리 등 다양한 데이터베이스 작업에 데이터베이스 컬렉션 내부의 문서에서 적용되는 UDF, 저장 프로시저 및 트리거를 제공합니다. 이러한 작업 각각과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신 다양한 데이터베이스 작업을 수행하고 애플리케이션 요청을 처리하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다.

    처리량은 각 컨테이너에 설정된 [요청 단위](request-units.md) 수에 따라 프로비전됩니다. 요청 단위 소비는 초당 비율로 평가됩니다. 해당 컨테이너에 프로비전된 요청 단위 비율을 초과하는 애플리케이션은 비율이 컨테이너에 프로비전된 수준 아래로 떨어질 때까지 제한됩니다. 애플리케이션에 더 높은 수준의 처리량이 필요한 경우 추가 요청 단위를 프로비전하여 처리량을 늘릴 수 있습니다.

    쿼리의 복잡성은 작업에 사용되는 요청 단위의 양에 영향을 줍니다. 조건자의 수, 조건자의 특성, UDF 수 및 원본 데이터 집합의 크기는 모두 쿼리 작업의 비용에 영향을 줍니다.

    모든 작업(만들기, 업데이트 또는 삭제)에 대한 오버헤드를 측정하려면 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 헤더를 검사하여 이 작업에 사용된 요청 단위 수를 측정합니다. ResourceResponse\<t> 또는 FeedResponse\<t>에서 동일한 requestcharge 속성을 확인할 수도 있습니다.

    #### <a name="async"></a>[동기화](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-request-charge-async"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Async API

    ```java
    CosmosAsyncItemResponse<CustomPOJO> response = asyncContainer.createItem(item).block();

    response.getRequestCharge();
    ```     

    #### <a name="sync"></a>[동기화](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-request-charge-sync"></a>Java SDK V4 (Maven com. azure:: azure-cosmos) Sync API    

    ```java
    CosmosItemResponse<CustomPOJO> response = syncContainer.createItem(item);

    response.getRequestCharge();
    ```     

    ---

    이 헤더에서 반환된 요청 비용은 프로비전된 처리량의 일부입니다. 예를 들어 RU 2000개를 프로비전했고 앞의 쿼리에서 1000개의 1KB 문서를 반환하는 경우 작업 비용은 1000입니다. 따라서 1초 이내에 서버는 후속 요청의 속도를 제한하기 전에 이러한 두 가지 요청만 인식합니다. 자세한 내용은 [요청 단위](request-units.md)와 [요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 참조하세요.

<a id="429"></a>
* **너무 큰 속도 제한/요청 속도 처리**

    클라이언트가 계정에 대해 예약된 처리량을 초과하려 할 때도 서버의 성능이 저하되거나 예약된 수준 이상의 처리량이 사용되지 않습니다. 서버는 RequestRateTooLarge (HTTP 상태 코드 429)를 사용 하 여 요청을 종료 하 고, 사용자가 요청을 다시 시도 전에 대기 해야 하는 시간 (밀리초)을 나타내는 [미리 헤더를 반환 합니다.](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK는 이 응답을 암시적으로 모두 catch하고, server-specified retry-after 헤더를 준수하고 요청을 다시 시도합니다. 동시에 여러 클라이언트가 계정에 액세스하지만 않으면 다음 재시도가 성공할 것입니다.

    하나 이상의 클라이언트가 누적 작업을 요청 빈도 이상에서 일관 되 게 작동 하는 경우 현재 클라이언트에서 내부적으로 9로 설정 된 기본 재시도 횟수는 충분 하지 않을 수 있습니다. 이 경우 클라이언트는 응용 프로그램에 상태 코드 429을 포함 하는 *CosmosClientException* 을 throw 합니다. 기본 재시도 횟수는 ConnectionPolicy 인스턴스에서 setRetryOptions를 설정하여 변경할 수 있습니다. 기본적으로 요청이 요청 률을 초과 하 여 계속 작동 하는 경우 누적 대기 시간 30 초 후에 상태 코드가 429 인 *CosmosClientException* 이 반환 됩니다. 현재 재시도 횟수가 최대 재시도 횟수보다 작은 경우에도 이러한 현상이 발생하기 때문에 기본값인 9 또는 사용자 정의 값으로 두세요.

    자동화된 재시도 동작은 대부분의 애플리케이션에 대한 복원력 및 유용성을 개선하는 데 도움이 되는 반면, 성능 벤치마크 수행 시 특히 대기 시간을 측정할 때 방해가 될 수 있습니다. 실험이 서버 제한에 도달하고 클라이언트 SDK를 자동으로 재시도하는 경우 클라이언트 관찰 대기 시간이 급증합니다. 성능 실험 중 대기 시간 급증을 방지하려면, 각 작업에 의해 반환된 비용을 측정하고 요청이 예약된 요청 속도 이하로 작동하고 있는지 확인합니다. 자세한 내용은 [요청 단위](request-units.md)를 참조하세요.

* **처리량을 높이기 위해 문서 크기를 줄이도록 설계**

    주어진 작업의 요청 비용(요청 처리 비용)은 문서 크기와 직접 관련이 있습니다. 큰 문서에서 작업하는 경우 작은 문서 작업에 비해 비용이 많이 듭니다. 항목 크기를 ~ 1KB 또는 유사한 순서나 크기를 갖도록 응용 프로그램 및 워크플로를 설계 하는 것이 이상적입니다. 대기 시간이 중요 한 응용 프로그램의 경우 많은 항목을 피해 야 합니다. 즉, 응용 프로그램의 속도가 느려질 수 있습니다.

## <a name="next-steps"></a>다음 단계

확장성 및 고성능을 위한 애플리케이션 설계 방법에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](partition-data.md)을 참조하세요.
