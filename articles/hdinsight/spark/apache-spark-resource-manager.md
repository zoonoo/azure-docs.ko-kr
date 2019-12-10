---
title: Azure HDInsight에서 Apache Spark 클러스터용 리소스 관리
description: 성능 향상을 위해 Azure HDInsight에서 Spark 클러스터에 대 한 리소스를 관리 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 8c4306b9fb7a6f45ccad45083b6efc019e9a2920
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951671"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터용 리소스 관리

[Apache Ambari](https://ambari.apache.org/) ui, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ui 및 [Apache Spark](https://spark.apache.org/) 클러스터와 연결 된 [Spark 기록 서버](./apache-azure-spark-history-server.md) 와 같은 인터페이스에 액세스 하는 방법과 최적의 성능을 위해 클러스터 구성을 조정 하는 방법에 대해 알아봅니다.

## <a name="open-the-spark-history-server"></a>Spark 기록 서버 열기

Spark 기록 서버는 완료되고 실행되는 Spark 애플리케이션의 웹 UI입니다. Spark의 웹 UI를 확장 한 것입니다. 전체 정보는 [Spark 기록 서버](./apache-azure-spark-history-server.md)를 참조 하세요.

## <a name="open-the-yarn-ui"></a>Yarn UI 열기

YARN UI를 사용하여 현재 Spark 클러스터에서 실행 중인 애플리케이션을 모니터링할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 Spark 클러스터를 엽니다. 자세한 내용은 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요.

2. **클러스터 대시보드에서** **Yarn**를 선택 합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.

    ![YARN UI 시작](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > 또는 Ambari UI에서 YARN UI를 시작할 수도 있습니다. Ambari UI에서 **YARN** > **빠른 링크** > **Active** > **리소스 관리자 UI**로 이동 합니다.

## <a name="optimize-clusters-for-spark-applications"></a>Spark 애플리케이션에 대해 클러스터 최적화

애플리케이션 요구 사항에 따라 Spark 구성에 사용할 수 있는 세 가지 주요 매개 변수는 `spark.executor.instances`, `spark.executor.cores` 및 `spark.executor.memory`입니다. 실행자는 Spark 애플리케이션을 위해 시작된 프로세스입니다. 작업자 노드에서 실행되며 애플리케이션에 대한 작업을 수행해야 합니다. 각 클러스터에 대한 실행자 및 실행자 크기의 기본 수는 작업자 노드 및 작업자 노드 크기의 수에 기반하여 계산됩니다. 이 정보는 클러스터 헤드 노드의 `spark-defaults.conf`에 저장됩니다.

세 가지 구성 매개 변수는 (클러스터에서 실행된는 모든 애플리케이션의 경우) 클러스터 수준에서 구성될 수 있거나 각 개별 애플리케이션에 대해 지정될 수 있습니다.

### <a name="change-the-parameters-using-ambari-ui"></a>Ambari UI를 사용하여 매개 변수 변경

1. Ambari UI에서 **Spark2** > **Configs** > **Custom Spark2-defaults**로 이동 합니다.

    ![Ambari 사용자 지정을 사용 하 여 매개 변수 설정](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Ambari 사용자 지정을 사용 하 여 매개 변수 설정")

1. 기본값으로 Spark 클러스터에서 4개의 애플리케이션을 동시에 실행할 수 있습니다. 다음 스크린샷에 표시된 대로 사용자 인터페이스에서 이러한 값을 변경할 수 있습니다.

    ![Ambari를 사용 하 여 매개 변수 설정](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Ambari를 사용하여 매개 변수 설정")

1. **저장** 을 선택 하 여 구성 변경 내용을 저장 합니다. 페이지 맨 위에서 영향을 받는 모든 서비스를 다시 시작 하 라는 메시지가 표시 됩니다. **다시 시작**을 선택합니다.

    ![서비스 다시 시작](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter notebook에서 실행 중인 애플리케이션에 대한 매개 변수 변경

Jupyter notebook에서 실행 중인 애플리케이션의 경우 `%%configure` magic을 사용하여 구성을 변경할 수 있습니다. 이상적으로 첫 번째 코드 셀을 실행하기 전에 애플리케이션의 시작 부분에 이를 변경해야 합니다. 이렇게 하면 구성이 생성된 경우 Livy 세션에 적용됩니다. 애플리케이션의 이후 단계에서 구성을 변경하려는 경우 `-f` 매개 변수를 사용해야 합니다. 그러나 이렇게 하면 애플리케이션의 모든 진행률이 손실됩니다.

다음 코드 조각에서는 Jupyter에서 실행 중인 애플리케이션에 대한 구성을 변경하는 방법을 보여줍니다.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

구성 매개 변수는 JSON 문자열로 전달되어야 하며, 아래 예제 열과 같이 매직 뒤의 다음 줄에 있어야 합니다.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>spark-submit를 사용하여 제출된 애플리케이션에 대한 매개 변수 변경

다음 명령은 `spark-submit`을 사용하여 제출된 배치 애플리케이션에 대한 구성 매개 변수를 변경하는 방법의 예제입니다.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>cURL을 사용하여 제출된 애플리케이션에 대한 매개 변수 변경

다음 명령은 cURL을 사용하여 제출된 배치 애플리케이션에 대한 구성 매개 변수를 변경하는 방법의 예제입니다.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Spark Thrift 서버에서 이러한 매개 변수 변경

Spark Thrift 서버에서는 Spark 클러스터에 대한 JDBC/ODBC 액세스를 제공하고 Spark SQL 쿼리를 제공하는 데 사용됩니다. Power BI, Tableau 등과 같은 도구를 사용 하 여 spark SQL 쿼리를 spark 응용 프로그램으로 실행 하기 위해 Spark Thrift 서버와 통신 하는 데 ODBC 프로토콜을 사용 합니다. Spark 클러스터를 만들 경우 Spark Thrift 서버에서 각 헤드 노드에 하나씩 두 개의 인스턴스를 시작합니다. 각 Spark Thrift 서버는 YARN UI에서 Spark 애플리케이션으로 표시됩니다.

Spark Thrift 서버는 Spark 동적 실행자 할당을 사용 하므로 `spark.executor.instances` 사용 되지 않습니다. 대신 Spark Thrift 서버는 `spark.dynamicAllocation.maxExecutors` 및 `spark.dynamicAllocation.minExecutors`을 사용하여 실행자 수를 지정합니다. 구성 매개 변수 `spark.executor.cores`및 `spark.executor.memory`는 실행 기 크기를 수정 하는 데 사용 됩니다. 다음 단계와 같이 이러한 매개 변수를 변경할 수 있습니다.

* **고급 spark2-thrift-sparkconf** 범주를 확장 하 `spark.dynamicAllocation.maxExecutors`매개 변수을 업데이트 하 고 `spark.dynamicAllocation.minExecutors`합니다.

    ![Spark thrift 서버 구성](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Spark Thrift 서버 구성")

* **사용자 지정 spark2-thrift-sparkconf** 범주를 확장 하 `spark.executor.cores`매개 변수을 업데이트 하 고 `spark.executor.memory`합니다.

    ![Spark thrift 서버 매개 변수 구성](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Spark thrift 서버 매개 변수 구성")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Spark Thrift 서버의 드라이버 메모리 변경

Spark Thrift 서버 드라이버 메모리는 헤드 노드 RAM 크기의 25%로 구성되어 제공된 헤드 노드의 총 RAM 크기는 14GB보다 큽니다. 다음 스크린샷에 표시된 대로 Ambari UI를 사용하여 드라이버 메모리 구성을 변경할 수 있습니다.

Ambari UI에서 **Spark2** > **Configs** > **Advanced Spark2-env**로 이동 합니다. 그런 다음 **spark_thrift_cmd_opts**에 대 한 값을 제공 합니다.

## <a name="reclaim-spark-cluster-resources"></a>Spark 클러스터 리소스 확보

Spark 동적 할당 때문에 Thrift 서버에서 사용되는 리소스만이 두 애플리케이션 마스터에 대한 리소스입니다. 이러한 리소스를 회수하려면 클러스터에서 실행되는 Thrift 서버 서비스를 중지해야 합니다.

1. Ambari UI의 왼쪽 창에서 **Spark2**를 선택 합니다.

2. 다음 페이지에서 **Spark2 Thrift Servers**를 선택 합니다.

    ![Thrift server1 다시 시작](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Thrift server1 다시 시작")

3. Spark2 Thrift 서버를 실행 하는 두 개의 헤드 노드가 표시 됩니다. 헤드 노드 중 하나를 선택 합니다.

    ![Thrift server2 다시 시작](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Thrift server2 다시 시작")

4. 다음 페이지는 헤드 노드에서 실행 중인 모든 서비스를 나열합니다. 목록에서 Spark2 Thrift Server 옆에 있는 드롭다운 단추를 선택한 다음 **중지**를 선택 합니다.

    ![Thrift server3 다시 시작](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Thrift server3 다시 시작")
5. 다른 헤드 노드에서도 이 단계를 반복합니다.

## <a name="restart-the-jupyter-service"></a>Jupyter 서비스 다시 시작

문서의 처음에 표시된 대로 Ambari 웹 UI가 시작됩니다. 왼쪽 탐색 창에서 **Jupyter**를 선택 하 고 **서비스 작업**을 선택한 다음 **모두 다시 시작**을 선택 합니다. 그러면 모든 헤드 노드에서 Jupyter 서비스가 시작됩니다.

![Jupyter 다시 시작](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Jupyter 다시 시작")

## <a name="monitor-resources"></a>리소스 모니터링

문서의 처음에 표시된 대로 Yarn UI가 시작됩니다. 화면 위쪽의 클러스터 메트릭 테이블에서 **사용된 메모리** 및 **총 메모리** 열 값을 확인합니다. 두 값이 비슷하면 리소스가 충분하지 않아 다음 애플리케이션을 시작하지 못할 수 있습니다. 동일한 현상이 **사용된 VCore** 및 **총 VCore** 열에도 적용됩니다. 또한 주 보기에서 응용 프로그램이 **허용 됨** 상태로 전환 **되 고 실행 중** 또는 **실패** 상태로 전환 되지 않는 경우에는 시작 하는 데 충분 한 리소스를 높게 유지 하는 것일 수도 있습니다.

![리소스 제한](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "리소스 제한")

## <a name="kill-running-applications"></a>애플리케이션 실행 종료

1. Yarn UI의 왼쪽 패널에서 **실행 중**을 선택 합니다. 실행 중인 응용 프로그램 목록에서 중지할 응용 프로그램을 결정 하 고 **ID**를 선택 합니다.

    ![Kill App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill App1")

2. 오른쪽 위 모서리에서 **응용 프로그램 중지** 를 선택 하 고 **확인**을 선택 합니다.

    ![Kill App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill App2")

## <a name="see-also"></a>참고 항목

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>데이터 분석가

* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight의 Apache Spark를 사용한 Application Insight 원격 분석 데이터 분석](apache-spark-analyze-application-insight-logs.md)
* [분산 심층 학습을 위해 Azure HDInsight Spark에서 Caffe 사용](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Apache Spark 개발자의 경우

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)
