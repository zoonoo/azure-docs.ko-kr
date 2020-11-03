---
title: Apache 사육 아웃에 연결 Apache Hive-Azure HDInsight
description: Azure HDInsight의 Apache 사육 사 문제로 인해 Apache Hive 보기에 액세스할 수 없음
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 1ac237edeea777225b7cf99946d359c921331b18
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288912"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache 사육 사에 대 한 연결을 설정 하지 못한 Apache Hive

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Hive 보기에 액세스할 수 없으며의 로그에 `/var/log/hive` 다음과 유사한 오류가 표시 됩니다.

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>원인

Hive에서에 대 한 연결을 설정 하지 못할 수 있습니다. 그러면 Hive 보기가 시작 되지 않습니다.

## <a name="resolution"></a>해결 방법

1. 사육 사 서비스가 정상 상태 인지 확인 합니다.

1. 사육 사 서비스에 Hive Server2에 대 한 ZNode 항목이 있는지 확인 합니다. 값이 없거나 잘못 되었습니다.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 연결을 다시 설정 하려면 HiveServer2를 다시 시작 하 고 다시 부팅 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]