---
title: Azure HDInsight의 YARN 문제 해결
description: Apache Hadoop YARN 및 Azure HDInsight 작업에 대한 일반적인 질문에 답합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272202"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Azure HDInsight를 사용하여 Apache Hadoop YARN 문제를 해결합니다.

Apache Ambari에서 Apache Hadoop YARN 페이로드를 사용할 때의 주요 문제 및 해결 방법을 알아봅니다.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>클러스터에서 새 YARN 큐를 만들려면 어떻게 하나요?

### <a name="resolution-steps"></a>해결 단계:

Ambari에서 다음 단계를 사용하여 새 YARN 큐를 만들고 모든 큐에 용량이 균형 있게 할당되도록 조정합니다.

이 예제에서는 기존 큐 두 개(**default** 및 **thriftsvr**) 모두 새 큐(spark)를 50% 용량으로 하여 50% 용량에서 25% 용량으로 변경됩니다.

| 큐 | 용량 | 최대 용량 |
| --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. **Ambari 뷰** 아이콘을 선택한 다음, 그리드 패턴을 선택합니다. 다음으로, **YARN 큐 관리자**를 선택합니다.

    ![아파치 암바리 대시 보드 YARN 큐 관리자](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. **default** 큐를 선택합니다.

    ![아파치 암바리 YARN 선택 기본 대기열](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. **default** 큐의 경우 **용량**을 50%에서 25%로 변경합니다. **thriftsvr** 큐의 경우 **용량**을 25%로 변경합니다.

    ![default 및 thriftsvr 큐에 대해 용량을 25%로 변경](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. 새 큐를 만들려면 **큐 추가**를 선택합니다.

    ![아파치 암바리 YARN 대시 보드 추가 큐](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. 새 큐 이름을 지정합니다.

    ![아파치 암바리 YARN 대시보드 이름 큐](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. **용량** 값을 50%로 두고 **작업** 단추를 선택합니다.

    ![아파치 암바리 원사 선택 액션](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. **큐 저장 및 새로 고침**을 선택합니다.

    ![큐 저장 및 새로 고침 선택](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

이러한 변경 내용은 YARN Scheduler UI에 즉시 표시됩니다.

### <a name="additional-reading"></a>추가 참조 자료

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>클러스터에서 YARN 로그를 다운로드하려면 어떻게 하나요?

### <a name="resolution-steps"></a>해결 단계:

1. SSH(Secure Shell) 클라이언트를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [더 보기](#additional-reading-2)를 참조하세요.

1. 현재 실행 중인 YARN 애플리케이션의 모든 애플리케이션 ID를 나열하려면 다음 명령을 사용합니다.

    ```apache
    yarn top
    ```

    ID가 **APPLICATIONID** 열에 나열됩니다. **APPLICATIONID** 열에서 로그를 다운로드할 수 있습니다.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. 모든 애플리케이션 마스터에 대한 YARN 컨테이너 로그를 다운로드하려면 다음 명령을 사용합니다.

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    amlogs.txt라는 로그 파일이 만들어집니다.

1. 최신 애플리케이션 마스터에 대한 YARN 컨테이너 로그만 다운로드하려면 다음 명령을 사용합니다.

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    latestamlogs.txt라는 로그 파일이 만들어집니다.

1. 처음 두 개 애플리케이션 마스터에 대한 YARN 컨테이너 로그를 다운로드하려면 다음 명령을 사용합니다.

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    first2amlogs.txt라는 로그 파일이 만들어집니다.

1. 모든 YARN 컨테이너 로그를 다운로드하려면 다음 명령을 사용합니다.

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    logs.txt라는 로그 파일이 만들어집니다.

1. 특정 컨테이너에 대한 YARN 컨테이너 로그를 다운로드하려면 다음 명령을 사용합니다.

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    containerlogs.txt라는 로그 파일이 만들어집니다.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>추가 판독값

- [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [아파치 하두롭 YARN 개념 및 응용 프로그램](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

- 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

- 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
