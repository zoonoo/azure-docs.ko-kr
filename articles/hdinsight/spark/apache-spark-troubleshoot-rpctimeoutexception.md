---
title: Apache Spark thrift에 대 한 RpcTimeoutException-Azure HDInsight
description: Apache Spark thrift 서버를 사용 하 여 대량 데이터 집합을 처리할 때 502 오류가 표시 됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b4b5268bef71bd6b23df6049fd3ac8af973d4385
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287752"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Spark thrift server에 대 한 RpcTimeoutException

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

`org.apache.spark.rpc.RpcTimeoutException` `Futures timed out` 다음 예제와 같이 Spark 응용 프로그램은 예외와 메시지를 사용 하 여 실패 합니다.

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` 및 `overhead limit exceeded` 오류는 `sparkthriftdriver.log` 다음 예제와 같이에 나타날 수도 있습니다.

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>원인

이러한 오류는 데이터를 처리 하는 동안 메모리 리소스가 부족 하기 때문에 발생 합니다. Java 가비지 수집 프로세스가 시작 되 면 Spark 응용 프로그램이 응답을 중지 하 게 될 수 있습니다. 쿼리가 시간 초과 되 고 처리를 중지 하기 시작 합니다. 이 `Futures timed out` 오류는 클러스터의 심각한 스트레스 상태를 나타냅니다.

## <a name="resolution"></a>해결 방법

작업자 노드를 추가 하거나 기존 클러스터 노드의 메모리 용량을 늘려 클러스터 크기를 늘립니다. 데이터 파이프라인을 조정 하 여 한 번에 처리 되는 데이터의 양을 줄일 수도 있습니다.

는 `spark.network.timeout` 모든 네트워크 연결에 대 한 제한 시간을 제어 합니다. 네트워크 시간 제한을 늘릴 경우 일부 중요 한 작업이 완료 될 때까지 시간이 더 걸릴 수 있지만이 경우 문제가 완전히 해결 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]