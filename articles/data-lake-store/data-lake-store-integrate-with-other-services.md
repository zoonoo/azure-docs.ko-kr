<properties
   pageTitle="데이터 레이크 저장소를 다른 Azure 서비스와 통합 | Azure"
   description="데이터 레이크 저장소가 다른 Azure 서비스와 통합하는 방법을 이해합니다."
   documentationCenter=""
   services="data-lake-store" 
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# 데이터 레이크 저장소와 다른 Azure 서비스 통합

Azure 데이터 레이크 저장소를 다른 Azure 서비스와 함께 사용하면 광범위한 범위의 시나리오를 사용할 수 있습니다. 다음 문서에는 데이터 레이크 저장소와 통합될 수 있는 서비스가 나열되어 있습니다.

## Azure HDInsight에 데이터 레이크 저장소 사용

데이터 레이크 저장소를 사용하는 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 클러스터를 HDFS 규격 저장소로 프로비전할 수 있습니다. 이 릴리스의 Windows 및 Linux의 Storm 및 Hadoop 클러스터의 경우 데이터 레이크 저장소를 추가 저장소로만 사용할 수 있습니다. 이러한 클러스트는 여전히 Azure 저장소(WASB)를 기본 저장소로 사용합니다. 그러나 Windows 및 Linux의 HBase 클러스터의 경우에는 데이터 레이크 저장소를 기본 저장소나 추가 저장소 또는 둘 다로 사용할 수 있습니다.

데이터 레이크 저장소를 사용하는 HDInsight 클러스터를 프로비전하는 방법에 대한 지침은 다음을 참조하세요.

* [Azure 포털을 사용하여 데이터 레이크 저장소로 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure PowerShell을 사용하여 데이터 레이크 저장소를 사용하는 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-powershell.md)


## Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용

[Azure 데이터 레이크 분석](data-lake-analytics/data-lake-analytics-overview.md)을 사용하면 클라우드 규모에서 빅 데이터 작업을 수행할 수 있습니다. 이를 통해 동적으로 리소스를 프로비전할 뿐만 아니라 지원되는 많은 데이터 원본(예: 데이터 레이크 저장소)에 저장될 수 있는 테라바이트 또는 심지어 엑사바이트의 데이터를 분석할 수 있습니다. 데이터 레이크 분석은 Azure 데이터 레이크 저장소에서 작업할 때 특히 최적화되어 빅 데이터 작업에 대한 최상의 성능, 처리량 및 병렬화를 제공합니다.

데이터 레이크 분석에 데이터 레이크 저장소를 사용하는 방법에 대한 지침은 [데이터 레이크 저장소를 사용하여 데이터 레이크 분석 시작](data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.


## Azure Data Factory에 데이터 레이크 저장소 사용

[Azure Data Factory](https://azure.microsoft.com/services/data-factory/)를 사용하여 Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, Azure 저장소 Blob 및 온-프레미스 데이터베이스에서 데이터를 수집할 수 있습니다. Azure 에코시스템의 첫 번째 클래스가 된 Azure Data Factory를 사용하면 이러한 원본에서 Azure 데이터 레이크 저장소로 데이터를 수집하는 작업을 오케스트레이션할 수 있습니다.

Azure Data Factory에 데이터 레이크 저장소를 사용하는 방법에 대한 지침은 [Data Factory를 사용하여 데이터 레이크 저장소에서 데이터 이동](data-factory/data-factory-azure-datalake-connector.md)을 참조하세요.


## 참고 항목

- [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
- [포털을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)
- [PowerShell을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-powershell.md)  

<!---HONumber=AcomDC_1203_2015-->