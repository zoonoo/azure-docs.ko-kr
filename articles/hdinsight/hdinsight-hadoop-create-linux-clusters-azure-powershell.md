<properties
   	pageTitle="Azure CLI를 사용하여 HDInsight에서 Linux에 Hadoop, HBase 또는 Storm 클러스터 만들기 | Microsoft Azure"
   	description="Azure CLI를 사용하여 HDInsight에서 Linux에 Hadoop, HBase 또는 Storm 클러스터를 만드는 방법에 대해 알아봅니다."
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
   	ms.date="10/14/2015"
   	ms.author="nitinme"/>

#Azure PowerShell을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 문서에서는 예제 스크립트와 Azure PowerShell을 사용하여 Linux 기반 HDInsight 클러스터를 프로비전하는 방법에 대한 정보를 제공합니다.

> [AZURE.NOTE]Azure PowerShell은 Windows 클라이언트에서만 사용할 수 있습니다. Linux, Unix 또는 Mac OS X 클라이언트를 사용하는 경우 Azure CLI를 사용하여 클러스터 만들기에 대한 정보에 대해 [Azure CLI를 사용하여 Linux 기반 HDInsight 클러스터 만들기](hdinsight-hadoop-create-linux-cluster-azure-cli.md)를 참조하세요.

##필수 조건

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- __Azure PowerSHell__ HDInsight Windows PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../install-configure-powershell.md)을 참조하세요. HDInsight에서 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)를 참조하세요. HDInsight Windows PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조](https://msdn.microsoft.com/library/azure/dn858087.aspx)를 참조하세요.

##클러스터 만들기

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

다음 스크립트는 새 클러스터를 만드는 방법을 보여 줍니다.

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

> [AZURE.NOTE]**$clusterCredentials**에 대해 지정한 값은 클러스터의 Hadoop 사용자 계정을 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에 연결합니다. **$sshCredentials**에 대해 지정한 값은 클러스터의 SSH 사용자를 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에서 원격 SSH 세션을 시작하고 작업을 실행합니다. Azure 포털에서 빠른 생성 옵션을 사용하여 클러스터를 프로비전하는 경우 기본 Hadoop 사용자 이름을 "admin"이고 기본 SSH 사용자 이름은 "hdiuser"입니다.

프로비전을 완료하는데 최대 15분까지 걸릴 수 있습니다.

##다음 단계

HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

###Hadoop 클러스터

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

###HBase 클러스터

* [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-stared-linux.md)
* [HDInsight에서 HBase용 Java 응용 프로그램 개발](hdinsight-hbase-build-java-maven-linux)

###Storm 클러스터

* [HDInsight의 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](hdinsight-storm-develop-python.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO3-->