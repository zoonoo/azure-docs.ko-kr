---
title: Azure HDInsight에서 요청에 응답 하지 않는 Apache HBase REST
description: Azure HDInsight에서 요청에 응답 하지 않는 Apache HBase REST 문제를 해결 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887175"
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

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
