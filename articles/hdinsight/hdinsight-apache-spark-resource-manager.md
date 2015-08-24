<properties 
	pageTitle="리소스 관리자를 사용하여 HDInsight에서 Apache Spark 클러스터에 리소스 할당 | Microsoft Azure" 
	description="성능 향상을 위해 HDInsight에서 Spark 클러스터에 리소스 관리자를 사용하는 방법을 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/19/2015" 
	ms.author="nitinme"/>


# Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리

리소스 관리자는 클러스터에서 실행되는 각 응용 프로그램이 사용하는 코어 및 RAM 등의 리소스를 관리할 수 있도록 하는 Spark 클러스터 대시보드의 구성 요소입니다.

## <a name="launchrm"></a>리소스 관리자를 시작하는 방법

1. Azure 포털에서 Spark 클러스터를 선택하고 맨 아래 포털 작업 표시줄에서 **Spark 대시보드**를 클릭합니다.

2. 대시보드의 위쪽 창에서 **리소스 관리자** 탭을 클릭합니다.

##<a name="scenariosrm"></a>리소스 관리자를 사용하여 이러한 문제를 수정하는 방법

Spark 클러스터를 사용하여 발생할 수 있는 일반적인 시나리오 및 리소스 관리자를 사용하여 해당 사항을 해결하는 방법에 대한 지침은 다음과 같습니다.

### HDInsight에서 Spark 클러스터가 느립니다.

HDInsight에서 Apache Spark 클러스터는 다중 테넌트를 위해 설계되었으므로 리소스가 여러 구성 요소(노트북, 작업 서버 등)에 걸쳐 분할됩니다. 실행할 리소스를 가져올 수 없는 구성 요소에 대한 걱정 없이 모든 Spark 구성 요소를 동시에 사용할 수 있지만 리소스가 조각화되므로 각 구성 요소의 성능이 저하됩니다. 사용자 요구에 따라 조정 가능합니다.


### Spark 클러스터를 Jupyter 노트북에서만 사용합니다. 모든 리소스를 이 노트북에 할당할 수 있나요?

1. **Spark 대시보드**에서 **스파크 UI** 탭을 클릭하여 응용 프로그램에 할당할 수 있는 최대 코어 수 및 최대 RAM을 확인합니다.

	![리소스 할당](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Resource.png "Spark 클러스터에 할당된 리소스 찾기")

	위의 화면 캡처로 이동하면 할당할 수 있는 최대 코어는 7개(총 8 코어 중 1개 사용 중)이고 할당할 수 있는 최대 RAM은 9GB입니다(총 12GB RAM 중 2GB는 따로 시스템 사용을 위해 남겨두고 1GB는 다른 응용 프로그램에서 사용 중임).

	또한 실행 중인 모든 응용 프로그램을 고려해야 합니다. **Spark UI** 탭에서 실행 중인 응용 프로그램을 살펴볼 수 있습니다.

	![응용 프로그램 실행](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Running.Apps.png "클러스터에서 실행 중인 응용 프로그램")

	
2. HDInsight Spark 대시보드에서 **리소스 관리자** 탭을 클릭하고 **기본 응용 프로그램 코어 수** 및 **작업자 노드당 기본 실행자 메모리**에 대한 값을 지정합니다. 다른 속성을 0으로 설정합니다.

	![리소스 할당](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Allocate.Resources.png "응용 프로그램에 리소스 할당")

### Spark 클러스터와 함께 BI 도구를 사용하지 않습니다. 리소스를 다시 가져올 수 있나요? 

Thrift 서버 코어 개수 및 Thrift 서버 실행자 메모리를 0으로 지정합니다. 코어 또는 메모리가 할당되지 않은 상태에서 Thrift 서버가 **대기**상태로 전환됩니다.

![리소스 할당](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.No.Thrift.png "Thrift 서버에 리소스 없음")

##<a name="seealso"></a>참고 항목

* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)
* [HDInsight 클러스터에서 Spark 프로비전](hdinsight-apache-spark-provision-clusters.md)
* [BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [HDInsight에서 Spark를 사용하여 기계 학습 응용 프로그램 빌드](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=August15_HO7-->