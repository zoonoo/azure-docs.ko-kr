<properties 
	pageTitle="스크립트 작업을 사용하여 Hadoop 클러스터에 Solr 설치 | Microsoft Azure" 
	description="Solr을 사용하여 HDInsight 클러스터를 사용자 지정하는 방법에 대해 알아봅니다. Solr을 설치하는 스크립트를 사용하는 스크립트 작업 구성 옵션을 사용하게 됩니다." 
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
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# HDInsight Hadoop 클러스터에서 Solr 설치 및 사용

**스크립트 작업** 클러스터 사용자 지정을 사용하여 Azure HDInsight의 Hadoop에서 모든 유형의 클러스터에 Solr을 설치할 수 있습니다. 스크립트 작업을 사용하면 클러스터를 생성할 때에만 클러스터를 사용자 지정하는 스크립트를 실행할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]을 참조하세요.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install.md)

이 항목에서는 스크립트 작업을 사용하여 Solr을 설치하는 방법에 대해 알아봅니다. Solr은 강력한 검색 플랫폼으로서 Hadoop에서 관리하는 데이터에 대한 엔터프라이즈 수준의 검색 기능을 제공합니다. HDInsight 클러스터에 Solr을 설치한 후에는 Solr을 사용하여 데이터를 검색하는 방법도 알아봅니다.

> [AZURE.NOTE]이 항목에 사용되는 샘플 스크립트로는 특정 구성의 Solr 클러스터가 만들어집니다. 서로 다른 컬렉션, 분할, 스키마, 복제 등으로 Solr 클러스터를 구성하려는 경우 그에 따라 이 스크립트와 Solr 바이너리를 수정해야 합니다.


## <a name="whatis"></a>Solr이란?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a>은 데이터에 대한 강력한 전체 텍스트 검색을 가능하게 해주는 엔터프라이즈 검색 플랫폼입니다. Hadoop는 막대한 양의 데이터를 저장 및 관리할 수 있도록 해주고 Apache Solr은 이 데이터를 신속하게 검색할 수 있는 검색 기능을 제공합니다. 이 항목에서는 HDInsight 클러스터를 사용자 지정하여 Solr을 설치하는 방법에 대한 지침을 제공합니다.

## <a name="install"></a>Solr을 설치하는 방법

HDInsight 클러스터에 Solr을 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1))에서 다운로드할 수 있습니다. 이 섹션에서는 Azure 포털을 사용하여 클러스터를 프로비전하면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다.


> [AZURE.NOTE]샘플 스크립트는 HDInsight 클러스터 버전 3.1에서만 작동합니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.


1. [사용자 지정 옵션을 사용하여 클러스터를 프로비저닝](hdinsight-provision-clusters.md#portal)에 설명된 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터 프로비저닝을 시작합니다. 
2. 아래와 같이 마법사의 **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-solr-install-v1/hdi-script-action-solr.png "스크립트 작업을 사용하여 클러스터 사용자 지정")
	
	<table border='1'>
	<tr><th>속성</th><th>값</th></tr>
	<tr><td>이름</td>
		<td>스크립트 작업의 이름을 지정합니다. 예를 들면 <b>Install Solr</b>과 같습니다.</td></tr>
	<tr><td>스크립트 URI</td>
		<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI(Uniform Resource Identifier)를 지정합니다. 예를 들면 <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i>과 같습니다.</td></tr>
	<tr><td>노드 유형</td>
		<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>작업자 노드만</b>을 선택할 수 있습니다.
	<tr><td>매개 변수</td>
		<td>스크립트에 필요한 경우 매개 변수를 지정합니다. Solr을 설치하는 스크립트에는 매개 변수가 필요하지 않으므로 비워 둘 수 있습니다.</td></tr>
</table>두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 프로비저닝을 시작합니다.

스크립트를 사용하여 Azure PowerShell 또는 HDInsight.NET SDK로 HDInsight에 Solr을 설치할 수도 있습니다. 이 절차에 대한 자세한 내용은 이 항목의 뒷부분에 제공됩니다.

## <a name="usesolr"></a>HDInsight에서 Solr을 사용하는 방법

데이터 파일로 Solr을 인덱싱하는 것부터 시작해야 합니다. 그런 다음 인덱싱한 데이터에 대해 Solr을 사용하여 검색 쿼리를 실행할 수 있습니다. HDInsight 클러스터에서 Solr을 사용하려면 다음 단계를 수행하세요.

1. **RDP(원격 데스크톱 프로토콜)를 사용하여 Solr이 설치된 HDInsight 클러스터에 원격으로 연결**합니다. Azure 포털에서 Solr을 설치하여 만든 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 클러스터에 원격으로 연결합니다. 지침은 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP를 사용하여 HDInsight 클러스터에 연결</a>을 참조하세요.

2. **데이터 파일을 업로드하여 Solr을 인덱싱**합니다. Solr을 인덱싱할 때 검색이 필요할 수 있는 문서를 포함시킵니다. Solr을 인덱싱하려면 RDP를 사용하여 클러스터에 원격으로 연결한 후 Hadoop 명령줄을 열고 **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**로 이동합니다. 다음 명령을 실행합니다.
	
		java -jar post.jar solr.xml monitor.xml

	콘솔에 다음 출력이 표시됩니다.

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	post.jar 유틸리티는 두 개의 샘플 문서 **solr.xml** 및 **monitor.xml**로 Solr을 인덱싱합니다. post.jar 유틸리티와 샘플 문서는 Solr 설치에서 사용할 수 있습니다.

3. **Solr 대시보드를 사용하여 인덱싱된 문서 내에서 검색**합니다. HDInsight 클러스터에 대한 RDP 세션에서 Internet Explorer를 열고 ****http://headnodehost:8983/solr/#/**에서 Solr 대시보드를 실행합니다. 왼쪽 창의 **Core Selector** 드롭다운에서 **collection1**을 선택하고, 그 안에서 **Query**를 클릭합니다. 한 예로, Solr의 모든 문서를 선택하고 반환하려면 다음 값을 제공하세요.
	1. **q** 텍스트 상자에 ***:***을 입력합니다. 이렇게 하면 Solr에서 인덱싱되는 문서는 모두 반환됩니다. 문서 내에서 특정 문자열을 검색하려는 경우 여기에 해당 문자열을 입력할 수 있습니다.
	2. **wt** 텍스트 상자에서 출력 형식을 선택합니다. 기본값은 **json**입니다. **Execute Query**를 클릭합니다.

		![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-solr-install-v1/hdi-solr-dashboard-query.png "Solr 대시보드에서 쿼리 실행")
	
	출력에는 Solr 인덱싱에 사용되는 두 문서가 반환됩니다. 출력은 다음과 유사합니다.

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **권장: Solr에서 인덱싱된 데이터를 HDInsight 클러스터와 연결된 Azure Blob 저장소에 백업합니다**. Solr 클러스터 노드에서 인덱싱된 데이터를 Azure Blob 저장소에 백업하는 것이 좋습니다. 이렇게 하려면 다음 단계를 수행합니다.

	1. RDP 세션에서 Internet Explorer를 열고 다음 URL을 가리킵니다.

			http://localhost:8983/solr/replication?command=backup

		다음과 같은 응답이 표시됩니다.

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. 원격 세션에서 {SOLR\_HOME}\\{Collection}\\data로 이동합니다. 샘플 스크립트를 통해 만든 클러스터의 경우 이 경로는 **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**입니다. 이 위치에 **snapshot.*timestamp***와 유사한 이름으로 만든 스냅숏 폴더가 표시됩니다.
	
	3. 스냅숏 폴더를 압축하고 Azure Blob 저장소에 업로드합니다. Hadoop 명령줄에서 다음 명령을 사용하여 스냅숏 폴더의 위치로 이동합니다.

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		이 명령은 스냅숏을 클러스터와 연결된 기본 저장소 계정 내의 컨테이너 아래에 있는 /example/data/에 복사합니다.

## <a name="usingPS"></a>Azure PowerShell을 사용하여 HDInsight Hadoop 클러스터에 Solr 설치

이 섹션에서는 스크립트 작업을 통해 스크립트를 호출하여 클러스터를 사용자 지정하는 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet을 사용합니다. 계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight Windows PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

다음 단계를 수행합니다.

1. Azure PowerShell 창을 열고 다음 변수를 선언합니다.

		# PROVIDE VALUES FOR THESE VARIABLES
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

		# Add the script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

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
	
4. 마지막으로 Solr이 설치된 사용자 지정된 클러스터 프로비전을 시작합니다.
	
		# Start provisioning a cluster with Solr installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.


## <a name="usingSDK"></a>.NET SDK를 사용하여 HDInsight Hadoop 클러스터에 Solr 설치

HDInsight .NET SDK는 .NET Framework 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 이 섹션에서는 SDK에서 스크립트 작업을 사용하여 Solr이 설치된 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. 다음 절차를 수행해야 합니다.

- HDInsight .NET SDK 설치
- 자체 서명된 인증서 만들기
- 콘솔 응용 프로그램 만들기
- 응용 프로그램 실행


**HDInsight .NET SDK를 설치하려면**

[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)에서 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

**자체 서명된 인증서를 만들려면**

자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.


**Visual Studio 응용 프로그램을 만들려면**

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
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

10. 사용자 지정 스크립트를 호출하여 Solr을 설치하는 [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 클래스를 사용하는 다음 코드를 Main() 함수에 추가합니다.

		// Add the script action to install Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //Because the script used does not require any parameters
        ));

11. 마지막으로 클러스터를 만듭니다.

		client.CreateCluster(clusterInfo);

11. 응용 프로그램에 대한 변경 내용을 저장하고 솔루션을 빌드합니다.

**응용 프로그램을 실행하려면**

Windows PowerShell 또는 Azure PowerShell 콘솔을 열고 Visual Studio 프로젝트를 저장한 위치로 이동하고 프로젝트 내의 \\bin\\debug 디렉터리로 이동한 후 다음 명령을 실행합니다.

	.\CreateSolrCluster <cluster-name>

클러스터 이름을 제공하고 ENTER 키를 눌러 Solr이 설치된 클러스터를 프로비전합니다.


## 참고 항목##
- [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]. 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 Spark를 설치합니다. Spark는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다.
- [HDInsight 클러스터에 R 설치][hdinsight-install-r]. 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 R을 설치합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. 수백 개의 기본 제공 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 고유한 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.
- [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=August15_HO8-->