---
title: Azure HDInsight에서 요청에 응답하지 않는 Apache HBase REST
description: Azure HDInsight에서 요청에 응답하지 않는 Apache HBase REST의 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: b42a37d752f245eb66f3952b56ee4e36ae954874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936946"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 요청에 응답하지 않는 Apache HBase REST

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache HBase REST 서비스가 Azure HDInsight의 요청에 응답하지 않습니다.

## <a name="cause"></a>원인

Apache HBase REST 서비스가 소켓을 누출하는 것이 원일일 수 있습니다. 이는 서비스를 오랜 시간 실행하는 경우, 특히 일반적인 현상입니다(예: 몇 달). 클라이언트 SDK에서 다음과 유사한 오류 메시지가 표시 될 수 있습니다.

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>해결 방법

호스트에 SSH 연결 후 아래 명령을 사용하여 HBase REST를 다시 시작합니다. 스크립트 작업을 사용하여 모든 작업자 노드에서 이 서비스를 다시 시작할 수도 있습니다.

```bash
sudo service hdinsight-hbrest restart
```

이 명령은 동일한 호스트에서 HBase 지역 서버를 중지합니다. Ambari를 통해 수동으로 HBase 지역 서버를 시작하거나 Ambari 자동 재시작 기능을 사용하여 자동으로 HBase 지역 서버를 복구할 수 있습니다.

문제가 계속되면 모든 작업자 노드에서 5분마다 실행되는 CRON 작업으로 다음 완화 스크립트를 설치할 수 있습니다. 이 완화 스크립트는 REST 서비스를 ping하고 REST 서비스가 응답하지 않는 경우 이를 다시 시작합니다.

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