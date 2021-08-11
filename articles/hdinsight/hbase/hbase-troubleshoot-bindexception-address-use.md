---
title: BindException - Azure HDInsight에서 이미 사용 중인 주소
description: BindException - Azure HDInsight에서 이미 사용 중인 주소
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: c0a7bc7629b5c2a2e458ba94d62e341f578fdd25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946395"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>시나리오: BindException - Azure HDInsight에서 이미 사용 중인 주소

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache HBase Region Server에서 다시 시작 작업을 완료하지 못했습니다. 지역 서버 시작에 실패한 작업자 노드의 `/var/log/hbase` 디렉터리에 있는 `region-server.log`에서 다음과 유사한 오류 메시지가 표시될 수 있습니다.

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>원인

과도한 워크로드 작업 중에 Apache HBase Region Server를 다시 시작합니다. 다음은 사용자가 Apache Ambari UI에서 HBase 지역 서버의 다시 시작 작업을 시작할 때 백그라운드에서 수행되는 작업입니다.

1. Ambari 에이전트가 영역 서버에 중지 요청을 보냅니다.

1. Ambari 에이전트에서 영역 서버가 정상적으로 종료될 때까지 30초간 대기합니다.

1. 애플리케이션이 영역 서버에 계속 연결될 경우 서버는 즉시 종료되지 않습니다. 종료되기 전에 30초 제한 시간에 만료됩니다.

1. 30초 후 Ambari 에이전트는 영역 서버에 강제 종료(`kill -9`) 명령을 전송합니다.

1. 이와 같이 갑작스럽게 종료되어 지역 서버 프로세스가 종료되더라도 프로세스와 연결된 포트가 해제되지 않을 수 있으며, 결국 `AddressBindException`으로 이어집니다.

## <a name="resolution"></a>해결 방법

HBase 지역 서버의 부하를 줄인 후 다시 시작합니다. 또한 먼저 모든 테이블을 플러시하는 것이 좋습니다. 테이블을 플러시하는 방법에 대한 참조는 [HDInsight HBase: 테이블을 플러시하여 Apache HBase 클러스터 다시 시작 시간을 개선하는 방법](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)을 참조하세요.

또는 다음 명령을 사용하여 작업자 노드의 지역 서버를 수동으로 다시 시작합니다.

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.