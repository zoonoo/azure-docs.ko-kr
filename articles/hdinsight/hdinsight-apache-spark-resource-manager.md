<properties 
	pageTitle="리소스 관리자를 사용하여 HDInsight에서 Apache Spark 클러스터에 리소스 할당 | Microsoft Azure" 
	description="성능 향상을 위해 HDInsight에서 Spark 클러스터에 리소스 관리자를 사용하는 방법을 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리(Linux)

Azure HDInsight(Linux)의 Spark는 클러스터 리소스를 관리하고 클러스터 상태를 모니터링할 수 있는 Ambari 웹 UI를 제공합니다. 또한 Spark 기록 서버를 사용하여 클러스터에서 실행이 완료된 응용 프로그램을 추적할 수 있습니다. YARN UI를 사용하여 현재 클러스터에서 실행 중인 응용 프로그램을 모니터링할 수 있습니다. 이 문서에서는 이러한 UI에 액세스하는 방법과 이러한 인터페이스를 사용하여 몇 가지 기본적인 리소스 관리 작업을 수행하는 방법을 설명합니다.

**필수 조건:**

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

## Ambari 웹 UI를 시작하는 방법

1. [Azure Preview 포털](https://ms.portal.azure.com/)의 시작 보드에서 Spark 클러스터 타일을 클릭합니다(Spark 클러스터를 시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다. 
 
2. Spark 클러스터 블레이드에서 **대시보드**를 클릭합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.

	![Ambari 시작](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "리소스 관리자 시작")

3. 이렇게 하면 아래와 같이 Ambari 웹 UI가 시작됩니다.

	![Ambari 웹 UI](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Ambari 웹 UI")

## Spark 기록 서버를 시작하는 방법

1. [Azure Preview 포털](https://ms.portal.azure.com/)의 시작 보드에서 Spark 클러스터 타일을 클릭합니다(Spark 클러스터를 시작 보드에 고정한 경우).

2. 클러스터 블레이드의 **빠른 링크**에서 **클러스터 대시보드**를 클릭합니다. **클러스터 대시보드** 블레이드에서 **Spark 기록 서버**를 클릭합니다.

	![Spark 기록 서버](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Spark 기록 서버")

	메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.


## Yarn UI를 시작하는 방법

YARN UI를 사용하여 현재 Spark 클러스터에서 실행 중인 응용 프로그램을 모니터링할 수 있습니다. YARN UI에 액세스하려면 클러스터에 대한 SSH 터널링을 활성화해야 합니다. 방법은 [SSH 터널링을 사용하여 Ambari 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

1. 아래 섹션과 같이 Ambari 웹 UI를 시작합니다.

2. Ambari 웹 UI에서 페이지의 왼쪽 목록에서 YARN을 선택합니다.

3. YARN 서비스 정보가 표시되면 **빠른 링크**를 선택합니다. 클러스터 헤드 노드 목록이 표시됩니다. 헤드 노드 중 하나를 선택한 다음 **ResourceManager UI**를 선택합니다.

	![YARN UI 시작](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "YARN UI 시작")

4. 그러면 YARN UI가 시작되고 다음과 유사한 페이지가 표시될 것입니다.

	![YARN UI](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "YARN UI")

##<a name="scenariosrm"></a>Ambari 웹 UI를 사용하여 리소스를 관리하는 방법

Spark 클러스터를 사용하여 발생할 수 있는 일반적인 시나리오 및 Ambari 웹 UI를 사용하여 해당 사항을 해결하는 방법에 대한 지침은 다음과 같습니다.

### Spark 클러스터와 함께 BI를 사용하지 않습니다. 리소스를 회수하려면 어떻게 해야 하나요?

1. 아래와 같이 Ambari 웹 UI를 시작합니다. 왼쪽의 탐색 창에서 **Spark**를 클릭한 다음 **구성**을 클릭합니다.

2. 구성 목록에서 **사용자 지정 spark-thrift-sparkconf**를 찾아서 **spark.executor.memory** 및 **spark.drivers.core**의 값을 **0**으로 변경합니다.

	![BI용 리소스](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "BI용 리소스")

3. **Save**를 클릭합니다. 변경 사항에 대한 설명을 입력한 다음 **저장**을 클릭합니다.

4. 페이지 맨 위에 Spark 서비스를 다시 시작하라는 메시지가 표시됩니다. **다시 시작**을 클릭하여 변경 내용을 적용합니다.


### 내 Jupyter Notebook이 예상대로 실행되지 않습니다. 서비스를 다시 시작하려면 어떻게 해야 하나요?

1. 아래와 같이 Ambari 웹 UI를 시작합니다. 왼쪽의 탐색 창에서 **Jupyter**를 클릭하고, **서비스 작업**을 클릭한 다음 **모두 다시 시작**을 클릭합니다. 그러면 모든 헤드 노드에서 Jupyter 서비스가 시작됩니다.

	![Jupyter 다시 시작](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Jupyter 다시 시작")

	


## <a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)

* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 응용 프로그램 만들기 및 실행

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)

* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### 확장

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_1223_2015-->