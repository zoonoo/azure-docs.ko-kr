---
title: 클러스터 성능 모니터링 - Azure HDInsight
description: Azure HDInsight에서 Apache Hadoop 클러스터의 상태와 성능을 모니터링 하는 방법입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 78ff8adcc2b50f89daa37112b14d219233559dab
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86075573"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Azure HDInsight에서 클러스터 성능 모니터링

성능과 리소스 사용률을 최적으로 유지하려면 HDInsight 클러스터의 상태 및 성능을 모니터링해야 합니다. 모니터링도 클러스터 구성 오류 및 사용자 코드 문제를 검색하고 해결하는 데 도움이 될 수 있습니다.

다음 섹션에서는 클러스터의 부하 및 Apache Hadoop YARN 큐의 로드를 모니터링 및 최적화하고 스토리지 제한 문제를 탐지하는 방법을 설명합니다.

## <a name="monitor-cluster-load"></a>클러스터 부하 모니터링

Hadoop 클러스터는 클러스터의 부하가 모든 노드에 균등하게 분산될 때 최적의 성능을 제공할 수 있습니다. 이렇게 하면 처리 작업이 RAM, CPU 또는 개별 노드의 디스크 리소스에 제약되지 않고 실행됩니다.

클러스터의 노드 및 해당 로드를 자세히 확인 하려면 [Ambari 웹 UI](hdinsight-hadoop-manage-ambari.md)에 로그인 한 다음 **호스트** 탭을 선택 합니다. 호스트는 정규화 된 도메인 이름으로 나열 됩니다. 각 호스트의 운영 상태는 색이 지정된 상태 표시기로 표시됩니다.

| 색상 | 설명 |
| --- | --- |
| 빨강 | 호스트에서 적어도 하나 이상의 마스터 구성 요소가 중단되었습니다. 마우스를 가져다 대면 영향을 받는 구성 요소 목록을 나열하는 도구 설명이 표시됩니다. |
| Orange | 호스트에서 하나 이상의 보조 구성 요소가 다운 되었습니다. 마우스를 가져다 대면 영향을 받는 구성 요소 목록을 나열하는 도구 설명이 표시됩니다. |
| 노랑 | Ambari 서버가 호스트에서 3 분 넘게 하트 비트를 받지 못했습니다. |
| 녹색 | 정상적인 실행 상태입니다. |

또한 각 호스트의 코어 수와 RAM 양, 디스크 사용량 및 로드 평균을 보여주는 열이 표시됩니다.

![Apache Ambari 호스트 탭 개요](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

해당 호스트에서 실행되는 구성 요소와 메트릭을 자세히 살펴보려면 호스트 이름 중 하나를 선택합니다. 메트릭은 CPU 사용량, 로드, 디스크 사용량, 메모리 사용, 네트워크 사용량 및 프로세스 수에 대한 선택 가능한 타임라인으로 표시됩니다.

![Apache Ambari 호스트 세부 정보 개요](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

경고를 설정하고 메트릭을 보는 방법에 대한 자세한 내용은 [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="yarn-queue-configuration"></a>YARN 큐 구성

Hadoop은 분산 플랫폼에서 실행되는 다양한 서비스를 갖고 있습니다. YARN(Yet Another Resource Negotiator)은 모든 부하가 클러스터 전체에 균등하게 분산되도록 이러한 서비스를 조정하고 클러스터 리소스를 할당합니다.

YARN은 JobTracker가 하는 두 가지 작업인 리소스 관리 및 일정 예약/모니터링 작업을 두 개의 디먼, 즉 글로벌 Resource Manager와 애플리케이션별 ApplicationMaster(AM)에 나눠줍니다.

Resource Manager는 *순수 스케줄러*로써 모든 경합 애플리케이션 간에 가용 리소스를 중재하는 일만 합니다. Resource Manager는 항상 모든 리소스를 사용하도록 보장하여 SLA, 용량 보장 등의 다양한 상수에 맞게 최적화합니다. ApplicationMaster는 Resource Manager의 리소스를 협상하고, NodeManager와 함께 작동하여 컨테이너와 컨테이너의 리소스 사용을 실행하고 모니터링합니다.

여러 테 넌 트가 많은 클러스터를 공유 하는 경우 클러스터 리소스에 대 한 경쟁이 있습니다. CapacityScheduler는 요청을 큐에 추가하여 리소스 공유를 도와주는 플러그형 스케줄러입니다. 또한 CapacityScheduler는 다른 응용 프로그램의 큐에서 무료 리소스를 사용할 수 있도록 하기 위해 *계층 큐* 를 지원 하 여 조직의 하위 큐 간에 리소스가 공유 되도록 합니다.

YARN은 이러한 큐에 리소스를 할당할 수 있도록 허용하며 모든 가용 리소스의 할당 여부를 보여줍니다. 큐에 대한 정보를 보려면 Ambari 웹 UI에 로그인한 다음, 상단의 메뉴에서 **YARN 큐 관리자**를 선택합니다.

![Apache Ambari YARN 큐 관리자](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

YARN 큐 관리자 페이지의 왼쪽에는 큐 목록과 각 큐에 할당된 용량 백분율이 표시됩니다.

![YARN 큐 관리자 세부 정보 페이지](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

큐에 대한 세부 정보를 보려면 Ambari 대시보드의 왼쪽에 있는 목록에서 **YARN** 서비스를 선택합니다. 그런 다음, **빠른 연결** 드롭다운 메뉴에서 활성 노드 아래의 **Resource Manager UI**를 선택합니다.

![리소스 관리자 UI 메뉴 링크](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Resource Manager UI의 왼쪽 메뉴에서 **스케줄러**를 선택합니다. *애플리케이션 큐* 아래에 큐 목록이 보입니다. 여기서 각 큐에 사용되는 용량, 작업이 큐 간에 얼마나 잘 분산되었는지 여부, 리소스가 제한된 작업이 있는지 여부를 볼 수 있습니다.

![Apache HAdoop 리소스 관리자 UI 메뉴](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>스토리지 제한

스토리지 수준에서 클러스터의 성능 병목 상태가 발생할 수 있습니다. 이러한 유형의 병목 상태는 대부분의 경우 실행 중인 태스크가 저장소 서비스에서 처리할 수 있는 것 보다 많은 IO를 전송할 때 발생 하는 IO (입/출력) 작업 *차단* 때문에 발생 합니다. 작업이 차단되면 현재 IO를 처리한 후 처리될 때까지 대기하는 IO 요청 큐가 생성됩니다. 블록은 물리적 제한이 아니라 SLA (서비스 수준 계약)에 따라 저장소 서비스에 의해 제한 되는 *저장소 제한*때문입니다. 이 제한은 단일 클라이언트 또는 테넌트가 서비스를 독점하지 못하게 방지하는 역할을 합니다. SLA는 Azure Storage에 대 한 IOPS (초당 Io 수)를 제한 합니다. 자세한 내용은 [standard Storage 계정에 대 한 확장성 및 성능 목표](../storage/common/scalability-targets-standard-account.md)를 참조 하세요.

Azure Storage 사용 하는 경우 제한을 포함 하 여 저장소 관련 문제를 모니터링 하는 방법에 대 한 자세한 내용은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)을 참조 하세요.

클러스터의 백업 저장소를 Azure Data Lake Storage (ADLS) 하는 경우 대역폭 제한으로 인해 제한이 발생할 가능성이 높습니다. 이 경우 작업 로그에서 제한 오류를 확인하여 제한을 파악할 수 있습니다. ADLS에 대한 내용은 다음 문서에서 해당 서비스에 대한 제한 섹션을 참조하세요.

* [HDInsight의 Apache Hive 및 Azure Data Lake Storage에 대한 성능 조정 지침](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [HDInsight의 MapReduce 및 Azure Data Lake Storage에 대한 성능 조정 지침](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [HDInsight의 Apache Storm 및 Azure Data Lake Storage에 대한 성능 조정 지침](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>노드 성능 저하 문제 해결

경우에 따라 클러스터의 디스크 공간이 부족 하 여 느려질이 발생할 수 있습니다. 다음 단계를 사용 하 여 조사 합니다.

1. [Ssh 명령을](./hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 각 노드에 연결 합니다.

1. 다음 명령 중 하나를 실행 하 여 디스크 사용량을 확인 합니다.

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. 출력을 검토 하 고 `mnt` 폴더 또는 다른 폴더에 있는 용량이 많은 파일이 있는지 확인 합니다. 일반적으로 `usercache` 및 `appcache` (mnt/resource/hadoop/yarn/local/usercache/hive/appcache/) 폴더에는 많은 파일이 포함 되어 있습니다.

1. 파일이 너무 많으면 현재 작업이 파일 증가를 야기 하거나 이전 작업에 실패 한 경우이 문제가 발생할 수 있습니다. 이 동작이 현재 작업으로 인 한 것인지 여부를 확인 하려면 다음 명령을 실행 합니다.

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. 이 명령이 특정 작업을 나타내는 경우 다음과 같은 명령을 사용 하 여 작업을 종료 하도록 선택할 수 있습니다.

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    `application_id`응용 프로그램 ID로 대체 합니다. 특정 작업이 표시 되지 않으면 다음 단계로 이동 합니다.

1. 위의 명령이 완료 된 후 또는 특정 작업이 표시 되지 않으면 다음과 유사한 명령을 실행 하 여 식별 한 용량이 많은 파일을 삭제 합니다.

    ```bash
    rm -rf filecache usercache
    ```

디스크 공간 문제에 대 한 자세한 내용은 [디스크 공간 부족](./hadoop/hdinsight-troubleshoot-out-disk-space.md)을 참조 하십시오.

> [!NOTE]  
> 유지 하려는 대용량 파일이 있지만 디스크 공간 부족 문제에 참여 하는 경우 HDInsight 클러스터를 확장 하 고 서비스를 다시 시작 해야 합니다. 이 절차를 완료 하 고 몇 분 정도 기다린 후에는 저장소가 해제 되 고 노드의 일반적인 성능이 복원 됩니다.

## <a name="next-steps"></a>다음 단계

다음 연결을 방문하여 클러스터 문제 해결 및 모니터링에 대해 자세히 알아봅니다.

* [HDInsight 로그 분석](hdinsight-debug-jobs.md)
* [Apache Hadoop YARN 로그를 사용하여 앱 디버그](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Linux 기반 HDInsight에서 Apache Hadoop 서비스에 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
