---
title: 클러스터 크기 조정 - Azure HDInsight
description: Azure HDInsight에서 워크 로드와 일치 하도록 Apache Hadoop 클러스터 탄력적으로 크기 조정
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7a278196b2f4edc35ebc8c4dc77116363e9f7901
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811822"
---
# <a name="scale-hdinsight-clusters"></a>HDInsight 클러스터 크기 조정

HDInsight는 클러스터의 작업자 노드 수를 증가 및 감소하는 옵션을 제공하여 유연성을 보장합니다. 이 탄력성를 사용 하면 몇 시간 또는 주말에 클러스터를 축소 하 고 최대 비즈니스 요구 시간 동안 확장할 수 있습니다.

정기적 일괄 처리를 수행 하는 경우에는 해당 작업 전에 HDInsight 클러스터를 몇 분 정도 확장 하 여 클러스터에 적절 한 메모리와 CPU 성능을 확보할 수 있습니다.  나중에, 처리가 완료된 후 사용량이 다시 줄어들면, HDInsight 클러스터를 더 적은 수의 작업자 노드로 축소할 수 있습니다.

아래에 설명 된 방법 중 하나를 사용 하 여 클러스터를 수동으로 확장 하거나 [자동 크기 조정](hdinsight-autoscale-clusters.md) 옵션을 사용 하 여 CPU, 메모리 및 기타 메트릭에 대 한 응답으로 시스템이 자동으로 확장 및 축소 되도록 할 수 있습니다.

> [!NOTE]  
> HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다.

## <a name="utilities-to-scale-clusters"></a>클러스터 크기 조정 유틸리티

Microsoft는 클러스터 크기를 조정 하는 다음과 같은 유틸리티를 제공 합니다.

|유틸리티나 | Description|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name >-TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name >-TargetInstanceCount \<NewSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --리소스 그룹 \<리소스 그룹 >--name \<Cluster name >--target-instance-count \<NewSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|azure hdinsight 클러스터 크기 \<조정 clusterName \<> 대상 인스턴스 수 > |
|[Azure Portal](https://portal.azure.com)|HDInsight 클러스터 창을 열고 왼쪽 메뉴에서 **클러스터 크기** 를 선택한 다음 클러스터 크기 창에서 작업자 노드 수를 입력 하 고 저장을 선택 합니다.|  

![클러스터 크기 조정](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

이러한 방법 중 하나를 사용하여 몇 분 만에 HDInsight 클러스터를 확장 또는 축소할 수 있습니다.

> [!IMPORTANT]  
> * 이전 CLI CLI는 더 이상 사용 되지 않으며 클래식 배포 모델에만 사용 해야 합니다. 다른 모든 배포의 경우 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 사용 합니다.  
> * PowerShell AzureRM 모듈은 더 이상 사용 되지 않습니다.  가능 하면 [Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) 을 사용 하세요.

## <a name="impact-of-scaling-operations"></a>크기 조정 작업의 영향

실행 중인 HDInsight 클러스터에 노드를 **추가** 하는 경우 (강화) 보류 중이거나 실행 중인 작업은 영향을 받지 않습니다. 크기 조정 프로세스가 실행되는 동안 새 작업을 안전하게 제출할 수 있습니다. 어떤 이유로 든 크기 조정 작업이 실패 하면 클러스터의 기능 상태를 유지 하기 위해 오류가 처리 됩니다.

노드를 **제거** 하는 경우 (규모 축소) 크기 조정 작업이 완료 되 면 보류 중이거나 실행 중인 작업이 모두 실패 합니다. 이 오류는 크기 조정 프로세스 중 일부 서비스가 다시 시작 되기 때문에 발생 합니다. 수동 크기 조정 작업을 수행 하는 동안 클러스터가 안전 모드에서 중단 될 수도 있습니다.

데이터 노드 수 변경에 따른 영향은 다음과 같이 HDInsight에서 지원하는 각 클러스터 유형에 따라 다릅니다.

* Apache Hadoop

    모든 보류 중인 또는 실행 중인 작업에 영향을 주지 않고 실행되는 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 크기 조정 작업의 오류는 정상적으로 처리되므로 클러스터는 항상 기능 상태로 남아 있습니다.

    데이터 노드 수를 줄여 Hadoop 클러스터를 축소하면 클러스터의 서비스 중 일부가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.

* Apache HBase

    HBase 클러스터가 실행 중인 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 클러스터의 헤드 노드에 로그인한 다음 명령 프롬프트 창에서 다음 명령을 실행하여 자동으로 지역 서버의 균형을 맞출 수도 있습니다.

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase 셸을 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache HBase 예제 시작](hbase/apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

* Apache Storm

    실행 중인 동안 Storm 클러스터에 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 하지만 크기 조정 작업이 성공적으로 완료되면 토폴로지 균형을 다시 조정해야 합니다.

    다음 두 가지 방법으로 사용하여 균형을 조정할 수 있습니다.

  * Storm 웹 UI
  * 명령줄 인터페이스(CLI) 도구

    자세한 내용은 [Apache Storm 설명서](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.

    Storm 웹 UI는 HDInsight 클러스터에서 제공됩니다.

    ![HDInsight Storm 규모 균형 재조정](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    다음은 Storm 토폴로지 균형을 다시 조정하는 CLI 명령의 예제입니다.

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>클러스터를 안전 하 게 확장 하는 방법

### <a name="scale-down-a-cluster-with-running-jobs"></a>실행 중인 작업을 사용 하 여 클러스터 규모 축소

규모 축소 작업 중에 실행 중인 작업이 실패 하는 것을 방지 하기 위해 다음 세 가지 작업을 시도할 수 있습니다.

1. 클러스터를 축소 하기 전에 작업이 완료 될 때까지 기다립니다.
1. 수동으로 작업을 종료 합니다.
1. 크기 조정 작업이 완료 된 후 작업을 다시 전송 합니다.

보류 중인 작업 및 실행 중인 작업 목록을 보려면 다음 단계를 수행 하 여 YARN **리소스 관리자 UI**를 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 클러스터를 선택 합니다.  지침에 대해서는 [클러스터 나열 및 표시](./hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요. 클러스터가 새 포털 페이지에서 열립니다.
2. 주 보기에서 **클러스터 대시보드** > **Ambari 홈**으로 이동 합니다. 클러스터 자격 증명을 입력 합니다.
3. Ambari UI의 왼쪽 메뉴에 있는 서비스 목록에서 **YARN** 를 선택 합니다.  
4. YARN 페이지에서 **빠른 링크** 를 선택 하 고 활성 헤드 노드 위로 마우스를 이동한 다음 **ResourceManager UI**를 선택 합니다.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`에서 ResourceManager UI에 직접 액세스할 수 있습니다.

현재 상태와 함께 작업 목록이 표시됩니다. 스크린샷에는 현재 실행 중인 작업이 하나 있습니다.

![ResourceManager UI 애플리케이션](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

실행 중인 해당 애플리케이션을 수동으로 종료하려면 SSH 셸에서 다음 명령을 실행합니다.

```bash
yarn application -kill <application_id>
```

예를 들어:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>안전 모드에서 중단

클러스터를 축소 하는 경우 HDInsight는 Apache Ambari 관리 인터페이스를 사용 하 여 먼저 추가 작업자 노드를 서비스 해제 합니다 .이 노드는 해당 HDFS 블록을 다른 온라인 작업자 노드에 복제 합니다. 그 후에 HDInsight는 클러스터를 안전 하 게 확장 합니다. HDFS는 크기 조정 작업을 수행 하는 동안 안전 모드로 전환 되며, 확장이 완료 되 면 발생 합니다. 그러나 일부 경우에는 복제 중인 파일 블록 때문에 크기 조정 작업을 수행 하는 동안 HDFS가 안전 모드에서 중단 됩니다.

기본적으로 HDFS는 사용할 수 있는 각 `dfs.replication` 파일 블록의 복사본 수를 제어 하는 3의 설정으로 구성 됩니다. 파일 블록의 각 복사본은 클러스터의 다른 노드에 저장 됩니다.

HDFS에서 예상 된 블록 복사본 수를 사용할 수 없는 것을 감지 하는 경우 HDFS는 safe 모드로 전환 되 고 Ambari에서 경고를 생성 합니다. HDFS가 크기 조정 작업을 위해 안전 모드로 전환 되었으나 필요한 노드 수가 복제에 대해 검색 되지 않았기 때문에 안전 모드를 종료할 수 없는 경우 클러스터가 안전 모드에서 중단 될 수 있습니다.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>안전 모드가 켜지는 오류 예제

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

클러스터 크기가 조정될 시간이 가까워질 때 `/var/log/hadoop/hdfs/` 폴더에서 이름 노드 로그를 검토하여 안전 모드가 시작되었는지 확인할 수 있습니다. 로그 파일의 이름은 `Hadoop-hdfs-namenode-hn0-clustername.*`입니다.

이전 오류의 근본 원인은 Hive가 쿼리를 실행하는 동안 HDFS에서 임시 파일을 사용하는 것입니다. HDFS가 안전 모드를 시작하면 Hive는 HDFS에 쓸 수 없으므로 쿼리를 실행할 수 없습니다. HDFS의 임시 파일은 개별 작업자 노드 VM에 탑재된 로컬 드라이브에 있으며, 다른 작업자 노드 간에 복제되어 최소 3개의 복제본이 유지됩니다.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>HDInsight가 안전 모드에서 중단 되는 것을 방지 하는 방법

다음과 같은 여러 가지 방법으로 HDInsight가 안전 모드를 유지하지 않도록 할 수 있습니다.

* HDInsight를 축소하기 전에 모든 Hive 작업을 중지합니다. 또는 실행 중인 Hive 작업과 충돌하지 않도록 축소 프로세스를 예약합니다.
* 축소하기 전에 HDFS에서 Hive의 스크래치 `tmp` 디렉터리 파일을 수동으로 정리합니다.
* HDInsight를 최소 3개의 작업자 노드로만 축소합니다. 작업자 노드 1개만큼 줄이지는 마세요.
* 필요한 경우 안전 모드를 종료하는 명령을 실행합니다.

다음 섹션에서는 이러한 옵션을 설명합니다.

#### <a name="stop-all-hive-jobs"></a>모든 Hive 작업 중지

하나의 작업자 노드를 축소하기 전에 모든 Hive 작업을 중지합니다. 워크로드가 예약되면 Hive 작업이 완료된 후에 축소를 실행합니다.

크기를 조정 하기 전에 Hive 작업을 중지 하면 tmp 폴더 (있는 경우)의 스크래치 파일 수를 최소화 하는 데 도움이 됩니다.

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive의 스크래치 파일을 수동으로 정리

Hive가 임시 파일을 남겨 두면 안전 모드를 피하기 위해 축소 전에 해당 파일을 수동으로 정리할 수 있습니다.

1. `hive.exec.scratchdir` 구성 속성을 살펴보면 Hive 임시 파일에 사용 되는 위치를 확인 합니다. 이 매개 변수는 다음 `/etc/hive/conf/hive-site.xml`에 설정 됩니다.

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive 서비스를 중지하고 모든 쿼리 및 작업이 완료되었는지 확인합니다.
2. 위에서 찾은 스크래치 디렉터리의 내용을 나열 `hdfs://mycluster/tmp/hive/` 하 여 파일이 포함 되어 있는지 확인 합니다.

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    다음은 파일이 있는 경우의 샘플 출력입니다.

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. 이러한 파일에 대해 Hive가 완료된 것이 확인되면 제거해도 됩니다. Yarn ResourceManager UI 페이지에서 확인하여 Hive에 쿼리가 없는지 알아봅니다.

    HDFS에서 파일을 제거하는 명령줄 예제는 다음과 같습니다.

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight를 세 개 이상의 작업자 노드로 확장

3 개 이상의 작업자 노드를 축소 하는 경우 클러스터가 안전 모드에서 중단 되 고 이전 단계가 작동 하지 않는 경우 세 개 이상의 작업자 노드를 유지 하 여 클러스터가 안전 모드로 전환 되는 것을 방지할 수 있습니다.

3 개의 작업자 노드를 유지 하는 것은 작업자 노드를 하나만으로 축소 하는 것 보다 비용이 많이 들기 때문에 클러스터가 안전 모드에서 중단 되지 않습니다.

#### <a name="run-the-command-to-leave-safe-mode"></a>안전 모드를 종료하는 명령 실행

마지막 옵션은 안전 모드 유지 명령을 실행 하는 것입니다. HDFS가 안전 모드를 시작 하는 이유가 복제 중인 Hive 파일 때문 이기 때문에 다음 명령을 실행 하 여 안전 모드를 유지할 수 있습니다.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase 클러스터 축소

영역 서버는 크기 조정 작업을 완료 한 후 몇 분 내에 자동으로 분산 됩니다. 지역 서버를 수동으로 조정 하려면 다음 단계를 완료 합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. HBase 셸을 시작합니다.

    ```bash
    hbase shell
    ```

3. 다음 명령을 사용하여 지역 서버의 부하를 수동으로 분산합니다.

    ```bash
    balancer
    ```

## <a name="next-steps"></a>다음 단계

* [자동으로 Azure HDInsight 클러스터 크기 조정](hdinsight-autoscale-clusters.md)
* [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)
