<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링 | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링
 
Ambari API를 사용하여 HDInsight 클러스터 버전 3.1 및 2.1을 모니터링하는 방법에 대해 알아봅니다.

##이 문서에서는 다음을 수행합니다.

- [Ambari 정의](#whatisambari)
- [필수 조건](#prerequisites)
- [신속한 시작](#jumpstart)
- [Ambari 모니터링 API](#monitor)
- [다음 단계](#nextsteps)


## <a id="whatisambari"></a> Ambari 정의

[Apache Ambari][ambari-home](영문)는 Apache Hadoop 클러스터를 프로비전하고 관리 및 모니터링합니다. Hadoop의 복잡성을 숨기고 클러스터 작업을 단순화하는 직관적인 연산자 도구 모음 및 강력한 API 집합이 포함되어 있습니다. API에 대한 자세한 내용은 [Ambari API 참조][ambari-api-reference](영문)를 참조하세요.


HDInsight는 현재 Ambari 모니터링 기능만 지원합니다. Ambari API v1.0은 HDInsight 클러스터 버전 2.1 및 3.0에서 지원됩니다.  이 문서에서는 HDInsight 클러스터 버전 3.1 및 2.1에서 Ambari API에 액세스하는 방법에 대해 설명합니다.  두 버전 간의 가장 큰 차이점은 작업 기록 서버와 같은 새 기능이 도입되면서 일부 구성 요소가 변경되었다는 것입니다.


##<a id="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- Azure PowerShell이 설치 및 구성된 **워크스테이션**. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install]을 참조하세요. PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행][powershell-script](영문)을 참조하세요.

	[Curl][curl](영문)은 선택 사항입니다. [여기][curl-download](영문)에서 설치할 수 있습니다.

	>[WACOM.NOTE] Windows에서 curl 명령을 사용할 때는 옵션 값에 작은따옴표 대신 큰따옴표를 사용합니다.

- **Azure HDInsight 클러스터**. 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 사용 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 자습서를 완료하려면 다음 데이터가 필요합니다.

	<table border="1">
	<tr><th>클러스터 속성</th><th>PowerShell 변수 이름</th><th>값</th><th>설명</th></tr>
	<tr><td>HDInsight 클러스터 이름</td><td>$clusterName</td><td></td><td>HDInsight 클러스터의 이름입니다.</td></tr>
	<tr><td>클러스터 사용자 이름</td><td>$clusterUsername</td><td></td><td>프로비전 시 지정된 클러스터 사용자 이름입니다.</td></tr>
	<tr><td>클러스터 암호</td><td>$clusterPassword</td><td></td><td>클러스터 사용자 암호입니다.</td></tr>
	</table>

	> [WACOM.NOTE] 테이블에 값을 채우세요.  이 자습서를 완료하는 데 유용합니다.



##<a id="jumpstart"></a>신속한 시작

Ambari를 사용하여 HDInsight 클러스터를 모니터링하는 몇 가지 방법이 있습니다.

**Azure PowerShell 사용**

다음은 *3.1 클러스터에서* MapReduce jobtracker 정보를 가져오는 PowerShell 스크립트입니다.  여기서 가장 큰 차이점은 이제 MapReduce가 아닌 YARN 서비스에서 이러한 세부 정보를 가져온다는 것입니다.

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'yarn.queueMetrics'

다음은 *2.1 클러스터에서* MapReduce jobtracker 정보를 가져오는 PowerShell 스크립트입니다.

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'mapred.JobTracker'

출력은 다음과 같습니다.

![Jobtracker Output][img-jobtracker-output]

**curl 사용**

다음은 Curl을 사용하여 클러스터 정보를 가져오는 예입니다.

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

출력은 다음과 같습니다.
	
	{"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
	 "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
	 "services"[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
	 "hosts":[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

2014/10/8 릴리스 정보:
이제 Ambari 끝점 "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}"을 사용할 때 *host_name* 필드가 호스트 이름이 아닌 노드의 FQDN(정규화된 도메인 이름)을 반환합니다. 2014/10/8 릴리스 이전 버전에서는 이 예제가 **headnode0**"만을 반환했습니다. 반면 2014/10/8 릴리스부터는 위의 예제에 나와 있는 것처럼 FQDN **headnode0.{ClusterDNS}.azurehdinsight.net**"이 반환됩니다. 이 변경은 HBase, Hadoop 등의 여러 클러스터 유형을 VNET(가상 네트워크) 하나에 배포할 수 있는 시나리오를 원활하게 수행하기 위해 필요한 작업이었습니다. 예를 들어 Hadoop의 백 엔드 플랫폼으로 HBase를 사용하는 등의 경우 이 변경이 적용됩니다.

##<a id="monitor"></a>Ambari 모니터링 API

다음 테이블은 가장 일반적으로 사용되는 Ambari 모니터링 API 호출을 나열합니다. API에 대한 자세한 내용은 [Ambari API 참조][ambari-api-reference](영문)를 참조하세요.

<table border="1">
<tr><th>모니터링 API 호출</th><th>URI</th><th>설명</th></tr>
<tr><td>클러스터 가져오기</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>클러스터 정보 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</tt></td><td>클러스터, 서비스, 호스트</td></tr>
<tr><td>서비스 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</tt></td><td>서비스에 포함: hdfs, mapreduce</td></tr>
<tr><td>서비스 정보 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</tt></td><td></td></tr>
<tr><td>서비스 구성 요소 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</tt></td><td>HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker</td></tr>
<tr><td>구성 요소 정보 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</tt></td><td>ServiceComponentInfo, host-components, 메트릭</td></tr>
<tr><td>호스트 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</tt></td><td>headnode0, workernode0</td></tr>
<tr><td>호스트 정보 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td></td></tr>
<tr><td>호스트 구성 요소 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</tt></td><td>namenode, resourcemanager</td></tr>
<tr><td>호스트 구성 요소 정보 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</tt></td><td>HostRoles, 구성 요소, 호스트, 메트릭</td></tr>
<tr><td>구성 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</tt></td><td>구성 유형: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>구성 정보 가져오기</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&tag=&lt;VersionName&gt; 
</tt></td><td>구성 유형: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>


##<a id="nextsteps"></a>다음 단계 

Ambari 모니터링 API 호출을 사용하는 방법을 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [관리 포털을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-portal]
- [Azure PowerShell을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-powershell]
- [명령줄 인터페이스를 사용하여 HDInsight 클러스터 관리][hdinsight-admin-cli]
- [HDInsight 설명서][hdinsight-documentation]
- [HDInsight 시작][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[Powershell-install]: ../install-configure-powershell/
[Powershell-script]: http://technet.microsoft.com/ko-kr/library/ee176949.aspx 

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-documentation]: /ko-kr/documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png


<!--HONumber=35.1-->
