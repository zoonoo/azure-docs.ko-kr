---
title: 클러스터 크기 조정 - Azure HDInsight
description: 워크 로드에 맞게 탄력적으로 Azure HDInsight 클러스터를 확장 합니다.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: eb68421c4f62d94eedf266a0c34a0e276eacc4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479269"
---
# <a name="scale-hdinsight-clusters"></a>HDInsight 클러스터 크기 조정

HDInsight는 클러스터의 작업자 노드 수를 증가 및 감소하는 옵션을 제공하여 유연성을 보장합니다. 이 탄력성을 사용 하면 시간 후 또는 주말에 클러스터를 축소 하 고 비즈니스 요구가 최대 수준일 때 확장할 수 있습니다.

정기적인 일괄 처리에 있는 경우 HDInsight 클러스터를 확장할 수 있습니다 잠시 후 작업을 하기 전에 클러스터에 충분 한 메모리 및 CPU 전원 되도록 합니다.  나중에, 처리가 완료된 후 사용량이 다시 줄어들면, HDInsight 클러스터를 더 적은 수의 작업자 노드로 축소할 수 있습니다.

수동으로 아래에 설명 된 방법 중 하나를 사용 하 여 클러스터 크기를 조정 하거나 사용할 수 있습니다 [자동 크기 조정](hdinsight-autoscale-clusters.md) 에 CPU, 메모리 및 기타 메트릭에 대 한 응답에서 보내는 옵션 시스템을 자동으로 확장 및 축소 합니다.

## <a name="utilities-to-scale-clusters"></a>클러스터 크기를 조정 하는 유틸리티

Microsoft는 클러스터 크기를 조정 하는 다음 유틸리티를 제공 합니다.

|유틸리티 | 설명|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[Azure CLI](hdinsight-administer-use-command-line.md)|azure hdinsight 클러스터 크기 조정 \<clusterName > \<대상 인스턴스의 수가 > |
|[Azure Portal](https://portal.azure.com)|HDInsight 클러스터 창의 열을 선택 합니다 **클러스터 크기** 왼쪽 메뉴에서 클러스터 크기 창에서 작업자 노드 수를 입력 하 고 저장을 선택 합니다.|  

![클러스터 크기 조정](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

이러한 방법 중 하나를 사용하여 몇 분 만에 HDInsight 클러스터를 확장 또는 축소할 수 있습니다.

> [!IMPORTANT]  
> * Aure 클래식 CLI가 사용 되지 않으며 클래식 배포 모델로 사용 해야 합니다. 다른 모든 배포에 사용 된 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)합니다.  
> * PowerShell AzureRM 모듈을 사용 하는 사용 되지 않습니다.  사용 하십시오 합니다 [Az 모듈](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) 가능 합니다.

## <a name="impact-of-scaling-operations"></a>크기 조정 작업의 영향

경우 있습니다 **추가** 노드를 실행 중인 HDInsight 클러스터 (강화), 보류 중이거나 실행 중인 모든 작업에는 영향을 받지 않습니다. 크기 조정 프로세스가 실행되는 동안 새 작업을 안전하게 제출할 수 있습니다. 크기 조정 작업이 어떤 이유로 든 실패 하면 클러스터는 작동 상태에서를 오류 처리 됩니다.

경우 있습니다 **제거** 크기 조정 작업이 완료 될 때 노드 (규모 축소) 모든 보류 중이거나 실행 중인 작업은 실패 합니다. 이 오류는 일부의 서비스 크기 조정 프로세스 중 다시 시작 때문입니다. 위험이 클러스터 수동 크기 조정 작업 중에 중단된 안전 모드를 가져올 수 있습니다.

## <a name="how-to-safely-scale-down-a-cluster"></a>클러스터를 안전 하 게 축소 하는 방법

### <a name="scale-down-a-cluster-with-running-jobs"></a>작업을 실행 하 여 클러스터를 축소

규모 축소 작업 중에 실패 하 여 실행 중인 작업을 하지 않으려면 다음 세 가지 작업을 시도할 수 있습니다.

1. 작업을 클러스터를 축소 하기 전에 완료 될 때까지 기다립니다.
1. 작업을 수동으로 종료 합니다.
1. 크기 조정 작업이 완료 된 후 작업을 다시 제출 합니다.

보류 중인 목록 및 실행 중인 작업을 보려면 YARN을 사용할 수 있습니다 **Resource Manager UI**, 다음 단계를 수행 합니다.

1. [Azure portal](https://portal.azure.com/), 클러스터를 선택 합니다.  지침에 대해서는 [클러스터 나열 및 표시](./hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요. 클러스터는 새 포털 페이지에서 열립니다.
2. 기본 보기에서로 이동 **클러스터 대시보드** > **Ambari 홈**합니다. 클러스터 자격 증명을 입력 합니다.
3. Ambari UI에서 선택 **YARN** 왼쪽 메뉴의 서비스 목록에 있습니다.  
4. YARN 페이지에서 선택 **빠른 링크** 하 고 활성 헤드 노드를 마우스로 클릭 **ResourceManager UI**합니다.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`에서 ResourceManager UI에 직접 액세스할 수 있습니다.

현재 상태와 함께 작업 목록이 표시됩니다. 스크린샷에서 작업이 한 현재 실행:

![ResourceManager UI 애플리케이션](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

실행 중인 해당 애플리케이션을 수동으로 종료하려면 SSH 셸에서 다음 명령을 실행합니다.

```bash
yarn application -kill <application_id>
```

예를 들면 다음과 같습니다.

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>안전 모드에 계속 쌓여

클러스터를 축소 하는 경우 HDInsight를 먼저 다른 온라인 작업자 노드로 하 여 HDFS 블록을 복제 하는 추가 작업자 노드 서비스 해제 하려면 Apache Ambari 관리 인터페이스를 사용 합니다. 그런 다음 HDInsight 클러스터 안전 하 게 조정합니다. HDFS는 크기 조정 작업 중 안전 모드로 이동 하 고는 크기 조정이 완료 되 면 할 합니다. 그러나 경우에 따라 HDFS에에서 고착 안전 모드 크기 조정 작업 중 파일 블록 언더 복제 때문입니다.

기본적으로 HDFS 구성에 사용 하 여는 `dfs.replication` 3 사용 가능한 각 파일 블록의 복사본 개수를 제어 하는 설정입니다. 파일 블록의 각 복사본은 클러스터의 다른 노드에 저장 됩니다.

HDFS 블록 복사본의 예상된 수를 사용할 수 없습니다를 발견 하면 HDFS가 안전 모드로 전환 하 고 Ambari 경고를 생성 합니다. HDFS는 크기 조정 작업에 대 한 안전 모드로 전환 후 종료할 수 없는 안전 모드 하므로 필요한 노드 수는 복제에 대 한 검색 되지 않습니다 하지만 경우 클러스터는 안전 모드에서 고정 될 수 있습니다.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>안전 모드가 켜지는 오류 예제

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

클러스터 크기가 조정될 시간이 가까워질 때 `/var/log/hadoop/hdfs/` 폴더에서 이름 노드 로그를 검토하여 안전 모드가 시작되었는지 확인할 수 있습니다. 로그 파일의 이름은 `Hadoop-hdfs-namenode-hn0-clustername.*`입니다.

이전 오류의 근본 원인은 Hive가 쿼리를 실행하는 동안 HDFS에서 임시 파일을 사용하는 것입니다. HDFS가 안전 모드를 시작하면 Hive는 HDFS에 쓸 수 없으므로 쿼리를 실행할 수 없습니다. HDFS의 임시 파일은 개별 작업자 노드 VM에 탑재된 로컬 드라이브에 있으며, 다른 작업자 노드 간에 복제되어 최소 3개의 복제본이 유지됩니다.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>HDInsight가 안전 모드에 계속 쌓여 하지 못하도록 하는 방법

다음과 같은 여러 가지 방법으로 HDInsight가 안전 모드를 유지하지 않도록 할 수 있습니다.

* HDInsight를 축소하기 전에 모든 Hive 작업을 중지합니다. 또는 실행 중인 Hive 작업과 충돌하지 않도록 축소 프로세스를 예약합니다.
* 축소하기 전에 HDFS에서 Hive의 스크래치 `tmp` 디렉터리 파일을 수동으로 정리합니다.
* HDInsight를 최소 3개의 작업자 노드로만 축소합니다. 작업자 노드 1개만큼 줄이지는 마세요.
* 필요한 경우 안전 모드를 종료하는 명령을 실행합니다.

다음 섹션에서는 이러한 옵션을 설명합니다.

#### <a name="stop-all-hive-jobs"></a>모든 Hive 작업 중지

하나의 작업자 노드를 축소하기 전에 모든 Hive 작업을 중지합니다. 워크로드가 예약되면 Hive 작업이 완료된 후에 축소를 실행합니다.

(있는 경우) 사용 하면 tmp 폴더에 있는 스크래치 파일의 수를 최소화 확장 하기 전에 Hive 작업을 중지 합니다.

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive의 스크래치 파일을 수동으로 정리

Hive가 임시 파일을 남겨 두면 안전 모드를 피하기 위해 축소 전에 해당 파일을 수동으로 정리할 수 있습니다.

1. 확인 하 여 Hive 임시 파일에 사용 되는 위치 확인 된 `hive.exec.scratchdir` 구성 속성입니다. 이 매개 변수 내에서 설정 `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive 서비스를 중지하고 모든 쿼리 및 작업이 완료되었는지 확인합니다.
2. 위의 찾을 스크래치 디렉터리의 내용을 나열 `hdfs://mycluster/tmp/hive/` 모든 파일에 포함 되어 있는지 확인 하려면:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    다음은 파일이 있는 경우의 샘플 출력입니다.

    ```
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

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>3 개 이상의 작업자 노드로 HDInsight 확장

세 개 이상의 작업자 노드를 축소 하는 경우에 자주 클러스터 안전 모드에서 중단 하 고 이전 단계가 작동 하지 않는 경우 세 개 이상의 작업자 노드를 유지 하 여 완전히 안전 모드를 진행 하 여 클러스터를 방지할 수 있습니다.

작업자 노드 3 개 유지 하 고 하나의 작업자 노드로 축소 하는 보다 가격이 더 비쌉니다 이지만 클러스터에서 안전 모드에 계속 쌓여 수 없게 됩니다.

#### <a name="run-the-command-to-leave-safe-mode"></a>안전 모드를 종료하는 명령 실행

마지막 방법은 leave safe mode 명령을 실행 하는 것입니다. Hive 파일에 대 한 요금이 덜 복제로 인해 HDFS가 안전 모드에 대 한 이유는 알고 있는 경우 안전 모드를 종료 하려면 다음 명령을 실행할 수 있습니다.


```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase 클러스터를 축소

지역 서버는 크기 조정 작업을 완료 한 후 몇 분 안에 자동으로 균형이 조정 됩니다. 지역 서버를 수동으로 조정 하려면 다음 단계를 수행 합니다.

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

* [Azure HDInsight 클러스터를 자동으로 크기 조정](hdinsight-autoscale-clusters.md)
* [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)
* [클러스터 크기 조정](hdinsight-administer-use-portal-linux.md#scale-clusters)
