---
title: Azure Cosmos DB의 일반적인 오류 문제 해결 Cassandra API
description: 이 문서에서는 Azure Cosmos DB에서 발생 하는 일반적인 문제를 해결 하는 방법에 대해 설명 Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658574"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB의 일반적인 문제 해결 Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB의 Cassandra API은 인기 있는 오픈 소스 Apache Cassandra 데이터베이스에 대 한 [유선 프로토콜 지원을](cassandra-support.md) 제공 하 고 [Azure Cosmos DB](./introduction.md)으로 구동 되는 호환성 계층입니다. 완벽 하 게 관리 되는 클라우드 기본 서비스인 Azure Cosmos DB는 Cassandra API에 대 한 [가용성, 처리량 및 일관성을 보장](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) 합니다. 이러한 보장은 Apache Cassandra의 레거시 구현에서 가능 하지 않습니다. 또한 Cassandra API 유지 관리 플랫폼 작업과 제로 가동 중지 시간 패치를 용이 하 게 합니다. 따라서 대부분의 백엔드 작업은 Apache Cassandra와 다르므로 일반적인 오류를 방지 하기 위해 특정 설정 및 방법을 권장 합니다. 

이 문서에서는 Azure Cosmos DB Cassandra API를 사용 하는 응용 프로그램에 대 한 일반적인 오류 및 해결 방법을 설명 합니다. 오류가 아래에 나열 되어 있지 않고 [Cassandra API에서 지원](cassandra-support.md)되는 작업을 실행할 때 오류가 발생 하는 경우, *네이티브 Apache Cassandra를 사용 하는 경우 오류가 표시 되지* 않으면 [Azure 지원 요청을 만듭니다](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
이는 가능한 원인 및 내부 예외를 많이 포함 하는 최상위 래퍼 예외 이며, 대부분은 클라이언트와 관련이 있을 수 있습니다. 
### <a name="solution"></a>솔루션
몇 가지 인기 있는 원인과 해결 방법은 다음과 같습니다. 
- Azure LoadBalancers 조정기의 유휴 시간 제한:로 매니페스트 될 수도 있습니다 `ClosedConnectionException` . 이 문제를 해결 하려면 드라이버에서 연결 유지 설정 ( [아래](#enable-keep-alive-for-java-driver)참조)을 설정 하 고 운영 체제에서 keep-alive 설정을 늘리거나 [Azure Load Balancer에서 유휴 시간 제한을 조정](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)합니다. 
- **클라이언트 응용 프로그램 리소스 소모:** 클라이언트 컴퓨터에 요청을 완료 하는 데 충분 한 리소스가 있는지 확인 합니다. 

## <a name="cannot-connect-to-host"></a>호스트에 연결할 수 없음
다음과 같은 오류가 표시 될 수 있습니다 `Cannot connect to any host, scheduling retry in 600000 milliseconds` .. 

### <a name="solution"></a>솔루션
이는 클라이언트 쪽에서 SNAT 고갈 될 수 있습니다. [아웃 바운드 연결에 대 한 SNAT의](../load-balancer/load-balancer-outbound-connections.md) 단계에 따라이 문제를 해결 하세요. Azure 부하 분산 장치는 기본적으로 4 분의 유휴 시간 제한이 있는 유휴 시간 제한 문제 일 수도 있습니다. [부하 분산 장치 유휴 시간 제한](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)에서 설명서를 참조 하세요. Tcp 사용-드라이버 설정에서 활성 상태를 유지 하 고 ( [아래](#enable-keep-alive-for-java-driver)참조) `keepAlive` 운영 체제의 간격을 4 분 미만으로 설정 합니다.

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
사용 된 총 요청 단위 수가 keyspace 또는 테이블에서 프로 비전 된 요청 단위 보다 많은 경우 따라서 요청이 제한 됩니다.
### <a name="solution"></a>솔루션
Azure Portal에서 keyspace 또는 테이블에 할당 된 처리량의 크기를 조정 하는 것이 좋습니다 (Cassandra API 크기 조정 작업은 [여기](manage-scale-cassandra.md) 참조). 또는 다시 시도 정책을 구현할 수 있습니다. Java의 경우 [v3 드라이버](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) 및 [v4 .x 드라이버](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)에 대 한 다시 시도 샘플을 참조 하세요. 또한 [Java 용 Azure Cosmos Cassandra Extensions를](https://github.com/Azure/azure-cosmos-cassandra-extensions)참조 하세요.

### <a name="overloadedexception-even-with-sufficient-throughput"></a>충분 한 처리량이 있는 경우에도 OverloadedException 
요청 볼륨에 대해 프로 비전 되는 충분 한 처리량과/또는 사용 된 요청 단위 비용에도 불구 하 고 시스템은 조정 요청으로 표시 됩니다. 예기치 않은 rate 제한의 두 가지 가능한 원인은 다음과 같습니다.
- **스키마 수준 작업:** Cassandra API은 스키마 수준 작업에 대 한 시스템 처리량 예산 (CREATE TABLE, ALTER TABLE, DROP TABLE)을 구현 합니다. 이 예산은 프로덕션 시스템의 스키마 작업에 충분 해야 합니다. 그러나 스키마 수준 작업이 많은 경우에는이 제한을 초과할 수 있습니다. 이 예산이 사용자 제어 되지 않으므로 실행 중인 스키마 작업의 수를 줄이는 것을 고려해 야 합니다. 이 작업을 수행 해도 문제가 해결 되지 않거나 작업에 적합 하지 않은 경우 [Azure 지원 요청을 만듭니다](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **데이터 오차:** 처리량이 Cassandra API에서 프로 비전 되는 경우 실제 파티션 간에 균등 하 게 분할 되 고 각 실제 파티션에는 상한이 있습니다. 특정 파티션에서 삽입 하거나 쿼리 하는 데이터 양이 많은 경우 해당 테이블에 대해 많은 양의 전체 처리량 (요청 단위)을 프로 비전 하는 동안에도 속도가 제한 될 수 있습니다. 데이터 모델을 검토 하 고 핫 파티션을 일으킬 수 있는 과도 한 불균형이 없는지 확인 합니다. 

## <a name="intermittent-connectivity-errors-java"></a>일시적인 연결 오류 (Java) 
연결이 예기치 않게 삭제 되거나 제한 시간이 초과 되었습니다.

### <a name="solution"></a>솔루션 
Java 용 Apache Cassandra 드라이버는 및의 두 가지 기본 다시 연결 정책을 제공 합니다. `ExponentialReconnectionPolicy` `ConstantReconnectionPolicy` 기본값은 `ExponentialReconnectionPolicy`입니다. 그러나 Azure Cosmos DB Cassandra API의 경우 `ConstantReconnectionPolicy` 2 초의 지연 시간을 사용 하는 것이 좋습니다. Java 2.x 드라이버에 대 한 [드라이버 설명서](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  를 참조 하 고 java 3.x 지침 [에 대해서는](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) 아래의 [Java 드라이버에 대 한 reconnectionpolicy 구성](#configuring-reconnectionpolicy-for-java-driver) 예를 참조 하세요.

## <a name="error-with-load-balancing-policy"></a>부하 분산 정책에 오류 발생

Java Datastax 드라이버의 v3. x에서 부하 분산 정책을 구현한 경우 다음과 같은 코드가 있습니다.

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

의 값이 `withLocalDc()` 연락처 지점 데이터 센터와 일치 하지 않으면 매우 간헐적 오류가 발생할 수 `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` 있습니다. 

### <a name="solution"></a>솔루션 
[CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) 구현 (작업을 수행 하려면 datastax 부 버전을 업그레이드 해야 할 수 있음):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>많은 테이블에서 개수가 실패 합니다.
`select count(*) from table`많은 수의 행에 대해 실행 되는 경우 또는 이와 유사한 서버는 시간 초과 됩니다.

### <a name="solution"></a>솔루션 
로컬 CQLSH 클라이언트를 사용 하는 경우 또는 설정을 변경할 수 `--connect-timeout` 있습니다 `--request-timeout` (자세한 내용은 [여기](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)참조). 이로 인해 충분 하지 않으며 카운트가 계속 해 서 발생 하는 경우 Azure Portal의 메트릭 탭으로 이동 하 여 메트릭을 선택한 `document count` 다음 데이터베이스 또는 컬렉션에 대 한 필터를 추가 하 여 Azure Cosmos DB 백 엔드 원격 분석에서 레코드 수를 가져올 수 있습니다 (Azure Cosmos DB의 아날로그 테이블). 그런 다음 레코드 수를 계산할 특정 시점에 대해 결과 그래프를 마우스로 가리킬 수 있습니다.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="메트릭 보기":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Java 드라이버용 ReconnectionPolicy 구성

### <a name="version-3x"></a>버전 3.x

Java 드라이버 버전 3.x의 경우 클러스터 개체를 만들 때 다시 연결 정책을 구성 합니다.

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>버전 4.x

Java 드라이버 버전 4.x의 경우 파일의 설정을 재정의 하 여 다시 연결 정책을 구성 합니다 `reference.conf` .

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Java 드라이버에 연결 유지 사용

### <a name="version-3x"></a>버전 3.x

Java 드라이버의 버전 2.x의 경우 클러스터 개체를 만들 때 keep-alive를 설정 하 고 [운영 체제에서 keep-alive를 사용 하도록](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)설정 해야 합니다.

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>버전 4.x

Java 드라이버 버전 4.x의 경우의 설정을 재정의 하 여 keep-alive를 설정 `reference.conf` 하 고 [운영 체제에서 keep-alive가 사용 하도록](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)설정 되어 있는지 확인 합니다.

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB Cassandra API에서 [지원 되는 기능](cassandra-support.md) 에 대해 알아봅니다.
- [네이티브 Apache Cassandra에서 Azure Cosmos DB로 마이그레이션하](cassandra-migrate-cosmos-db-databricks.md) 는 방법에 대해 알아봅니다 Cassandra API
