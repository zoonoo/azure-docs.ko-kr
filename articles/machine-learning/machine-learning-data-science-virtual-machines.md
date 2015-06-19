<properties 
	pageTitle="Azure에서 데이터 과학용 가상 컴퓨터 또는 SQL Server 가상 컴퓨터 설정" 
	description="데이터 과학 가상 컴퓨터 설정" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Azure에서 데이터 과학용 가상 컴퓨터 또는 SQL Server 가상 컴퓨터 설정

여러 유형의 Azure 가상 컴퓨터를 프로비전하고 클라우드 기반 데이터 과학 환경의 일부로 사용하도록 구성할 수 있습니다. 사용할 가상 컴퓨터 유형에 대한 결정은 기계 학습으로 모델링할 데이터의 유형 및 양과 클라우드에서 해당 데이터의 대상에 따라 달라집니다. 이러한 결정을 내릴 때 고려할 질문에 대한 지침은 [Azure 기계 학습 데이터 과학 환경 계획](machine-learning-data-science-plan-your-environment.md)을 참조하세요. 여기에서는 두 가지 시나리오에 대해 다룹니다.

Azure VM 및 SQL 서비스 포함 Azure VM을 IPython Notebook 서버로 설정하는 방법을 설명하는 지침이 제공됩니다. 가상 컴퓨터는 Windows에서 실행되며 Azure 저장소 탐색기 및 AzCopy와 같은 지원 도구뿐만 아니라 데이터 과학 프로젝트에 유용한 기타 유틸리티로 구성됩니다. 예를 들어 Azure 저장소 탐색기와 AzCopy는 로컬 컴퓨터에서 Azure 저장소로 데이터를 업로드하거나 저장소에서 로컬 컴퓨터로 데이터를 다운로드하는 데 편리한 방법을 제공합니다. 다음 두 가지 지침이 제공됩니다.

* [데이터 과학용 Azure 가상 컴퓨터 설정](machine-learning-data-science-setup-virtual-machine.md)에서는 SQL이 아닌 Azure 저장소를 사용하여 데이터를 저장할 수 있는 경우 IPython Notebook 및 데이터 과학을 수행하는 데 사용되는 기타 도구와 함께 Azure 가상 컴퓨터를 프로비전하는 방법을 보여 줍니다. 

* [데이터 과학용 Azure SQL Server 가상 컴퓨터 설정](machine-learning-data-science-setup-sql-server-virtual-machine.md)에서는 SQL 데이터베이스에 데이터를 저장하는 경우 IPython Notebook 및 데이터 과학을 수행하는 데 사용되는 기타 도구와 함께 Azure SQL Server 가상 컴퓨터를 프로비전하는 방법을 보여 줍니다.

프로비전 및 구성한 후에는 데이터 탐색 및 처리, Azure 기계 학습 및 클라우드 데이터 과학 프로세스와 함께 수행할 다른 작업 등에 이러한 가상 컴퓨터를 IPython Notebook 서버로 사용할 준비가 완료됩니다. 이 프로세스에는 데이터를 HDInsight로 이동한 후 Azure 기계 학습에서 데이터를 통해 학습할 준비를 수행하면서 데이터를 처리 및 샘플링하는 단계가 포함될 수 있습니다.

* Azure Blob 저장소에서 HDInsight로 데이터를 이동하는 방법에 대한 자세한 내용은 [데이터를 만들어 Azure Blob 저장소에서 Hive 테이블로 로드](machine-learning-data-science-hive-tables.md)를 참조하세요.
* Hive 쿼리를 사용하여 HDInsight에서 데이터를 처리하는 방법에 대한 자세한 내용은 [클라우드 데이터 과학 프로세스에서 HDInsight Hadoop 클러스터로 Hive 쿼리 제출](machine-learning-data-science-hive-queries.md)을 참조하세요.
* HDInsight에서 데이터를 샘플링하는 방법에 대한 자세한 내용은 [Azure HDInsight Hive 테이블에서 데이터 샘플링](machine-learning-data-science-sample-data-hive.md)을 참조하세요.


> [AZURE.NOTE] Azure 가상 컴퓨터는 **종량제**로 비용이 청구됩니다. 가상 컴퓨터를 사용하지 않을 때 비용이 청구되지 않도록 하려면 [Azure 관리 포털](http://manage.windowsazure.com/)에서 **중지(할당 해제)** 상태에 있어야 합니다. 가상 컴퓨터를 할당 해제하는 방법 및 단계별 지침은 [사용하지 않을 때 가상 컴퓨터 종료 및 할당 해제](machine-learning-data-science-setup-virtual-machine.md#shutdown)를 참조하세요. 




<!--HONumber=49--> 