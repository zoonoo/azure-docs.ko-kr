---
title: Apache ZooKeeper 서버가 Azure HDInsight에서 쿼럼을 형성하지 못함
description: Apache ZooKeeper 서버가 Azure HDInsight에서 쿼럼을 형성하지 못함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 05/20/2020
ms.openlocfilehash: dc93121d7565b95b9bd604160028659f3a741b0c
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860497"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper 서버가 Azure HDInsight에서 쿼럼을 형성하지 못함

이 문서에서는 Azure HDInsight 클러스터의 Zookeeper 관련 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

* Resource Manager가 모두 대기 모드로 전환됩니다.
* Namenode가 모두 대기 모드입니다.
* Zookeeper 연결 실패로 인해 Spark, Hive 및 Yarn 작업 또는 Hive 쿼리가 실패합니다.
* 보안 Spark 또는 보안 대화형 Hive 클러스터에서 LLAP 디먼이 시작되지 않습니다.

## <a name="sample-log"></a>샘플 로그

다음과 유사한 오류 메시지가 표시될 경우:

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>관련 문제

* Yarn, NameNode 및 Livy와 같은 고가용성 서비스는 여러 가지 이유로 중단될 수 있습니다.
* 로그에서 Zookeeper 연결과 관련된 로그를 확인합니다.
* 문제가 반복적으로 발생하는지 확인합니다(일회성 사례에는 이러한 솔루션을 사용하지 마세요).
* Zookeeper 연결 문제로 인해 일시적으로 작업이 실패할 수 있습니다.

## <a name="common-causes-for-zookeeper-failure"></a>Zookeeper 실패의 일반적인 원인

* Zookeeper 서버에서 높은 CPU 사용량
  * Ambari UI에서 Zookeeper 서버의 CPU 사용량이 거의 100%에 이르면 해당 시간 동안 열려 있는 Zookeeper 세션이 만료되어 시간이 초과될 수 있습니다.
* Zookeeper 클라이언트가 시간 초과를 자주 보고함
  * Resource Manager, Namenode 및 기타 로그에서 클라이언트 연결 시간 제한이 자주 표시됨
  * 이로 인해 쿼럼에서 손실, 잦은 장애 조치(failover) 및 기타 문제가 발생할 수 있습니다.

## <a name="check-for-zookeeper-status"></a>Zookeeper 상태 확인

* /etc/hosts 파일 또는 Ambari UI에서 Zookeeper 서버 찾기
* 다음 명령을 실행합니다.
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181`(또는 2182)  
  * 포트 2181은 Apache Zookeeper 인스턴스입니다.
  * 포트 2182는 HDInsight Zookeeper에서 사용합니다(기본적으로 HA가 아닌 서비스에 HA를 제공하기 위해).
  * 명령이 출력을 표시하지 않는 경우에는 Zookeeper 서버가 실행되고 있지 않음을 의미합니다.
  * 서버가 실행 중인 경우 클라이언트 연결의 정적 및 기타 통계가 결과에 포함됩니다.

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>매시간 CPU 로드가 최대치까지 상승

* Zookeeper 서버에 로그인하고 /etc/crontab을 확인합니다.
* 현재 시간별 작업이 실행 중인 경우 다른 Zookeeper 서버에서 시작 시간을 임의로 지정합니다.
  
## <a name="purging-old-snapshots"></a>오래된 스냅샷 제거

* Zookeeper는 오래된 스냅샷을 자동으로 제거하도록 구성되어 있습니다.
* 기본적으로 최근 30개의 스냅샷이 보존됩니다.
* 보존되는 스냅샷 수는 `autopurge.snapRetainCount` 구성 키에 의해 제어됩니다. 이 속성은 다음 파일에서 찾을 수 있습니다.
  * Hadoop Zookeeper의 경우 `/etc/zookeeper/conf/zoo.cfg`
  * HDInsight Zookeeper의 경우 `/etc/hdinsight-zookeeper/conf/zoo.cfg`
* `autopurge.snapRetainCount` 값을 3으로 설정하고 Zookeeper 서버를 다시 시작합니다.
  * Hadoop Zookeeper 구성을 업데이트하고 Ambari를 통해 서비스를 다시 시작합니다.
  * 수동으로 HDInsight Zookeeper를 중지하고 다시 시작합니다.
    * `sudo lsof -i :2182`는 종료할 프로세스 ID를 제공함
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* 수동으로 스냅샷을 제거하지 마세요. 수동으로 스냅샷을 삭제하면 데이터가 손실될 수 있습니다.

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>Zookeeper 서버 로그의 CancelledKeyException에는 스냅샷 정리가 필요하지 않음

* 이 예외는 일반적으로 클라이언트가 더 이상 활성 상태가 아니므로 서버에서 메시지를 보낼 수 없음을 의미합니다.
* 또한 이 예외는 Zookeeper 클라이언트가 중간에 세션을 종료함을 나타냅니다.
* 이 문서에 설명된 다른 증상을 확인합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.
- [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.
- 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.