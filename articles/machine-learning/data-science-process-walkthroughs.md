---
title: "팀 데이터 과학 프로세스 연습 | Microsoft 문서"
description: "연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fde069560d53c5b5010bbb59b52d60a6971d8dac
ms.lasthandoff: 11/17/2016


---
# <a name="team-data-science-process-walkthroughs"></a>팀 데이터 과학 프로세스 연습
여기에서 항목별로 구분된 종단 간 연습에서는 **특정 시나리오**에 대한 팀 데이터 과학 프로세스의 단계를 보여줍니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다.

## <a name="use-sql-data-warehouse"></a>SQL 데이터 웨어하우스 사용
[작업 중인 데이터 과학 프로세스: SQL 데이터 웨어하우스 사용](machine-learning-data-science-process-sqldw-walkthrough.md) 연습에서는 공개적으로 사용 가능한 NYC Taxi Trip 및 요금 데이터 집합에 대한 SQL DW(SQL 데이터 웨어하우스)를 사용하여 기계 학습 분류 및 회귀 모델을 구축하고 배포하는 방법을 보여 줍니다.

## <a name="use-sql-server"></a>SQL Server 사용
[실행 중인 팀 데이터 과학 프로세스: SQL Server 사용](machine-learning-data-science-process-sql-walkthrough.md) 연습은 SQL Server와 공개적으로 제공되는 NYC Taxi Trips 및 요금 데이터 집합을 사용하여 기계 학습 모델 및 회기 모델을 구축하고 배포하는 방법을 보여 줍니다.

## <a name="use-hdinsight-hadoop-clusters"></a>HDInsight Hadoop 클러스터 사용
[실행 중인 팀 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용](machine-learning-data-science-process-hive-walkthrough.md) 연습에서는 [Azure HDInsight Hadoop 클러스터](https://azure.microsoft.com/services/hdinsight/)를 사용하여 공개적으로 사용 가능한 NYC Taxi Trips 및 요금 데이터 집합에서 데이터를 저장, 탐색 및 기능 엔지니어링합니다.

## <a name="use-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>1TB 데이터 집합에서 Azure HDInsight Hadoop 클러스터 사용
[실행 중인 팀 데이터 과학 프로세스 - 1TB 데이터 집합에서 Azure HDInsight Hadoop 클러스터 사용](machine-learning-data-science-process-hive-criteo-walkthrough.md) 연습에서는 [Azure HDInsight Hadoop 클러스터](https://azure.microsoft.com/services/hdinsight/)를 사용하여 공개적으로 사용 가능한 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 데이터 집합 중 하나에서 데이터를 저장, 탐색, 기능 엔지니어링 및 다운 샘플링하는 종단 간 시나리오를 보여 줍니다.

## <a name="data-science-using-python-with-spark-on-azure"></a>Azure에서 Spark와 함께 Python을 사용하는 데이터 과학
[Azure HDInsight에서 Spark를 사용하는 데이터 과학](machine-learning-data-science-spark-overview.md) 연습에서는 [Azure HDInsight Spark 클러스터](https://azure.microsoft.com/services/hdinsight/)를 사용하는 종단 간 시나리오에서 팀 데이터 과학 프로세스를 사용하여 공개적으로 사용 가능한 NYC Taxi Trips 및 요금 데이터 집합에서 데이터를 저장, 탐색 및 기능 엔지니어링합니다. 

## <a name="data-science-using-scala-with-spark-on-azure"></a>Azure에서 Spark를 사용하는 데이터 과학
[Azure에서 Spark와 함께 Scala를 사용하는 데이터 과학](machine-learning-data-science-process-scala-walkthrough.md) 연습에서는 Azure HDInsight Spark 클러스터에서 Spark 확장성 있는 기계 학습 라이브러리(MLlib) 및 SparkML 패키지를 사용하여 감독 기계 학습 작업에 대해 Scala를 사용하는 방법을 보여 줍니다. [데이터 과학 프로세스](http://aka.ms/datascienceprocess): 데이터 수집 및 탐색, 시각화, 기능 엔지니어링, 모델링, 모델 사용으로 이루어진 작업을 단계별로 안내합니다. 작성된 모델은 로지스틱 및 선형 회귀, 임의 포리스트 및 그라데이션 향상된 트리를 포함합니다.

## <a name="use-azure-data-lake-storage-and-analytics"></a>Azure Data Lake 저장소 및 분석 사용
[Azure Data Lake에서 확장성 있는 데이터 과학: 종단 간 연습](machine-learning-data-science-process-data-lake-walkthrough.md)에서는 고객이 팁을 지불할지 여부를 예측하기 위해 NYC Taxi 데이터 집합 샘플에서 데이터 탐색 및 이진 분류 작업을 수행하는 데 Azure Data Lake를 사용하는 방법을 보여 줍니다. 

## <a name="use-r-with-sql-server-r-services"></a>SQL Server R 서비스와 함께 R 사용
[SQL Server R 서비스를 사용하는 데이터 과학 종단 간 연습](https://msdn.microsoft.com/library/mt612857.aspx) 연습에서는 R 코드, SQL Server 데이터 및 사용자 지정 SQL 함수를 조합한 데이터 과학자를 제공하여 SQL Server에 R 모델을 구축하고 배포합니다.

## <a name="use-t-sql-with-sql-server-r-services"></a>SQL Server R 서비스와 함께 T-SQL 사용
[SQL 개발자를 위한 데이터베이스 내 고급 분석](https://msdn.microsoft.com/library/mt683480.aspx) 연습은 R 솔루션을 작동시키는 SQL Server R 서비스를 사용하여 Transact-SQL과 함께 고급 분석 솔루션을 구축하는 경험을 SQL 프로그래머에게 제공합니다.

## <a name="whats-next"></a>다음 작업
Azure에서 데이터 과학 프로세스를 구성하는 작업을 안내하는 토픽에 대한 개요는 [데이터 과학 프로세스](http://aka.ms/datascienceprocess)를 참조하세요. 


