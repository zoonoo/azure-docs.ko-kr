---
title: Azure HDInsight에서 Apache HBase Master가 시작되지 않음
description: Azure HDInsight에서 Apache HMaster(HBase Master)가 시작되지 않음
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: c30077d0d8f359e93745b53755f9dae998073d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936898"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Azure HDInsight에서 Apache HMaster(HBase Master)가 시작되지 않음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 발생하는 문제의 문제 해결 단계와 가능한 해결 방법을 설명합니다.

## <a name="scenario-atomic-renaming-failure"></a>시나리오: 원자성 이름 바꾸기 실패

### <a name="issue"></a>문제

시작 프로세스 중에 예기치 않은 파일이 식별되었습니다.

### <a name="cause"></a>원인

시작 프로세스 중에 HMaster는 데이터를 스크래치(.tmp) 폴더에서 데이터 폴더로 이동하는 등의 다양한 초기화 단계를 수행합니다. 또한 HMaster는 WAL(미리 쓰기 로그) 폴더를 확인하여 응답하지 않는 지역 서버가 있는지 확인합니다.

HMaster는 WAL 폴더에서 기본 목록 명령을 수행합니다. 언제든지 이러한 폴더에 예기치 않은 파일이 있을 경우 예외가 발생하고 시작되지 않습니다.

### <a name="resolution"></a>해결 방법

호출 스택을 확인하고 문제를 일으킬 수 있는 폴더(예: WAL 폴더 또는 .tmp 폴더)를 확인합니다. 그런 다음 클라우드 탐색기 또는 HDFS 명령을 사용하여 문제가 있는 파일을 찾습니다. 일반적으로 `*-renamePending.json` 파일입니다. `*-renamePending.json` 파일은 WASB 드라이버에서 원자성 이름 바꾸기 작업을 구현하는 데 사용되는 저널 파일입니다. 이 구현의 버그로 인해 해당 파일은 프로세스 크래시가 발생한 후에도 남아 있을 수 있습니다. 클라우드 탐색기 또는 HDFS 명령을 사용하여 이 파일을 강제로 삭제합니다.

경우에 따라 이 위치에 `$$$.$$$` 같은 임시 파일이 있을 수도 있습니다. 이 파일을 보기 위해 HDFS `ls` 명령을 사용해야 합니다. 클라우드 탐색기에서는 이 파일을 볼 수 없습니다. 이 파일을 삭제하려면 HDFS 명령 `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`을 사용합니다.

이러한 명령을 실행한 후 HMaster는 즉시 시작됩니다.

---

## <a name="scenario-no-server-address-listed"></a>시나리오: 나열된 서버 주소 없음

### <a name="issue"></a>문제

`hbase: meta` 테이블이 온라인 상태가 아님을 나타내는 메시지가 표시될 수 있습니다. `hbck`를 실행하면 `hbase: meta table replicaId 0 is not found on any region.`가 보고될 수 있습니다. HMaster 로그에 `No server address listed in hbase: meta for region hbase: backup <region name>` 메시지가 표시될 수 있습니다.  

### <a name="cause"></a>원인

HBase를 다시 시작한 후 HMaster를 초기화할 수 없습니다.

### <a name="resolution"></a>해결 방법

1. HBase 셸에서 다음 명령을 입력합니다(해당되는 경우 실제 값 변경).

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. `hbase: namespace` 엔터티를 삭제합니다. 이 항목은 `hbase: namespace` 테이블이 검사될 때 보고되는 것과 같은 오류일 수 있습니다.

1. Ambari UI에서 활성 HMaster를 다시 시작하여 HBase를 실행 상태로 만듭니다.

1. HBase 셸에서 모든 오프라인 테이블을 가져오려면 다음 명령을 실행합니다.

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>시나리오: java.io.IOException: 시간 초과됨

### <a name="issue"></a>문제

`java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`와 같은 심각한 예외로 인해 HMaster 시간이 초과됩니다.

### <a name="cause"></a>원인

HMaster 서비스를 다시 시작할 때 플러시되지 않은 많은 테이블 및 영역이 있는 경우 이 문제가 발생할 수 있습니다. 시간 제한은 HMaster의 알려진 오류입니다. 일반 클러스터 시작 작업이 오래 걸릴 수 있습니다. 네임스페이스 테이블이 아직 할당되지 않은 경우 HMaster가 종료됩니다. 많은 양의 플러시되지 않은 데이터가 존재하고 시간 제한 5분이 충분하지 않으면 시작 작업 시간이 길어집니다.

### <a name="resolution"></a>해결 방법

1. Apache Ambari UI에서 **HBase** > **Configs** 로 이동합니다. 사용자 지정 `hbase-site.xml` 파일에서 다음 설정을 추가합니다.

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 필요한 서비스(HMaster 및 가능한 경우 다른 HBase 서비스)를 다시 시작합니다.

---

## <a name="scenario-frequent-region-server-restarts"></a>시나리오: 빈번한 지역 서버 다시 시작

### <a name="issue"></a>문제

노드가 주기적으로 다시 부팅됩니다. 지역 서버 로그에서 다음과 유사한 항목이 표시될 수 있습니다.

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>원인

길어진 `regionserver` JVM GC 일시 중지입니다. 일시 중지가 발생하면 `regionserver`가 응답하지 않으며 zk 세션 시간 제한 40초 내에 하트비트를 HMaster에 보낼 수 없습니다. HMaster는 `regionserver`가 중지된 것으로 판단하며 `regionserver`를 중단하고 다시 시작합니다.

### <a name="resolution"></a>해결 방법

Zookeeper 세션 시간 제한을 변경합니다. `hbase-site` 설정 `zookeeper.session.timeout`뿐만 아니라 Zookeeper `zoo.cfg` 설정 `maxSessionTimeout`도 변경해야 합니다.

1. Ambari UI에 액세스하고, **HBase -> Configs -> 설정** 으로 이동하고, 시간 제한 섹션에서 Zookeeper 세션 시간 제한의 값을 변경합니다.

1. Ambari UI에 액세스하고, **Zookeeper -> Configs -> 사용자 지정** `zoo.cfg`로 이동하고, 다음 설정을 추가/변경합니다. 값이 HBase `zookeeper.session.timeout`과 동일한지 확인합니다.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 필요한 서비스를 다시 시작합니다.

---

## <a name="scenario-log-splitting-failure"></a>시나리오: 로그 분할 실패

### <a name="issue"></a>문제

HMaster가 HBase 클러스터에 표시되지 않았습니다.

### <a name="cause"></a>원인

보조 스토리지 계정에 대해 HDFS 및 HBase 설정이 잘못 구성되었습니다.

### <a name="resolution"></a>해결 방법

hbase.rootdir: wasb://@.blob.core.windows.net/hbase를 설정하고 Ambari에서 서비스를 다시 시작합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.