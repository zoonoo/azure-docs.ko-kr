<properties title="HDInsight Release Notes" pageTitle="HDInsight 릴리스 정보 | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />


#Microsoft HDInsight 릴리스 정보

## 2014/10/15 릴리스 정보 ##

이 핫픽스 릴리스에서는 Templeton을 많이 사용하는 사용자에게 영향을 주는 Templeton의 메모리 누수 현상이 해결되었습니다. 요청을 실행하기 위한 메모리가 부족하여 Templeton을 많이 사용하는 사용자에게 500 오류 코드가 표시되는 경우가 있었습니다. 이 문제를 해결하려면 Templeton 서비스를 다시 시작해야 했습니다. 이 릴리스에서는 해당 문제가 해결되었습니다.


## 2014/10/7 릴리스 정보 ##

* 이제 Ambari 끝점 "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}"을 사용할 때 *host_name* 필드가 호스트 이름 대신 노드의 FQDN(정규화된 도메인 이름)을 반환합니다. 예를 들어 "**headnode0**"가 반환되는 대신 FQDN인 "**headnode0.{ClusterDNS}.azurehdinsight.net**"이 반환됩니다. 이 변경은 HBase, Hadoop 등의 여러 클러스터 유형을 VNET(가상 네트워크) 하나에 배포할 수 있는 시나리오를 원활하게 수행하기 위해 필요한 작업이었습니다. 예를 들어 Hadoop의 백 엔드 플랫폼으로 HBase를 사용하는 등의 경우 이 변경이 적용됩니다.

* HDInsight 클러스터의 기본 배포에 대해 새 메모리 설정이 제공됩니다. 이전의 기본 메모리 설정에서는 배포 중인 CPU 코어 수에 대한 지침을 적절하게 고려하지 않았습니다. 아래 표에는 기본 4개 CPU 코어(8개 컨테이너) HDInsight 클러스터에서 사용되는 새 메모리 설정이 개별 항목으로 나와 있습니다. 이번 릴리스 이전에 사용되었던 값도 괄호 안에 나와 있습니다. 
 
<table border="1">
<tr><th>구성 요소</th><th>메모리 할당</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB(이전에는 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144MB(변경되지 않음)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144MB(변경되지 않음)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB(이전에는 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m(이전에는 -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB(이전에는 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m(이전에는 -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB(이전에는 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m(이전에는 -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256MB(이전에는 200MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536MB(변경되지 않음)</td></tr>

</table><br>

HDInsight에서 사용하는 Hortonworks Data Platform에서 YARN 및 MapReduce에 사용되는 메모리 구성 설정에 대한 자세한 내용은 [HDP 메모리 구성 설정 확인](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html)을 참조하세요. Hortonworks에서는 적절한 메모리 설정을 계산하는 도구도 제공합니다.

HDInsight PowerShell/SDK 오류: "*클러스터가 Http 서비스 액세스를 위해 구성되어 있지 않습니다.*":

* 이 오류는 SDK/PowerShell 버전과 클러스터의 버전 차이로 인해 발생할 수 있는 알려진 [호환성 문제](https://social.msdn.microsoft.com/Forums/azure/en-US/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight)입니다. 8/15 또는 그 이후에 만든 클러스터는 가상 네트워크에 대한 새로운 프로비전 기능을 지원합니다. 그러나 이전 버전의 SDK/PowerShell에서는 이 기능이 올바르게 해석되지 않습니다. 따라서 일부 작업 제출 작업이 실패합니다. SDK API 또는 PowerShell cmdlet을 사용하여 작업을 제출하는 경우(**Use-AzureHDInsightCluster**, **Invoke-Hive**) 해당 작업이 실패하고 "*<clustername> 클러스터가 Http 서비스 액세스를 위해 구성되어 있지 않습니다.*" 오류 메시지가 표시되거나 작업에 따라 "*클러스터에 연결할 수 없습니다.*" 등의 다른 오류 메시지가 표시될 수 있습니다.

* HDInsight SDK 및 Azure PowerShell의 최신 버전에서는 이러한 호환성 문제가 해결되었습니다. HDInsight SDK는 버전 1.3.1.6 이상으로, Azure PowerShell 도구는 버전 0.8.8 이상으로 업데이트하는 것이 좋습니다. 최신 HDInsight SDK는 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)에서, Azure PowerShell 도구는 [Azure PowerShell을 설치 및 구성하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/install-configure-powershell/)에서 얻을 수 있습니다.

* 클러스터 버전이 동일하게 유지되면 SDK와 PowerShell은 클러스터에 대한 새 업데이트에서도 계속 작동합니다. 예를 들어 클러스터 버전 3.1은 SDK/PowerShell의 최신 버전인 1.3.1.6 및 0.8.8과 항상 호환됩니다.


## HDinsight 3.1의 2014/9/12 릴리스 정보##

* 이 릴리스는 HDP(Hortonworks Data Platform) 2.1.5를 기반으로 합니다. 이 릴리스에서 수정된 버그 목록은 Hortonworks 사이트의 [이 릴리스에서 수정된 버그](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) 페이지를 참조하세요.
* pig 라이브러리 폴더의 "avro-mapred-1.7.4.jar" 파일이 avro-mapred-1.7.4-hadoop2.jar로 변경되었습니다. 이 파일의 내용에는 사소한 버그 수정(줄 바꿈 하지 않음)이 포함되어 있습니다. 고객은 파일 이름을 바꿀 때 줄 바꿈을 방지할 수 있도록 JAR 파일 자체의 이름에 대한 직접 종속성을 사용하지 않는 것이 좋습니다.


## 2014/8/21 릴리스 정보 ##

* Templeton 컨트롤러 작업에 대한 기본 메모리 제한을 1GB(이전 기본값은 512MB)로 설정하는 다음과 같은 새로운 WebHCat 구성(HIVE-7155)이 추가됩니다.
	
	* templeton.mapper.memory.mb (=1024)
	* 이 변경은 낮은 메모리 제한으로 인해 특정 Hive 쿼리가 발생하는 다음 오류를 해결합니다. "컨테이너가 실제 메모리 제한 이상으로 실행합니다."
	* 이전 기본값으로 다시 돌리려면 클러스터 생성 시 다음 명령을 사용하여 PowerShell SDK를 통해 이 구성 값을 512로 설정하면 됩니다.
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* zookeeper 역할의 호스트 이름이 zookeeper로 변경되었습니다. 이 변경은 클러스터 내의 이름 확인에 영향을 주지만 외부 REST API에는 영향을 주지 않습니다. zookeepernode 호스트 이름을 사용하는 구성 요소가 있는 경우 새로운 이름을 사용하도록 해당 구성 요소를 업데이트해야 합니다. 3개 zookeeper 노드의 새로운 이름은 다음과 같습니다. 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* HBase 버전 지원 매트릭스가 업데이트됩니다. 프로덕션 HBase 작업에는 버전 HDInsight 3.1(HBase 버전 0.98)만 지원됩니다. 미리 보기에 사용 가능한 버전 3.0은 앞으로 지원되지 않습니다. 전환 기간 중 고객은 계속 3.0 버전의 클러스터를 만들 수 있습니다. 

## 2014/8/15 이전에 만든 클러스터 관련 참고 사항 ##

SDK/PowerShell과 클러스터 간의 버전 차이로 인해 HDInsight PowerShell/SDK 오류가 발생하고 "<clustername> 클러스터가 Http 서비스 액세스를 위해 구성되어 있지 않습니다." 메시지 또는 작업에 따라 "클러스터에 연결할 수 없습니다."와 같은 기타 오류 메시지가 표시될 수 있습니다. 8/15 또는 그 이후에 만든 클러스터는 가상 네트워크에 대한 새로운 프로비전 기능을 지원합니다. 그러나 이전 버전의 SDK/PowerShell에서는 이 기능이 올바르게 해석되지 않아 작업을 제출하는 작업이 실패합니다. SDK API 또는 PowerShell cmdlet(예: Use-AzureHDInsightCluster 또는 Invoke-AzureHDInsightHiveJob)을 사용하여 작업을 제출하는 경우 해당 작업이 실패하고 위에서 설명한 오류 메시지 중 하나가 표시될 수 있습니다.

SDK 및 Azure PowerShell의 최신 버전에서는 이러한 호환성 문제가 해결되었습니다. HDInsight SDK는 버전 1.3.1.6 이상으로, Azure PowerShell 도구는 버전 0.8.8 이상으로 업데이트하는 것이 좋습니다. 최신 HDInsight SDK는 [NuGet](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/)에서, Azure PowerShell 도구는 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/?linkid=9811175&clcid=0x409)를 사용하여 얻을 수 있습니다.

클러스터 버전이 동일하게 유지되면 SDK와 PowerShell은 클러스터에 대한 새 업데이트에서도 계속 작동합니다. 예를 들어 클러스터 버전 3.1은 SDK/PowerShell의 최신 버전인 1.3.1.6 및 0.8.8과 항상 호환됩니다.


## 2014/7/28 릴리스 정보 ##

* **새로운 지역에서 사용 가능한 HDInsight**: 이 릴리스에서는 HDInsight 제공 지역을 새로운 3개 지역으로 확장했습니다. 이제 HDInsight 고객은 다음 지역에 클러스터를 만들 수 있습니다. 
	* 동아시아 
	* 미국 중북부 
	* 미국 중남부 
* 이 릴리스에서 HDInsight v1.6(HDP1.1, Hadoop 1.0.3) 및 HDInsight v2.1(HDP1.3, Hadoop 1.2)이 Azure 관리 포털에서 제거됩니다. HDInsight PowerShell cmdlet([New-AzureHDInsightCluster](http://msdn.microsoft.com/ko-kr/library/dn593744.aspx))을 사용하거나 [HDInsight SDK](http://msdn.microsoft.com/ko-kr/library/azure/dn469975.aspx)를 사용하여 계속 Hadoop 클러스터를 만들 수 있습니다. 자세한 내용은 [HDInsight 구성 요소 버전](http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-component-versioning/) 페이지를 참조하세요.
* 이 릴리스의 HDP(Hortonworks Data Platform) 변경 내용: 

<table border="1">
<tr><th>HDP</th><th>변경 내용</th></tr>
<tr><td>HDP 1.3/HDI 2.1</td><td>변경 내용 없음</td></tr>
<tr><td>HDP 2.0/HDI 3.0</td><td>변경 내용 없음</td></tr>
<tr><td>HDP 2.1/HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## 2014/6/24 릴리스 정보 ##

이 릴리스에는 HDInsight 서비스의 새로운 개선 기능이 포함되어 있습니다. 

* **HDP 2.1 사용 가능**: 이제 HDP 2.1을 포함하는 HDInsight 3.1이 일반 공급되며 새로운 클러스터의 기본 버전입니다.
* **HBase - Azure 관리 포털 개선 사항**: 미리 보기에서 HBase 클러스터를 사용할 수 있습니다. 이제 3번의 클릭으로 포털에서 HBase 클러스터를 만들 수 있습니다.

![](http://i.imgur.com/cmOl5fM.png)

HBase에서는, 수백만 개의 끝점에서 들어오는 센서 및 원격 분석 데이터를 저장하는 서비스의 대형 데이터 집합을 사용하는 대화형 웹 사이트에서 HDInsight에 대한 여러 실시간 작업을 구성할 수 있습니다. 다음 단계는 Hadoop 작업으로 이러한 작업의 데이터를 분석하는 것이며, PowerShell 및 Hive 클러스터 대시보드와 같이 제공된 환경을 통해 HDInsight에서 즉시 수행할 수 있습니다.

### 이제 HDInsight 3.1에 사전 설치되는 Apache Mahout ###

 [Mahout](http://hortonworks.com/hadoop/mahout/)은 HDInsight 3.1 Hadoop 클러스터에 사전 설치됩니다. 따라서 추가 클러스터 구성 없이도 Mahout 작업을 실행할 수 있습니다. 예를 들어 RDP(원격 데스크톱 프로토콜)를 사용하면 Hello world Mahout 명령을 실행하는 추가 단계 없이도 Hadoop 클러스터에 원격으로 연결할 수 있습니다.

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

이 절차에 대한 자세한 설명은 Apache Mahout 웹 사이트의 [Breiman 예제](https://mahout.apache.org/users/classification/breiman-example.html) 설명서를 참조하세요. 


### Hive 쿼리는 HDinsight 3.1에서 Tez를 사용할 수 있음 ###

이제 Hive 0.13은 HDInsight 3.1에서 사용 가능하며 Tez를 사용하여 쿼리를 실행할 수 있으므로, 실질적인 성능 개선을 이룰 수 있습니다. 
Tez는 기본적으로 Hive 쿼리에 사용할 수 없습니다. Tez를 사용하려면 옵트인해야 합니다. 다음 코드 조각을 실행하여 Tez를 사용하도록 설정할 수 있습니다.

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks에서 표준 벤치마크로 전달되는 Tez와 함께 사용하는 Hive 쿼리 성능 개선 사항의 자세한 분석을 게시했습니다. 자세한 내용은 [Enterprise Hadoop용 Apache Hive 13 벤치마킹](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/)을 참조하세요. 

Tez와 함께 Hive 사용에 대한 자세한 내용은 [Tez의 Hive 위키 페이지](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)를 참조하세요.

###전 세계 이용 가능 여부
Hadoop 2.2의 Azure HDInsight 릴리스부터 모든 주요 Azure 지역에서 HDInsight를 사용할 수 있게 되었습니다. 특별히 서유럽과 동남아시아 데이터 센터를 온라인으로 전환했습니다. 따라서 고객은 준수 요구 사항이 비슷한 영역에 있는, 가까운 데이터 센터에서 클러스터를 찾을 수 있습니다. 


###클러스터 버전 간에 수행할 수 있는 작업과 수행할 수 없는 작업

**HDInsight 3.1 클러스터에 사용되는 Oozie 메타 저장소가 이전 버전인 HDInsight 2.1 클러스터와 호환되지 않으므로 해당 이전 버전에서는 사용할 수 없음**

HDInsight 3.1 클러스터와 함께 배포한 사용자 지정 Oozie 메타 저장소 데이터베이스는 HDInsight 2.1 클러스터와 함께 다시 사용할 수 없습니다. 해당 메타 저장소를 원래 2.1 클러스터에서 만든 경우에도 마찬가지입니다. 메타 저장소를 3.1 클러스터와 함께 사용하면 메타 저장소 스키마가 업그레이드되어 2.1 클러스터에 필요한 메타 저장소와는 더 이상 호환되지 않으므로 이 시나리오는 지원되지 않습니다. HDInsight 3.1 클러스터와 함께 사용했던 Oozie 메타 저장소를 다시 사용하려고 하면 2.1 클러스터를 사용할 수 없게 됩니다. 

**Oozie 메타 저장소는 클러스터 간에 공유할 수 없음**
일반적이고 직접적으로 설명하자면 Oozie 메타 저장소는 특정 클러스터에 연결되며 클러스터 간에 공유할 수 없습니다.

###주요 변경 내용

**접두사 구문**:
HDInsight 3.0 및   3.1 클러스터에서는 "wasb://" 구문만 지원됩니다. 이전 "asv://" 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.0 클러스터 이상 버전에서는 지원되지 않습니다. 즉, 정확히 "asv://" 구문을 명시적으로 사용하며 HDInsight 3.0   또는 3.1 클러스터에 제출하는 작업은 실패하게 됩니다. 대신 wasb:// 구문을 사용해야 합니다. 또한 asv:// 구문을 사용하는 리소스에 대한 명시적 참조를 포함하는 기존의 메타스토어로 만들어 HDInsight 3.0 또는 3.1 클러스터에 제출하는 작업도 실패하게 됩니다. 리소스 주소를 지정하려면 wasb://를 사용하여 이러한 메타스토어를 다시 만들어야 합니다. 


**포트**: HDInsight 서비스에 사용되는 포트가 변경되었습니다. 기존에 사용하는 포트 번호는 Windows OS 사용 후 삭제되는 포트 범위 내에 있었습니다. 포트는 수명이 짧은 인터넷 프로토콜 기반 통신용으로 미리 정의된 사용 후 삭제되는 범위에서 자동으로 할당됩니다. 허용되는 새로운 HDP(Hortonworks Data Platform) 서비스 포트 집합은 이제 이 범위를 벗어나므로 헤드 노드에서 실행 중인 서비스에 사용되는 포트로 인해 발생할 수 있는 충돌을 방지합니다. 새로운 포트 번호로 인한 특별한 변경은 없습니다. 이제 사용되는 번호는 다음과 같습니다.

 **HDInsight 1.6(HDP 1.1)**
<table border="1">
<tr><th>이름</th><th>값</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 and 3.1(HDP 2.0 and 2.1)**
<table border="1">
<tr><th>이름</th><th>값</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###종속성 

다음 종속성이 HDInsight 3.x (HDP2.x)에 추가되었습니다.

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

다음 종속성은 더 이상 HDInsight 3.x (HDP2.x)에 존재하지 않습니다.

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
*     json :
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* HBase:
* snappy

###버전 변경 내용 

다음 버전 변경 내용이 HDInsight 2.x(HDP1.x)와 HDInsight 3.x(HDP2.x) 간에 수행되었습니다.

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###드라이버
SQL Server JDBC 드라이버는 HDInsight에서 내부적으로 사용되며 외부 작업에는 사용되지 않습니다. ODBC를 사용하여 HDInsight에 연결하려는 경우 Microsoft Hive ODBC 드라이버를 사용하세요. Hive ODBC 사용에 대한 자세한 내용은 [Microsoft Hive ODBC 드라이버를 사용하여 HDInsight에 Excel 연결][connect-excel-with-hive-ODBC]를 참조하세요.


### 버그 수정 ###

이 릴리스에서 몇 가지 버그 수정을 통해 다음 HDInsight  (Hortonworks Data Platform - HDP) 버전을 새로 고쳤습니다.

* HDInsight 2.1(HDP 1.3)
* HDInsight 3.0(HDP 2.0)
* HDInsight 3.1(HDP 2.1)

## Hortonworks 릴리스 정보 ##

HDInsight 클러스터의 버전에 사용되는 HDP에 대한 릴리스 정보는 다음 위치에서 사용할 수 있습니다.

* HDInsight 클러스터 버전 3.1에서는 [Hortonworks Data Platform 2.1][hdp-2-1-1]을 기반으로 하는 Hadoop 배포를 사용합니다. 이는 Azure HDInsight 포털을 사용할 때 만든 기본 Hadoop 클러스터입니다.

* HDInsight 클러스터 버전 3.0에서는 [Hortonworks Data Platform 2.0][hdp-2-0-8]을 기반으로 하는 Hadoop 배포를 사용합니다.

* HDInsight 클러스터 버전 2.1에서는 [Hortonworks Data Platform 1.3][hdp-1-3-0]을 기반으로 하는 Hadoop 배포를 사용합니다. 

* HDInsight 클러스터 버전 1.6에서는 [Hortonworks Data Platform 1.1][hdp-1-1-0]을 기반으로 하는 Hadoop 배포를 사용합니다. 




[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html




