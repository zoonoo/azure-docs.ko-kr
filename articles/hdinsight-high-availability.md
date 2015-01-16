<properties urlDisplayName="HDInsight High Availability" pageTitle="HDInsight의 Hadoop 클러스터 가용성 | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight이 배포하는 가용성과 안정성이 뛰어난 클러스터에 대해 알아봅니다." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Availability of Hadoop clusters in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />


#HDInsight에서 Hadoop 클러스터의 가용성 및 안정성

## 소개 ##
HDInsight에서 배포한 Hadoop 클러스터에는 작업을 관리하는 데 필요한 서비스 가용성 및 안정성을 높이기 위해 두 번째 헤드 노드가 추가되었습니다. 일반적으로 표준 방식으로 구현된 Hadoop 클러스터에는 헤드 노드가 1개뿐입니다. 이러한 클러스터는 작업자 노드의 실패를 원활하게 관리하도록 디자인되었지만 헤드 노드에서 실행되는 마스터 서비스가 작동 중단되면 클러스터 작동이 중단될 수 있습니다. 

![](http://i.imgur.com/jrUmrH4.png)

HDInsight는 보조 헤드 노드(헤드 노드 1)를 추가하여 이러한 단일 오류 지점을 없앱니다. ZK([


## How to check on the service status on the active headnode ##
To determine which headnode is active and to check on the status of the services running on that head node, you must connect to the Hadoop cluster using the Remote Desktop Protocol (RDP). The ability to remote into the cluster is disabled by default in Azure, so it must be first be enabled. For instructions on how to do this in the portal, see [Connect to HDInsight clusters using RDP](../hdinsight-administer-use-management-portal/#rdp)
Once you have remoted into the cluster, double-click on the **Hadoop Service Available Status** icon located on the desktop to obtain status about which headnode the Namenode, Jobtracker, Templeton, Oozieservice, Metastore, and Hiveserver2 services are running, or for HDI 3.0, the Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore and Hiveserver2.

![](http://i.imgur.com/MYTkCHW.png)


## How to access log files on the secondary headnode ##

To access job logs on the secondary headnode in the event that it has become the active headnode, browsing the JobTracker UI still works as it does for the primary active node. To access the Job Tracker, you must connect to the Hadoop cluster using the Remote Desktop Protocol (RDP) as described in the previous section. Once you have remoted into the cluster, double-click on the **Hadoop Name Node** icon located on the desktop and then click on the **NameNode logs** to get to the directory of logs on the secondary headnode.

![](http://i.imgur.com/eL6jzgB.png)


## How to configure the size of the headnode ##
The headnodes are allocated as large VMs by default. This size is adequate for the management of most Hadoop jobs run on the cluster. But there are scenarios that may require that extra large VMs are needed for the headnodes. One example is when the cluster has to manage a large number of small Oozie jobs. 

Extra large VMs can be configured using either Azure PowerShell cmdlets or the HDInsight SDK.

The creation and provisioning of a cluster using PowerShell is documented in [Administer HDInsight using PowerShell](../hdinsight-administer-use-powershell/). The configuration of an extra large headnode requires the addition of the `-HeadNodeVMSize ExtraLarge` parameter to the `New-AzureHDInsightcluster` cmdlet used in this code.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

For SDK, the story is similar. The creation and provisioning of a cluster using the SDK is documented in [Using HDInsight .NET SD](../hdinsight-provision-clusters/#sdk). The configuration of an extra large headnode requires the addition of the `HeadNodeSize = NodeVMSize.ExtraLarge` parameter to the `ClusterCreateParameters()` method used in this code.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge Headnode VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };


**References**	

- [ZooKeeper][zookeeper]
- [Connect to HDInsight clusters using RDP](../hdinsight-administer-use-management-portal/#rdp)
- [Using HDInsight .NET SDK](../hdinsight-provision-clusters/#sdk) 


[zookeeper]: http://zookeeper.apache.org/ 







<!--HONumber=35.1-->
