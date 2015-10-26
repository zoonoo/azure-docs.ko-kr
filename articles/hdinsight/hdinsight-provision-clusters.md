<properties
   pageTitle="HDInsight의 Hadoop 클러스터 만들기 | Microsoft Azure"
   	description="Azure Preview 포털, Azure PowerShell, 명령줄 또는 .NET SDK를 사용하여 Azure HDInsight용 클러스터를 만드는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/29/2015"
   ms.author="jgao"/>

# HDInsight에서 Hadoop 클러스터 만들기

HDInsight 클러스터를 만드는 계획을 세우는 방법에 대해 알아봅니다.

[AZURE.INCLUDE [선택기](../../includes/hdinsight-portal-management-selector.md)]

* [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters-v1.md)

**필수 조건:**

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.


## 기본 구성 옵션


- **클러스터 이름**

	클러스터 이름은 클러스터를 식별하는 데 사용됩니다. 클러스터 이름은 다음 지침을 따라야 합니다.

	- 이 필드는 3~63자 사이의 문자열이어야 합니다.
	- 이 필드에는 문자, 숫자 및 하이픈만 포함할 수 있습니다.

- **구독 이름**

	하나의 HDInsight 클러스터는 하나의 Azure 구독에 연결됩니다.

- **리소스 그룹 이름**

	응용 프로그램은 일반적으로 웹앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. ARM(Azure 리소스 관리자)을 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션와 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하세요.	
- **운영 체제**

	다음 두 운영 체제 중 하나에서 HDInsight 클러스터를 만들 수 있습니다. - **Windows의 HDInsight(Windows Server 2012 R2 Datacenter)** - **Linux의 HDInsight(Linux용 Ubuntu 12.04 LTS) (미리 보기)** HDInsight는 Azure에서 Linux 클러스터를 구성하는 옵션을 제공합니다. Linux 기반으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합 또는 Linux 또는 Unix를 사용하고 기존 Hadoop Linux 기반 솔루션에서 마이그레이션에 익숙한 경우에 Linux 클러스터를 구성합니다. 자세한 내용은 [Linux 기반 HDInsight에서 Hadoop 시작](hdinsight-hadoop-linux-get-started.md)을 참조하세요.


- **HDInsight 버전**

	이 클러스터에서 사용할 HDInsight 버전을 결정하는 데 사용됩니다. 자세한 내용은 [HDInsight의 Hadoop 클러스터 버전 및 구성 요소](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)를 참조하세요.

- **클러스터 유형** 및 **클러스터 크기(데이터 노드라고도 함)**

	HDInsight를 사용하면 고객이 서로 다른 데이터 분석 작업을 위한 다양한 클러스터 형식을 배포할 수 있습니다. 현재 제공되는 클러스터 형식은 다음과 같습니다.

	- Hadoop 클러스터: 쿼리 및 분석 작업용
	- HBase 클러스터: NoSQL 작업용
	- Storm 클러스터: 실시간 이벤트 처리 작업용
	- Spark 클러스터(미리 보기): 메모리 내 처리, 대화형 쿼리, 스트림 및 기계 학습 작업용

	![HDInsight 클러스터](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]*Azure HDInsight 클러스터*는 *HDInsight의 Hadoop 클러스터* 또는 *HDInsight 클러스터*라고도 합니다. 경우에 따라 *Hadoop 클러스터*로도 사용됩니다. 이들은 모두 Microsoft Azure 환경에서 호스트되는 Hadoop 클러스터를 말합니다.

	지정된 클러스터 형식 내의 다양한 노드에 대해 다양한 역할이 있으므로 고객이 지정된 역할에서 작업 세부 정보에 적절하게 노드 크기를 조정할 수 있습니다. 예를 들어 수행된 분석의 형식이 많은 양의 메모리 집약적인 경우 Hadoop 클러스터는 많은 양의 메모리로 만든 작업자 노드가 있을 수 있습니다.

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	HDInsight용 Hadoop 클러스터는 두 가지 역할로 배포됩니다.

	- 헤드 노드(노드 2개)
	- 데이터 노드(노드 1개 이상)

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HDInsight용 HBase 클러스터는 세 가지 역할로 배포됩니다. - 헤드 서버(노드 2개) - 지역 서버(노드 1개 이상) - 마스터/Zookeeper 노드(노드 3개)

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	HDInsight용 Storm 클러스터는 세 가지 역할로 배포됩니다. - Nimbus 노드(노드 2개) - 감독자 서버(노드 1개 이상) - Zookeeper 노드(노드 3개)


	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	HDInsight용 Spark 클러스터는 세 가지 역할로 배포됩니다. - 헤드 노드(노드 2개) - 작업자 노드(노드 1개 이상) - Zookeeper 노드(노드 3개) (A1 Zookeeper의 경우 무료)

	고객은 클러스터의 수명 기간 동안 해당 노드의 사용량에 대한 대금이 청구됩니다. 대금 청구는 클러스터를 만들면 시작되고 클러스터가 삭제되면 중지됩니다(클러스터를 할당 해제하거나 보류할 수 없음). 클러스터 크기는 클러스터 가격에 영향을 줍니다. 학습 목적인 경우는 데이터 노드를 하나 사용하는 것이 좋습니다. HDInsight 가격에 대한 자세한 내용은 [HDInsight 가격 책정](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)을 참조하세요.


	>[AZURE.NOTE]클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의하세요.

- **지역/가상 네트워크(위치라고도 함)**

	![Azure 지역](./media/hdinsight-provision-clusters/Azure.regions.png)

	지원되는 지역 목록은 [HDInsight 가격 책정](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)에서 **지역** 드롭다운 목록을 클릭하세요.

- **노드 크기**

	![HDInsight VM 노드 크기](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	노드의 VM 크기를 선택합니다. 자세한 내용은 [클라우드 서비스에 적합한 크기](cloud-services-sizes-specs.md)를 참조하세요.

	선택한 VM에 따라 비용이 달라질 수 있습니다. HDInsight에서는 클러스터 노드에 모든 표준 계층 VM을 사용합니다. VM 크기가 가격에 미치는 영향에 대한 자세한 내용은 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 가격</a>을 참조하세요.


- **HDInsight 사용자**

	HDInsight 클러스터를 사용하면 프로비전 중에 두 개의 사용자 계정을 구성할 수 있습니다.

	- HTTP 사용자. 기본 사용자 이름은 Azure Preview 포털에서 기본 구성을 사용하는 admin입니다.
	- RDP 사용자(Windows 클러스터): RDP를 사용하여 클러스터에 연결하는 데 사용됩니다. 계정을 만들 때 만료 날짜는 오늘부터 90일 이내로 설정해야 합니다.
	- SSH 사용자(Linux 클러스터): SSH를 사용하여 클러스터에 연결하는 데 사용됩니다. [Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)의 단계에 따라 클러스터를 만든 후 추가 SSH 사용자 계정을 만들 수 있습니다.



- **Azure 저장소 계정**

	원래 HDFS는 클러스터에 있는 많은 로컬 디스크를 사용합니다. HDInsight는 데이터 저장소로 Azure Blob 저장소를 대신 사용합니다. Azure Blob 저장소는 HDInsight와 매끄럽게 통합되는 강력한 범용 저장소 솔루션입니다. HDFS(Hadoop Distributed File System) 인터페이스를 통해 HDInsight의 전체 구성 요소 집합을 Blob 저장소에서 구조적 또는 비구조적 데이터에 대해 직접 작동할 수 있습니다. Blob 저장소에 데이터를 저장하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

	구성 중에 Azure 저장소 계정과 해당 Azure 저장소 계정의 Azure Blob 저장소 컨테이너를 지정해야 합니다. 일부 생성 프로세스는 Azure 저장소 계정 및 Blob 저장소 컨테이너를 미리 만들어 두어야 합니다. Blob 저장소 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요에 따라 클러스터에서 액세스할 수 있는 추가 Azure 저장소 계정(연결된 저장소)을 지정할 수 있습니다. 또한 클러스터는 전체 공용 읽기 액세스 또는 Blob 전용 공용 읽기 액세스로 구성된 모든 Blob 컨테이너에 액세스할 수도 있습니다. 액세스 제한에 대한 자세한 내용은 [Azure 저장소 리소스에 대한 액세스 관리](storage-manage-access-to-resources.md)를 참조하세요.

	![HDInsight 저장소](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Blob 저장소 컨테이너는 이미지에 나온 것처럼 Blob 집합 그룹화를 제공합니다.

	![Azure Blob 저장소](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]여러 클러스터에서 하나의 Blob 저장소 컨테이너를 공유하지 마세요. 이 기능은 지원되지 않습니다.

	보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-use-blob-storage.md)을 참조하세요.

- **Hive/Oozie Metastore**

	Metastore는 Hive 테이블, 파티션, 스키마, 열 등 Hive 및 Oozie 메타데이터에 대한 정보를 포함합니다. Metastore를 사용하면 Hive 및 Oozie 메타데이터를 보존할 수 있으므로 새 클러스터를 만들 때 Hive 테이블 또는 Oozie 작업을 다시 만들 필요가 없습니다. 기본적으로 Hive는 포함된 Azure SQL 데이터베이스를 사용하여 이 정보를 저장합니다. 포함된 데이터베이스에서는 클러스터가 삭제된 경우 메타데이터를 유지할 수 없습니다. 예를 들어, Hive Metastore로 만든 클러스터가 있습니다. 몇 개의 Hive 테이블을 만들었습니다. 클러스터를 삭제하고 동일한 Hive Metastore를 사용하여 클러스터를 다시 만든 후, 원래 클러스터에서 만든 Hive 테이블을 볼 수 있습니다.

## 고급 구성 옵션

>[AZURE.NOTE]이 섹션은 현재 Windows 기본 HDInsight 클러스터에만 적용됩니다.

### HDInsight 클러스터 사용자 지정을 사용하여 클러스터 사용자 지정

경우에 따라 구성 파일을 구성해야 할 수 있습니다. 다음은 그 중 몇 가지 파일입니다.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

클러스터는 이미지로 다시 설치하므로 변경 내용을 유지할 수 없습니다. 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)(영문)을 참조하세요. 클러스터의 수명 동안 변경 내용을 유지하려면 생성 프로세스 중에 HDInsight 클러스터 사용자 지정을 사용할 수 있습니다.

다음은 Hive 구성을 사용자 지정하는 Azure PowerShell 스크립트 예제입니다.

	# hive-site.xml configuration
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60

	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues

	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

다른 구성 파일을 사용자 지정하는 추가 샘플:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

자세한 내용은 Azim Uddin의 [HDInsight 클러스터 만들기 사용자 지정](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx) 블로그를 참조하세요.




### 스크립트 작업을 사용하여 클러스터 사용자 지정

만드는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 동작**을 통해 호출됩니다. 스크립트 동작은 Preview 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.


### Azure 가상 네트워크 사용

[Azure 가상 네트워크](http://azure.microsoft.com/documentation/services/virtual-network/)에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결합니다(사이트 간 또는 지점 및 사이트 간).

	사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.

	![사이트 간 구성 다이어그램](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

	![지점 및 사이트 간 구성 다이어그램](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

가상 네트워크의 기능과 이점에 대한 자세한 내용은 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.

> [AZURE.NOTE]HDInsight 클러스터를 프로비전하기 전에 Azure 가상 네트워크를 만들어야 합니다. 자세한 내용은 [가상 네트워크에 Hadoop 클러스터 만들기](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network)를 참조하세요.
>
> Azure HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹 기반 가상 네트워크와는 연동되지 않습니다. Azure PowerShell cmdlet Get-AzureVNetConfig를 사용하여 기존 Azure 가상 네트워크가 위치 기반인지 여부를 확인합니다. 가상 네트워크가 위치 기반이 아니면 다음과 같은 옵션을 사용할 수 있습니다.
>
> - 기존 가상 네트워크 구성을 내보낸 다음 새 가상 네트워크를 만듭니다. 모든 새 가상 네트워크는 기본적으로 위치 기반입니다.
> - 위치 기반 가상 네트워크로 마이그레이션합니다. [지역 범위로 기존 서비스 마이그레이션](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)을 참조하세요.
>
> 각 클러스터에 단일 서브넷을 지정하는 것이 좋습니다.

## Preview 포털을 사용하여 만들기

[기본 구성 옵션] 및 필드 설명을 위한 [고급 구성 옵션]을 참조할 수 있습니다.

**HDInsight 클러스터를 만들려면**

1. [Azure Preview 포털][azure-preview-portal]에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure Preview 포털에서 새 클러스터 만들기](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure Preview 포털에서 새 클러스터 만들기")

3. 다음 값을 입력하거나 선택합니다.

  * **클러스터 이름**: 클러스터의 이름을 입력합니다. 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.
  * **클러스터 유형**: **Hadoop**을 선택합니다.
  * **클러스터 운영 체제**: **Windows Server 2012 R2 데이터 센터**를 선택합니다.
  * **구독**: 이 클러스터를 만드는 데 사용할 Azure 구독을 선택합니다.
  * **리소스 그룹**: 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.
  * **자격 증명**: Hadoop 사용자(HTTP 사용자)의 사용자 이름 및 암호를 구성합니다. 클러스터에 원격 데스크톱을 사용하도록 설정하는 경우 원격 데스크톱 사용자의 사용자 이름 및 암호와 계정 만료 날짜를 구성해야 합니다. 아래쪽의 **선택**을 클릭하여 변경 내용을 저장합니다.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **데이터 원본**: 클러스터의 기본 파일 시스템으로 사용할 기존 Azure 저장소 계정을 선택하거나 새 Azure 저장소 계정을 만듭니다.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **노드 가격 책정 계층**: 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **옵션 구성**: 클러스터 버전을 선택하고 **가상 네트워크**에 가입, Hive 및 Oozie의 데이터를 유지하기 위한 **외부 Metastore** 설정 등 기타 선택적 설정을 구성하고 스크립트 동작을 사용하여 사용자 지정 구성 요소를 설치하기 위해 클러스터를 사용자 지정하거나 클러스터에 추가 저장소 계정을 사용합니다.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Windows based HDInsight cluster can only be placed into a classical virtual network.
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. **만들기**를 클릭합니다. **시작 보드에 고정**을 선택하면 Preview 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 만드는 중임을 나타내고 생성이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.


	| 만드는 동안 | 만들기 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 프로비전](./media/hdinsight-provision-clusters/provisioning.png) | ![프로비전된 클러스터 타일](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.
	

5. 생성이 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다. 클러스터 블레이드는 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 필수 정보를 제공합니다.


	![클러스터 블레이드](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "클러스터 속성")


	다음을 사용하여 이 블레이드의 위쪽과 **필수** 섹션에 있는 아이콘을 이해합니다.


	* **설정** 및 **모든 설정**: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 **설정** 블레이드를 표시합니다.
	* **대시보드**, **클러스터 대시보드** 및 **URL**: 이러한 항목을 통해 클러스터에서 작업을 실행하기 위한 웹 포털인 클러스터 대시보드에 액세스할 수 있습니다.
	* **원격 데스크톱**: 클러스터 노드에 대해 원격 데스크톱을 사용하거나 사용하지 않도록 설정할 수 있습니다.
	* **클러스터 크기 조정**: 이 클러스터의 작업자 노드 수를 변경할 수 있습니다.
	* **삭제**: HDInsight 클러스터를 삭제합니다.
	* **빠른 시작**(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-provision-clusters/quickstart.png)): HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.
	* **사용자**(![사용자 아이콘](./media/hdinsight-provision-clusters/users.png)): Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.
	

		> [AZURE.IMPORTANT]이는 _오직_ Preview 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.
		
	* **태그**(![태그 아이콘](./media/hdinsight-provision-clusters/tags.png)): 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.


## Azure 리소스 관리자 템플릿을 사용하여 만들기

ARM(Azure 리소스 관리자) 템플릿을 사용하면 클러스터를 쉽게 배포하고 다시 배포할 수 있습니다. 다음 절차는 4개의 작업자 노드를 사용하여 북유럽 데이터 센터의 Linux 운영 체제에서 Hadoop 클러스터를 만듭니다.

**ARM 템플릿을 사용하여 클러스터를 배포하려면**

1. 부록 A의 json 파일을 사용자의 워크스테이션에 저장합니다.
2. 필요한 경우 매개 변수를 만듭니다.
3. 다음 PowerShell 스크립트를 사용하여 템플릿을 실행합니다.

		$resourceGroupName = "<ResourceGroupName>"
		$Location = "<ResourceGroupLocation>"
		
		$armDeploymentName = "<ARMDeploymentName>"
		$clusterName = "<ClusterName>"
		$clusterStorageAccountName = "<DefaultStorageAccountName>"
		
		# Connect to Azure
		Switch-AzureMode -Name AzureResourceManager
		Add-AzureAccount
		
		# Create a resource group
		New-AzureResourceGroup -Name $resourceGroupName -Location $Location
		
		# Create cluster and the dependent storage accounge
		$parameters = @{clusterName="$clusterName";clusterStorageAccountName="$clusterStorageAccountName"}
		
		New-AzureResourceGroupDeployment `
		    -Name $armDeploymentName `
		    -ResourceGroupName $resourceGroupName `
		    -TemplateFile E:\Tutorials\HDIARMTemplates\ARMTemplate-create-hadoop-cluster-with-storage.json `
		    -TemplateParameterObject $parameters
		
		# List cluster
		Get-AzureHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName

다른 방법을 사용한 ARM 템플릿 배포는 [Azure 리소스 관리자 템플릿으로 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.


## Azure PowerShell을 사용하여 만들기
Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 섹션에서는 Azure PowerShell을 사용하여 HDInsight 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. HDInsight Windows PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../install-configure-powershell.md)을 참조하세요. HDInsight에서 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)를 참조하세요. HDInsight Windows PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조](https://msdn.microsoft.com/library/azure/dn858087.aspx)를 참조하세요.


Azure PowerShell을 사용하여 HDInsight 클러스터를 만들려면 다음 절차가 필요합니다.

- Azure 리소스 그룹 만들기
- Azure 저장소 계정 만들기
- Azure Blob 컨테이너 만들기
- HDInsight 클러스터 만들기


		# Sign in
		Add-AzureAccount

		###########################################
		# Create required items, if none exist
		###########################################

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
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

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
									-ClusterName $clusterName `
									-Location $location `
									-ClusterSizeInNodes $clusterNodes `
									-ClusterType Hadoop `
									-OSType Windows `
									-HttpCredential $credentials `
									-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
									-DefaultStorageAccountKey $storageAccountKey `
									-DefaultStorageContainer $containerName  

	![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

## HDInsight .NET SDK 사용하여 만들기
HDInsight .NET SDK는 .NET Framework 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 아래 지침에 따라 Visual Studio 콘솔 응용 프로그램을 만들고 클러스터를 만들기 위한 코드를 붙여 넣으세요.

**Visual Studio 콘솔 응용 프로그램을 만들려면**

1. Visual Studio를 사용하여 새 C# 콘솔 응용 프로그램을 만듭니다.
2. NuGet 패키지 관리자 콘솔 창에서 다음 NuGet 명령을 실행합니다.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 열고 다음 코드를 붙여 넣은 후 변수 값을 제공합니다.


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
		        private const string NewClusterLocation = "<location>";		//must be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster creation");

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
		                OSType = NewClusterOsType
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


## 온-프레미스 SQL Server Integration Services를 사용하여 HDInsight 클러스터 만들기

SSIS(SQL Server Integration Services)를 사용하여 HDInsight 클러스터를 만들거나 삭제할 수 있습니다. Azure Feature Pack for SSIS는 HDInsight 클러스터에 대한 작업을 하는 다음 구성 요소를 제공합니다.


- [Azure HDInsight 클러스터 만들기 작업][ssisclustercreate]
- [Azure HDInsight 클러스터 삭제 작업][ssisclusterdelete]
- [Azure 구독 연결 관리자][connectionmanager]

[여기][ssispack]서 Azure Feature Pack for SSIS에 대해 자세히 알아보세요.


##<a id="nextsteps"></a> 다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작](hdinsight-get-started.md) - HDInsight 클러스터를 시작하는 방법을 알아봅니다.
* [HDInsight에서 Sqoop 사용](hdinsight-use-sqoop.md) - HDInsight와 SQL 데이터베이스 또는 SQL Server 간에 데이터를 복사하는 방법을 알아봅니다.
* [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md) - Azure PowerShell을 사용하여 HDInsight 작업을 하는 방법을 알아봅니다.
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md) - 프로그래밍 방식으로 작업을 HDInsight에 제출하는 방법을 알아봅니다.
* [Azure HDInsight SDK 문서][hdinsight-sdk-documentation] - HDInsight SDK를 살펴봅니다.



##A-ARM 템플릿 부록

다음 Azure 리소스 관리자 템플릿은 종속 Azure 저장소 계정을 사용하여 Hadoop 클러스터를 만듭니다.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "hdiuser",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
	    },
	    "clusterStorageType": {
	      "type": "string",
	      "defaultValue": "Standard_LRS",
	      "allowedValues": [
	        "Standard_LRS",
	        "Standard_GRS",
	        "Standard_ZRS"
	      ]
	    },
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/en-US/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/en-US/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/en-US/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/en-US/library/mt146778(v=sql.120).aspx

<!---HONumber=Oct15_HO3-->