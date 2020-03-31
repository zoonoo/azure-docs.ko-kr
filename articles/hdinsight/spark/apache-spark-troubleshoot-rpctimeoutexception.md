---
title: RpcTimeout아파치 스파크 중고품에 대한 예외 - Azure HDInsight
description: 아파치 스파크 중고품 서버를 사용하여 대용량 데이터 세트를 처리할 때 502 개의 오류가 표시됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894277"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>시나리오: RpcTimeoutAzure HDInsight에서 아파치 스파크 중고품 서버에 대 한 예외

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

Spark 응용 프로그램은 `org.apache.spark.rpc.RpcTimeoutException` 다음과 같은 `Futures timed out`예에서와 같이 예외 및 메시지로 실패합니다.

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`오류는 `overhead limit exceeded` 다음 예제와 `sparkthriftdriver.log` 같이 나타날 수도 있습니다.

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>원인

이러한 오류는 데이터 처리 중에 메모리 리소스가 부족하여 발생합니다. Java 가비지 수집 프로세스가 시작되면 Spark 응용 프로그램이 교수형에 처할 수 있습니다. 쿼리가 시간 시간 중지되고 처리가 중지됩니다. 이 `Futures timed out` 오류는 심한 응력 하에 있는 클러스터를 나타냅니다.

## <a name="resolution"></a>해결 방법

작업자 노드를 더 추가하거나 기존 클러스터 노드의 메모리 용량을 늘려 클러스터 크기를 늘립니다. 한 번에 처리되는 데이터의 양을 줄이기 위해 데이터 파이프라인을 조정할 수도 있습니다.

모든 `spark.network.timeout` 네트워크 연결에 대한 시간 시간을 제어합니다. 네트워크 시간 시간을 늘리면 일부 중요한 작업이 완료되는 데 더 많은 시간이 필요할 수 있지만 이로 인해 문제가 완전히 해결되지는 않습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
