<properties
	pageTitle="스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정 | Microsoft Azure"
	description="스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# 스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정(Windows)

HDInsight는 사용자 지정 스크립트를 호출하는 **스크립트 작업**이라는 구성 옵션을 제공하며 이는 만들기 프로세스 중 클러스터에서 수행할 사용자 지정을 정의합니다. 이 스크립트를 사용하여 클러스터에 추가 소프트웨어를 설치하거나 클러스터에서 응용 프로그램의 구성을 변경할 수 있습니다.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-v1.md)

> [AZURE.NOTE]이 문서에 있는 정보는 Windows 기반 HDInsight 클러스터에 지정됩니다. Linux 기반 클러스터에 지정된 이 문서의 버전은 [스크립트 동작(Linux)을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

HDInsight 클러스터를 사용자 지정하는 방법은 추가 Azure 저장소 계정 포함, hadoop 구성 파일(core-site.xml, hive-site.xml 등) 변경, 클러스터의 공통 위치에 공유 라이브러리(예: Hive, Oozie) 추가 등을 비롯해 다양합니다. 이러한 사용자 지정은 Azure PowerShell, Azure HDInsight .NET SDK 또는 Azure Preview 포털을 통해 수행할 수 있습니다. 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기][hdinsight-provision-cluster]를 참조하세요.

## 클러스터 만들기 프로세스의 스크립트 작업

스크립트 작업은 클러스터를 만드는 프로세스 동안에만 사용됩니다. 다음 다이어그램에서는 만들기 프로세스 중 스크립트 작업을 실행할 때를 보여줍니다.

![HDInsight 클러스터 사용자 지정 및 클러스터 만드는 동안의 단계][img-hdi-cluster-states]

스크립트가 실행 중이면 클러스터의**Cluster 사용자 지정** 단계가 시작됩니다. 이 단계에서 스크립트는 시스템 관리자 계정으로 클러스터의 지정된 모든 노드에서 병렬로 실행되며 해당 노드에서 모든 관리자 권한을 제공합니다.

> [AZURE.NOTE]**Cluster 사용자 지정** 단계 중 클러스터 노드에 대한 관리 권한이 있으므로 스크립트를 사용하여 Hadoop 관련 서비스를 비롯한 서비스의 중지 및 시작과 같은 작업을 수행할 수 있습니다. 따라서 스크립트의 일부로 스크립트 실행이 완료되기 전에 Ambari 서비스 및 기타 Hadoop 관련 서비스가 실행 중인지 확인해야 합니다. 클러스터가 생성되는 동안 클러스터의 상태를 확인하려면 이러한 서비스가 필요합니다. 클러스터에서 이러한 서비스에 영향을 주는 구성을 변경하는 경우 제공되는 도우미 함수를 사용해야 합니다. 도우미 함수에 대한 자세한 내용은 [HDInsight용 스크립트 작업 스크립트 개발][hdinsight-write-script]을 참조하세요.

스크립트의 출력 및 오류 로그는 클러스터에 대해 지정한 기본 저장소 계정에 저장됩니다. 오류는 **u<\\cluster-name-fragment><\\time-stamp>setuplog**라는 이름으로 테이블에 저장됩니다. 클러스터의 모든 노드(헤드 노드 및 작업자 노드)에서 실행된 스크립트의 집계 로그입니다.

각 클러스터에서는 지정된 순서대로 호출되는 여러 스크립트 작업을 사용할 수 있습니다. 스크립트를 헤드 노드, 작업자 노드 또는 두 노드 모두에서 실행할 수 있습니다.

HDInsight는 HDInsight 클러스터에서 다음 구성 요소를 설치하는 여러 스크립트를 제공합니다.

이름 | 스크립트
----- | -----
**Spark 설치** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]을 참조하세요.
**R 설치** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. [HDInsight 클러스터에서 R 설치 및 사용][hdinsight-install-r]을 참조하세요.
**Solr 설치** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. [HDInsight 클러스터에서 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)을 참조하세요.
- **Giraph 설치** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)을 참조하세요.



## Azure Preview 포털을 사용하여 스크립트 호출

**Azure Preview 포털에서**

1. [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md#portal)에서 설명한 대로 클러스터를 만들기 시작합니다.
2. 아래와 같이, 선택적 구성의 **스크립트 작업** 블레이드에 대해 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

	<table border='1'>
	<tr><th>속성</th><th>값</th></tr>
	<tr><td>이름</td>
		<td>스크립트 작업의 이름을 지정합니다.</td></tr>
	<tr><td>스크립트 URI</td>
		<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다.</td></tr>
	<tr><td>헤드/작업자</td>
		<td>사용자 지정 스크립트가 실행되는 노드(**헤드** 또는 **작업자**)를 지정합니다.</b>
	<tr><td>매개 변수</td>
		<td>스크립트에 필요한 경우 매개 변수를 지정합니다.</td></tr>
</table>ENTER 키를 누르고 두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다.

3. **선택**을 클릭하여 스크립트 작업 구성을 저장하고 계속 클러스터를 만듭니다.

## Azure PowerShell을 사용하여 스크립트 호출

이 다음 PowerShell 스크립트는 Windows 기반 HDInsight 클러스터에 Spark를 설치하는 방법을 보여줍니다. 다른 소프트웨어를 설치하려면 스크립트에서 스크립트 파일을 바꿔야 합니다.

이 섹션에서는 스크립트 작업을 통해 스크립트를 호출하여 클러스터를 사용자 지정하는 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet을 사용합니다. 계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight용 Azure PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

다음 단계를 수행합니다.

1. Azure PowerShell 창을 열고 다음 변수를 선언합니다.

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. 사용할 기본 저장소 및 클러스터의 노드와 같은 구성 값을 지정합니다.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. **Add-AzureHDInsightScriptAction** cmdlet을 사용하여 클러스터 구성에 스크립트 작업을 추가합니다. 나중에 클러스터가 생성되는 중에 스크립트 작업이 실행됩니다.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	**Add-AzureHDInsightScriptAction** cmdlet에서 다음과 같은 매개 변수를 사용합니다.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">매개 변수</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">정의</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">구성</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">스크립트 작업 정보가 추가되는 구성 개체입니다</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">이름</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트 작업의 이름입니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">사용자 지정 스크립트가 실행되는 노드를 지정합니다. 유효한 값은 HeadNode(헤드 노드에 설치) 또는 DataNode(데이터 노드에 설치)입니다. 두 값 중 하나 또는 모두 사용할 수 있습니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">실행되는 스크립트의 URI를 지정합니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">매개 변수</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트에 필요한 매개 변수입니다. 이 항목에서 사용되는 샘플 스크립트에는 매개 변수가 필요하지 않으므로 위의 코드 조각에는 이 매개 변수가 없습니다.
</td></tr>
</table>

4. 마지막으로 Spark가 설치된 사용자 지정된 클러스터를 만들기 시작합니다.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.

## .NET SDK를 사용하여 스크립트 호출 

다음 샘플은 Windows 기반 HDInsight 클러스터에서 Spark를 설치하는 방법을 보여줍니다. 다른 소프트웨어를 설치하려면 코드에서 스크립트 파일을 바꿔야 합니다.

**Spark로 HDInsight 클러스터를 만들려면**

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 만듭니다.
2. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Program.cs 파일에서 다음 using 문을 사용합니다.

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. 클래스의 코드를 다음과 같이 둡니다.

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. **F5** 키를 눌러 응용 프로그램을 실행합니다.


## HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원
Microsoft Azure HDInsight 서비스는 Hadoop에 형성된 오픈 소스 기술의 에코시스템을 사용하여 클라우드에 빅 데이터 응용 프로그램을 빌드할 수 있는 유연한 플랫폼입니다. Microsoft Azure에서는 <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure 지원 FAQ 웹 사이트</a>의 **지원 범위** 섹션에 설명된 대로 일반적인 수준의 오픈 소스 기술을 제공합니다. HDInsight 서비스는 아래에 설명된 일부 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

- **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어, YARN ResouceManager, Hive 쿼리 언어(HiveQL) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능</a>에 있습니다.
- **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

기본 제공 구성 요소는 완전히 지원되며, Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.

> [AZURE.WARNING]HDInsight 클러스터와 함께 제공된 구성 요소는 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/)).

HDInsight 서비스는 사용자 지정 구성 요소를 사용하는 여러 방법을 제공합니다. 구성 요소가 클러스터에 설치되고 사용되는 방법과 상관없이, 동일한 수준의 지원이 적용됩니다. 다음은 HDInsight 클러스터에서 사용자 지정 구성 요소를 사용할 수 있는 가장 일반적인 방법의 목록입니다.

1. 작업 제출 - 사용자 지정 구성 요소를 실행하거나 사용하는 Hadoop 또는 기타 유형의 작업을 클러스터에 제출할 수 있습니다.
2. 클러스터 사용자 지정 - 클러스터를 만들 때 클러스터 노드에 설치되는 사용자 지정 구성 요소 및 추가 설정을 지정할 수 있습니다.
3. 샘플 - 인기 있는 사용자 지정 구성 요소의 경우, Microsoft와 다른 사람들이 이러한 구성 요소를 HDInsight 클러스터에서 어떻게 사용할 수 있는지에 대한 샘플을 제공할 수 있습니다. 이러한 샘플은 지원 없이 제공됩니다.

## 스크립트 작업 스크립트 개발

[HDInsight용 스크립트 작업 스크립트 개발][hdinsight-write-script]을 참조하세요.


## 참고 항목

- [HDInsight의 Hadoop 클러스터 만들기][hdinsight-provision-cluster]에서는 다른 사용자 지정 옵션을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침을 제공합니다.
- [HDInsight용 스크립트 작업 스크립트 개발][hdinsight-write-script]
- [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]
- [HDInsight 클러스터에서 R 설치 및 사용][hdinsight-install-r]
- [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)
- [HDInsight 클러스터에 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "클러스터를 만드는 동안의 단계"

<!---HONumber=Oct15_HO2-->