---
title: Azure HDInsight의 Apache Spark thrift 서버에 대 한 RpcTimeoutException
description: Apache Spark thrift 서버를 사용 하 여 대량 데이터 집합을 처리할 때 502 오류가 표시 됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 220465105733b715c1990fd73904b9dc095637d4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088609"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Spark thrift 서버에 대 한 RpcTimeoutException

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

다음 예제와 같이 Spark `org.apache.spark.rpc.RpcTimeoutException` 응용 프로그램은 예외와 `Futures timed out`메시지를 사용 하 여 실패 합니다.

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`및 `overhead limit exceeded` 오류는 `sparkthriftdriver.log` 다음 예제와 같이에 나타날 수도 있습니다.

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>원인

이러한 오류는 데이터를 처리 하는 동안 메모리 리소스가 부족 하기 때문에 발생 합니다. Java 가비지 수집 프로세스가 시작 되 면 Spark 응용 프로그램이 중단 될 수 있습니다. 쿼리가 시간 초과 되 고 처리를 중지 하기 시작 합니다. 이 `Futures timed out` 오류는 클러스터의 심각한 스트레스 상태를 나타냅니다.

## <a name="resolution"></a>해결 방법

작업자 노드를 추가 하거나 기존 클러스터 노드의 메모리 용량을 늘려 클러스터 크기를 늘립니다. 데이터 파이프라인을 조정 하 여 한 번에 처리 되는 데이터의 양을 줄일 수도 있습니다.

는 `spark.network.timeout` 모든 네트워크 연결에 대 한 제한 시간을 제어 합니다. 네트워크 시간 제한을 늘릴 경우 일부 중요 한 작업이 완료 될 때까지 시간이 더 걸릴 수 있지만이 경우 문제가 완전히 해결 되지 않습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
