---
title: BindException - Azure HDInsight에서 이미 사용 중인 주소
description: BindException - Azure HDInsight에서 이미 사용 중인 주소
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887345"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>시나리오: BindException - Azure HDInsight에서 이미 사용 중인 주소

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 HBase 지역 서버에서 다시 시작 작업이 완료되지 않습니다. 지역 `region-server.log` 서버 `/var/log/hbase` 시작이 실패하는 작업자 노드의 in directory에서 다음과 유사한 오류 메시지가 표시될 수 있습니다.

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>원인

워크로드가 많은 작업 중에 아파치 HBase 지역 서버를 다시 시작합니다. 다음은 사용자가 아파치 암바리 UI에서 HBase 지역 서버의 다시 시작 작업을 시작할 때 일어나는 일입니다.

1. Ambari 에이전트가 영역 서버에 중지 요청을 보냅니다.

1. Ambari 에이전트는 지역 서버가 정상적으로 종료될 때까지 30초 동안 기다립니다.

1. 애플리케이션이 영역 서버에 계속 연결될 경우 서버는 즉시 종료되지 않습니다. 종료되기 전에 30초 제한 시간에 만료됩니다.

1. 30초 후 Ambari 에이전트는 영역 서버에 강제 종료(`kill -9`) 명령을 전송합니다.

1. 이 갑작스러운 종료로 인해 지역 서버 프로세스가 종료되지만 프로세스와 연결된 포트가 해제되지 `AddressBindException`않아 결국 에 이르게 됩니다.

## <a name="resolution"></a>해결 방법

다시 시작하기 전에 HBase 지역 서버의 부하를 줄입니다. 또한 먼저 모든 테이블을 플러시하는 것이 좋습니다. 테이블을 플러시하는 방법에 대한 참조는 [HDInsight HBase: 테이블을 플러시하여 Apache HBase 클러스터 다시 시작 시간을 개선하는 방법](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)을 참조하세요.

또는 다음 명령을 사용하여 작업자 노드에서 지역 서버를 수동으로 다시 시작해 보십시오.

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
