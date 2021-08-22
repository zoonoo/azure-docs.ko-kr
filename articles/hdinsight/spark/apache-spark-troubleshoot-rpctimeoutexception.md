---
title: Apache Spark Thrift에 대한 RpcTimeoutException - Azure HDInsight
description: Apache Spark Thrift 서버를 사용하여 큰 데이터 세트를 처리할 때 502 오류가 표시됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 353301b55031e1dde181cac61f47edd68cc33131
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282190"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Spark Thrift 서버에 대한 RpcTimeoutException

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

다음 예제와 같이 `org.apache.spark.rpc.RpcTimeoutException` 예외와 `Futures timed out` 메시지로 인해 Spark 애플리케이션이 실패합니다.

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

다음 예제와 같이 `sparkthriftdriver.log`에 `OutOfMemoryError` 및 `overhead limit exceeded` 오류도 표시될 수 있습니다.

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>원인

이 오류는 데이터를 처리하는 동안 메모리 리소스가 부족하기 때문에 발생합니다. Java 가비지 수집 프로세스가 시작되면 이로 인해 Spark 애플리케이션이 응답을 중지할 수 있습니다. 쿼리가 시간 초과되기 시작하고 처리를 중지합니다. `Futures timed out` 오류는 클러스터가 심각한 스트레스 상태임을 나타냅니다.

## <a name="resolution"></a>해결 방법

작업자 노드를 추가하거나 기존 클러스터 노드의 메모리 용량을 늘려 클러스터 크기를 늘립니다. 데이터 파이프라인을 조정하여 한 번에 처리되는 데이터 양을 줄일 수도 있습니다.

`spark.network.timeout`은 모든 네트워크 연결의 시간 제한을 제어합니다. 네트워크 시간 제한을 늘리면 일부 중요한 작업이 완료될 때까지 추가 시간이 확보될 수 있지만 이 경우 문제가 완전히 해결되지 않습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]