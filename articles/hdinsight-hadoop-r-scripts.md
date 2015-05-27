<properties 
	pageTitle="HDInsight에서 R을 사용하여 클러스터 사용자 지정 | Microsoft Azure" 
	description="R을 설치하고 사용하여 Hadoop 클러스터를 사용자 지정하는 방법을 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# HDInsight Hadoop 클러스터에 R 설치 및 사용

**스크립트 작업** 클러스터 사용자 지정을 사용하여 HDInsight의 Hadoop에서 모든 유형의 클러스터에 R을 설치할 수 있습니다. 이렇게 하면 데이터 과학자 및 분석가가 R을 사용하여 HDInsight에 배포된 Hadoop 클러스터에서 많은 양의 데이터를 처리하기 위한 강력한 MapReduce/YARN 프로그래밍 프레임워크를 배포할 수 있습니다.

스크립트 작업을 사용하면 클러스터를 생성할 때에만 클러스터를 사용자 지정하는 스크립트를 실행할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]을 참조하세요.


## <a name="whatIs"></a>R이란?

<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a>은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. R은 수백 개의 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 자체 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다. R은 다양한 분야에서 전문 통계학자와 과학자 대부분의 기본 프로그래밍 환경입니다.

R 스크립트는 클러스터를 만들 때 스크립트 작업을 사용하여 사용자 지정하여 R 환경을 설치한 HDInsight의 Hadoop 클러스터에서 실행할 수 있습니다. R은 Azure Blob 저장소(WASB)와 호환되므로 HDInsight의 R을 사용하여 이 저장소에 저장된 데이터를 처리할 수 있습니다.

## <a name="install"></a>R을 설치하는 방법

HDInsight 클러스터에 R을 설치하기 위한 [샘플 스크립트](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)는 Azure 저장소의 읽기 전용 Blob에서 다운로드할 수 있습니다. 이 섹션에서는 Azure 포털을 사용하여 클러스터를 프로비전하면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [AZURE.NOTE]샘플 스크립트는 HDInsight 클러스터 버전 3.1에서 도입되었습니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](../hdinsight-component-versioning/)을 참조하세요.

1. [사용자 지정 옵션을 사용하여 클러스터를 프로비저닝](../hdinsight-provision-clusters/#portal)에 설명된 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터 프로비저닝을 시작합니다. 
2. 아래와 같이 마법사의 **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "스크립트 작업을 사용하여 클러스터 사용자 지정")
	
	<table border='1'>
	<tr><th>속성</th><th>값</th></tr>
	<tr><td>이름</td>
		<td>스크립트 작업의 이름(예: <b>Install R</b>)을 지정합니다.</td></tr>
	<tr><td>스크립트 URI</td>
		<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 대한 URI(예: <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i>)를 지정합니다.</td></tr>
	<tr><td>노드 유형</td>
		<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>작업자 노드만</b>을 선택할 수 있습니다.
	<tr><td>매개 변수</td>
		<td>스크립트에 필요한 경우 매개 변수를 지정합니다. 그러나 R을 설치하는 스크립트는 매개 변수가 필요하지 않으므로 공백으로 둘 수 있습니다.</td></tr>
	</table>
	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 프로비저닝을 시작합니다.

스크립트를 사용하여 Azure PowerShell 또는 HDInsight.NET SDK로 HDInsight에 R을 설치할 수도 있습니다. 이 절차에 대한 자세한 내용은 이 문서의 뒷부분에 제공됩니다.

## <a name="useR"></a>HDInsight에서 R 스크립트를 실행하는 방법
이 섹션에서는 HDInsight를 사용하는 Hadoop 클러스터에서 R 스크립트를 실행하는 방법에 대해 설명합니다.

1. **클러스터에 대한 원격 데스크톱 연결 설정**: Azure 포털에서 R을 설치하여 만든 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 클러스터에 연결합니다. 지침은 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP를 사용하여 HDInsight 클러스터에 연결</a>을 참조하세요.

2. **R 콘솔 열기**: R 설치에서는 R 콘솔에 대한 링크를 헤드 노드의 데스크톱에 놓습니다. 이 링크를 클릭하여 R 콘솔을 엽니다.

3. **R 스크립트 실행**: R 콘솔에서 R 스크립트를 붙여 넣고 선택한 후 Enter 키를 눌러 콘솔에서 직접 스크립트를 실행할 수 있습니다. 다음은 1에서 100까지의 숫자를 생성한 후 각 숫자에 2를 곱하는 간단한 예제 스크립트입니다.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

첫 번째 두 줄은 R이 설치된 RHadoop 라이브러리를 호출하고, 마지막 줄은 결과를 콘솔에 인쇄합니다. 출력은 다음과 같습니다.

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Azure PowerShell을 사용하여 HDInsight 클러스터에 R 설치

이 섹션에서는 스크립트 작업을 통해 스크립트를 호출하여 클러스터를 사용자 지정하는 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet을 사용합니다. 계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

다음 단계를 수행합니다.

1. Azure PowerShell 콘솔을 열고 다음 변수를 선언합니다.

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. 구성 값(예: 클러스터의 노드) 및 사용할 기본 저장소를 지정합니다.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** cmdlet으로 샘플 스크립트를 호출하여 R을 설치합니다. 예를 들면 다음과 같습니다.

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">사용자 지정 스크립트가 실행되는 노드를 지정합니다. 유효한 값은 <strong>HeadNode</strong>(헤드 노드에 설치) 또는 <strong>DataNode</strong>(데이터 노드에 설치)입니다. 두 값 중 하나 또는 모두 사용할 수 있습니다.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">매개 변수</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트에 필요한 매개 변수입니다. 
</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">실행되는 스크립트의 URI를 지정합니다.</td></tr>
</table>
	
4. 마지막으로 R이 설치되도록 사용자 지정된 클러스터를 프로비전합니다.
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.


## <a name="usingSDK"></a>.NET SDK를 사용하여 HDInsight에서 R 설치

HDInsight .NET SDK는 .NET 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.

SDK를 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차를 수행합니다.

- [HDInsight .NET SDK 설치](#installSDK)
- [자체 서명된 인증서 만들기](#createCert)
- [Visual Studio에서 .NET 응용 프로그램 만들기](#createApp)
- [응용 프로그램 실행](#runApp)

다음 섹션에서는 이러한 절차를 수행하는 방법을 보여 줍니다.

### <a name="installSDK"></a>HDInsight .NET SDK를 설치하려면

[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)(영문)에서 가장 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

### <a name="createCert"></a>자체 서명된 인증서를 만들려면

자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.


### <a name="createApp"></a>Visual Studio에서 .NET 응용 프로그램을 만들려면

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
</table>

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	.NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다.

7. **솔루션 탐색기**에서 **Program.cs**를 두 번 클릭하여 엽니다.

8. 파일 맨 위에 다음 **using** 문을 추가합니다.

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. **Main()** 함수에서 다음 코드를 붙여넣고 변수 값을 지정합니다.
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
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

10. 다음 코드를 **Main()** 함수에 추가하여 [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 클래스를 사용하고 사용자 지정 스크립트를 호출하여 R을 설치합니다.

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. 끝으로, 클러스터를 만듭니다.

		client.CreateCluster(clusterInfo);

11. 응용 프로그램에 대한 변경 내용을 저장하고 솔루션을 빌드합니다.

### <a name="runApp"></a>응용 프로그램을 실행하려면

Azure PowerShell 콘솔을 열고 프로젝트를 저장한 위치로 이동하고 프로젝트 내의 \\bin\\debug 디렉터리로 이동한 후 다음 명령을 실행합니다.

	.\CreateRCluster <cluster-name>

클러스터 이름을 제공하고 ENTER 키를 눌러 R이 설치된 클러스터를 프로비전합니다.

## <a name="seeAlso"></a>참고 항목

- [HDInsight 클러스터에 Spark 설치 및 사용][hdinsight-install-spark] - 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Spark를 설치하고 사용하는 방법에 대한 지침을 제공합니다. Spark는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다.
- [HDInsight 클러스터에 Giraph 설치](../hdinsight-hadoop-giraph-install). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.
- [HDInsight 클러스터에 Solr 설치](../hdinsight-hadoop-solr-install). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.

[powershell-install-configure]: install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/

<!--HONumber=54-->