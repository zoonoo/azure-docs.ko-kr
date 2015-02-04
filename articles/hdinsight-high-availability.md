<properties urlDisplayName="HDInsight High Availability" pageTitle="HDInsight의 Hadoop 클러스터 가용성 | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight이 배포하는 가용성과 안정성이 뛰어난 클러스터에 대해 알아봅니다." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Availability of Hadoop clusters in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />


#HDInsight에서 Hadoop 클러스터의 가용성 및 안정성

## 소개 ##
HDInsight에서 배포한 Hadoop 클러스터에는 작업을 관리하는 데 필요한 서비스 가용성 및 안정성을 높이기 위해 두 번째 헤드 노드가 추가되었습니다. 일반적으로 표준 방식으로 구현된 Hadoop 클러스터에는 헤드 노드가 1개뿐입니다. 이러한 클러스터는 작업자 노드의 실패를 원활하게 관리하도록 디자인되었지만 헤드 노드에서 실행되는 마스터 서비스가 작동 중단되면 클러스터 작동이 중단될 수 있습니다. 

![](http://i.imgur.com/jrUmrH4.png)

HDInsight는 보조 헤드 노드(헤드 노드 1)를 추가하여 이러한 단일 오류 지점을 없앱니다. ZK([ZooKeeper][zookeeper]) 노드가 추가되었으며 헤드 노드의 리더 선택을 위해, 그리고 활성 헤드 노드(HeadNode0)가 비활성화될 때 보조 헤드 노드(헤드 노드 1)로 장애 조치(failover)될 때 작업자 노드 및 GW(게이트웨이)에서 알 수 있도록 하는 데 사용됩니다.


## 활성 헤드 노드의 서비스 상태를 확인하는 방법 ##
활성 상태인 헤드 노드를 확인하고 해당 헤드 노드에서 실행되는 서비스의 상태를 확인하기 위해서는 RDP(원격 데스크톱 프로토콜)를 사용하여 Hadoop 클러스터에 연결해야 합니다. 클러스터에 원격으로 연결하는 기능은 Azure에서는 기본적으로 사용되지 않도록 설정되므로 먼저 사용하도록 설정해야 합니다. 포털에서 이 작업을 하는 방법에 대한 자세한 내용은 [RDP를 사용하여 HDInsight 클러스터에 연결](영문)을 참조하세요.(../hdinsight-administer-use-management-portal/#rdp)
클러스터에 원격으로 연결한 경우 바탕 화면에 있는 **Hadoop 서비스 사용 가능 상태** 아이콘을 두 번 클릭하여 Namenode, Jobtracker, Templeton, Oozieservice, Metastore 및 Hiveserver2 서비스가 실행 중인 헤드 노드에 대한 상태를 표시하고, HDI 3.0의 경우에는 Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore 및 Hiveserver2 서비스가 실행 중인 헤드 노드에 대한 상태를 표시합니다.

![](http://i.imgur.com/MYTkCHW.png)


## 보조 헤드 노드의 로그 파일에 액세스하는 방법 ##

보조 헤드 노드가 활성 헤드 노드가 될 경우 이 노드의 작업 로그에 액세스하려는 경우 기본 활성 노드에서와 같이 JobTracker UI를 사용하면 됩니다. 작업 추적기에 액세스하려면 이전 섹션에 설명된 것처럼 RDP(원격 데스크톱 프로토콜)를 사용하여 Hadoop 클러스터에 연결해야 합니다. 클러스터에 원격으로 연결한 경우 바탕 화면에 있는 **Hadoop 이름 노드** 아이콘을 두 번 클릭한 후 **NameNode 로그**를 클릭하여 보조 헤드 노드의 로그 디렉터리로 이동합니다.

![](http://i.imgur.com/eL6jzgB.png)


## 헤드 노드의 크기를 구성하는 방법 ##
헤드 노드는 기본적으로 대규모 VM으로 할당됩니다. 이 크기는 클러스터에서 실행되는 대부분의 Hadoop 작업의 관리에 적합합니다. 그렇지만 초대규모 VM이 헤드 노드에 필요할 수 있는 시나리오가 있습니다. 한 가지 예로 클러스터가 많은 수의 소형 Oozie 작업을 관리해야 하는 경우를 들 수 있습니다. 

초대규모 VM은 Azure PowerShell cmdlet 또는 HDInsight SDK를 사용하여 구성할 수 있습니다.

PowerShell을 사용하는 클러스터의 생성 및 프로비전은 [PowerShell을 사용하여 HDInsight 관리](../hdinsight-administer-use-powershell/)(영문)에 설명되어 있습니다. 초대규모 헤드 노드를 구성하려면 `-HeadNodeVMSize ExtraLarge` 매개 변수를 이 코드에 사용되는 `New-AzureHDInsightcluster` cmdlet에 추가해야 합니다.

    # Azure PowerShell에서 새 HDInsight 클러스터 만들기
	# ExtraLarge Headnode VM으로 구성됨
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

SDK의 경우에도 상황은 비슷합니다. SDK를 사용하는 클러스터의 생성 및 프로비전은 [HDInsight .NET SD 사용](../hdinsight-provision-clusters/#sdk)(영문)에 설명되어 있습니다. 초대규모 헤드 노드를 구성하려면 `HeadNodeSize = NodeVMSize.ExtraLarge` 매개 변수를 이 코드에 사용되는 `HeadNodeSize = NodeVMSize.ExtraLarge` 메서드에 추가해야 합니다.

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


**참조**	

- [ZooKeeper][zookeeper]
- [RDP를 사용하여 HDInsight 클러스터에 연결](../hdinsight-administer-use-management-portal/#rdp)
- [HDInsight .NET SDK 사용](../hdinsight-provision-clusters/#sdk) 


[zookeeper]: http://zookeeper.apache.org/ 






<!--HONumber=35.1-->
