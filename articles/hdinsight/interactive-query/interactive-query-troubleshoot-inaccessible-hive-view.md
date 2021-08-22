---
title: Apache ZooKeeper에 Apache Hive 연결 -Azure HDInsight
description: Azure HDInsight의 Apache ZooKeeper 문제로 인해 Apache Hive 보기에 액세스할 수 없음
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: cc7ad8d32e4d15b8e0671d162b1796619b4879c7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290722"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>시나리오: Apache Hive가 Azure HDInsight에서 Apache ZooKeeper에 대한 연결을 설정하지 못함

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Hive 보기에 액세스할 수 없으며 `/var/log/hive`의 로그에 다음과 유사한 오류가 표시됩니다.

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (<zookeepername1>.cloud.wbmi.com:2181,<zookeepername2>.cloud.wbmi.com:2181,<zookeepername3>.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>원인

Hive가 Zookeeper에 대한 연결을 설정하지 못하여 Hive View가 시작되지 않을 수 있습니다.

## <a name="resolution"></a>해결 방법

1. ZooKeeper 서비스가 정상 상태인지 확인합니다.

1. ZooKeeper 서비스에 Hive Server2에 대한 ZNode 항목이 있는지 확인합니다. 값이 없거나 잘못되었습니다.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server <zookeepername1>
    [zk: <zookeepername1>(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 연결을 다시 설정하려면 ZooKeeper 노드와 HiveServer2를 다시 부팅합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]