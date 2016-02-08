<properties
   pageTitle="HDInsight에서 Windows 기반 Hadoop 클러스터 만들기 | Microsoft Azure"
   	description="Azure HDInsight에 대한 클러스터를 만드는 방법을 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/22/2016"
   ms.author="jgao"/>

# HDInsight에서 Windows 기반 Hadoop 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-create-windows-cluster-selector.md)]

HDInsight 클러스터를 만드는 계획을 세우는 방법에 대해 알아봅니다.

###필수 조건:

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.


## 기본 구성 옵션

다음은 HDInsight 클러스터를 만들기 위한 기본 구성 옵션입니다.

- **클러스터 이름**

	클러스터 이름은 클러스터를 식별하는 데 사용됩니다. 클러스터 이름은 다음 지침을 따라야 합니다.

	- 이 필드는 3~63자 사이의 문자열이어야 합니다.
	- 이 필드에는 문자, 숫자 및 하이픈만 포함할 수 있습니다.

- **구독 이름**

	하나의 HDInsight 클러스터는 하나의 Azure 구독에 연결됩니다.

- **리소스 그룹 이름**

	ARM(Azure 리소스 관리자)을 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하세요.
	
- **운영 체제**

	다음 두 운영 체제 중 하나에서 HDInsight 클러스터를 만들 수 있습니다. - **Windows의 HDInsight(Windows Server 2012 R2 Datacenter)** - **Linux의 HDInsight(Linux용 Ubuntu 12.04 LTS)**: HDInsight는 Azure에서 Linux 클러스터를 구성하는 옵션을 제공합니다. Linux 기반으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합 또는 Linux 또는 Unix를 사용하고 기존 Hadoop Linux 기반 솔루션에서 마이그레이션에 익숙한 경우에 Linux 클러스터를 구성합니다. 자세한 내용은 [Linux 기반 HDInsight에서 Hadoop 시작](hdinsight-hadoop-linux-get-started.md)을 참조하세요.

- **클러스터 형식** 및 **클러스터 크기(데이터 노드라고도 함)**

	HDInsight를 사용하면 고객이 서로 다른 데이터 분석 작업을 위한 다양한 클러스터 형식을 배포할 수 있습니다. 현재 제공되는 클러스터 형식은 다음과 같습니다.

	- Hadoop 클러스터: 쿼리 및 분석 작업용
	- HBase 클러스터: NoSQL 작업용
	- Storm 클러스터: 실시간 이벤트 처리 작업용
	- Spark 클러스터(미리 보기): 메모리 내 처리, 대화형 쿼리, 스트림 및 기계 학습 작업용

	![HDInsight 클러스터](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] *Azure HDInsight 클러스터*는 *HDInsight의 Hadoop 클러스터* 또는 *HDInsight 클러스터*라고도 부릅니다. 경우에 따라 *Hadoop 클러스터*로도 사용됩니다. 이들은 모두 Microsoft Azure 환경에서 호스트되는 Hadoop 클러스터를 말합니다.

	지정된 클러스터 형식 내의 다양한 노드에 대해 다양한 역할이 있으므로 고객이 지정된 역할에서 워크로드의 세부 정보에 적절하게 노드 크기를 조정할 수 있습니다. 예를 들어 수행된 분석의 형식이 많은 양의 메모리 집약적인 경우 Hadoop 클러스터는 많은 양의 메모리로 만든 작업자 노드가 있을 수 있습니다.

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	HDInsight용 Hadoop 클러스터는 두 가지 역할로 배포됩니다.

	- 헤드 노드(노드 2개)
	- 데이터 노드(노드 1개 이상)

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HDInsight용 HBase 클러스터는 세 가지 역할로 배포됩니다. - 헤드 서버(노드 2개) - 지역 서버(노드 1개 이상) - 마스터/Zookeeper 노드(노드 3개)

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	HDInsight용 Storm 클러스터는 세 가지 역할로 배포됩니다. - Nimbus 노드(노드 2개) - 감독자 서버(노드 1개 이상) - Zookeeper 노드(노드 3개)


	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	HDInsight용 Spark 클러스터는 세 가지 역할로 배포됩니다. - 헤드 노드(노드 2개) - 작업자 노드(노드 1개 이상) - Zookeeper 노드(노드 3개) (A1 Zookeeper의 경우 무료)

	고객은 클러스터의 수명 기간 동안 해당 노드의 사용량에 대한 대금이 청구됩니다. 대금 청구는 클러스터를 만들면 시작되고 클러스터가 삭제되면 중지됩니다(클러스터를 할당 해제하거나 보류할 수 없음). 클러스터 크기는 클러스터 가격에 영향을 줍니다. 학습 목적인 경우는 데이터 노드를 하나 사용하는 것이 좋습니다. HDInsight 가격에 대한 자세한 내용은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)을 참조하세요.


	>[AZURE.NOTE] 클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의하세요.

- **HDInsight 버전**

	이 클러스터에서 사용할 HDInsight 버전을 결정하는 데 사용됩니다. 자세한 내용은 [HDInsight의 Hadoop 클러스터 버전 및 구성 요소](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)를 참조하세요.


- **위치(영역)**

	HDInsight 클러스터와 해당 기본 저장소 계정은 같은 Azure 위치에 있어야 합니다.
	
	![Azure 지역](./media/hdinsight-provision-clusters/Azure.regions.png)

	지원되는 지역 목록은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)에서 **지역** 드롭다운 목록을 클릭하세요.

- **노드 크기**

	![HDInsight VM 노드 크기](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	노드의 VM 크기를 선택합니다. 자세한 내용은 [클라우드 서비스에 적합한 크기](cloud-services-sizes-specs.md)를 참조하세요. 클러스터에서 사용하는 계산 리소스의 크기를 선택할 수 있습니다. 예를 들어 많은 메모리가 필요한 작업을 수행할 것으로 예상되는 경우 더 많은 메모리를 포함하는 계산 리소스를 선택할 수 있습니다.

	>[AZURE.NOTE] 노드에 대해 사용되는 가상 컴퓨터 이미지는 HDInsight 서비스의 구현 세부 정보이므로 클러스터에서 사용되는 노드는 가상 컴퓨터로 계산되지 않습니다. 그러나 노드에서 사용되는 계산 코어는 구독에 사용할 수 있는 계산 코어의 총 수에 계산되지 않습니다. HDInsight 클러스터를 만들 때 노드 가격 책정 계층 블레이드의 요약 섹션에서 클러스터에서 사용할 코어 수와 사용 가능한 코어 수를 확인할 수 있습니다.

	클러스터 유형마다 서로 다른 노드 유형, 노드 수 및 노드 크기를 포함합니다. 예를 들어, Hadoop 클러스터 유형은 _헤드 노드_ 2개, 기본 _데이터 노드_ 4개를 포함하는 반면, Storm 클러스터 유형은 _nimbus 노드_ 2개, _zookeeper 노드_ 3개 및 기본 _감독자 노드_ 4개를 포함합니다.

	> [AZURE.IMPORTANT] 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.

	Azure Preview 포털을 사용하여 클러스터를 구성하는 경우 노드 크기는 __노드 가격 책정 계층__ 블레이드를 통해 사용하는 다양한 노드 크기와 연결된 비용도 표시합니다.

	> [AZURE.IMPORTANT] 클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 가격 책정에 대한 자세한 내용은 [HDInsight 가격 정보](https://azure.microsoft.com/pricing/details/hdinsight/)를 참조하세요.


- **HDInsight 사용자**

	HDInsight 클러스터를 사용하면 프로비전 중에 두 개의 사용자 계정을 구성할 수 있습니다.

	- HTTP 사용자. 기본 사용자 이름은 Azure 포털에서 기본 구성을 사용하는 admin입니다.
	- RDP 사용자(Windows 클러스터): RDP를 사용하여 클러스터에 연결하는 데 사용됩니다. 계정을 만들 때 만료 날짜는 오늘부터 90일 이내로 설정해야 합니다.
	- SSH 사용자(Linux 클러스터): SSH를 사용하여 클러스터에 연결하는 데 사용됩니다. [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)의 단계에 따라 클러스터를 만든 후 추가 SSH 사용자 계정을 만들 수 있습니다.



- **Azure 저장소 계정**

	원래 HDFS는 클러스터에 있는 많은 로컬 디스크를 사용합니다. HDInsight는 데이터 저장소로 Azure Blob 저장소를 대신 사용합니다. Azure Blob 저장소는 HDInsight와 매끄럽게 통합되는 강력한 범용 저장소 솔루션입니다. HDFS(Hadoop Distributed File System) 인터페이스를 통해 HDInsight의 전체 구성 요소 집합을 Blob 저장소에서 구조적 또는 비구조적 데이터에 대해 직접 작동할 수 있습니다. Blob 저장소에 데이터를 저장하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

	구성 중에 Azure 저장소 계정과 해당 Azure 저장소 계정의 Azure Blob 저장소 컨테이너를 지정해야 합니다. 일부 생성 프로세스는 Azure 저장소 계정 및 Blob 저장소 컨테이너를 미리 만들어 두어야 합니다. Blob 저장소 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요에 따라 클러스터에서 액세스할 수 있는 추가 Azure 저장소 계정(연결된 저장소)을 지정할 수 있습니다. 또한 클러스터는 전체 공용 읽기 액세스 또는 Blob 전용 공용 읽기 액세스로 구성된 모든 Blob 컨테이너에 액세스할 수도 있습니다. 액세스 제한 자세한 내용은 [Azure 저장소 리소스에 대한 액세스 관리](storage-manage-access-to-resources.md)를 참조하세요.

	![HDInsight 저장소](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Blob 저장소 컨테이너는 이미지에 나온 것처럼 Blob 집합 그룹화를 제공합니다.

	![Azure Blob 저장소](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] 여러 클러스터에서 하나의 Blob 저장소 컨테이너를 공유하지 마세요. 이 기능은 지원되지 않습니다.

	보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-use-blob-storage.md)을 참조하세요.

- **Hive/Oozie Metastore**

	Metastore는 Hive 테이블, 파티션, 스키마, 열 등 Hive 및 Oozie 메타데이터에 대한 정보를 포함합니다. Metastore를 사용하면 Hive 및 Oozie 메타데이터를 보존할 수 있으므로 새 클러스터를 만들 때 Hive 테이블 또는 Oozie 작업을 다시 만들 필요가 없습니다. 기본적으로 Hive는 포함된 Azure SQL 데이터베이스를 사용하여 이 정보를 저장합니다. 포함된 데이터베이스에서는 클러스터가 삭제된 경우 메타데이터를 유지할 수 없습니다. 예를 들어, Hive Metastore로 만든 클러스터가 있습니다. 몇 개의 Hive 테이블을 만들었습니다. 클러스터를 삭제하고 동일한 Hive Metastore를 사용하여 클러스터를 다시 만든 후, 원래 클러스터에서 만든 Hive 테이블을 볼 수 있습니다.
    
    > [AZURE.NOTE] HBase 클러스터 유형에는 Metastore 구성을 사용할 수 없습니다.

## HDInsight 클러스터 사용자 지정을 사용하여 클러스터 사용자 지정(bootstrap)

경우에 따라 구성 파일을 구성해야 할 수 있습니다.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

클러스터는 이미지로 다시 설치하므로 변경 내용을 유지할 수 없습니다. 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)(영문)을 참조하십시오. 클러스터의 수명 동안 변경 내용을 유지하려면 생성 프로세스 중에 HDInsight 클러스터 사용자 지정을 사용할 수 있습니다. 클러스터의 구성을 변경하고 이러한 Azure 이미지로 다시 설치 다시 부팅 다시 시작 이벤트에서 구성을 유지하려면 이 방법을 사용하는 것이 좋습니다. 구성 변경 내용은 서비스가 시작되기 전에 적용되므로 서비스를 다시 시작할 필요가 없습니다.

샘플을 더 보려면 [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.

## 스크립트 동작을 사용하여 클러스터 사용자 지정

만드는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 동작**을 통해 호출됩니다. 스크립트 동작은 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.


## Azure 가상 네트워크 사용

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결합니다(사이트 간 또는 지점 및 사이트 간).

	사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.

	![사이트 간 구성 다이어그램](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

	![지점 및 사이트 간 구성 다이어그램](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

가상 네트워크에 대한 특정 구성 요구 사항을 포함하여 가상 네트워크로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

## 클러스터 생성 방법

이 문서에서는 Windows 기반 HDInsight 클러스터 만들기에 대한 기본 정보를 알아봤습니다. 아래 표를 사용하여 사용자 요구에 적합한 방법으로 클러스터를 만드는 방법에 대한 구체적인 정보를 확인합니다.

| 클러스터를 만드는 방법... | 웹 브라우저 사용... | 명령줄 사용 | REST API 사용 | SDK 사용 | Linux, Mac OS X 또는 Unix에서 | Windows에서 |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure 포털](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [ARM 템플릿](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0128_2016-->