<properties
   	pageTitle="HDInsight에서 Linux에 Hadoop 클러스터 프로비전 | Microsoft Azure"
   	description="관리 포털, 명령줄 및.NET SDK를 사용하여 Linux 기반 HDInsight의 Hadoop 클러스터를 프로비전하는 방법을 알아봅니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="08/07/2015"
   	ms.author="nitinme"/>


#사용자 지정 옵션을 사용하여 HDInsight의 Hadoop Linux 클러스터 프로비전(미리 보기)

이 문서에서는 Azure 포털, Azure PowerShell, Azure CLI 또는 HDInsight .NET SDK를 사용하여 Azure HDInsight에서 사용자 지정 방식으로 Hadoop Linux 클러스터를 프로비전하는 다양한 방법을 알아봅니다.

## HDInsight 클러스터의 정의

Hadoop 또는 빅 데이터에 대해 이야기할 때마다 클러스터를 언급한 이유가 궁금하셨나요? 그것은 바로 Hadoop을 사용하면 여러 다른 클러스터 노드에 분산된 대규모 데이터를 처리할 수 있기 때문입니다. 클러스터는 하나의 마스터(헤드 노드 또는 이름 노드라고도 함)와 개수에 관계없는 작업자 노드(데이터 노드라고도 함)가 있는 마스터/작업자 아키텍처로 구성됩니다. 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>을 참조하세요.

![HDInsight 클러스터][img-hdi-cluster]


HDInsight 클러스터는 Hadoop 구현 세부 정보를 추출하므로 여러 다른 클러스터 노드와 통신하는 방법을 걱정할 필요가 없습니다. HDInsight 클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 자세한 내용은 [HDInsight의 Hadoop 소개](hdinsight-hadoop-introduction.md)(영문)를 참조하세요. 변동되는 데이터는 Azure Blob 저장소에 저장됩니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md)을 참조하세요.


이 문서에서는 클러스터를 프로비전하는 여러 다른 방법에 대한 지침을 제공합니다. 클러스터를 프로비전하는 빠른 시작 접근 방법을 살펴보려면 [Linux에서 Azure HDInsight 시작](../hdinsight-hadoop-linux-get-started.md)을 참조하세요.

**필수 구성 요소**

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **SSH(보안 셸) 키**. 암호 대신 키가 있는 SSH를 사용하여 Linux 클러스터에 원격으로 연결하려는 경우 키를 사용하는 방법이 더 안전하므로 권장됩니다. SSH 키를 생성하는 방법에 대한 지침은 다음 문서를 참조하세요.
	-  Linux 컴퓨터 - [Linux, Unix 또는 OS X에서 Linux 기반 HDInsight(Hadoop)와 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	-  Windows 컴퓨터 - [Windows에서 Linux 기반 HDInsight(Hadoop)와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a id="configuration"></a>구성 옵션

### Linux 기반 클러스터

HDInsight는 Azure에서 Linux 클러스터를 프로비전의 옵션을 제공합니다. Linux 기반으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합 또는 Linux 또는 Unix를 사용하고 기존 Hadoop Linux 기반 솔루션에서 마이그레이션에 익숙한 경우에 Linux 클러스터를 프로비전합니다. Linux의 Azure HDInsight에 대한 자세한 내용은 [HDInsight의 Hadoop 소개](hdinsight-hadoop-introduction.md)를 참조하세요


### 저장소 추가

구성 중에는 Azure Blob 저장소 계정과 기본 컨테이너를 지정해야 합니다. 이 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요한 경우 역시 클러스터에 연결되는 추가 Blob도 지정할 수 있습니다.


보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md)을 참조하세요.


### 메타 저장소

Metastore는 Hive 테이블, 파티션, 스키마, 열 등에 대한 정보를 포함합니다. 이 정보는 Hive가 HDFS(Hadoop Distributed File System) 또는 HDInsight용 Azure Blob 저장소에서 데이터가 저장된 위치를 찾는 데 사용됩니다. Hive는 기본적으로 포함된 데이터베이스를 사용하여 이 정보를 저장합니다.

HDInsight 클러스터를 프로비전할 때는 Hive용 Metastore를 포함할 SQL 데이터베이스를 지정할 수 있습니다. 그러면 메타데이터 정보가 SQL 데이터베이스에 외부 저장되므로 클러스터를 삭제해도 해당 정보를 보존할 수 있습니다. Azure에서 SQL 데이터베이스를 만드는 방법에 대한 자세한 내용은 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요.


## <a id="options"></a> HDInsight Linux 클러스터 프로비전에 대한 옵션

Windows 기반 컴퓨터와 Linux 컴퓨터에서 HDInsight Hadoop Linux 클러스터를 프로비전할 수 있습니다. 다음 표에서 서로 다른 운영 체제에서 사용할 수 있는 프로비전 옵션에 대한 정보를 제공하고 각각에 대한 지침을 연결합니다.

다음 운영 체제를 실행하는 컴퓨터에서 LINUX 클러스터 프로비전| Azure 포털 사용 | Azure PowerShell 사용 | .NET SDK 사용
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| [여기](#portal)를 클릭 | 해당 없음 | 해당 없음
Windows | [여기](#portal)를 클릭 | [여기](#powershell)를 클릭 | [여기](#sdk)를 클릭

### <a id="portal"></a> Azure 포털 사용

HDInsight 클러스터는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 동일한 데이터 센터에 위치한 Azure 저장소 계정이 필요합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md)을 참조하세요. Azure 저장소 계정 만들기에 대한 자세한 내용은 [저장소 계정을 만드는 방법](../storage-create-storage-account.md)을 참조하세요.


> [AZURE.NOTE]현재 **동남아시아**, **북유럽**, **서유럽** 및 **미국 남중부** 지역에서만 HDInsight Linux 클러스터를 호스트할 수 있습니다.

**HDInsight 클러스터를 만들려면**

1. [Azure 미리 보기 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure Preview 포털에서 새 클러스터 만들기](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Azure Preview 포털에서 새 클러스터 만들기")

3. **클러스터 이름**을 입력하고 **클러스터 유형**으로 **Hadoop**을 선택하고 **클러스터 운영 체제** 드롭다운에서 **Ubuntu**를 선택합니다. 클러스터 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.

	![클러스터 이름 및 유형 입력](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "클러스터 이름 및 유형 입력")

4. 둘 이상의 구독이 있는 경우 **구독** 항목을 클릭하여 클러스터에 사용할 Azure 구독을 선택합니다.

5. **리소스 그룹**을 클릭하여 기존 리소스 그룹 목록을 표시한 다음 클러스터를 만들 리소스 그룹을 선택합니다. 또는 **새로 만들기**를 클릭한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

	> [AZURE.NOTE]사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. **자격 증명**을 클릭한 다음 관리 사용자의 암호를 입력합니다. **SSH 사용자 이름**과 **암호** 또는 **공개 키**(SSH 사용자를 인증하는 데 사용됨)도 입력해야 합니다. 공개 키를 사용하는 것이 권장 방식입니다. 아래쪽의 **선택**을 클릭하여 자격 증명 구성을 저장합니다.

	![클러스터 자격 증명 제공](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "클러스터 자격 증명 제공")

	HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)


7. **데이터 원본**을 클릭하여 클러스터의 기존 데이터 원본을 선택하거나 새로 만듭니다.

	![데이터 원본 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "데이터 원본 구성 제공")

	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 **데이터 원본** 블레이드의 항목을 이해합니다.

	- **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **From all subscriptions(모든 구독에서)**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **액세스 키**를 입력하려면 이 항목을 **액세스 키**로 설정합니다.

	- **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 나타나는 필드를 사용하여 저장소 계정의 이름을 입력합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.

	- **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.

	- **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지역입니다.

		> [AZURE.IMPORTANT]기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.

	**선택**을 클릭하여 데이터 원본 구성을 저장합니다.

8. **노드 가격 책정 계층**을 클릭하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "클러스터 노드 수 지정")

	**선택**을 클릭하여 노드 가격 책정 구성을 저장합니다.

9. **옵션 구성**을 클릭하여 클러스터 버전을 선택하고 **가상 네트워크**에 가입, Hive 및 Oozie의 데이터를 유지하기 위한 **외부 Metastore** 설정 등 기타 선택적 설정을 구성하고 스크립트 동작을 사용하여 사용자 지정 구성 요소를 설치하기 위해 클러스터를 사용자 지정하거나 클러스터에 추가 저장소 계정을 사용합니다.

	* **HDInsight 버전** 드롭다운을 클릭하고 클러스터에 사용할 버전을 선택합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

	* **외부 Metastore**를 클릭하여 클러스터와 연결된 Hive 및 Oozie 메타데이터를 저장하는 데 사용할 SQL 데이터베이스를 지정합니다.

		![사용자 지정 Metastore 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "외부 Metastore 지정")

		**Use an existing SQL DB for Hive metadata(Hive 메타데이터에 기존 SQL DB 사용)**에 대해 **예**를 클릭하고 SQL 데이터베이스를 선택한 다음 데이터베이스의 사용자 이름/암호를 입력합니다. **Use an existing SQL DB for Oozie metadata(Oozie 메타데이터에 기존 SQL DB 사용)**를 원하는 경우 이러한 단계를 반복합니다. **옵션 구성** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.

		>[AZURE.NOTE]메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

	* **Azure 저장소 키**를 클릭하여 클러스터와 연결할 추가 저장소 계정을 지정합니다. **Azure 저장소 키** 블레이드에서 **저장소 키 추가**를 클릭한 다음 기존 저장소 계정을 선택하거나 새 계정을 만듭니다.

		![추가 저장소 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "추가 저장소 계정 지정")

		**새 HDInsight 클러스터** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.

10. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 클릭합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 프로비전 중임을 나타내고 프로비전이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

	| 프로비전 중 | 프로비전 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 프로비전](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![프로비전된 클러스터 타일](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]클러스터를 만드는 데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.

11. 프로비전이 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다. 클러스터 블레이드는 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 필수 정보를 제공합니다.

	![클러스터 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "클러스터 속성")

	다음을 사용하여 이 블레이드의 위쪽과 **필수** 섹션에 있는 아이콘을 이해합니다.

	* **설정** 및 **모든 설정**: 클러스터의 자세한 구정 정보에 액세스할 수 있는 클러스터의 **설정** 블레이드를 표시합니다.

	* **대시보드**, **클러스터 대시보드** 및 **URL**: 이러한 항목을 통해 클러스터에서 작업을 실행하기 위한 웹 포털인 클러스터 대시보드에 액세스할 수 있습니다.

	* **보안 셸**: SSH를 사용하여 클러스터에 액세스하는 데 필요한 정보입니다.

	* **삭제**: HDInsight 클러스터를 삭제합니다.

	* **빠른 시작**(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)): HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.

	* **사용자**(![사용자 아이콘](./media/hdinsight-hadoop-provision-linux-clusters/users.png)): Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.

		> [AZURE.IMPORTANT]이는 _오직_ Azure Preview 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.

	* **태그**(![태그 아이콘](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)): 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.



### <a id="powershell"></a> Azure PowerShell 사용
Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 섹션에서는 Azure PowerShell을 사용하여 HDInsight 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. HDInsight Windows PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../install-configure-powershell.md)을 참조하세요. HDInsight에서 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)를 참조하세요. HDInsight Windows PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조](https://msdn.microsoft.com/library/azure/dn858087.aspx)를 참조하세요.


Azure PowerShell을 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

- Azure 리소스 그룹 만들기
- Azure 저장소 계정 만들기
- Azure Blob 컨테이너 만들기
- HDInsight 클러스터 만들기

Linux 클러스터를 프로비전하기 위해 설정해야 하는 두 가지 중요한 매개 변수는 OS 종류를 지정하는 매개 변수와 SSH 사용자 정보를 지정하는 매개 변수입니다.

- **-OSType** 매개 변수는 **Linux**로 지정해야 합니다.
- 클러스터의 원격 세션에 SSH를 사용하려면 SSH 사용자 암호 또는 SSH 공개 키를 지정하면 됩니다. SSH 사용자 암호와 SSH 공개 키를 둘 다 지정한 경우에는 키가 무시됩니다. 원격 세션에 SSH 키를 사용하려면 SSH 암호를 묻는 메시지가 나타날 때 빈 SSH 암호를 지정해야 합니다. HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)


			# Use the new Azure Resource Manager mode
			Switch-AzureMode AzureResourceManager

			###########################################
			# Create required items, if none exist
			###########################################

			# Sign in
			Add-AzureAccount

			# Select the subscription to use
			$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
			Select-AzureSubscription -SubscriptionName $subscriptionName

			# Register your subscription to use HDInsight
			Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

			# Create an Azure Resource Group
			$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
			$location = "<Location>"                        # For example, "West US"
			New-AzureResourceGroup -Name $resourceGroupName -Location $location

			# Create an Azure Storage account
			$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
			New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

			# Create an Azure Blob Storage container
			$containerName = "<ContainerName>"              # Provide a container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
			$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
			New-AzureStorageContainer -Name $containerName -Context $destContext

			###########################################
			# Create an HDInsight Cluster
			###########################################

			# Skip these variables if you just created them
			$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
			$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
			$containerName = "<ContainerName>"              # Provide the container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

			# Set these variables
			$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
			$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
			$credentials = Get-Credential
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]**$clusterCredentials**에 대해 지정한 값은 클러스터의 Hadoop 사용자 계정을 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에 연결합니다. **$sshCredentials**에 대해 지정한 값은 클러스터의 SSH 사용자를 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에서 원격 SSH 세션을 시작하고 작업을 실행합니다. Azure 포털에서 빠른 생성 옵션을 사용하여 클러스터를 프로비전하는 경우 기본 Hadoop 사용자 이름을 "admin"이고 기본 SSH 사용자 이름은 "hdiuser"입니다.

클러스터 프로비전을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

![HDI.CLI.Provision][image-hdi-ps-provision]

###<a id="sdk"></a> HDInsight .NET SDK 사용
HDInsight .NET SDK는 .NET Framework 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 아래 지침에 따라 Visual Studio 콘솔 응용 프로그램을 만들고 클러스터를 만들기 위한 코드를 붙여넣으세요.

**Visual Studio 콘솔 응용 프로그램을 만들려면**

1. Visual Studio 2013을 엽니다.

2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">속성</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">값</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **Nuget 패키지 관리자**를 클릭한 다음 **솔루션의 Nuget 패키지 관리**를 클릭합니다. 대화 상자 내의 검색 텍스트 상자에서 **HDInsight**를 검색합니다. 표시되는 결과에서 다음을 설치합니다.

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Azure 인증을 검색하고 표시되는 결과에서 **Microsoft.Azure.Common.Authentication**을 설치합니다.

6. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 열고 다음 코드를 붙여넣은 후 변수의 값을 제공합니다.


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Linux;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";
				private const string NewClusterSshUserName = "sshuser";
        		private const string NewClusterSshPublicKey = "<ssh public key>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
		        }

		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                Location = NewClusterLocation,
		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType,
						SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. **F5** 키를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용 프로그램의 상태가 표시되며 또한 Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. HDInsight 클러스터를 만들려면 몇 분정도 걸릴 수 있습니다.



##<a id="nextsteps"></a> 다음 단계
이 문서에서는 Linux에서 HDInsight Hadoop 클러스터를 프로비전하는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하십시오.

- [Linux에서 HDInsight 작업](hdinsight-hadoop-linux-information.md): Linux에서 HDInsight 클러스터 관련 작업의 미묘한 차이를 알 수 있습니다.
- [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md): Ambari 웹 또는 Ambari REST API를 사용하여 HDInsight 클러스터의 Linux 기반 Hadoop을 모니터링하고 관리하는 방법에 대해 알아봅니다.
- [HDInsight에서 MapReduce 사용](hdinsight-use-mapreduce.md): 클러스터에서 MapReduce 작업을 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Hive 사용](hdinsight-use-hive.md): 클러스터에서 Hive 쿼리를 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Pig 사용](hdinsight-use-pig.md): 클러스터에서 Pig 작업을 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md): HDInsight가 HDInsight 클러스터에서 데이터를 저장하는데 Azure Blob 저장소를 사용하는 방법에 대해 알아봅니다.
- [HDInsight에 데이터 업로드](hdinsight-upload-data.md): HDInsight 클러스터에서 Azure Blob 저장소에 저장된 데이터로 작업하는 방법에 대해 알아봅니다.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "클러스터 나열 및 표시"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "HDInsight에서 Sqoop 사용"

<!---HONumber=August15_HO8-->