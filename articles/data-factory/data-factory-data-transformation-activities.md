<properties 
	pageTitle="데이터 변환 작업 | Microsoft Azure" 
	description="Azure Data Factory 서비스를 사용하여 데이터를 변환하고 분석하는 방법을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="spelluru"/>

# Azure Data Factory를 사용한 분석 및 변환

## 개요
Azure Data Factory의 변환 작업을 통해 원시 데이터를 변환 및 처리하여 예측 가능한, 통찰력 있는 정보로 만듭니다. 변환 작업은 Azure HDInsight 클러스터나 Azure Batch와 같은 컴퓨팅 환경에서 실행됩니다. Azure Data Factory는 개별적 또는 다른 작업과 연계하여 [파이프라인](data-factory-create-pipelines.md)에 추가할 수 있는 다음과 같은 변환 작업을 지원합니다.


변환 작업 | 컴퓨팅 환경 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop] [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [기계 학습 활동: 배치 실행 및 리소스 업데이트](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[저장 프로시저](data-factory-stored-proc-activity.md) | Azure SQL |
[데이터 레이크 분석 U-SQL](data-factory-usql-activity.md) | Azure 데이터 레이크 분석 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] 또는 Azure Batch
   

컴퓨팅 환경을 위한 연결된 서비스를 만들고 변환 작업을 정의할 때 이 연결된 서비스를 사용해야 합니다. 데이터 팩터리에서 지원하는 컴퓨팅 환경은 두 가지 유형이 있습니다.

1. **주문형**: 이 경우 데이터 팩터리에서 완전히 컴퓨팅 환경을 관리합니다. 데이터를 처리하기 위한 작업을 제출하기 전에 데이터 팩터리 서비스에서 자동으로 컴퓨팅 환경을 만들고 작업이 완료되면 제거합니다. 사용자는 작업 실행, 클러스터 관리, 부트스트래핑 작업에 대한 주문형 컴퓨팅 환경의 세부적인 설정을 구성 및 제어할 수 있습니다. 
2. **자체 환경 사용**: 이 경우 사용자 고유의 컴퓨팅 환경(예: HDInsight 클러스터)을 데이터 팩터리에 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 이를 사용하여 작업을 실행하는 데이터 팩터리 서비스와 사용자에 의해 관리됩니다. 

데이터 팩터리에서 지원하는 계산 연결된 서비스에 대한 자세한 내용은 [계산 연결된 서비스](data-factory-compute-linked-services.md) 문서를 참조하세요.

<!---HONumber=Nov15_HO3-->