---
title: Azure HDInsight 클러스터의 안전 모드에서 로컬 HDFS가 중단 됨
description: Azure HDInsight에서 Apache 클러스터의 안전 모드로 로컬 Apache HDFS 정지 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 0f4a7702c4f85c162b0485e06cabc99b963ff210
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289157"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터의 안전 모드에서 로컬 HDFS가 중단 됨

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

로컬 Apache HDFS(Hadoop 분산 파일 시스템)가 HDInsight 클러스터에서 안전 모드 상태로 중단되었습니다. 다음과 유사한 오류 메시지가 표시 됩니다.

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>원인

HDInsight 클러스터는 아래 노드가 매우 적게 축소 되었거나, 노드 수가 HDFS 복제 요소에 가깝습니다.

## <a name="resolution"></a>해결 방법

1. 다음 명령을 사용 하 여 HDInsight 클러스터에서 HDFS 상태를 보고 합니다.

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. 다음 명령을 사용 하 여 HDInsight 클러스터에서 HDFS의 무결성을 확인 합니다.

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. 복제된 블록에서 누락되었거나 손상된 부분이 없거나 해당 블록을 무시할 수 있다고 판단되면 다음 명령을 실행하여 이름 노드를 안전 모드에서 제거합니다.

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]