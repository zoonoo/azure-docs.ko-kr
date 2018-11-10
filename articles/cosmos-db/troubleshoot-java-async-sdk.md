---
title: Azure Cosmos DB Java 비동기 SDK 진단 및 문제 해결 | Microsoft Docs
description: 클라이언트 쪽 로깅 기타 타사 도구 등의 기능을 사용하여 Azure Cosmos DB 문제를 파악, 진단 및 해결합니다.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshoot
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ef1d2d0751bf1b1a7ee88fbf37e44e6316dee8f8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249875"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Azure Cosmos DB SQL API 계정으로 Java 비동기 SDK를 사용하는 경우 문제 해결
이 문서에서는 Azure Cosmos DB SQL API 계정으로 [Java 비동기 ADK](sql-api-sdk-async-java.md)를 사용할 때 일반적인 문제, 해결, 진단 단계 및 도구를 설명합니다.
Java 비동기 SDK는 Azure Cosmos DB SQL API에 액세스하기 위한 클라이언트 쪽 논리적 표현을 제공합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

이 목록을 사용하여 시작합니다.
    * 이 문서의 [일반적인 문제 및 해결 방법] 섹션을 살펴봅니다.
    * SDK는 [github의 오픈 소스](https://github.com/Azure/azure-cosmosdb-java)이며 적극적으로 모니터링하는 [문제 섹션](https://github.com/Azure/azure-cosmosdb-java/issues)이 있습니다. 해결 방법이 이미 제출된 유사한 문제가 있는지 확인합니다.
    * [성능 팁](performance-tips-async-java.md)을 검토하고 제안된 사례를 따릅니다.
    * 이 문서의 나머지 부분을 따르고, 솔루션을 찾지 못한 경우 [GitHub 문제](https://github.com/Azure/azure-cosmosdb-java/issues)를 제출합니다.

## <a name="common-issues-workarounds"></a>일반적인 문제 및 해결 방법

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>네트워크 문제, Netty 읽기 시간 제한 오류, 낮은 처리량, 높은 대기 시간

#### <a name="general-suggestions"></a>일반 제안
* 앱이 Cosmos DB 계정과 동일한 지역에서 실행되고 있는지 확인합니다. 
* 앱이 실행되는 호스트에서 CPU 사용량을 확인합니다. CPU 사용량이 90% 이상인 경우 더 높은 구성의 호스트에서 앱을 실행하거나 더 많은 머신에서 부하를 분산하는 것이 좋습니다.

#### <a name="connection-throttling"></a>연결 제한
연결 제한은 [호스트 머신의 연결 제한] 또는 [Azure SNAT(PAT) 포트 고갈] 중 하나로 인해 발생할 수 있습니다.

##### <a name="connection-limit-on-host"></a>호스트 머신의 연결 제한
일부 Linux 시스템(예: 'Red Hat')에는 열려 있는 파일의 총 수에 상한이 있습니다. Linux의 소켓은 파일로 구현되므로 이 숫자는 총 연결 수도 제한합니다.
다음 명령 실행:

```bash
ulimit -a
```
오픈 파일 수("nofile")는 충분히 커야 합니다(최소한 연결 풀 크기의 두 배). [성능 팁](performance-tips-async-java.md)에서 더 자세히 알아봅니다.

##### <a name="snat"></a>Azure SNAT(PAT) 포트 고갈

앱이 Azure VM에서 배포되는 경우 기본적으로 [Azure SNAT 포트](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)는 VM 외부의 모든 엔드포인트에 대한 연결을 설정하는 데 사용됩니다. VM에서 Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)으로 제한됩니다.

Azure SNAT 포트는 Azure VM에 사설 IP 주소 및 VM에서 공용 IP 주소에 연결을 설정하려고 하는 프로세스가 있는 경우에만 사용됩니다. 따라서 Azure SNAT 제한을 피하는 두 가지 해결 방법이 있습니다.
    * [VNET 서비스 엔드포인트 사용](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)에 설명된 대로 Azure VM VNET의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 서비스 엔드포인트가 활성화된 경우 공용 IP에서 cosmos DB로 요청이 더 이상 전송되지 않고 대신 VNET 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용 중인 경우 서비스 엔드포인트를 활성화할 때 [VNET ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl)을 사용하여 방화벽에 서브넷을 추가합니다.
    * Azure VM에 공용 IP를 할당합니다.

#### <a name="http-proxy"></a>Http 프록시

HttpProxy를 사용하는 경우 HttpProxy에서 SDK `ConnectionPolicy`에서 구성된 연결 수를 지원할 수 있는지 확인합니다.
그렇지 않으면 연결 문제가 발생할 수 있습니다.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>잘못된 코딩 패턴: Netty IO 스레드 차단

SDK는 Azure Cosmos DB 서비스와 통신하기 위해 [Netty](https://netty.io/) IO 라이브러리를 사용합니다. 비동기 API가 있으며 netty의 비차단 IO API를 사용합니다. SDK의 IO 작업은 IO netty 스레드에서 수행됩니다. IO netty 스레드의 수는 앱 머신의 CPU 코어 수와 동일하도록 구성됩니다. netty IO 스레드는 비차단 netty IO 작업에만 사용해야 합니다. SDK는 앱의 코드에 대한 netty IO 스레드 중 하나에서 API 호출 결과를 반환합니다. 앱이 netty 스레드의 결과를 받은 후 netty 스레드에서 장시간 작업을 수행하는 경우 SDK에 해당 내부 IO 작업을 수행하는 데 충분한 수의 IO 스레드가 없을 수 있습니다. 이러한 앱 코딩으로 인해 낮은 처리량, 높은 대기 시간 및 `io.netty.handler.timeout.ReadTimeoutException` 실패가 발생할 수 있습니다. 해결 방법은 작업에 시간이 걸리는 것을 알고 있는 경우 스레드를 전환하는 것입니다.

   예를 들어 다음 코드 조각은 netty 스레드에서 몇 밀리초 이상이 걸리는 장시간 작업을 수행하는 경우 결과적으로 IO 작업을 처리할 netty IO 스레드가 없는 상태가 될 수 있고 그 결과로 ReadTimeoutException이 발생함을 보여줍니다.
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   해결 방법은 시간이 걸리는 작업을 수행하는 스레드를 변경하는 것입니다. 앱에 대한 스케줄러의 싱글톤 인스턴스를 정의합니다.
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   시간이 걸리는 작업을 수행해야 할 때마다(예: 계산이 많은 작업, IO 차단) `.observeOn(customScheduler)` API를 사용하여 스레드를 `customScheduler`에서 제공하는 작업자로 전환합니다.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
`observeOn(customScheduler)`을 사용하여 netty IO 스레드를 해제하고 customScheduler에서 제공한 사용자 고유의 사용자 지정 스레드로 전환합니다. 이 수정으로 문제가 해결되고 더 이상 `io.netty.handler.timeout.ReadTimeoutException` 실패가 발생하지 않습니다.

### <a name="connection-pool-exhausted-issue"></a>연결 풀 고갈 문제

`PoolExhaustedException`은 클라이언트 쪽 오류입니다. 이 오류가 자주 발생하는 경우 이는 앱 워크로드가 SDK 연결 풀에서 제공할 수 있는 것보다 크다는 것을 나타냅니다. 연결 풀 크기를 늘리거나 여러 앱에서 부하를 분산하는 것이 도움이 될 수 있습니다.

### <a name="request-rate-too-large"></a>너무 많은 요청 빈도
이 오류는 프로비전된 처리량을 사용했고 나중에 다시 시도해야 함을 나타내는 서버 쪽 오류입니다. 이 오류가 자주 발생하는 경우 컬렉션 처리량을 늘리는 것이 좋습니다.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Azure Cosmos DB 에뮬레이터 연결 오류

Cosmos DB 에뮬레이터 HTTPS 인증서는 자체 서명입니다. SDK를 에뮬레이터와 함께 사용하려면 에뮬레이터 인증서를 Java TrustStore로 가져와야 합니다. [여기](local-emulator-export-ssl-certificates.md)에 설명한 대로


## <a name="enable-client-sice-logging"></a>클라이언트 SDK 로깅 사용

비동기 Java SDK는 log4j 및 logback과 같은 인기 있는 로깅 프레임워크로 로깅을 지원하는 로깅 외관으로 SLF4j를 사용합니다.

예를 들어 로깅 프레임워크로 log4j를 사용하려는 경우 다음 라이브러리를 Java 클래스 경로에 추가합니다.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

log4j 구성도 추가합니다.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

자세한 내용은 [sfl4j 로깅 설명서](https://www.slf4j.org/manual.html)를 검토하세요.

## <a name="netstats"></a>OS 네트워크 통계
netstat 명령을 실행하여 `Established` 상태, `CLOSE_WAIT` 상태 등에 있는 연결 수를 확인합니다.

Linux에서 다음 명령을 실행할 수 있습니다.
```bash
netstat -nap
```
Cosmos DB 엔드포인트에 대한 연결로만 결과를 필터링합니다.

`Established` 상태에서 Cosmos DB 엔드포인트에 대한 연결 수는 구성된 연결 풀 크기보다 크지 않아야 합니다.

`CLOSE_WAIT` 상태에서 Cosmos DB 엔드포인트에 대한 많은 연결이 있는 경우(예: 1,000개 초과하는 연결) 연결이 설정되고 신속하게 손상되어 잠재적인 문제가 발생할 수 있음을 나타냅니다. 자세한 내용은 [일반적인 문제 및 해결 방법] 섹션을 검토하세요.

 <!--Anchors-->
[일반적인 문제 및 해결 방법]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[호스트 머신의 연결 제한]: #connection-limit-on-host
[Azure SNAT(PAT) 포트 고갈]: #snat


