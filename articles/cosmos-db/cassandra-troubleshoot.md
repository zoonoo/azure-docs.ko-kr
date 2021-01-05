---
title: Azure Cosmos DB의 일반적인 오류 문제 해결 Cassandra API
description: 이 문서에서는 Azure Cosmos DB에서 발생 하는 일반적인 문제를 해결 하는 방법에 대해 설명 Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: c969e4fac3ae30088cfe47a7b0edff22c578cb8b
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802374"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB의 일반적인 문제 해결 Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB의 Cassandra API은 인기 있는 오픈 소스 Apache Cassandra 데이터베이스에 대 한 [유선 프로토콜 지원을](cassandra-support.md) 제공 하 고 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)으로 구동 되는 호환성 계층입니다. 완벽 하 게 관리 되는 클라우드 기본 서비스인 Azure Cosmos DB는 Cassandra API에 대 한 [가용성, 처리량 및 일관성을 보장](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) 합니다. 이러한 보장은 Apache Cassandra의 레거시 구현에서 가능 하지 않습니다. 또한 Cassandra API 유지 관리 플랫폼 작업과 제로 가동 중지 시간 패치를 용이 하 게 합니다. 따라서 대부분의 백엔드 작업은 Apache Cassandra와 다르므로 일반적인 오류를 방지 하기 위해 특정 설정 및 방법을 권장 합니다. 

이 문서에서는 Azure Cosmos DB Cassandra API를 사용 하는 응용 프로그램에 대 한 일반적인 오류 및 해결 방법을 설명 합니다.

## <a name="common-errors-and-solutions"></a>일반 오류 및 해결 방법

| 오류               |  Description             | 해결 방법  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | 사용 된 총 요청 단위 수가 keyspace 또는 테이블에서 프로 비전 된 요청 단위 보다 많은 경우 따라서 요청이 제한 됩니다. | Azure Portal에서 keyspace 또는 테이블에 할당 된 처리량의 크기를 조정 하는 것이 좋습니다 (Cassandra API 크기 조정 작업은 [여기](manage-scale-cassandra.md) 참조). 또는 다시 시도 정책을 구현할 수 있습니다. Java의 경우 [v3 드라이버](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) 및 [v4 .x 드라이버](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)에 대 한 다시 시도 샘플을 참조 하세요. 또한 [Java 용 Azure Cosmos Cassandra Extensions를](https://github.com/Azure/azure-cosmos-cassandra-extensions) 참조 하세요. |
| OverloadedException (Java) 처리량이 충분 합니다. | 요청 볼륨에 대해 프로 비전 되는 충분 한 처리량과/또는 사용 된 요청 단위 비용에도 불구 하 고 시스템에 제한 요청이 표시 됩니다.  | Cassandra API은 스키마 수준 작업에 대 한 시스템 처리량 예산 (CREATE TABLE, ALTER TABLE, DROP TABLE)을 구현 합니다. 이 예산은 프로덕션 시스템의 스키마 작업에 충분 해야 합니다. 그러나 스키마 수준 작업이 많은 경우에는이 제한을 초과할 수 있습니다. 이 예산이 사용자 제어 되지 않으므로 실행 중인 스키마 작업의 수를 줄이는 것을 고려해 야 합니다. 이 작업을 수행 해도 문제가 해결 되지 않거나 작업에 적합 하지 않은 경우 [Azure 지원 요청을 만듭니다](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | 연결에 성공한 후 유휴 시간이 지나면 응용 프로그램에서 연결할 수 없습니다.| 이 오류는 Azure LoadBalancers 조정기의 유휴 시간 제한 (4 분) 때문일 수 있습니다. 드라이버에서 연결 유지 설정을 설정 하 고 (아래 참조) 운영 체제에서 연결 유지 설정을 늘리거나 [Azure Load Balancer에서 유휴 시간 제한을 조정](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)합니다. |
| 기타 일시적인 연결 오류 (Java) | 연결이 예기치 않게 삭제 되거나 시간 초과 됩니다. | Java 용 Apache Cassandra 드라이버는 및의 두 가지 기본 다시 연결 정책을 제공 합니다. `ExponentialReconnectionPolicy` `ConstantReconnectionPolicy` 기본값은 `ExponentialReconnectionPolicy`입니다. 그러나 Azure Cosmos DB Cassandra API의 경우 `ConstantReconnectionPolicy` 2 초의 지연 시간을 사용 하는 것이 좋습니다. Java [2.x에 대](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) 한 [드라이버 설명서](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/) 및 java 3.x 지침 (아래 예제 참조)을 참조 하세요.|

오류가 위에 나열 되어 있지 않으며 [Cassandra API에서 지원](cassandra-support.md)되는 작업을 실행할 때 오류가 발생 하는 경우 ( *네이티브 Apache Cassandra를 사용 하* 는 동안 오류가 나타나지 않음) [Azure 지원 요청을 만듭니다](../azure-portal/supportability/how-to-create-azure-support-request.md) .

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

