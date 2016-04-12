<properties
   pageTitle="HDInsight의Apache Spark 클러스터 프로비전 | Microsoft Azure"
   description="Azure 포털, Azure PowerShell, 명령줄 또는 HDInsight .NET SDK를 사용하여 Azure HDInsight용 Spark 클러스터를 프로비전하는 방법에 대해 알아봅니다."
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
    ms.date="12/08/2015"
    ms.author="nitinme"/>

# 사용자 지정 옵션을 사용하여 HDInsight에서 Apache Spark 클러스터 만들기(Windows)

> [AZURE.NOTE] 이제 HDInsight가 Linux에서 Spark 클러스터를 제공합니다. HDInsight Linux에서 사용자 지정 Spark 클러스터를 만드는 방법은 [HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

대부분의 시나리오의 경우 [HDInsight에서 Apache Spark 시작](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)에서 설명한 것처럼 빠른 만들기 메서드를 사용하여 Spark를 만들 수 있습니다. 특정 시나리오에서 사용자 지정된 클러스터를 만들 수 있습니다. 예를 들어 클러스터의 수명을 넘도록 영구 Hive 메타데이터를 유지하기 위해 외부 메타 데이터 저장소에 연결하거나 클러스터와 함께 추가 저장소를 사용할 수 있습니다.

이러한 그리고 다른 시나리오의 경우 이 문서는 Azure 포털, Azure PowerShell 또는 HDInsight.NET SDK를 사용하여 HDInsight에서 사용자 지정된 Spark 클러스터를 만드는 방법에 대한 지침을 제공합니다.


**필수 조건:**

이 문서의 지침을 시작하기 전에 Azure 구독이 있어야 합니다. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

##<a id="configuration"></a>다른 구성 옵션 정의

### 저장소 추가

구성 중에는 Azure Blob 저장소 계정과 기본 컨테이너를 지정해야 합니다. 이 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요한 경우 역시 클러스터에 연결되는 추가 Azure 저장소 계정도 지정할 수 있습니다.

>[AZURE.NOTE] 여러 클러스터에서 하나의 Blob 저장소 컨테이너를 공유하지 마세요. 이 기능은 지원되지 않습니다.

보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

### 메타 저장소

Spark는 스키마를 원시 데이터에 대해 Hive 테이블 정의할 수 있습니다. 외부 메타 스토어에 이러한 스키마와 테이블 메타데이터를 저장할 수 있습니다. Metastore를 사용하면 Hive 메타데이터를 보존할 수 있으므로 새 클러스터를 만들 때 Hive 테이블을 다시 만들 필요가 없습니다. Hive는 기본적으로 포함된 데이터베이스를 사용하여 이 정보를 저장합니다. 포함된 데이터베이스에서는 클러스터가 삭제된 경우 메타데이터를 유지할 수 없습니다.

Azure에서 SQL 데이터베이스를 만드는 방법에 대한 자세한 내용은 [첫 Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started.md)를 참조하세요.

### 클러스터 사용자 지정

만드는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 작업**을 통해 호출됩니다. 스크립트 작업은 Azure 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-customize-cluster]을 참조하세요.


### 가상 네트워킹

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결합니다(사이트 간 또는 지점 및 사이트 간).

	사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.

	![사이트 간 구성 다이어그램](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

	![지점 및 사이트 간 구성 다이어그램](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

가상 네트워크에 대한 특정 구성 요구 사항을 포함하여 가상 네트워크로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

##<a id="portal"></a> Azure 포털 사용

HDInsight에서 Spark 클러스터는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 동일한 데이터 센터에 위치한 Azure 저장소 계정이 필요합니다. 자세한 내용은 [HDInsight와 함께 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하십시오. Azure 저장소 계정 만들기에 대한 자세한 내용은 [저장소 계정을 만드는 방법][azure-create-storageaccount]을 참조하세요.

**사용자 지정 만들기 옵션을 사용하여 HDInsight 클러스터를 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.1.png "Azure 포털에서 새 클러스터 만들기")

3. **클러스터 이름**을 입력하고 **클러스터 형식**으로 **Spark**를 선택하고 **클러스터 운영 체제** 드롭다운에서 **Windows Server 2012 R2 Datacenter**를 선택합니다. 클러스터 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.

	![클러스터 이름 및 유형 입력](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.2.png "클러스터 이름 및 유형 입력")

4. 둘 이상의 구독이 있는 경우 **구독** 항목을 클릭하여 클러스터에 사용할 Azure 구독을 선택합니다.

5. **리소스 그룹**을 클릭하여 기존 리소스 그룹 목록을 표시한 다음 클러스터를 만들 리소스 그룹을 선택합니다. 또는 **새로 만들기**를 클릭한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

	> [AZURE.NOTE] 사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. **자격 증명**을 클릭한 다음 **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**를 입력합니다. 클러스터 노드에 대해 원격 데스크톱을 사용하도록 설정하려면 **원격 데스크톱 사용**에 대해 **예**를 클릭합니다. 클러스터에 대한 원격 데스크톱 액세스가 만료되는 경우 날짜를 선택하고 원격 데스크톱 사용자에 대한 사용자 이름/암호를 제공합니다. 아래쪽의 **선택**을 클릭하여 자격 증명 구성을 저장합니다.

	![클러스터 자격 증명 제공](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.3.png "클러스터 자격 증명 제공")

7. **데이터 원본**을 클릭하여 클러스터의 기존 데이터 원본을 선택하거나 새로 만듭니다.

	![데이터 원본 블레이드](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.4.png "데이터 원본 구성 제공")

	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 **데이터 원본** 블레이드의 항목을 이해합니다.

	- **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **From all subscriptions(모든 구독에서)**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **액세스 키**를 입력하려면 이 항목을 **액세스 키**로 설정합니다.

	- **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.

	- **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.

	- **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지리적인 지역입니다.

		> [AZURE.IMPORTANT] 기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.

	**선택**을 클릭하여 데이터 원본 구성을 저장합니다.

8. **노드 가격 책정 계층**을 클릭하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.5.png "클러스터 노드 수 지정")

	**선택**을 클릭하여 노드 가격 책정 구성을 저장합니다.

9. **옵션 구성**을 클릭하여 클러스터 버전을 선택하고 **가상 네트워크**에 가입, Hive 및 Oozie의 데이터를 유지하기 위한 **외부 Metastore** 설정 등 기타 선택적 설정을 구성하고 스크립트 동작을 사용하여 사용자 지정 구성 요소를 설치하기 위해 클러스터를 사용자 지정하거나 클러스터에 추가 저장소 계정을 사용합니다.

	* **HDInsight 버전** 드롭다운을 클릭하고 클러스터에 사용할 버전을 선택합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

	* **가상 네트워크**를 클릭하여 가상 네트워크의 일환으로 클러스터를 구성하려면 구성 세부 정보를 제공합니다. **가상 네트워크** 블레이드에서 **가상 네트워크**를 클릭한 다음 사용하려는 네트워크를 클릭합니다. 마찬가지로 네트워크에 대한 **서브넷**을 선택한 다음 **선택**을 클릭하여 가상 네트워크 구성을 저장합니다.

		![가상 네트워크 블레이드](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.6.png "가상 네트워크 세부 정보 지정")

	* **외부 Metastore**를 클릭하여 클러스터와 연결된 Hive 및 Oozie 메타데이터를 저장하는데 사용할 SQL 데이터베이스를 지정합니다.

		![사용자 지정 Metastore 블레이드](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.7.png "외부 Metastore 지정")

		**Hive 메타데이터에 기존 SQL DB 사용**에 대해 **예**를 클릭하고 SQL 데이터베이스를 선택한 다음 데이터베이스의 사용자 이름/암호를 입력합니다. **Oozie 메타데이터에 기존 SQL DB 사용**을 원하는 경우 이러한 단계를 반복합니다. **옵션 구성** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.

		>[AZURE.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

	* 클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우 **스크립트 동작**을 클릭합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요. 스크립트 동작 블레이드에서는 화면 캡처에 표시된 것과 같은 세부 정보를 제공합니다.

		![스크립트 동작 블레이드](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.8.png "스크립트 동작 지정")

		**선택**을 클릭하여 스크립트 동작 구성 변경 내용을 저장합니다.

	* **Azure 저장소 키**를 클릭하여 클러스터와 연결할 추가 저장소 계정을 지정합니다. **Azure 저장소 키** 블레이드에서 **저장소 키 추가**를 클릭한 다음 기존 저장소 계정을 선택하거나 새 계정을 만듭니다.

		![추가 저장소 블레이드](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.9.png "추가 저장소 계정 지정")

		**새 HDInsight 클러스터** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.

10. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 클릭합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘은 클러스터를 만드는 중임을 나타내고 생성이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

	| 만드는 동안 | 만들기 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 만들기](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![만든 클러스터 타일](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE] 클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 생성 프로세스를 확인하세요.

11. 생성이 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다. 클러스터 블레이드는 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 필수 정보를 제공합니다.

	![클러스터 블레이드](./media/hdinsight-apache-spark-provision-clusters/hdispark.cluster.blade.png "클러스터 속성")

	다음을 사용하여 이 블레이드의 위쪽과 **필수** 및 **빠른 링크** 섹션에 있는 아이콘을 이해합니다.

	* **설정** 및 **모든 설정**: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 **설정** 블레이드를 표시합니다.

	* **대시보드** 및 **URL**: 이러한 항목을 통해 클러스터에서 작업을 실행하기 위한 웹 포털인 클러스터 대시보드에 액세스할 수 있습니다.

	* **원격 데스크톱**: 클러스터 노드에 대해 원격 데스크톱을 사용하거나 사용하지 않도록 설정할 수 있습니다.

	* **클러스터 크기 조정**: 이 클러스터의 작업자 노드 수를 변경할 수 있습니다.

	* **삭제**: HDInsight 클러스터를 삭제합니다.

	* **빠른 시작**(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.

	* **사용자**(![사용자 아이콘](./media/hdinsight-apache-spark-provision-clusters/users.png)): Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.

		> [AZURE.IMPORTANT] 이는 _오직_ Azure 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.

	* **태그**(![태그 아이콘](./media/hdinsight-apache-spark-provision-clusters/tags.png)): 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.

	* **클러스터 대시보드**: 클러스터 대시보드를 자체적으로 시작하거나 Zeppelin 및 Jupyter Notebook을 시작할 수 있는 클러스터 대시보드 블레이드를 시작합니다.


##<a id="powershell"></a>Azure PowerShell 사용

[HDInsight 클러스터 만들기](hdinsight-provision-clusters.md#create-using-azure-powershell)를 참조하세요.

New-AzureRmHDInsightCluster cmdlet에서 Spark 클러스터 유형 스위치를 지정합니다.

	-ClusterType Spark


## ARM 기반 HDInsight .NET SDK 사용

[HDInsight 클러스터 만들기](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk)를 참조하세요.

Spark 클러스터 형식을 지정합니다.

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


##<a id="nextsteps"></a> 다음 단계

* [HDInsight에서 Spark를 사용하여 BI 도구로 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools-v1.md)을 참조하여 Power BI 및 Tableau와 같은 BI 도구로 HDInsight에서 Apache Spark를 사용하는 방법을 배울 수 있습니다.
* [기계 학습 응용 프로그램을 구축하기 위해 HDInsight에서 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)을 참조하여 HDInsight에서 Apache Spark를 사용하여 컴퓨터 학습 응용 프로그램을 작성하는 방법을 배울 수 있습니다.
* [실시간 스트리밍 응용 프로그램을 구축하기 위해 HDInsight에서 Spark 사용](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)을 참조하여 HDInsight에서 Apache Spark를 사용하여 스트리밍 응용 프로그램을 작성하는 방법을 배울 수 있습니다.
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager-v1.md)를 참조하여 스파크 클러스터에 할당된 리소스를 관리하도록 리소스 관리자를 사용하는 방법을 배울 수 있습니다.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "HDInsight에서 Sqoop 사용"

<!---HONumber=AcomDC_0330_2016-->