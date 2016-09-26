<properties
   pageTitle="HDInsight의 Hadoop 클러스터 만들기 | Microsoft Azure"
   	description="Azure 포털을 사용하여 Azure HDInsight에 클러스터를 만드는 방법을 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Azure 포털을 사용하여 HDInsight의 Windows 기반 Hadoop 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터를 만드는 방법을 알아봅니다. Microsoft [Azure 포털](../azure-portal-overview.md)은 Azure 리소스를 프로비전하고 관리할 수 있는 중앙 위치입니다. Azure 포털은 HDInsight에서 Linux 기반 또는 Windows 기반 Hadoop 클러스터를 만드는 데 사용할 수 있는 도구 중 하나입니다. 다른 클러스터 만들기 도구 및 기능은 이 페이지 맨 위에 있는 탭 선택을 클릭하거나 [클러스터 생성 방법](hdinsight-provision-clusters.md#cluster-creation-methods)을 참조하세요.

##필수 조건:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

## 클러스터 만들기


**HDInsight 클러스터를 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure 포털에서 새 클러스터 만들기")

3. 다음 값을 입력하거나 선택합니다.

  * **클러스터 이름**: 클러스터의 이름을 입력합니다. 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.
  * **클러스터 유형**: **Hadoop**을 선택합니다. 다른 옵션은 **HBase**, **Storm** 및 **Spark**를 포함합니다.
  * **클러스터 운영 체제**: **Windows**를 선택합니다. Linux 기반 클러스터를 만들려면 **Linux**를 선택합니다.
  * **버전**: [HDInsight 버전](hdinsight-component-versioning.md)을 참조하세요.
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

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.

            &nbsp;

            > [AZURE.IMPORTANT] When creating a metastore, do not use a database name that contains dashes or hyphens, as this can cause the cluster creation process to fail.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. **만들기**를 클릭합니다. **시작 보드에 고정**을 선택하면 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 만드는 중임을 나타내고 생성이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.
	
    클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.
	

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
	

		> [AZURE.IMPORTANT] 이는 _오직_ 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.
		
	* **태그(![태그 아이콘](./media/hdinsight-provision-clusters/tags.png))**: 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.

##클러스터 사용자 지정

- [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.
- [스크립트 작업을 사용하여 Windows 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.

##다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight 클러스터를 시작하는 방법을 알아봅니다.
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md) - 프로그래밍 방식으로 작업을 HDInsight에 제출하는 방법을 알아봅니다.
* [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0914_2016-->