<properties
	pageTitle="Azure CLI를 사용하여 Hadoop 클러스터 관리 | Microsoft Azure"
	description="Azure CLI를 사용하여 HDInsight에서 Hadoop 클러스터를 관리하는 방법"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="jgao"/>

# Azure CLI를 사용하여 HDInsight의 Hadoop 클러스터 관리

[AZURE.INCLUDE [선택기](../../includes/hdinsight-portal-management-selector.md)]

[Azure 명령줄 인터페이스](xplat-cli-install.md)를 사용하여 Azure HDInsight의 Hadoop 클러스터를 관리하는 방법을 알아봅니다. Azure CLI는 Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다.

Azure CLI는 오픈 소스입니다. 소스 코드는 <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>의 GitHub에서 관리됩니다.

이 문서에서는 HDInsight을 통한 Azure CLI 사용에 대해서만 설명합니다. Azure CLI를 사용하는 방법에 대한 일반 가이드는 [Azure CLI 설치 및 구성][azure-command-line-tools]을 참조하세요.


##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **Azure CLI** - 설치 및 구성 정보는 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요.
- **Azure에 연결**. 다음 명령을 사용합니다.

		azure login

	회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](xplat-cli-connect.md)을 참조하세요.
	
- **Azure 리소스 관리자 모드로 전환**. 다음 명령을 사용합니다.

		azure config mode arm


##클러스터 만들기

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

HDInsight 클러스터를 만들려면 먼저 ARM(Azure 리소스 관리) 및 Azure Blob 저장소 계정이 있어야 합니다. HDInsight 클러스터를 만들려면 다음을 지정해야 합니다.

- **Azure 리소스 그룹**: Azure 리소스 그룹 내에서 데이터 레이크 분석 계정을 만들어야 합니다. Azure 리소스 관리자를 사용하면 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 

	구독 중인 리소스 그룹을 나열하려면:
	
		azure group list 
	
	새 리소스 그룹을 만들려면 다음을 수행합니다.
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight 클러스터 이름**

- **위치**: HDInsight 클러스터를 지원하는 Azure 데이터 센터 중 하나입니다. 지원되는 위치에 대한 목록은 [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

- **기본 저장소 계정** - HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정이 필요합니다.

	새 Azure 저장소 계정을 만들려면:
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE]저장소 계정은 데이터 센터에 HDInsight와 함께 배치되어야 합니다. ZRS는 테이블을 지원하지 않으므로 저장소 계정 유형은 ZRS일 수 없습니다.

	Azure Preview 포털을 사용하여 Azure 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제][azure-create-storageaccount]를 참조하세요.
	
	저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 명령을 사용하여 정보를 검색할 수 있습니다.
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Azure Preview 포털을 사용하여 정보를 얻는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제][azure-create-storageaccount]의 "저장소 액세스 키 보기, 복사 및 다시 생성" 섹션을 참조하세요.

- **(선택 사항) 기본 Blob 컨테이너**: **azure hdinsight cluster create** 명령은 컨테이너가 없는 경우 컨테이너를 만듭니다. 미리 컨테이너를 만들도록 선택하는 경우 다음 명령을 사용할 수 있습니다.

	azure storage container create --account-name "<Storage Account Name>" --account-key <StorageAccountKey> [ContainerName]

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<Storage Account Name>" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##구성 파일을 사용하여 클러스터 만들기
일반적으로 HDInsight 클러스터를 만들고 해당 클러스터에서 작업을 실행한 후에 비용을 줄이기 위해 클러스터를 삭제합니다. 명령줄 인터페이스에는 클러스터를 만들 때마다 다시 사용할 수 있도록 구성을 파일에 저장하는 옵션이 있습니다.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName ""<Storage Account Name>".blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName ""<Storage Account Name>".blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"

	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

	azure hdinsight cluster create --config <file>



![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##클러스터 세부 정보 나열 및 표시
클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][image-cli-clusterlisting]


##클러스터 삭제
클러스터를 삭제하려면 다음 명령을 사용합니다.

	azure hdinsight cluster delete <ClusterName>

##클러스터 크기 조정

Azure PowerShell을 사용하여 Hadoop 클러스터 크기를 변경하려면 클라이언트 컴퓨터에서 다음 명령을 실행합니다.

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>

##다음 단계
이 문서에서는 HDInsight 클러스터 관리 작업을 수행하는 여러 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure Preview 포털을 사용하여 HDInsight 관리][hdinsight-admin-portal]
* [Azure PowerShell을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-powershell]
* [Azure HDInsight 시작][hdinsight-get-started]
* [Azure CLI를 사용하는 방법][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "클러스터 나열 및 표시"

<!---HONumber=Nov15_HO2-->