<properties
	pageTitle="HDInsight의 Hadoop 클러스터 가용성 | Microsoft Azure"
	description="Linux 기반 HDInsight 클러스터는 추가 헤드 노드를 사용하여 안정성과 가용성을 향상합니다."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="larryfr"/>

#HDInsight에서 Hadoop 클러스터의 가용성 및 안정성

두 번째 헤드 노드는 Azure HDInsight에 의해 배포된 Linux 기반 Hadoop 클러스터에 사용됩니다. 따라서 Azure에서 실행 중인 Hadoop 서비스 및 작업의 가용성과 안정성이 향상됩니다.

> [AZURE.NOTE]이 문서에 사용된 단계는 Linux 기반 HDInsight 클러스터에 한정됩니다. Windows 기반 클러스터를 사용하는 경우 Windows 관련 정보는 [HDInsight에서 Windows 기반 Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability.md)을 참조하세요.

##헤드 노드 이해

일부 Hadoop 구현에서는 데이터(작업자) 노드의 오류를 원활하게 관리하는 서비스 및 구성 요소를 호스트하는 단일 헤드 노드가 사용됩니다. 그러나 헤드 노드에서 실행 중인 마스터 서비스가 중단되면 클러스터 작동이 중단됩니다.

HDInsight 클러스터는 주 노드에서 오류가 발생할 경우 마스터 서비스 및 구성 요소가 보조 노드에서 계속 실행될 수 있도록 보조 헤드 노드를 제공합니다.

> [AZURE.IMPORTANT]두 헤드 노드는 동시에 클러스터 내에서 활성화되어 실행됩니다. 일부 서비스(예: HDFS 또는 YARN)는 항상 헤드 노드 하나에서만 '활성화'됩니다(다른 헤드 노드에서는 '대기'). 다른 서비스(예: HiveServer2 또는 Hive MetaStore)는 두 헤드 노드에서 동시에 활성화됩니다.

[ZooKeeper](http://zookeeper.apache.org/) 노드(ZK)는 헤드 노드에서 마스터 서비스의 리더 선택에 사용되고 서비스, 데이터(작업자) 노드 및 게이트웨이에서 마스터 서비스가 활성화된 헤드 노드를 알도록 하는 데 사용됩니다.

## 헤드 노드 액세스

일반적으로 공용 게이트웨이(Ambari 웹 및 REST API)를 통한 클러스터에 대한 모든 액세스는 헤드 노드가 여러 개인 것에 영향을 받지 않습니다. 요청은 활성 헤드 노드로 라우트되고 적절하게 서비스됩니다.

SSH를 사용하여 클러스터에 액세스하고 포트 22(SSH의 기본값)를 통해 연결할 경우 headnode0에 연결되고 포트 23을 통해 연결할 경우 headnode1에 연결됩니다.

### 내부 FQDN(정규화된 도메인 이름)

HDInsight 클러스터의 노드는 클러스터에서만 액세스할 수 있는 내부 IP 주소와 FQDN을 가집니다(예: 헤드 노드에 대한 SSH 세션 또는 클러스터에서 실행 중인 작업). 내부 FQDN 또는 IP 주소를 사용하여 클러스터의 서비스에 액세스할 경우, Ambari를 사용하여 서비스 액세스 시 사용할 IP 또는 FQDN을 확인해야 합니다.

예를 들어 Oozie 서비스는 헤드 노드에서만 실행될 수 있으며, SSH 세션에서 `oozie` 명령을 사용하려면 서비스에 대한 URL이 필요합니다. 다음 명령을 사용하여 Ambari에서 URL을 검색할 수 있습니다.

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

그러면 다음과 유사한 값이 반환되며, 이 값은 `oozie` 명령에 사용할 내부 URL을 포함합니다.

	"oozie.base.url": "http://headnode0.CLUSTERNAME-ssh.d9.internal.cloudapp.net:11000/oozie"

## 서비스 상태를 확인하는 방법

Ambari 웹 UI 또는 Ambari REST API를 사용하여, 헤드 노드에서 실행되는 서비스의 상태를 확인할 수 있습니다.

###Ambari REST API

다음 명령을 사용하여 Ambari REST API를 통해 서비스의 상태를 확인할 수 있습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* **PASSWORD**를 HTTP 사용자(관리자) 계정 암호로 바꿉니다.

* **CLUSTERNAME**을 클러스터 이름으로 바꿉니다.

* **SERVICENAME**을 상태를 확인할 서비스의 이름으로 바꿉니다.

예를 들어 **password** 암호를 사용하여 **mycluster**라는 클러스터의 **HDFS** 서비스 상태를 확인하려면 다음을 사용합니다.

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

응답은 다음과 유사합니다.

	{
	  "href" : "http://headnode0.mycluster-ssh.j7.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

URL은 현재 서비스가 **headnode0**에서 실행되고 있음을 알려줍니다.

상태는 현재 서비스가 실행되고 있음(**STARTED**)을 알려줍니다.

클러스터에 설치된 서비스를 모르는 경우 다음을 사용하여 목록을 검색할 수 있습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####서비스 구성 요소

서비스는 개별적으로 상태를 확인하려는 구성 요소를 포함할 수 있습니다. 예를 들어 HDFS는 NameNode 구성 요소를 포함합니다. 구성 요소에 대한 정보를 보려면 명령은 다음과 같습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

서비스에서 제공하는 구성 요소를 모르는 경우 다음을 사용하여 목록을 검색할 수 있습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Ambari 웹 UI

Ambari 웹 UI는 https://CLUSTERNAME.azurehdinsight.net에서 볼 수 있습니다. **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터의 HTTP 사용자 자격 증명을 입력합니다. 기본 HTTP 사용자 이름은 **admin**이고 암호는 클러스터를 만들 때 입력한 암호입니다.

Ambari 페이지로 이동하면 설치된 서비스가 페이지 왼쪽에 나열됩니다.

![설치된 서비스](./media/hdinsight-high-availability-linux/services.png)

서비스 옆에 상태를 나타내는 여러 아이콘이 표시될 수 있습니다. 서비스와 관련된 모든 경고는 페이지 위쪽의 **Alert(경고)** 링크를 사용하여 볼 수 있습니다. 각 서비스를 선택하여 해당 서비스에 대한 자세한 내용을 볼 수 있습니다.

서비스 페이지에서는 각 서비스의 상태 및 구성에 대한 정보를 제공하지만 서비스가 실행되고 있는 헤드 노드에 대한 정보는 제공하지 않습니다. 이 정보를 보려면 페이지 위쪽의 **Hosts(호스트)** 링크를 사용하세요. 그러면 헤드 노드를 비롯한 클러스터 내의 호스트가 표시됩니다.

![호스트 목록](./media/hdinsight-high-availability-linux/hosts.png)

헤드 노드 중 하나의 링크를 선택하면 해당 노드에서 실행되고 있는 서비스 및 구성 요소가 표시됩니다.

![구성 요소 상태](./media/hdinsight-high-availability-linux/nodeservices.png)

## 보조 헤드 노드의 로그 파일에 액세스하는 방법

###SSH

SSH를 통해 헤드 노드에 연결된 동안에는 **/var/log**에서 로그 파일을 찾을 수 있습니다. 예를 들어 **/var/log/hadoop-yarn/yarn**은 YARN에 대한 로그를 포함합니다.

각 헤드 노드는 고유한 로그 항목을 포함할 수 있으므로 두 가지 다에서 로그를 확인해야 합니다.

###Ambari

> [AZURE.NOTE]Ambari를 통해 로그 파일에 액세스하려면 SSH 터널이 필요합니다. 개별 서비스의 웹 사이트는 인터넷에서 공개적으로 노출되지 않기 때문입니다. SSH 터널의 사용에 대한 정보는 [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie, 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

Ambari 웹 UI에서 로그를 보려는 서비스(예: YARN)를 선택한 다음 **빠른 연결**을 사용하여 로그를 볼 헤드 노드를 선택합니다.

![빠른 연결을 사용하여 로그 보기](./media/hdinsight-high-availability-linux/viewlogs.png)

## 헤드 노드의 크기를 구성하는 방법 ##

헤드 노드의 크기는 클러스터를 만들 때만 선택할 수 있습니다. 헤드 노드의 기본 크기는 **A3**이며 코어 4개, 7GB 메모리 및 로컬 저장소 285GB를 제공합니다. [HDInsight 가격 책정 페이지](http://azure.microsoft.com/pricing/details/hdinsight/)에서 HDInsight에 사용할 수 있는 다양한 VM 크기(각각의 코어, 메모리 및 로컬 저장소 포함)의 목록을 찾을 수 있습니다.

새 클러스터를 만들 때 노드 크기를 지정할 수 있습니다. 다음은 [Azure Preview 포털][preview-portal], [Azure PowerShell][azure-powershell] 및 [Azure CLI][azure-cli]를 사용하여 크기를 지정하는 방법에 대한 정보를 제공합니다.

* **Azure Preview 포털**: 새 클러스터를 만들 때 클러스터의 헤드 및 데이터(작업자) 노드 크기(가격 책정 계층)를 설정하는 옵션이 제공됩니다.

	![노드 크기 선택이 포함된 클러스터 만들기 마법사의 이미지](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**: `azure hdinsight cluster create` 명령을 사용하는 경우 `--headNodeSize` 매개 변수를 사용하여 헤드 노드의 크기를 설정할 수 있습니다.

* **Azure PowerShell**: `New-AzureHDInsightCluster` cmdlet을 사용하는 경우 `-HeadNodeVMSize` 매개 변수를 사용하여 헤드 노드의 크기를 설정할 수 있습니다.

##다음 단계

이 문서에서는 Azure HDInsight가 Hadoop에 고가용성을 제공하는 방법에 대해 알아보았습니다. 이 문서에 언급된 항목에 대한 자세한 내용을 보려면 다음을 사용하세요.

- [Ambari REST 참조](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Azure CLI 설치 및 구성](../xplat-cli-install.md)

- [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)

- [Ambari를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md)

- [Linux 기반 HDInsight 클러스터 프로비전을](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=Oct15_HO3-->