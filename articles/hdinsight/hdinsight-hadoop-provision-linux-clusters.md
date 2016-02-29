<properties
   	pageTitle="Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터 만들기 | Microsoft Azure"
   	description="브라우저, Azure CLI, Azure PowerShell, REST를 사용하거나 SDK를 통해 Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터를 만드는 방법에 대해 알아봅니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="01/28/2016"
   	ms.author="nitinme"/>


#HDInsight에서 Linux 기반 Hadoop 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-create-linux-cluster-selector.md)]

이 문서에서는 Azure에서 Linux 기반 HDInsight 클러스터를 만드는 다양한 방법과 클러스터에 사용할 수 있는 선택적 구성에 대해 알아봅니다. HDInsight는 Azure 클라우드 플랫폼의 서비스로 Apache Hadoop, Apache Storm 및 Apache HBase를 제공합니다.

> [AZURE.NOTE] 이 문서에서는 클러스터를 만드는 다양한 방법에 대한 지침을 제공합니다. 클러스터를 만드는 빠른 시작 접근 방법을 원하는 경우 [Linux에서 Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

## HDInsight 클러스터의 정의

Hadoop 클러스터는 클러스터에 있는 작업의 분산 처리에 사용되는 여러 가상 컴퓨터(노드)로 구성됩니다. Azure는 개별 노드의 설치 및 구현에 대한 세부 구현을 추상화하므로 일반적인 구성 정보만 제공해야 합니다.

###클러스터 유형

여러 유형의 HDInsight가 제공됩니다.

| 클러스터 유형 | 필요한 경우 다음 사용... |
| ------------ | ----------------------------- |
| Hadoop은 | 쿼리 및 분석(배치 작업) |
| HBase | NoSQL 데이터 저장소 |
| Storm | 실시간 이벤트 처리 |
| Spark(미리 보기) | 메모리 내 처리, 대화형 쿼리, 마이크로 배치 스트림 처리 |

구성하는 동안 이러한 클러스터 유형 중 하나를 선택합니다. [스크립트 동작](#scriptaction)을 사용하여 이러한 기본 유형에 Hue 또는 R과 같은 다른 기술을 추가할 수 있습니다.

각 클러스터 유형에는 클러스터 내에 있는 노드에 대한 고유한 용어, 노드 수 및 각 노드 유형에 대한 기본 VM 크기가 포함됩니다.

> [AZURE.IMPORTANT] 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.

![HDInsight Hadoop 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

HDInsight에 대한 Hadoop 클러스터에는 두 형식의 노드가 있습니다.

- 헤드 노드(노드 2개)
- 데이터 노드(노드 1개 이상)

![HDInsight HBase 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

HDInsight용 HBase 클러스터에는 다음과 같은 3가지 노드 유형이 있습니다. - 헤드 서버(노드 2개) - 지역 서버(노드 1개 이상) - 마스터/Zookeeper 노드(노드 3개)

![HDInsight Storm 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

HDInsight용 Storm 클러스터에는 다음과 같은 3가지 노드 유형이 있습니다. - Nimbus 노드(노드 2개) - 감독자 서버(노드 1개 이상) - Zookeeper 노드(노드 3개)


![HDInsight Spark 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

HDInsight용 Spark 클러스터에는 다음과 같은 3가지 노드 유형이 있습니다. - 헤드 노드(노드 2개) - 작업자 노드(노드 1개 이상) - Zookeeper 노드(노드 3개)(A1 Zookeeper VM 크기의 경우 무료)

###HDInsight에 대한 Azure 저장소

각 클러스터 유형은 클러스터와 연결된 하나 이상의 Azure 저장소 계정을 포함합니다. HDInsight는 이러한 저장소 계정에서 클러스터를 위한 데이터 저장소로 Azure Blob를 사용합니다. 클러스터와 별도로 데이터를 유지하면 사용 중이 아닐 때 데이터는 보존하면서 클러스터를 삭제할 수 있습니다. 그런 다음 자세한 분석을 수행해야 하는 경우 새 클러스터에 대해 동일한 저장소 계정을 사용할 수 있습니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

## <a id="configuration"></a>기본 구성 옵션

다음 섹션에서는 HDInsight 클러스터를 만들 때 사용할 수 있는 필수 구성 옵션에 대해 설명합니다.

###클러스터 이름

클러스터 이름은 클러스터에 대한 고유 식별자를 제공하며 이는 인터넷을 통해 클러스터에 액세스하기 위한 도메인 이름의 일부로 사용됩니다. 예를 들어 인터넷을 통해 _mycluster_라는 클러스터를 _mycluster_.azurehdinsight.net으로 사용할 수 있습니다.

클러스터 이름은 다음 지침을 따라야 합니다.

- 이 필드는 3~63자 사이의 문자열이어야 합니다.
- 이 필드에는 문자, 숫자 및 하이픈만 포함할 수 있습니다.

###클러스터 유형

클러스터 유형으로 클러스터에 대한 특수한 용도의 구성을 선택할 수 있습니다. 다음은 Linux 기반 HDInsight에 사용할 수 있는 유형입니다.

| 클러스터 유형 | 필요한 경우 다음 사용... |
| ------------ | ----------------------------- |
| Hadoop은 | 쿼리 및 분석(배치 작업) |
| HBase | NoSQL 데이터 저장소 |
| Storm | 실시간 이벤트 처리 |
| Spark(미리 보기) | 메모리 내 처리, 대화형 쿼리, 마이크로 배치 스트림 처리 |

[스크립트 동작](#scriptaction)을 사용하여 이러한 기본 유형에 Hue 또는 R과 같은 다른 기술을 추가할 수 있습니다.

###클러스터 운영 체제

다음 두 운영 체제 중 하나에서 HDInsight 클러스터를 프로비전할 수 있습니다.

- **Windows용 HDInsight(Windows Server 2012 R2 Datacenter)**: Hadoop 포함 클러스터에서 실행할 Windows 기반 서비스 및 기술과 통합해야 하는 경우 또는 기존 Windows 기반 Hadoop 배포에서 마이그레이션하는 경우 이 옵션을 선택합니다.

- **Linux용 HDInsight(Linux용 Ubuntu 12.04 LTS)**: Linux 또는 Unix에 익숙하거나, 기존 Linux 기반 Hadoop 솔루션에서 마이그레이션하거나, Linux용으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합하려는 경우 이 옵션을 선택합니다. 자세한 내용은 [Linux 기반 HDInsight에서 Hadoop 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

> [AZURE.NOTE] 이 문서에 있는 정보는 Linux 기반 HDInsight 클러스터를 사용하고 있다고 가정합니다. Windows 기반 클러스터에 대한 자세한 내용은 [HDInsight에서 Windows 기반 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md)를 참조하세요.

###구독 이름

여러 Azure 구독이 있는 경우 구독 이름을 사용하여 HDInsight 클러스터를 만들 때 사용할 구독을 선택합니다.

###리소스 그룹

응용 프로그램은 일반적으로 웹앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. ARM(Azure 리소스 관리자)을 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션와 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

###자격 증명

HDInsight 클러스터에는 두 가지 인증 유형을 사용할 수 있습니다.

* __Admin__ 또는 __HTTPs__ 사용자: 웹 또는 클러스터에서 노출하는 REST 서비스에 액세스할 때는 클러스터에 대한 관리자 계정이 주로 사용됩니다. 클러스터에 직접 로그인하는 데 사용할 수 없습니다.

* __SSH 사용자 이름__: [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) 클라이언트를 사용하여 클러스터에 원격으로 액세스하는 데 SSH 사용자 계정이 사용됩니다. 대부분 클러스터 헤드 노드에 대한 원격 명령줄 액세스를 제공하는 데 사용됩니다.

관리자 계정은 암호로 보호되며 관리자 계정을 사용한 클러스터에 대한 모든 웹 통신은 보안 HTTPS 연결을 통해 수행되어야 합니다.

SSH 사용자는 암호 또는 인증서를 사용하여 인증할 수 있습니다. 인증서 인증이 가장 안전한 옵션이기는 하지만 공용 및 개인 인증서 쌍을 만들고 유지 관리해야 합니다.

SSH 키 만들기 및 사용 방법을 비롯하여 HDInsight와 함께 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

* [Linux, Unix 또는 Mac OS X 클라이언트 - HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Windows 클라이언트 - HDInsight와 함께 SSH를 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

###데이터 원본

HDInsight는 클러스터에 대한 기본 저장소로 Azure Blob 저장소를 사용합니다. 내부적으로 Hadoop 및 클러스터의 기타 소프트웨어는 이 저장소를 HDFS(Hadoop Distributed File System) 호환 시스템으로 간주합니다.

새 클러스터를 만들 때 새 Azure 저장소 계정을 만들거나 기존 계정을 사용해야 합니다.

> [AZURE.IMPORTANT] 클러스터는 기본 저장소 계정과 동일한 데이터 센터에 있어야 하므로 저장소 계정에 대해 선택하는 지리적 위치에 따라 HDInsight 클러스터의 위치가 결정됩니다.
>
> 지원되는 지역 목록은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)에서 **지역** 드롭다운 목록을 클릭하세요.

####기본 저장소 컨테이너

HDInsight는 저장소 계정에 _기본 저장소 컨테이너_를 만듭니다. 이 컨테이너는 HDInsight 클러스터에 대한 기본 저장소입니다.

기본적으로 이 컨테이너 이름은 클러스터 이름과 동일합니다. HDInsight가 Azure Blob 저장소와 연동되는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop으로 HDFS 호환 가능한 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

>[AZURE.WARNING] 여러 클러스터에서 하나의 컨테이너를 공유하지 마세요. 이 기능은 지원되지 않습니다.

###노드 크기

클러스터에서 사용하는 계산 리소스의 크기를 선택할 수 있습니다. 예를 들어 많은 메모리가 필요한 작업을 수행할 것으로 예상되는 경우 더 많은 메모리를 포함하는 계산 리소스를 선택할 수 있습니다.

>[AZURE.NOTE] 노드에 대해 사용되는 가상 컴퓨터 이미지는 HDInsight 서비스의 구현 세부 정보이므로 클러스터에서 사용되는 노드는 가상 컴퓨터로 계산되지 않습니다. 그러나 노드에서 사용되는 계산 코어는 구독에 사용할 수 있는 계산 코어의 총 수에 계산되지 않습니다. HDInsight 클러스터를 만들 때 노드 가격 책정 계층 블레이드의 요약 섹션에서 클러스터에서 사용할 코어 수와 사용 가능한 코어 수를 확인할 수 있습니다.

클러스터 유형마다 서로 다른 노드 유형, 노드 수 및 노드 크기를 포함합니다. 예를 들어, Hadoop 클러스터 유형은 _헤드 노드_ 2개, 기본 _데이터 노드_ 4개를 포함하는 반면, Storm 클러스터 유형은 _nimbus 노드_ 2개, _zookeeper 노드_ 3개 및 기본 _감독자 노드_ 4개를 포함합니다.

> [AZURE.IMPORTANT] 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.

Azure Preview 포털을 사용하여 클러스터를 구성하는 경우 노드 크기는 __노드 가격 책정 계층__ 블레이드를 통해 사용하는 다양한 노드 크기와 연결된 비용도 표시합니다.

> [AZURE.IMPORTANT] 클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 가격 책정에 대한 자세한 내용은 [HDInsight 가격 정보](https://azure.microsoft.com/pricing/details/hdinsight/)를 참조하세요.

##<a id="optionalconfiguration"></a>선택적 구성

다음 섹션에서는 이러한 구성이 필요한 시나리오와 선택적 구성 옵션에 대해 설명합니다.

### HDInsight 버전

이 클러스터에 사용할 HDInsight 버전을 결정하는 데 사용됩니다. 자세한 내용은 [HDInsight의 Hadoop 클러스터 버전 및 구성 요소](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)를 참조하세요.

### Azure 가상 네트워크 사용

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결합니다(사이트 간 또는 지점 및 사이트 간).

    | 사이트 간 구성 | 지점 및 사이트 간 구성 |
    | -------------------------- | --------------------------- |
    | 사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.<br />![사이트 간 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | 지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.<br />![지점 및 사이트 간 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

가상 네트워크에 대한 특정 구성 요구 사항을 포함하여 가상 네트워크로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

### 메타 저장소

Metastore는 Hive 테이블, 파티션, 스키마, 열에 대한 정보와 같은 Hive 및 Oozie 메타데이터를 포함합니다. Metastore를 사용하면 Hive 및 Oozie 메타데이터를 보존할 수 있으므로 새 클러스터를 만들 때 Hive 테이블 또는 Oozie 작업을 다시 만들 필요가 없습니다.

Metastore 구성 옵션을 사용하여 SQL 데이터베이스를 사용하는 외부 Metastore를 지정할 수 있습니다. 그러면 메타데이터 정보가 데이터베이스에 외부 저장되므로 클러스터를 삭제해도 해당 정보를 보존할 수 있습니다. Azure에서 SQL 데이터베이스를 만드는 방법에 대한 자세한 내용은 [첫 Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started.md)를 참조하세요.

> [AZURE.NOTE] HBase 클러스터 유형에는 Metastore 구성을 사용할 수 없습니다.

###<a id="scriptaction"></a>스크립트 동작

클러스터를 프로비전하는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 이러한 스크립트는 **스크립트 동작**을 통해 호출됩니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

> [AZURE.IMPORTANT] 클러스터 노드 이미지가 다시 설치된 후에 이러한 구성 요소를 사용할 수 없기 때문에 클러스터를 만든 후에 추가 구성 요소를 추가하도록 지원하지 않습니다. 스크립트 동작을 통해 설치된 구성 요소는 프로세스를 이미지한 일부로 다시 설치됩니다.

### 저장소 추가

일부 경우에는 클러스터에 추가 저장소를 추가할 수도 있습니다. 예를 들어 서로 다른 지리적 지역 또는 서비스에 대해 여러 Azure 저장소 계정을 포함하지만 HDInsight로 모두 분석하려는 경우가 있습니다.

보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

##<a id="nextsteps"></a><a id="options"></a> 만들기 방법

이 문서에서는 Linux 기반 HDInsight 클러스터 만들기에 대한 기본 정보에 대해 알아봤습니다. 아래 표를 사용하여 사용자 요구에 적합한 방법으로 클러스터를 만드는 방법에 대한 구체적인 정보를 확인합니다.

| 클러스터를 만드는 방법... | 웹 브라우저 사용... | 명령줄 사용 | REST API 사용 | SDK 사용 | Linux, Mac OS X 또는 Unix에서 | Windows에서 |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure Preview 포털](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |


[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage/storage-create-storage-account.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "클러스터 나열 및 표시"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "HDInsight에서 Sqoop 사용"

<!---HONumber=AcomDC_0218_2016-->