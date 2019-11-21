---
title: Azure HDInsight에서 Apache Storm 토폴로지 배포 및 관리
description: Learn how to deploy, monitor, and manage Apache Storm topologies using the Storm Dashboard on Linux-based HDInsight. Visual Studio용 Hadoop 도구를 사용합니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 82c5db4f75f131ebdc2434955108e7d50237d9ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228870"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Storm 토폴로지 배포 및 관리 

이 문서에서는 HDInsight의 Storm 클러스터에서 실행되는 [Apache Storm](https://storm.apache.org/) 토폴로지의 모니터링 및 관리에 관한 기본 사항을 알아봅니다.

## <a name="prerequisites"></a>전제 조건

* HDInsight의 Apache Storm 클러스터. [Azure Portal을 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하고 **클러스터 유형**에 **Storm**을 선택합니다.

* (Optional) Familiarity with Secure Shell (SSH) and Secure Copy (SCP). 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* (Optional) Visual Studio, Azure SDK 2.5.1 or newer, and the Data Lake Tools for Visual Studio. For more information, see [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Submit a topology using Visual Studio

You can use the Data Lake Tools for Visual Studio to submit C# or hybrid topologies to your Storm cluster. 다음 단계는 샘플 애플리케이션을 사용합니다. For information about topology creation using the Data Lake Tools, see [Apache Storm topologies with Visual Studio and C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. If you haven't already installed the latest version of the Data Lake tools for Visual Studio, see [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Data Lake Tools for Visual Studio는 예전에 HDInsight Tools for Visual Studio였습니다.
    >
    > Data Lake Tools for Visual Studio are included in the **Azure Workload** for Visual Studio 2019.

2. Visual Studio를 엽니다.

3. In the **Start** window, select **Create a new project**.

4. In the **Create a new project** window, select the search box, and enter *Storm*. Then choose **Storm Sample** from the result list and select **Next**.

5. In the **Configure your new project** window, enter a **Project name**, and go to or create a **Location** to save the new project in. 그런 다음 **만들기**를 선택합니다.

    ![Configure your new project window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

6. In **Solution Explorer**, right-click the project, and choose **Submit to Storm on HDInsight**.

    > [!NOTE]  
    > 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

7. In the **Submit Topology** dialog box, under the **Storm Cluster** drop-down list, choose your Storm on HDInsight cluster, and then select **Submit**. You can monitor whether the submission is successful by viewing the **Output** pane.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Submit a topology using SSH and the Storm command

To submit a topology to Storm using SSH:

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. Replace `USERNAME` with the name of your SSH user name (such as *sshuser*). `CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꿉니다.

    ```shell
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    For more information on using SSH to connect to your HDInsight cluster, see [Connect to HDInsight (Apache Hadoop) using SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use the following command to start the *WordCount* example topology:

    ```ssh
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    이 명령은 클러스터에서 예제 WordCount 토폴로지를 시작합니다. 이 토폴로지는 임의로 문장을 생성한 다음 문장에서 각 단어의 발생 횟수를 계산합니다.

    > [!NOTE]  
    > When submitting topology to the cluster, you must first copy the .jar file containing the cluster before using the `storm` command. 클러스터에 파일을 복사하려면 `scp` 명령을 사용할 수 있습니다. 예를 들어 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`을 입력합니다.
    >
    > The *WordCount* example, and other storm starter examples, are already included on your cluster at `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submit a topology programmatically

Nimbus 서비스를 사용하여 토폴로지를 프로그래밍 방식으로 배포할 수 있습니다. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)는 Nimbus 서비스를 통해 토폴로지를 배포하고 시작하는 방법을 보여 주는 예제 Java 애플리케이션을 제공합니다.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitor and manage a topology in Visual Studio

When you submit a topology using Visual Studio, the **Storm Topologies View** window appears. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 토폴로지를 선택합니다.

![Monitor topology, Storm Topologies View window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> **서버 탐색기**에서 **Storm 토폴로지**를 볼 수도 있습니다. **Azure** > **HDInsight**를 확장하고 HDInsight 클러스터에서 Storm을 마우스 오른쪽 단추로 클릭한 다음 **Storm 토폴로지 보기**를 선택합니다.

이러한 구성 요소에 대한 정보를 보려면 Spout 또는 Bolt에 대한 셰이프를 선택합니다. A tooltip with component information appears for the item selected.

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. To do these operations, use the **Deactivate** and **Reactivate** buttons in the **Actions** area at the top of the **Storm Topologies View** window.

### <a name="rebalance-a-topology"></a>Rebalance a topology

토폴로지의 균형을 재조정하면 시스템이 토폴로지의 병렬 처리를 수정할 수 있습니다. For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

To rebalance a topology, use the **Rebalance** button in the **Actions** area of the **Storm Topologies View** window.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. If the topology was deactivated, it remains deactivated.

### <a name="kill-a-running-topology"></a>실행 중인 토폴로지를 중단합니다.

Storm topologies continue running until they're stopped or the cluster is deleted. To stop a topology, use the **Kill** button in the **Actions** area.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitor and manage a topology using SSH and the Storm command

`storm` 유틸리티를 사용하면 명령줄에서 실행하는 토폴로지로 작업할 수 있습니다. 전체 명령 목록에는 `storm -h` 를 사용합니다.

### <a name="list-topologies"></a>목록 토폴로지

다음 명령을 사용하여 실행하는 토폴로지를 나열합니다.

```shell
storm list
```

이 명령은 다음 텍스트와 유사한 정보를 반환합니다.

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. Use the following commands to deactivate or reactivate:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>실행 중인 토폴로지를 중단합니다.

Storm 토폴로지가 일단 시작되면 중지될 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 명령을 사용합니다.

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Rebalance a topology

토폴로지의 균형을 재조정하면 시스템이 토폴로지의 병렬 처리를 수정할 수 있습니다. For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. 비활성화되어 있다면 비활성화된 상태를 유지합니다.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitor and manage a topology using the Storm UI

The Storm UI provides a web interface for working with running topologies, and it's included on your HDInsight cluster. To view the Storm UI, use a web browser to open `https://CLUSTERNAME.azurehdinsight.net/stormui`, where *CLUSTERNAME* is the name of your cluster.

> [!NOTE]  
> If you're asked to provide a user name and password, enter the cluster administrator username and password that you used when creating the cluster.

### <a name="storm-ui-main-page"></a>Storm UI main page

Storm UI의 기본 페이지에서는 다음 정보를 제공합니다.

| 섹션 | 설명 |
| --- | --- |
| **Cluster summary** | Storm 클러스터에 대한 기본 정보입니다. |
| **Nimbus summary** | A list of basic Nimbus information. |
| **Topology summary** | 실행 중인 토폴로지 목록입니다. To view more information about a specific topology, select its link in the **Name** column. |
| **Supervisor summary** | Storm 감독자에 대한 정보입니다. To see the worker resources associated with a specific supervisor, select its link in the **Host** or **Id** column. |
| **Nimbus configuration** | 클러스터에 대한 Nimbus 구성입니다. |

The Storm UI main page looks similar to this web page:

![Main page, Storm UI, Apache Storm topologies, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>토폴로지 요약

**토폴로지 요약** 섹션의 링크를 선택하면 토폴로지에 대한 다음 정보가 표시됩니다.

| 섹션 | 설명 |
| --- | --- |
| **Topology summary** | 토폴로지에 대한 기본 정보입니다. |
| **Topology actions** | Management actions that you can do for the topology. The available actions are described later in this section. |
| **Topology stats** | 토폴로지에 대한 통계입니다. To set the time frame for an entry in this section, select its link in the **Window** column. |
| **Spouts** *(time frame)* | 토폴로지에서 사용하는 Spout입니다. To view more information about a specific spout, select its link in the **Id** column. |
| **Bolts** *(time frame)* | 토폴로지에서 사용하는 Bolt입니다. To view more information about a specific bolt, select its link in the **Id** column. |
| **Worker resources** | A list of worker resources. To view more information about a specific worker resource, select its link in the **Host** column. |
| **Topology visualization** | A **Show Visualization** button that displays a visualization of the topology. |
| **Topology configuration** | 선택한 토폴로지의 구성입니다. |

The Storm topology summary page looks similar to this web page:

![Topology summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

In the **Topology actions** section, you can select the following buttons to do an action:

| 단추 | 설명 |
| --- | --- |
| **활성화** | 비활성화된 토폴로지 처리를 다시 시작합니다. |
| **Deactivate** | 실행 중인 토폴로지를 일시 중지합니다. |
| **Rebalance** | 토폴로지의 병렬 처리를 조정합니다. You should rebalance running topologies after you've changed the number of nodes in the cluster. This operation allows the topology to adjust parallelism to compensate for the additional or reduced number of nodes in the cluster.<br/><br/>자세한 내용은 <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm 토폴로지의 병렬 처리 이해</a>를 참조하세요.
| **Kill** | 지정된 시간 제한 후 Storm 토폴로지를 종료합니다. |
| **디버그** | Begins a debugging session for the running topology. |
| **Stop Debug** | Ends the debugging session for the running topology. |
| **Change Log Level** | Modifies the debugging log level. |

##### <a name="spout-and-bolt-summary"></a>Spout and bolt summary

**Spouts** 또는 **Bolts** 섹션에서 Spout를 선택하면 선택한 항목에 대해 다음 정보가 표시됩니다.

| 섹션 | 설명 |
| --- | --- |
| **Component summary** | Spout 또는 Bolt에 대한 기본 정보입니다. |
| **Component actions** | **Debug** and **Stop Debug** buttons. |
| **Spout stats** or **Bolt stats** | Spout 또는 Bolt에 대한 통계입니다. To set the time frame for an entry in this section, select its link in the **Window** column. |
| (Bolt-only)<br/>**Input stats** *(time frame)* | Bolt에서 사용하는 입력 스트림에 대한 정보입니다. |
| **Output stats** *(time frame)* | Spout 또는 Bolt가 내보낸 스트림에 대한 정보입니다. |
| **Profiling and debugging** | Controls for profiling and debugging the components on this page. You can set the **Status / Timeout (Minutes)** value, and you can select buttons for **JStack**, **Restart Worker**, and **Heap**. |
| **Executors** *(time frame)* | Spout 또는 Bolt의 인스턴스에 대한 정보입니다. To view a log of diagnostic information produced for this instance, select the **Port** entry for a specific executor. You can also see the worker resources associated with a specific executor by selecting its link in the **Host** column. |
| **Errors** | Spout 또는 Bolt에 대한 오류 정보입니다. |

The Storm bolt summary page looks similar to this web page:

![Bolt summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitor and manage the topology using the REST API

The Storm UI is built on top of the REST API, so you can do similar management and monitoring tasks by using the REST API. REST API를 사용하여 Storm 토폴로지를 관리 및 모니터링하는 사용자 지정 도구를 만들 수 있습니다.

자세한 내용은 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)를 참조하세요. 다음 정보는 HDInsight에서 Apache Storm과 REST API 사용하기에 관한 것입니다.

> [!IMPORTANT]  
> The Storm REST API is not publicly available over the internet. It must be accessed using an SSH tunnel to the HDInsight cluster head node. For information on creating and using an SSH tunnel, see [Use SSH tunneling to access Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>기본 URI

The base URI for the REST API on Linux-based HDInsight clusters is available at URL address `https://HEADNODEFQDN:8744/api/v1/`, where you replace *HEADNODEFQDN* with the head node. The domain name of the head node is generated during cluster creation and isn't static.

You can find the fully qualified domain name (FQDN) for the cluster head node in several ways:

| FQDN discovery method | 설명 |
| --- | --- |
| SSH session | Use the command `headnode -f` from an SSH session to the cluster. |
| Ambari Web | On the Ambari cluster web page (`https://CLUSTERNAME.azurehdinsight.net`), select **Services** from the top of the page, then select **Storm**. **요약** 탭에서 **Storm UI 서버**를 선택합니다. Storm UI 및 REST API가 호스팅하는 노드의 FQDN은 페이지 맨 위에 표시됩니다. |
| Ambari REST API | Use the command `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` to retrieve information about the node that the Storm UI and REST API are running on. Replace the two instances of *CLUSTERNAME* with the cluster name. When you're prompted, enter the password for the user (admin) account. In the response, the "host_name" entry of the JSON output contains the FQDN of the node. |

### <a name="authentication"></a>Authentication

Requests to the REST API must use *basic authentication*, so you have to use the administrator name and password for the HDInsight cluster.

> [!NOTE]  
> 기본 인증은 일반 텍스트로 전송되기 때문에 클러스터와의 안전한 통신을 위해서는 *항상* HTTPS를 사용해야 합니다.

### <a name="return-values"></a>반환 값

REST API에서 반환되는 정보는 클러스터 내에서만 사용할 수 있습니다. For example, the fully qualified domain name (FQDN) returned for [Apache ZooKeeper](https://zookeeper.apache.org/) servers isn't accessible from the internet.

## <a name="next-steps"></a>다음 단계

[Apache Maven을 사용하여 Java 기반 토폴로지를 개발](apache-storm-develop-java-topology.md)하는 방법을 알아봅니다.

For a list of more example topologies, see [Example Apache Storm topologies in Azure HDInsight](apache-storm-example-topology.md).
