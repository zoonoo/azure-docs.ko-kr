<properties 
	pageTitle="Azure 데이터 팩터리 사용에 대한 일반적인 시나리오" 
	description="이 문서에서는 Azure 데이터 팩터리 서비스 사용에 대한 몇 가지 일반적인 시나리오에 대해 알아봅니다." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 사용에 대한 일반적인 시나리오.
이 섹션에서는 Azure 데이터 팩터리에서 현재 지원하고 계속해서 허브 시나리오로 확장될 몇 가지 예제 시나리오에 대해 설명합니다.

> [AZURE.NOTE]이 섹션을 읽기 전에 [Azure 데이터 팩터리 소개][datafactory-introduction] 문서를 읽으세요.

## 시나리오 \#1: 데이터 허브에 대한 데이터 원본
![원본 데이터 허브][image-data-factory-introduction-secenario1-source-datahub]

기업에는 서로 다른 형식의 데이터가 서로 다른 원본에 있습니다. 따라서 정보 생산 시스템을 구축하는 첫 번째 단계에서는 필요한 모든 데이터 원본과 SaaS 서비스, 파일 공유, FTP, 웹 서비스 등과 같은 처리에 연결하고 후속 처리에 필요한 데이터를 이동합니다.

데이터 팩터리가 없으면 기업은 사용자 지정 데이터 이동 구성 요소를 구축하거나 사용자 지정 서비스를 작성하여 이러한 데이터 원본과 처리를 통합해야 합니다. 이 경우 비용이 많이 들고 이러한 서비스를 통합 및 관리하기 어려우며, 엔터프라이즈급 모니터링 및 경고 기능과 완전히 관리되는 서비스에서 제공할 수 있는 제어 기능이 부족한 경우가 많습니다.
  
Azure 데이터 팩터리를 사용하면 데이터 저장소 및 처리 서비스가 허브 컨테이너로 수집되므로 계산 및 저장소 작업이 용이해지고 최적화되며, 통합 리소스 사용 관리가 가능해지고, 필요에 따라 데이터 이동 서비스가 제공됩니다.

## 시나리오 \#2: 정보 생산 운영
운영 시나리오는 논리적으로 데이터 원본 지정 시나리오의 다음 단계입니다. 데이터가 허브에 있으면 이제 유지 관리할 수 있고 제어된 일정에 따라 변환된 데이터를 안정적으로 생성하여 프로덕션 환경에 신뢰할 수 있는 데이터를 공급하는 데이터 파이프라인을 작성하고 운영해야 합니다. Azure 데이터 팩터리의 데이터 변환은 Hadoop\(Azure HDInsight\)에서 실행되는 Hive, Pig 및 사용자 지정 C\# 처리를 통해 이루어집니다. 이러한 변환은 데이터를 지우고, 중요 데이터 필드를 표시하며, 매우 다양한 복잡한 방식으로 데이터에 대한 기타 작업을 수행하는 데 사용될 수 있습니다. 일반적으로 운영은 복잡하고 유지 관리하기가 어려운 인프라 및 사용자 지정 서비스를 통해 이루어지며, 그러한 솔루션의 구현, 관리, 확장, 문제 해결 및 버전 관리에 대한 많은 어려움을 야기합니다.
  
데이터 팩터리를 완전히 관리되는 서비스로 사용하면 사용자는 이러한 파이프라인을 일회성 일정이나 복잡한 반복 일정으로 정의하여 운영할 수 있고 오케스트레이션이 데이터 팩터리 서비스에 의해 직접 처리됩니다. 허브를 사용할 경우, 허브 내의 모든 데이터 및 처리에 대한 클러스터 관리가 사용자를 대신하여 처리되므로 사용자는 인프라 관리 대신 변형 분석에 집중할 수 있습니다. Azure 데이터 팩터리는 불안정한 사용자 지정 서비스를 사용할 경우에 발생하는 어려움을 제거하여 기업이 안정적이고 재현 가능한 방식으로 신뢰할 수 있는 정보를 생산할 수 있게 해줍니다.


## 시나리오 \#3: 정보 생산을 데이터 검색과 통합
기존의 BI 접근 방식 및 기술은 "신뢰할 수 있는 출처의 정보"를 제공했지만, 신중하게 제어되는 변경 요청 프로세스로 인해 요청이 항상 밀려 있는 심각한 부작용이 거의 항상 존재했습니다. 빠르게 변화하는 비즈니스 질문에 대처하려면 기업은 정보 생산 시스템을 정보 사용 시스템과 더 유연하게 연결할 수 있어야 합니다. Azure 데이터 팩터리는 정보 생산을 위해 이러한 시스템을 효율적인 데이터 파이프라인에 연결하는 문제를 해결하고 신뢰할 수 있는 최신 데이터를 쉽게 사용할 수 있는 형식으로 제공하여 정보 사용 문제를 해결하는 데 도움이 됩니다.
  
Azure 데이터 팩터리는 생성되는 데이터를 간단히 사용할 수 있도록 다음 기능을 지원합니다.

- Excel, Tableau 등의 기존 BI 도구에서 사용할 수 있도록 생성된 데이터 자산을 일회성 또는 예약된 일정에 따라 관계형 데이터 마트로 간단히 이동합니다.
- 데이터 팩터리에서 생성된 데이터 자산을 Excel의 파워 쿼리에서 직접 사용합니다.

파워 쿼리에서 데이터를 사용하려면 다음 항목을 참조하세요.

- [파워 쿼리: Microsoft Azure 테이블 저장소에 연결][Power-Query-Azure-Table]
- [파워 쿼리: Microsoft Azure Blob 저장소에 연결][Power-Query-Azure-Blob]
- [파워 쿼리: Microsoft Azure SQL 데이터베이스에 연결][Power-Query-Azure-SQL]
- [파워 쿼리: Microsoft 온-프레미스 SQL Server에 연결][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png




<!--HONumber=54-->