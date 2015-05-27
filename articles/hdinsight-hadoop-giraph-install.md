<properties 
	pageTitle="HDInsight에서 스크립트 작업을 사용하여 Hadoop 클러스터에 Giraph설치| Azure" 
	description="HDInsight 클러스터를 사용자 지정하여 Giraph를 설치하는 방법을 알아봅니다. 스크립트 작업 구성 옵션을 사용하여 스크립트를 사용하고 Giraph를 설치합니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# HDInsight Hadoop 클러스터에 Giraph 설치 및 사용

**스크립트 작업** 클러스터 사용자 지정을 사용하여 Azure HDInsight의 Hadoop에서 모든 유형의 클러스터에 Giraph를 설치할 수 있습니다. 스크립트 작업을 사용하면 클러스터를 생성할 때에만 클러스터를 사용자 지정하는 스크립트를 실행할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize](영문)을 참조하세요.

이 항목에서는 스크립트 작업을 사용하여 Giraph를 설치하는 방법을 알아봅니다. Giraph를 설치한 후 대규모 그래프를 처리하기 위해 가장 일반적인 응용 프로그램에 Giraph를 사용하는 방법도 알아보겠습니다.


## <a name="whatis"></a>Giraph란?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a>를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다. 그래프는 인터넷과 같은 대규모 네트워크의 라우터 간 연결, 소셜 네트워크(또는 소셜 그래프)상의 사람들 간 관계 등 개체 간의 관계를 모델링합니다. 그래프 처리를 통해 그래프의 개체 간 관계를 추론하여 다음을 수행할 수 있습니다.

- 현재 관계를 기반으로 하여 잠재적인 친구 파악.
- 네트워크의 두 컴퓨터 간에 가장 짧은 경로 파악.
- 웹 페이지의 페이지 순위 계산.

   
## <a name="install"></a>Giraph를 설치하는 방법

HDInsight 클러스터에 Giraph를 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1))에서 다운로드할 수 있습니다. 이 섹션에서는 Azure 포털을 사용하여 클러스터를 프로비전하면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [AZURE.NOTE]샘플 스크립트는 HDInsight 클러스터 버전 3.1에서만 작동합니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

1. [사용자 지정 옵션을 사용하여 클러스터를 프로비저닝](hdinsight-provision-clusters.md#portal)에 설명된 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터 프로비저닝을 시작합니다. 
2. 아래와 같이 마법사의 **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "스크립트 작업을 사용하여 클러스터 사용자 지정")
	
	<table border='1'>
	<tr><th>속성</th><th>값</th></tr>
	<tr><td>이름</td>
	<td>스크립트 작업의 이름을 지정합니다. 예: <b>Install Giraph</b>.</td></tr>
	<tr><td>스크립트 URI</td>
	<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI(Uniform Resource Identifier)를 지정합니다. 예: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
	<tr><td>노드 유형</td>
	<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>작업자 노드만</b>을 선택할 수 있습니다.
	<tr><td>매개 변수</td>
	<td>스크립트에 필요한 경우 매개 변수를 지정합니다. Giraph를 설치하는 스크립트는 매개 변수가 필요하지 않으므로 공백으로 둘 수 있습니다.</td></tr>
	</table>
두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 프로비저닝을 시작합니다.

스크립트를 사용하여 Azure PowerShell 또는 HDInsight.NET SDK로 HDInsight에 Giraph를 설치할 수도 있습니다. 이 절차에 대한 자세한 내용은 이 항목의 뒷부분에 제공됩니다.

## <a name="usegiraph"></a>HDInsight에서 Giraph를 사용하는 방법

SimpleShortestPathsComputation 예제를 사용하여 그래프의 개체 간 가장 짧은 경로를 찾기 위한 기본 <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> 구현을 보여줍니다. 다음 단계에 따라 샘플 데이터와 샘플 jar을 업로드하고, SimpleShortestPathsComputation 예제를 사용하여 작업을 실행한 후 결과를 확인합니다.

1. 샘플 데이터 파일을 Azure Blob 저장소에 업로드합니다. 로컬 워크스테이션에서 **tiny_graph.txt**라는 새 파일을 만듭니다. 이 파일은 다음 줄을 포함해야 합니다.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	tiny_graph.txt 파일을 HDInsight 클러스터의 기본 저장소에 업로드합니다. 데이터를 업로드하는 방법은 [HDInsight에서 Hadoop 작업에 대한 데이터 업로드](hdinsight-upload-data.md)를 참조하세요.

	이 데이터는 [source_id, source_value,[[dest_id], [edge_value],...]] 등의 형식을 사용하여 방향이 지정된 그래프의 개체 간 관계를 설명합니다. 각 줄은 **source_id** 개체와 하나 이상의 **dest_id** 개체 간 관계를 나타냅니다. **edge_value**(또는 가중치)는 **source_id**와 **dest_id** 간 연결의 강도 또는 거리로 생각할 수 있습니다.

	개체 간 거리로 위의 값(또는 가중치)을 사용하여 그리면 다음과 같을 수 있습니다.

	![원과 거리가 다른 선으로 그린 tiny_graph.txt](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. SimpleShortestPathsComputation 예제를 실행합니다. 다음 Azure PowerShell cmdlet에서 tiny_graph.txt 파일을 입력으로 사용하여 예제를 실행합니다. 그렇게 하려면 [Azure PowerShell][powershell-install-configure]을 설치하고 구성해야 합니다.

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	위의 예제에서는**clustername**을 Giraph가 설치된 HDInsight 클러스터의 이름으로 바꿉니다.

5. 결과를 확인합니다. 작업이 완료되면 결과가 __wasb:///example/out/shotestpaths__ 폴더에 있는 두 출력 파일에 저장됩니다. 파일 이름은 __part-m-00001__ 및 __part-m-00002__입니다. 다음 단계에 따라 다운로드하고 출력을 확인합니다.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	그러면 워크스테이션의 현재 디렉터리에 __example/output/shortestpaths__ 디렉터리 구조가 생성되고 해당 위치에 두 출력 파일이 다운로드됩니다.

	__Cat__ cmdlet을 사용하여 파일 내용을 표시합니다.

		Cat example/output/shortestpaths/part*

	출력은 다음과 유사합니다.


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	SimpleShortestPathComputation 예제는 개체 ID 1로 시작하도록 하드 코딩되며 다른 개체에 대한 가장 짧은 경로를 찾습니다. 따라서 출력은 `destination_id distance`이며, 여기서 distance는 가장자리를 기준으로 개체 ID 1과 대상 ID 간의 거리 값(또는 가중치)입니다.
	
	이를 시각화하면 ID 1과 다른 모든 개체 간의 가장 짧은 경로를 이동하여 결과를 확인할 수 있습니다. ID 1과 ID 4 간의 가장 짧은 경로는 5입니다. <span style="color:orange">ID 1과 3</span> 사이의 총 거리와 <span style="color:red">ID 3과 4</span> 사이의 총 거리를 더한 값입니다.

	![가장 짧은 경로와 함께 원으로 그린 개체](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png)


## <a name="usingPS"></a>Azure PowerShell을 사용하여 HDInsight Hadoop 클러스터에 Giraph 설치

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
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. 사용할 기본 저장소 및 클러스터의 노드와 같은 구성 값을 지정합니다.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** cmdlet을 사용하여 클러스터 구성에 스크립트 작업을 추가합니다. 나중에 클러스터가 생성되는 중에 스크립트 작업이 실행됩니다.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

	**Add-AzureHDInsightScriptAction** cmdlet에서 다음과 같은 매개 변수를 사용합니다.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">매개 변수</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">정의</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">구성</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">스크립트 작업 정보가 추가되는 구성 개체입니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">이름</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트 작업의 이름입니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">사용자 지정 스크립트가 실행되는 노드입니다. 유효한 값은 HeadNode(헤드 노드에 설치) 또는 DataNode(데이터 노드에 설치)입니다. 두 값 중 하나 또는 모두 사용할 수 있습니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">실행되는 스크립트의 URI입니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">매개 변수</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트에 필요한 매개 변수입니다. 이 항목에서 사용되는 샘플 스크립트에는 매개 변수가 필요하지 않으므로 위의 코드 조각에는 이 매개 변수가 없습니다.
</td></tr>
</table>
	
4. 끝으로, Giraph가 설치된 사용자 지정된 클러스터 프로비전을 시작합니다.
	
		# Start provisioning a cluster with Giraph installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.


## <a name="usingSDK"></a>.NET SDK를 사용하여 HDInsight Hadoop 클러스터에서 Giraph 설치

HDInsight .NET SDK는 .NET Framework 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 이 섹션에서는 SDK에서 스크립트 작업을 사용하여 Giraph가 설치된 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. 다음 절차를 수행해야 합니다.

- HDInsight .NET SDK 설치
- 자체 서명된 인증서 만들기
- 콘솔 응용 프로그램 만들기
- 응용 프로그램 실행


**HDInsight .NET SDK 설치**

[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)(영문)에서 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

**자체 서명된 인증서 만들기**

자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.


**Visual Studio 응용 프로그램 만들기**

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">이름</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
</table>

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	.NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다.

7. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.

8. 파일 맨 위에 다음 using 문을 추가합니다.

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Main() 함수에서 다음 코드를 복사하여 붙여넣고 변수 값을 지정합니다.
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. 다음 코드를 Main() 함수에 추가하여 [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 클래스를 사용하고 사용자 지정 스크립트를 호출하여 Giraph를 설치합니다.

		// Add the script action to install Giraph
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //Because the script used does not require any parameters
        ));

11. 끝으로, 클러스터를 만듭니다.

		client.CreateCluster(clusterInfo);

12. 응용 프로그램에 대한 변경 내용을 저장하고 솔루션을 빌드합니다.

**응용 프로그램 실행**

Azure PowerShell 콘솔을 열고 Visual Studio 프로젝트를 저장한 위치로 이동하고 프로젝트 내의 \\bin\\debug 디렉터리로 이동한 후 다음 명령을 실행합니다.

	.\CreateGiraphCluster <cluster-name>

클러스터 이름을 제공하고 ENTER 키를 눌러 Giraph가 설치된 클러스터를 프로비전합니다.


## 참고 항목##
- [HDInsight 클러스터에 Spark 설치 및 사용][hdinsight-install-spark](영문) - 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Spark를 설치하고 사용하는 방법에 대한 지침을 제공합니다. Spark는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다.
- [HDInsight 클러스터에 R 설치][hdinsight-install-r](영문) - 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 R을 설치하고 사용하는 방법에 대한 지침을 제공합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. 수백 개의 기본 제공 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 고유한 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.
- [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install.md)(영문). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!--HONumber=54-->