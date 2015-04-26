<properties 
	pageTitle="스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정| Azure" 
	description="스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다." 
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
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# 스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정

Azure HDInsight 클러스터를 사용자 지정하여 클러스터에 소프트웨어를 추가로 설치하거나 클러스터에서 응용 프로그램 구성을 변경할 수 있습니다. HDInsight는 클러스터에서 수행할 사용자 지정을 정의하는 사용자 지정 스크립트를 호출하는 **스크립트 작업**이라는 구성 옵션을 제공합니다. 이러한 스크립트는  *as it is being deployed* 클러스터를 사용자 지정하는 데 사용될 수 있습니다.  

HDInsight 클러스터를 사용자 지정하는 방법은 그 밖에도 추가 저장소 계정 포함, hadoop 구성 파일(core-site.xml, hive-site.xml 등) 변경, 클러스터의 공통 위치에 공유 라이브러리(예: Hive, Oozie) 추가 등을 비롯해 다양합니다. 이러한 사용자 지정은 HDInsight PowerShell, .NET SDK 또는 Azure 관리 포털을 사용하여 수행할 수 있습니다. 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight에서 Hadoop 클러스터 프로비전][hdinsight-provision-cluster](영문)을 참조하세요.



> [AZURE.NOTE] 스크립트 작업을 사용한 클러스터 사용자 지정은 HDInsight 클러스터 버전 3.1에서만 지원됩니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)을 참조하세요.



## 이 문서의 내용

- [클러스터를 만드는 동안 스크립트를 사용하는 방법](#lifecycle)
- [클러스터 사용자 지정을 위한 스크립트를 작성하는 방법](#writescript)
- [스크립트 작업을 사용하여 클러스터를 사용자 지정하는 방법](#howto)
- [클러스터 사용자 지정 예제](#example)
- [HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어에 대한 지원](#support)


## <a name="lifecycle"></a>클러스터를 만드는 동안 스크립트를 사용하는 방법

스크립트 작업을 사용할 경우 HDInsight 클러스터를 만드는 동안에만 클러스터를 사용자 지정할 수 있습니다. HDInsight 클러스터를 만드는 중 거치는 단계는 다음과 같습니다.

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

스크립트가 호출되는 때는 클러스터 생성 과정에서  *HDInsightConfiguration* 단계가 완료되고  *ClusterOperational* 단계가 시작되기 전입니다. 각 클러스터에는 지정된 순서대로 호출되는 여러 스크립트 작업을 사용할 수 있습니다.

> [AZURE.NOTE] HDInsight 클러스터를 사용자 지정하는 옵션은 표준 Azure HDInsight 구독의 일부로 추가 요금 없이 제공됩니다.

### 스크립트 작동 방식

스크립트를 헤드 노드, 작업자 노드 또는 두 노드 모두에서 실행할 수 있습니다. 스크립트가 실행 중이면 클러스터의  *ClusterCustomization* 단계가 시작됩니다. 이 단계에서 스크립트는 시스템 관리자 계정으로 클러스터의 지정된 모든 노드에서 병렬로 실행되며 해당 노드에서 모든 관리자 권한을 제공합니다. 

> [AZURE.NOTE]  *Cluster customization* 단계 중 클러스터 노드에 대한 관리 권한이 있으므로 스크립트를 사용하여 Hadoop 관련 서비스를 비롯한 서비스의 중지 및 시작과 같은 작업을 수행할 수 있습니다. 따라서 스크립트의 일부로 스크립트 실행이 완료되기 전에 Ambari 서비스 및 기타 Hadoop 관련 서비스가 실행 중인지 확인해야 합니다. 클러스터가 생성되는 동안 클러스터의 상태를 확인하려면 이러한 서비스가 필요합니다. 클러스터에서 이러한 서비스에 영향을 주는 구성을 변경하는 경우 제공되는 도우미 함수를 사용해야 합니다. 도우미 함수에 대한 자세한 내용은 [HDInsight를 사용한 스크립트 작업 개발][hdinsight-write-script](영문)을 참조하세요.

스크립트의 출력 및 오류 로그는 클러스터에 대해 지정한 기본 저장소 계정에 저장됩니다. 로그는 이름이  *u<\cluster-name-fragment><\time-stamp>setuplog*인 테이블에 저장됩니다. 이는 클러스터의 모든 노드(헤드 노드 및 작업자 노드)에서 실행된 스크립트의 집계 로그입니다.

## <a name="writescript"></a>클러스터 사용자 지정을 위한 스크립트를 작성하는 방법

클러스터 사용자 지정 스크립트를 작성하는 방법에 대한 자세한 내용은 [HDInsight를 사용한 스크립트 작업 개발][hdinsight-write-script](영문)을 참조하세요. 

## <a name="howto"></a>스크립트 작업을 사용하여 클러스터를 사용자 지정하는 방법

Azure 관리 포털, PowerShell cmdlet 또는 HDInsight.NET SDK에서 스크립트 작업을 사용하여 클러스터를 사용자 지정할 수 있습니다. 

**관리 포털에서**

1. [사용자 지정 옵션을 사용하여 클러스터를 프로비저닝](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal)에 설명된 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터 프로비저닝을 시작합니다. 
2. 마법사의 스크립트 작업 페이지에서 **스크립트 작업 추가**를 클릭하고 아래와 같이 스크립트 작업에 대한 세부 정보를 제공합니다.

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다.</td></tr>
		<tr><td>노드 유형</td>
			<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>Head nodes only</b> 또는 <b>Worker nodes only</b>를 선택할 수 있습니다.
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다.</td></tr>
	</table>

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 프로비저닝을 시작합니다. 
  
**PowerShell cmdlet 사용**

HDInsight PowerShell 명령을 사용하여 단일 스크립트 작업 또는 여러 스크립트 작업을 실행합니다. **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet을 사용하여 사용자 지정 스크립트를 호출할 수 있습니다. 이 cmdlet을 사용하려면 Azure PowerShell이 설치 및 구성되어 있어야 합니다. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

HDInsight 클러스터를 배포할 때 단일 스크립트 작업을 실행하려면 다음 PowerShell 명령을 사용합니다.

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

HDInsight 클러스터를 배포할 때 여러 스크립트 작업을 실행하려면 다음 PowerShell 명령을 사용합니다.

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**HDInsight .NET SDK 사용**

HDInsight .NET SDK는 <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> 클래스를 제공하여 사용자 지정 스크립트를 호출합니다. HDInsight .NET SDK를 사용하려면

1. Visual Studio 응용 프로그램을 만들고 Nuget에서 SDK를 설치합니다. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭하고 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. SDK를 사용하여 클러스터를 만듭니다. 자세한 내용은 [.NET SDK를 사용하여 HDInsight 클러스터 프로비전](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#sdk)을 참조하세요.

3. **ScriptAction** 클래스를 사용하여 아래와 같이 사용자 지정 스크립트를 호출합니다.

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>클러스터 사용자 지정 예제

HDInsight는 시작하는 데 도움을 주기 위해 HDInsight 클러스터에서 다음 구성 요소를 설치하는 샘플 스크립트를 제공합니다.

- **Spark를 설치합니다**. [HDInsight 클러스터에 Spark 설치][hdinsight-install-spark]를 참조하세요.
- **R을 설치합니다**. [HDInsight 클러스터에서 R 설치][hdinsight-install-r]를 참조하세요.
- **Solr을 설치합니다**. [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)
- **Giraph를 설치합니다**. [HDInsight 클러스터에 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)

## <a name="support"></a>HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어에 대한 지원
Microsoft Azure HDInsight 서비스는 Hadoop에 형성된 오픈 소스 기술의 에코시스템을 사용하여 클라우드에 빅 데이터 응용 프로그램을 빌드할 수 있는 유연한 플랫폼입니다. Microsoft Azure는 <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure 지원 FAQ 웹 사이트의 지원 범위 섹션</a>에서 설명된 것처럼 오픈 소스 기술에 대해 일반적인 수준의 지원을 제공합니다. 또한 HDInsight 서비스는 아래에 설명된 일부 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

- **기본 제공 구성 요소**. 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어 Yarn 리소스 관리자, Hive 쿼리 언어 및 Mahout 라이브러리가 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">여기</a>에서 다운로드할 수 있습니다.
- **사용자 지정 구성 요소**. 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

기본 제공 구성 요소는 완전히 지원되며, Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.

사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 다음과 같은 여러 커뮤니티 사이트를 이용할 수 있습니다. <a href ="https://social.msdn.microsoft.com/Forums/azure/ko-kr/home?forum=hdinsight" target="_blank">HDInsight에 대한 MSDN 포럼</a>, <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>. 또한 Apache 프로젝트는 <a href="http://apache.org" target="_blank">http://apache.org</a>에 프로젝트 사이트가 있습니다(예: <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>, <a href="http://spark.apache.org/" target="_blank">Spark)</a>.

HDInsight 서비스는 사용자 지정 구성 요소를 사용하는 여러 방법을 제공합니다. 구성 요소가 클러스터에 설치되고 사용되는 방법과 상관없이, 동일한 수준의 지원이 적용됩니다. 다음은 HDInsight 클러스터에서 사용자 지정 구성 요소를 사용할 수 있는 가장 일반적인 방법의 목록입니다.

1. 작업 제출. Hadoop 또는 기타 유형의 작업을 사용자 지정 구성 요소를 실행하거나 사용하는 클러스터에 제출할 수 있습니다.
2. 클러스터 사용자 지정. 클러스터를 만들 때 클러스터 노드에 설치되는 사용자 지정 구성 요소 및 추가 설정을 지정할 수 있습니다.
3. 샘플. 인기 있는 사용자 지정 구성 요소의 경우, Microsoft와 다른 사람들이 이러한 구성 요소를 HDInsight 클러스터에서 어떻게 사용할 수 있는지에 대한 샘플을 제공할 수 있습니다. 이러한 샘플은 지원 없이 제공됩니다.


## 참고 항목##
[사용자 지정 옵션을 사용하여 HDInsight의 Hadoop 클러스터 프로비전][hdinsight-provision-cluster]에서는 다른 사용자 지정 옵션을 사용하여 HDInsight 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"

<!--HONumber=42-->
