---
title: 클러스터 크기 조정 - Azure HDInsight
description: Azure HDInsight의 워크로드에 맞게 아파치 하두롭 클러스터를 탄력적으로 확장
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 0fc067f0be4ac5d2b8fa7db9ad7999efe06625a0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804506"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터 확장

HDInsight는 클러스터의 작업자 노드 수를 확장하고 축소하는 옵션을 통해 탄력성을 제공합니다. 이 탄력성을 사용하면 몇 시간 후 또는 주말에 클러스터를 축소할 수 있습니다. 그리고 피크 비즈니스 요구 기간 동안 확장합니다.

클러스터에 충분한 리소스가 있도록 정기적인 일괄 처리 전에 클러스터를 확장합니다. 처리가 완료되고 사용량이 줄어들면 HDInsight 클러스터를 더 적은 수의 작업자 노드로 축소합니다.

아래에 설명된 방법 중 하나를 사용하여 클러스터를 수동으로 확장할 수 있습니다. 자동 크기 [조정](hdinsight-autoscale-clusters.md) 옵션을 사용하여 특정 메트릭에 대한 응답으로 자동으로 확장 및 축소할 수도 있습니다.

> [!NOTE]  
> HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다.

## <a name="utilities-to-scale-clusters"></a>클러스터 를 확장하는 유틸리티

Microsoft는 클러스터를 확장하는 다음 유틸리티를 제공합니다.

|유틸리티 | Description|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[`Set-AzHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) | [`az hdinsight resize`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure 클래식 CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure portal](https://portal.azure.com)|HDInsight 클러스터 창을 열고 왼쪽 메뉴에서 **클러스터 크기를** 선택한 다음 클러스터 크기 창에서 작업자 노드 수를 입력하고 저장을 선택합니다.|  

![Azure 포털 규모 클러스터 옵션](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

이러한 방법 중 하나를 사용하여 몇 분 만에 HDInsight 클러스터를 확장 또는 축소할 수 있습니다.

> [!IMPORTANT]  
> * Azure 클래식 CLI는 더 이상 사용되지 않으며 클래식 배포 모델에서만 사용해야 합니다. 다른 모든 배포의 경우 [Azure CLI를](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)사용합니다.
> * PowerShell AzureRM 모듈은 더 이상 사용되지 않습니다.  가능하면 [Az 모듈을](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) 사용하십시오.

## <a name="impact-of-scaling-operations"></a>확장 작업의 영향

실행 중인 HDInsight 클러스터(확장)에 노드를 **추가하면** 작업이 영향을 받지 않습니다. 크기 조정 프로세스가 실행되는 동안 새 작업을 안전하게 제출할 수 있습니다. 크기 조정 작업이 실패하면 클러스터가 기능 상태로 유지됩니다.

노드(축소)를 **제거하면** 크기 조정 작업이 완료되면 보류 중이거나 실행 중인 작업이 실패합니다. 이 오류는 크기 조정 프로세스 중에 일부 서비스가 다시 시작되기 때문입니다. 수동 크기 조정 작업 중에 클러스터가 안전 모드에 갇아닐 수 있습니다.

데이터 노드 수 변경에 따른 영향은 다음과 같이 HDInsight에서 지원하는 각 클러스터 유형에 따라 다릅니다.

* Apache Hadoop

    작업에 영향을 주지 않고 실행 중인 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 크기 조정 작업의 실패는 정상적으로 처리됩니다. 클러스터는 항상 기능 상태로 남아 있습니다.

    데이터 노드 수가 적어 Hadoop 클러스터가 축소되면 일부 서비스가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.

* Apache HBase

    실행 중인 동안 HBase 클러스터에 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 지역 서버의 균형을 수동으로 조정할 수 있습니다. 클러스터 헤드노드에 로그인하여 다음 명령을 실행합니다.

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase 셸을 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache HBase 예제 시작](hbase/apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

* Apache Storm

    Storm이 실행되는 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 그러나 크기 조정 작업을 성공적으로 완료한 후에는 토폴로지의 균형을 조정해야 합니다.

    다음 두 가지 방법으로 사용하여 균형을 조정할 수 있습니다.

  * Storm 웹 UI
  * 명령줄 인터페이스(CLI) 도구

    자세한 내용은 [아파치 스톰 문서를](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)참조하십시오.

    Storm 웹 UI는 HDInsight 클러스터에서 제공됩니다.

    ![HDInsight Storm 규모 균형 재조정](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    다음은 Storm 토폴로지 균형을 다시 조정하는 CLI 명령의 예제입니다.

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>클러스터를 안전하게 축소하는 방법

### <a name="scale-down-a-cluster-with-running-jobs"></a>실행 중인 작업으로 클러스터 축소

축소 작업 중에 실행 중인 작업이 실패하지 않도록 하려면 다음 세 가지를 시도해 볼 수 있습니다.

1. 클러스터를 축소하기 전에 작업이 완료될 때까지 기다립니다.
1. 작업을 수동으로 종료합니다.
1. 크기 조정 작업이 종료된 후 작업을 다시 제출합니다.

보류 중인 작업 및 실행 중인 작업 목록을 보려면 다음 단계다음에 YARN **리소스 관리자 UI를**사용할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com/)클러스터를 선택합니다.  지침에 대해서는 [클러스터 나열 및 표시](./hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요. 클러스터가 새 포털 페이지에서 열립니다.
2. 메인 보기에서 클러스터 **대시보드** > **Ambari 홈으로**이동합니다. 클러스터 자격 증명을 입력합니다.
3. Ambari UI에서 왼쪽 메뉴의 서비스 목록에서 **YARN을** 선택합니다.  
4. YARN 페이지에서 빠른 **링크를** 선택하고 활성 헤드 노드 위로 마우스를 가져간 다음 **리소스 관리자 UI를**선택합니다.

    ![아파치 암바리 빠른 링크 리소스 관리자 UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`을 사용 하 여 리소스 관리자 UI에 직접 액세스할 수 있습니다.

현재 상태와 함께 작업 목록이 표시됩니다. 스크린샷에는 현재 실행 중인 작업이 하나 있습니다.

![리소스 관리자 UI 응용 프로그램](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

실행 중인 해당 애플리케이션을 수동으로 종료하려면 SSH 셸에서 다음 명령을 실행합니다.

```bash
yarn application -kill <application_id>
```

예를 들어:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>안전 모드에 갇히기

클러스터를 축소할 때 HDInsight는 아파치 암바리 관리 인터페이스를 사용하여 추가 작업자 노드를 먼저 해제합니다. 노드는 HDFS 블록을 다른 온라인 작업자 노드에 복제합니다. 그 후 HDInsight는 클러스터를 안전하게 축소합니다. HDFS는 배율 조정 작업 중에 안전 모드로 전환됩니다. HDFS는 스케일링이 완료되면 나올 예정입니다. 그러나 경우에 따라 HDFS는 파일 블록 의 복제 부족으로 인해 크기 조정 작업 중에 안전 모드에 갇혀 있습니다.

기본적으로 HDFS는 사용 가능한 `dfs.replication` 각 파일 블록의 복사본 수를 제어하는 1의 설정으로 구성됩니다. 파일 블록의 각 복사본은 클러스터의 다른 노드에 저장됩니다.

예상 되는 블록 복사본 수를 사용할 수 없는 경우 HDFS 안전 모드로 들어가고 Ambari 경고를 생성 합니다. HDFS는 크기 조정 작업에 안전 모드로 들어갈 수 있습니다. 복제에 필요한 수의 노드가 검색되지 않으면 클러스터가 안전 모드로 멈출 수 있습니다.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>안전 모드가 켜지는 오류 예제

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

클러스터 크기가 조정될 시간이 가까워질 때 `/var/log/hadoop/hdfs/` 폴더에서 이름 노드 로그를 검토하여 안전 모드가 시작되었는지 확인할 수 있습니다. 로그 파일의 이름은 `Hadoop-hdfs-namenode-<active-headnode-name>.*`입니다.

근본 원인은 Hive가 쿼리를 실행하는 동안 HDFS의 임시 파일에 의존하기 때문에 발생했습니다. HDFS가 안전 모드로 들어가면 HDFS에 쓸 수 없으므로 Hive는 쿼리를 실행할 수 없습니다. HDFS의 임시 파일은 개별 작업자 노드 VM에 탑재된 로컬 드라이브에 있습니다. 파일은 최소 3개의 복제본에서 다른 작업자 노드 간에 복제됩니다.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>HDInsight가 안전 모드에 갇히지 않도록 하는 방법

다음과 같은 여러 가지 방법으로 HDInsight가 안전 모드를 유지하지 않도록 할 수 있습니다.

* HDInsight를 축소하기 전에 모든 Hive 작업을 중지합니다. 또는 실행 중인 Hive 작업과 충돌하지 않도록 축소 프로세스를 예약합니다.
* 축소하기 전에 HDFS에서 Hive의 스크래치 `tmp` 디렉터리 파일을 수동으로 정리합니다.
* HDInsight를 최소 3개의 작업자 노드로만 축소합니다. 작업자 노드 1개만큼 줄이지는 마세요.
* 필요한 경우 안전 모드를 종료하는 명령을 실행합니다.

다음 섹션에서는 이러한 옵션을 설명합니다.

#### <a name="stop-all-hive-jobs"></a>모든 Hive 작업 중지

하나의 작업자 노드를 축소하기 전에 모든 Hive 작업을 중지합니다. 워크로드가 예약되면 Hive 작업이 완료된 후에 축소를 실행합니다.

크기 조정 전에 Hive 작업을 중지하면 tmp 폴더의 스크래치 파일 수를 최소화할 수 있습니다(있는 경우).

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive의 스크래치 파일을 수동으로 정리

Hive가 임시 파일을 남겨 두면 안전 모드를 피하기 위해 축소 전에 해당 파일을 수동으로 정리할 수 있습니다.

1. `hive.exec.scratchdir` 구성 속성을 확인하여 Hive 임시 파일에 사용되는 위치를 확인합니다. 이 매개 변수는 다음과 함께 `/etc/hive/conf/hive-site.xml`설정됩니다.

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive 서비스를 중지하고 모든 쿼리 및 작업이 완료되었는지 확인합니다.

1. 위에 있는 스크래치 디렉토리의 내용을 `hdfs://mycluster/tmp/hive/` 나열하여 파일이 포함되어 있는지 확인합니다.

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    다음은 파일이 있는 경우의 샘플 출력입니다.

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. 이러한 파일에 대해 Hive가 완료된 것이 확인되면 제거해도 됩니다. Hive에는 원사 리소스 관리자 UI 페이지에서 보면 실행 중인 쿼리가 없는지 확인합니다.

    HDFS에서 파일을 제거하는 명령줄 예제는 다음과 같습니다.

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight를 3개 이상의 작업자 노드로 확장

클러스터가 3개 미만의 작업자 노드로 축소할 때 안전 모드에 자주 갇혀 있는 경우 세 개 이상의 작업자 노드를 유지합니다.

세 명의 작업자 노드를 갖는 것은 하나의 작업자 노드로 축소하는 것보다 비용이 많이 듭니다. 그러나 이 작업을 수행하면 클러스터가 안전 모드에 갇히지 않습니다.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>HDInsight를 하나의 작업자 노드로 확장

클러스터가 하나의 노드로 축소되더라도 작업자 노드 0은 여전히 유지됩니다. 작업자 노드 0은 서비스 해제할 수 없습니다.

#### <a name="run-the-command-to-leave-safe-mode"></a>안전 모드를 종료하는 명령 실행

마지막 옵션은 Leave 안전 모드 명령을 실행하는 것입니다. HIVE 파일 미수정으로 인해 HDFS가 안전 모드로 들어간 경우 다음 명령을 실행하여 안전 모드를 그대로 둡니다.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>아파치 HBase 클러스터 축소

지역 서버는 크기 조정 작업을 완료한 후 몇 분 이내에 자동으로 균형을 이룹니다. 지역 서버의 균형을 수동으로 조정하려면 다음 단계를 완료합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 SSH 사용을](hdinsight-hadoop-linux-use-ssh-unix.md)참조하십시오.

2. HBase 셸을 시작합니다.

    ```bash
    hbase shell
    ```

3. 다음 명령을 사용하여 지역 서버의 부하를 수동으로 분산합니다.

    ```bash
    balancer
    ```

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 클러스터 자동 크기 조정](hdinsight-autoscale-clusters.md)
* [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)
