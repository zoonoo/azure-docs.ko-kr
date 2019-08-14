---
title: Azure HDInsight에서 Apache HBase Master 시작 실패
description: Azure HDInsight에서 Apache HBase Master (HMaster) 시작 실패
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935407"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Azure HDInsight에서 Apache HBase Master (HMaster) 시작 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="scenario-atomic-renaming-failure"></a>시나리오: 원자성 이름 바꾸기 실패

### <a name="issue"></a>문제점

시작 프로세스 중에 식별 된 예기치 않은 파일입니다.

### <a name="cause"></a>원인

시작 프로세스 중에 HMaster는 데이터를 스크래치 (.tmp) 폴더에서 데이터 폴더로 이동 하는 등의 여러 초기화 단계를 수행 합니다. 또한 HMaster는 WALs (미리 쓰기 로그) 폴더를 확인 하 여 비활성 지역 서버가 있는지 확인 합니다. 이러한 모든 경우에는 이러한 폴더에 대 `list` 한 기본 명령을 수행 합니다. 언제 든 지 이러한 폴더에 예기치 않은 파일이 있는 경우 예외를 throw 하 고 시작 하지 않습니다.

### <a name="resolution"></a>해결 방법

이러한 상황에서 호출 스택을 확인 하 여 문제를 일으킬 수 있는 폴더 (예: WALs folder 또는 .tmp 폴더)를 확인 합니다. 그런 다음 클라우드 탐색기 또는 hdfs 명령을 통해 문제 파일을 찾습니다. 문제 파일은 일반적으로 `*-renamePending.json` 파일 (WASB 드라이버의 원자성 이름 바꾸기 작업을 구현 하는 데 사용 되는 저널 파일)입니다. 이 구현의 버그로 인해 이러한 파일은 프로세스 충돌이 발생할 경우에 발생할 수 있습니다. 클라우드 탐색기를 통해이 파일을 강제로 삭제 합니다. 또한이 위치에 $ 특성 임시 파일이 있을 수 있습니다. 이 파일은 클라우드 탐색기를 통해 볼 수 없으며 hdfs `ls` 명령만을 통해서만 볼 수 있습니다. Hdfs 명령을 `hdfs dfs -rm //\$\$\$.\$\$\$` 사용 하 여이 파일을 삭제할 수 있습니다.

문제 파일이 제거 되 면 HMaster가 즉시 시작 됩니다.

---

## <a name="scenario-no-server-address-listed"></a>시나리오: 나열 된 서버 주소 없음

### <a name="issue"></a>문제점

HMaster 로그는 "hbase: meta for region: meta에 나열 된 서버 주소 없음"과 유사한 오류 메시지를 표시 합니다.

### <a name="cause"></a>원인

HBase를 다시 시작한 후 HMaster를 초기화할 수 없습니다.

### <a name="resolution"></a>해결 방법

1. HBase 셸에서 다음 명령을 실행 합니다 (해당 되는 경우 실제 값 변경).

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. hbase: 네임스페이스 테이블을 검사하는 동안 동일한 오류가 보고될 수 있으므로 hbase: 네임스페이스의 항목을 삭제합니다.

1. Ambari UI에서 활성 HMaster를 다시 시작하여 HBase를 실행 상태로 만듭니다.

1. HBase 셸에서 다음 명령을 실행하여 모든 오프라인 테이블을 가져옵니다.

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>시나리오: java. IOException: Timedout

### <a name="issue"></a>문제점

HMaster는와 같은 `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`예외를 제외 하 고 시간 초과 됩니다.

### <a name="cause"></a>원인

제한 시간은 HMaster의 알려진 오류입니다. 일반 클러스터 시작 작업이 오래 걸릴 수 있습니다. 네임 스페이스 테이블이 아직 할당 되지 않은 경우 HMaster가 종료 됩니다. 많은 양의 플러시된 데이터가 존재 하 고 시간 제한이 5 분 이면 충분 하지 않은 시작 작업이 발생 합니다.

### <a name="resolution"></a>해결 방법

1. Ambari UI에 액세스 하 고, HBase-> Configs로 이동 `hbase-site.xml` 하 고, 사용자 지정에서 다음 설정을 추가 합니다.

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 필요한 서비스(주로 HMaster 및 가능한 경우 다른 HBase 서비스)를 다시 시작합니다.

---

## <a name="scenario-frequent-regionserver-restarts"></a>시나리오: 자주 수행 되는 지역 서버 다시 시작

### <a name="issue"></a>문제점

노드가 주기적으로 재부팅 됩니다. 지역 서버 로그에서 다음과 유사한 항목이 표시 될 수 있습니다.

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>원인

긴 지역 서버 JVM GC 일시 중지 일시 중지 하면 지역 서버는 응답 하지 않고, zk 세션 시간 제한 40s 내에서 HMaster에 하트 비트가 전송 되지 않습니다. HMaster는 지역 서버는 중단 된 것으로 판단 되 면 지역 서버를 중단 하 고 다시 시작 합니다.

### <a name="resolution"></a>해결 방법

Hbase 사이트 설정 뿐만 아니라도로를 사육 아웃으로 설정 `zookeeper.session.timeout` 하는 것이 아니라 사육 아웃 `maxSessionTimeout` 사이트 세션 제한 시간을 변경 합니다. cfg 설정을 변경 해야 합니다.

1. Ambari UI에 액세스 하 고, **HBase-> Configs-> 설정**으로 이동 하 고, 시간 제한 섹션에서 사육 아웃 세션 제한 시간 값을 변경 합니다.

1. Ambari UI에 액세스 하 고, **Configs-> 사용자 지정** 동물원으로 > 이동 하 고, 다음 설정을 추가/변경 합니다. 값이 hbase `zookeeper.session.timeout`와 동일한 지 확인 합니다.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 필요한 서비스를 다시 시작 합니다.

---

## <a name="scenario-log-splitting-failure"></a>시나리오: 로그 분할 실패

### <a name="issue"></a>문제점

HMasters를 HBase 클러스터에서 가져오지 못했습니다.

### <a name="cause"></a>원인

보조 저장소 계정에 대 한 HDFS 및 HBase 설정이 잘못 구성 되었습니다.

### <a name="resolution"></a>해결 방법

Ambari에서 hbase wasb://@.blob.core.windows.net/hbase 를 설정 하 고 서비스를 다시 시작 합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
