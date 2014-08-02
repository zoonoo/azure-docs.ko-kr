<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor HDInsight clusters using the Ambari API" pageTitle="Monitor HDInsight clusters using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor HDInsight clusters using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

Ambari API를 사용하여 HDInsight 클러스터 모니터링
=================================================

Ambari API를 사용하여 HDInsight 클러스터 버전 2.1을 모니터링하는 방법을 알아봅니다.

**예상 완료 시간:** 15분

이 문서에서는 다음을 수행합니다.
--------------------------------

-   [Ambari 정의](#whatisambari)
-   [필수 조건](#prerequisites)
-   [신속한 시작](#jumpstart)
-   [Ambari 모니터링 API](#monitor)
-   [다음 단계](#nextsteps)

Ambari 정의
-----------

[Apache Ambari](http://ambari.apache.org/)(영문)는 Apache Hadoop 클러스터를 프로비전하고 관리 및 모니터링합니다. Hadoop의 복잡성을 숨기고 클러스터 작업을 단순화하는 직관적인 연산자 도구 모음 및 강력한 API 집합이 포함되어 있습니다. API에 대한 자세한 내용은 [Ambari API 참조](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)(영문)를 참조하십시오.

HDInsight는 현재 Ambari 모니터링 기능만 지원합니다. Ambari API v1.0은 HDInsight 클러스터 버전 2.1 및 3.0에서 지원됩니다. 이 문서에서는 HDInsight 클러스터 버전 2.1에서 Ambari API 실행에 대해서만 다룹니다.

필수 조건
---------

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure PowerShell이 설치 및 구성된 **워크스테이션**. 자세한 내용은 [Azure PowerShell 설치 및 구성](../install-configure-powershell/)을 참조하십시오. PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행](http://technet.microsoft.com/en-us/library/ee176949.aspx)(영문)을 참조하십시오.

    [Curl](http://curl.haxx.se)(영문)은 선택 사항입니다. [여기](http://curl.haxx.se/download.html)(영문)에서 설치할 수 있습니다.

    > [WACOM.NOTE] Windows에서 curl 명령을 사용할 때는 옵션 값에 작은따옴표 대신 큰따옴표를 사용합니다.

-   **Azure HDInsight 클러스터**. 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 사용 시작](../hdinsight-get-started/) 또는 [HDInsight 클러스터 프로비전](../hdinsight-provision-clusters/)을 참조하십시오. 자습서를 완료하려면 다음 데이터가 필요합니다.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">클러스터 속성</th><th data-morhtml="true">PowerShell 변수 이름</th><th data-morhtml="true">값</th><th data-morhtml="true">설명</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 이름</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터의 이름입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">클러스터 사용자 이름</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">프로비전 시 지정된 클러스터 사용자 이름입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">클러스터 암호</td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">클러스터 사용자 암호입니다.</td></tr>
  </table>

    > [WACOM.NOTE] 테이블에 값을 채우십시오. 이 자습서를 완료하는 데 유용합니다.

신속한 시작
-----------

Ambari를 사용하여 HDInsight 클러스터를 모니터링하는 몇 가지 방법이 있습니다.

**Azure PowerShell 사용**

다음은 MapReduce jobtracker 정보를 가져오는 PowerShell 스크립트입니다.

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

![Jobtracker 출력](./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png)

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
                 "host_name":"workernode0"}}]}

Ambari 모니터링 API
-------------------

다음 테이블은 가장 일반적으로 사용되는 Ambari 모니터링 API 호출을 나열합니다. API에 대한 자세한 내용은 [Ambari API 참조](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)(영문)를 참조하십시오.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">모니터링 API 호출</th><th data-morhtml="true">URI</th><th data-morhtml="true">설명</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">클러스터 가져오기</td><td data-morhtml="true">/api/v1/clusters</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">클러스터 정보 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td><td data-morhtml="true">클러스터, 서비스, 호스트</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">서비스 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td><td data-morhtml="true">서비스 포함 사항: hdfs, mapreduce</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">서비스 정보 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">서비스 구성 요소 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td><td data-morhtml="true">HDFS: namenode, datanode<br data-morhtml="true" />MapReduce: jobtracker; tasktracker</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">구성 요소 정보 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td><td data-morhtml="true">ServiceComponentInfo, host-components, 메트릭</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">호스트 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td><td data-morhtml="true">headnode0, workernode0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">호스트 정보 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">호스트 구성 요소 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</td><td data-morhtml="true">namenode, resourcemanager</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">호스트 구성 요소 정보 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</td><td data-morhtml="true">HostRoles, 구성 요소, 호스트, 메트릭</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">구성 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</td><td data-morhtml="true">구성 형식: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">구성 정보 가져오기</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&amp;tag=&lt;VersionName&gt; 
</td><td data-morhtml="true">구성 형식: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>

다음 단계
---------

Ambari 모니터링 API 호출을 사용하는 방법을 알아보았습니다. 자세한 내용은 다음을 참조하십시오.

-   [관리 포털을 사용하여 HDInsight 클러스터 관리](../hdinsight-administer-use-management-portal/)
-   [Azure PowerShell을 사용하여 HDInsight 클러스터 관리](../hdinsight-administer-use-powershell/)
-   [명령줄 인터페이스를 사용하여 HDInsight 클러스터 관리](../hdinsight-administer-use-command-line/)
-   [HDInsight 설명서](/en-us/documentation/services/hdinsight/)
-   [HDInsight 시작](../hdinsight-get-started/)

