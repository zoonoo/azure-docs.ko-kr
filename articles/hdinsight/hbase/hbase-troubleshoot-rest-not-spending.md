---
title: Azure HDInsight에서 요청에 응답 하지 않는 Apache HBase REST
description: Azure HDInsight에서 요청에 응답 하지 않는 Apache HBase REST 문제를 해결 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286971"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>시나리오: Apache HBase REST가 Azure HDInsight에서 요청에 응답 하지 않음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache HBase REST 서비스는 Azure HDInsight의 요청에 응답 하지 않습니다.

## <a name="cause"></a>원인

이는 Apache HBase REST 서비스가 소켓을 누출 하는 것일 수 있습니다 .이는 서비스를 오랜 시간 동안 실행 하는 경우 특히 일반적입니다 (예: months). 클라이언트 SDK에서 다음과 유사한 오류 메시지가 표시 될 수 있습니다.

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>해결 방법

호스트에 Ssh 후 아래 명령을 사용 하 여 HBase REST를 다시 시작 합니다. 스크립트 작업을 사용 하 여 모든 작업자 노드에서이 서비스를 다시 시작할 수도 있습니다.

```bash
sudo service hdinsight-hbrest restart
```

이 명령은 동일한 호스트에서 HBase 지역 서버를 중지 합니다. Ambari를 통해 수동으로 HBase 지역 서버를 시작 하거나 Ambari 자동 다시 시작 기능을 사용 하 여 자동으로 HBase 지역 서버를 자동으로 복구할 수 있습니다.

문제가 계속 되 면 모든 작업자 노드에서 5 분 마다 실행 되는 CRON 작업으로 다음 완화 스크립트를 설치할 수 있습니다. 이 완화 스크립트는 rest 서비스를 ping 하 고 REST 서비스가 응답 하지 않는 경우 다시 시작 합니다.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]