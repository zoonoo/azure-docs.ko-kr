<properties
   	pageTitle="HDInsight에서 Linux에 Hadoop, HBase 또는 Storm 클러스터 프로비전 | Microsoft Azure"
   	description="관리 포털, 명령줄 및.NET SDK를 사용하여 Linux 기반 HDInsight의 Hadoop 클러스터를 프로비전하는 방법을 알아봅니다."
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
   	ms.date="09/21/2015"
   	ms.author="nitinme"/>


#사용자 지정 옵션을 사용하여 HDInsight의 Hadoop Linux 클러스터 프로비전을 참조하세요.

이 문서에서는 Azure 포털, Azure PowerShell, Azure CLI 또는 HDInsight .NET SDK를 사용하여 Azure HDInsight에서 사용자 지정 방식으로 Hadoop Linux 클러스터를 프로비전하는 다양한 방법을 알아봅니다.

## HDInsight 클러스터의 정의

Hadoop 또는 빅 데이터에 대해 이야기할 때마다 클러스터를 언급한 이유가 궁금하셨나요? 그것은 바로 Hadoop을 사용하면 여러 다른 클러스터 노드에 분산된 대규모 데이터를 처리할 수 있기 때문입니다. 클러스터는 하나의 마스터(헤드 노드 또는 이름 노드라고도 함)와 개수에 관계없는 작업자 노드(데이터 노드라고도 함)가 있는 마스터/작업자 아키텍처로 구성됩니다. 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>을 참조하세요.

![HDInsight 클러스터][img-hdi-cluster]


HDInsight 클러스터는 Hadoop 구현 세부 정보를 추출하므로 여러 다른 클러스터 노드와 통신하는 방법을 걱정할 필요가 없습니다. HDInsight 클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 자세한 내용은 [HDInsight의 Hadoop 소개](hdinsight-hadoop-introduction.md)(영문)를 참조하세요. 변동되는 데이터는 Azure Blob 저장소에 저장됩니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md)을 참조하세요.


이 문서에서는 클러스터를 프로비전하는 여러 다른 방법에 대한 지침을 제공합니다. 클러스터를 프로비전하는 빠른 시작 접근 방법을 살펴보려면 [Linux에서 Azure HDInsight 시작](../hdinsight-hadoop-linux-get-started.md)을 참조하세요.

**필수 구성 요소**

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **SSH(보안 셸) 키**. 암호 대신 키가 있는 SSH를 사용하여 Linux 클러스터에 원격으로 연결하려는 경우 키를 사용하는 방법이 더 안전하므로 권장됩니다. SSH 키를 생성하는 방법에 대한 지침은 다음 문서를 참조하세요.
	-  Linux 컴퓨터 - [Linux, Unix 또는 OS X에서 Linux 기반 HDInsight(Hadoop)와 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	-  Windows 컴퓨터 - [Windows에서 Linux 기반 HDInsight(Hadoop)와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a id="configuration"></a>구성 옵션

### 저장소 추가

구성 중에는 Azure Blob 저장소 계정과 기본 컨테이너를 지정해야 합니다. 이 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요한 경우 역시 클러스터에 연결되는 추가 Blob도 지정할 수 있습니다.


보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md)을 참조하세요.


### 메타 저장소

Metastore는 Hive 테이블, 파티션, 스키마, 열 등에 대한 정보를 포함합니다. 이 정보는 Hive가 HDFS(Hadoop Distributed File System) 또는 HDInsight용 Azure Blob 저장소에서 데이터가 저장된 위치를 찾는 데 사용됩니다. Hive는 기본적으로 포함된 데이터베이스를 사용하여 이 정보를 저장합니다.

HDInsight 클러스터를 프로비전할 때는 Hive용 Metastore를 포함할 SQL 데이터베이스를 지정할 수 있습니다. 그러면 메타데이터 정보가 SQL 데이터베이스에 외부 저장되므로 클러스터를 삭제해도 해당 정보를 보존할 수 있습니다. Azure에서 SQL 데이터베이스를 만드는 방법에 대한 자세한 내용은 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요.

### 스크립트 작업을 사용하여 클러스터 사용자 지정

프로비전하는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 이러한 스크립트는 **스크립트 동작**을 통해 호출됩니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

> [AZURE.IMPORTANT]클러스터 노드 이미지가 다시 설치된 후에 이러한 구성 요소를 사용할 수 없기 때문에 클러스터를 프로비전한 후에 추가 구성 요소를 추가하도록 지원하지 않습니다. 스크립트 동작을 통해 설치된 구성 요소는 프로세스를 이미지한 일부로 다시 설치됩니다.

### Azure 가상 네트워크 사용

[Azure 가상 네트워크](http://azure.microsoft.com/documentation/services/virtual-network/)에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결합니다(사이트 간 또는 지점 및 사이트 간).

	사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.

	![사이트 간 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png)

	지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

	![지점 및 사이트 간 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png)

가상 네트워크의 기능과 이점에 대한 자세한 내용은 [Azure 가상 네트워크 개요](http://msdn.microsoft.com/library/azure/jj156007.aspx)를 참조하세요.

> [AZURE.NOTE]클러스터를 프로비전하기 전에 Azure 가상 네트워크를 만들어야 합니다. 자세한 내용은 [가상 네트워크를 만드는 방법](virtual-networks-create-vnet.md)을 참조하세요.
>
> Azure HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹 기반 가상 네트워크와는 연동되지 않습니다. Azure PowerShell cmdlet Get-AzureVNetConfig를 사용하여 기존 Azure 가상 네트워크가 위치 기반인지 여부를 확인합니다. 가상 네트워크가 위치 기반이 아니면 다음과 같은 옵션을 사용할 수 있습니다.
>
> - 기존 가상 네트워크 구성을 내보낸 다음 새 가상 네트워크를 만듭니다. 모든 새 가상 네트워크는 기본적으로 위치 기반입니다.
> - 위치 기반 가상 네트워크로 마이그레이션합니다. [지역 범위로 기존 서비스 마이그레이션](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)을 참조하세요.
>
> 각 클러스터에 단일 서브넷을 지정하는 것이 좋습니다.
>
> 현재(2015년 8월 25일) Azure 가상 네트워크에서 하나의 Linux 기반 클러스터만 프로비전할 수 있습니다.
>
> Linux 기반 HDInsight로 v1(클래식) Azure 가상 네트워크를 사용할 수 없습니다. 가상 네트워크가 v2(Azure 리소스 관리자)여야만 Azure Preview 포털에서 HDInsight 클러스터를 생성하는 동안 옵션으로 나열되거나 Azure CLI 또는 Azure PowerShell에서 클러스터를 만들 때 사용할 수 있습니다.
>
> v1 네트워크에 리소스가 있고 HDInsight가 가상 네트워크를 통해 이러한 리소스에 직접 액세스할 수 있도록 하려면 v2 가상 네트워크를 v1 가상 네트워크에 연결하는 방법에 대한 정보를 [클래식 VNet을 새 VNet에 연결](../virtual-network/virtual-networks-arm-asm-s2s.md)에서 참조하세요. 이 연결이 설정되면 v2 가상 네트워크에 HDInsight 클러스터를 만들 수 있습니다.

## <a id="options"></a> HDInsight Linux 클러스터 프로비전에 대한 옵션

Windows 기반 컴퓨터와 Linux 컴퓨터에서 HDInsight Hadoop Linux 클러스터를 프로비전할 수 있습니다. 다음 표에서 서로 다른 운영 체제에서 사용할 수 있는 프로비전 옵션에 대한 정보를 제공하고 각각에 대한 지침을 연결합니다.

다음 운영 체제를 실행하는 컴퓨터에서 LINUX 클러스터 프로비전 | Azure 포털 사용 | Azure CLI 사용 | Azure PowerShell 사용 | .NET SDK 사용
-----------------| --------| ------------------------| -------------------| ---------- | ---------
Linux| [여기](#portal)를 클릭 | [여기](#cli)를 클릭 | 해당 없음 | 해당 없음
Mac OS X | [여기](#portal)를 클릭 | [여기](#cli)를 클릭 | 해당 없음 | 해당 없음
Windows | [여기](#portal)를 클릭 | [여기](#cli)를 클릭 | [여기](#powershell)를 클릭 | [여기](#sdk)를 클릭

### <a id="portal"></a> Azure 포털 사용

HDInsight 클러스터는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 동일한 데이터 센터에 위치한 Azure 저장소 계정이 필요합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md)을 참조하세요. Azure 저장소 계정 만들기에 대한 자세한 내용은 [저장소 계정을 만드는 방법](../storage-create-storage-account.md)을 참조하세요.


> [AZURE.NOTE]현재 **동남아시아**, **북유럽**, **서유럽** 및 **미국 남중부** 지역에서만 HDInsight Linux 클러스터를 호스트할 수 있습니다.

**HDInsight 클러스터를 만들려면**

1. [Azure 미리 보기 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure 미리 보기 포털에서 새 클러스터 만들기](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Azure 미리 보기 포털에서 새 클러스터 만들기")

3. **클러스터 이름**을 입력하고 **클러스터 형식**으로 **Hadoop**을 선택하고 **클러스터 운영 체제** 드롭다운에서 **Ubuntu**를 선택합니다. 클러스터 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.


	> [AZURE.NOTE]HBase 또는 Storm 클러스터를 프로비전하려면 **클러스터 형식** 드롭다운 목록에서 적절한 값을 선택합니다.


	![클러스터 이름 및 유형 입력](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "클러스터 이름 및 유형 입력")

4. 둘 이상의 구독이 있는 경우 **구독** 항목을 클릭하여 클러스터에 사용할 Azure 구독을 선택합니다.

5. **리소스 그룹**을 클릭하여 기존 리소스 그룹 목록을 표시한 다음 클러스터를 만들 리소스 그룹을 선택합니다. 또는 **새로 만들기**를 클릭한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

	> [AZURE.NOTE]사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. **자격 증명**을 클릭한 다음 관리 사용자의 암호를 입력합니다. **SSH 사용자 이름**과 **암호** 또는 **공개 키**(SSH 사용자를 인증하는 데 사용됨)도 입력해야 합니다. 공개 키를 사용하는 것이 권장 방식입니다. 아래쪽의 **선택**을 클릭하여 자격 증명 구성을 저장합니다.

	![클러스터 자격 증명 제공](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "클러스터 자격 증명 제공")

	HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)


7. **데이터 원본**을 클릭하여 클러스터의 기존 데이터 원본을 선택하거나 새로 만듭니다.

	![데이터 원본 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "데이터 원본 구성 제공")

	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 **데이터 원본** 블레이드의 항목을 이해합니다.

	- **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **From all subscriptions(모든 구독에서)**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **선택키**를 입력하려면 이 항목을 **선택키**로 설정합니다.

	- **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.

	- **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.

	- **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지역입니다.

		> [AZURE.IMPORTANT]기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.

	**선택**을 클릭하여 데이터 원본 구성을 저장합니다.

8. **노드 가격 책정 계층**을 클릭하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "클러스터 노드 수 지정")

	**선택**을 클릭하여 노드 가격 책정 구성을 저장합니다.

9. **옵션 구성**을 클릭하여 클러스터 버전을 선택하고 **가상 네트워크**에 가입, Hive 및 Oozie의 데이터를 유지하기 위한 **외부 Metastore** 설정 등 기타 선택적 설정을 구성하며 스크립트 동작을 사용하여 사용자 지정 구성 요소를 설치하기 위해 클러스터를 사용자 지정하거나 클러스터에 추가 저장소 계정을 사용합니다.

	* **HDInsight 버전** 드롭다운을 클릭하고 클러스터에 사용할 버전을 선택합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.


	* **가상 네트워크**: 클러스터를 가상 네트워크에 배치하려는 경우 Azure 가상 네트워크 및 서브넷을 선택합니다.

		![가상 네트워크 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.6.png "가상 네트워크 세부 정보 지정")

    	>[AZURE.NOTE]Windows 기반 HDInsight 클러스터는 클래식 가상 네트워크에만 배치될 수 있습니다.


	* **외부 Metastore**를 클릭하여 클러스터와 연결된 Hive 및 Oozie 메타데이터를 저장하는데 사용할 SQL 데이터베이스를 지정합니다.

		![사용자 지정 Metastore 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "외부 Metastore 지정")

		**Hive 메타데이터에 기존 SQL DB 사용**에 대해 **예**를 클릭하고 SQL 데이터베이스를 선택한 다음 데이터베이스의 사용자 이름/암호를 입력합니다. **Oozie 메타데이터에 기존 SQL DB 사용**를 원하는 경우 이러한 단계를 반복합니다. **옵션 구성** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.

		>[AZURE.NOTE]메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.


	* **스크립트 동작**: 클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우에 사용합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요. 스크립트 동작 블레이드에서는 화면 캡처에 표시된 것과 같은 세부 정보를 제공합니다.

		![스크립트 동작 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.8.png "스크립트 동작 지정")


	* **Azure 저장소 키**를 클릭하여 클러스터와 연결할 추가 저장소 계정을 지정합니다. **Azure 저장소 키** 블레이드에서 **저장소 키 추가**를 클릭한 다음 기존 저장소 계정을 선택하거나 새 계정을 만듭니다.

		![추가 저장소 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "추가 저장소 계정 지정")

		**새 HDInsight 클러스터** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.

10. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 클릭합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 프로비전 중임을 나타내고 프로비전이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

	| 프로비전 중 | 프로비전 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 프로비전](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![프로비전된 클러스터 타일](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.

11. 프로비전이 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다. 클러스터 블레이드는 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 필수 정보를 제공합니다.

	![클러스터 블레이드](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "클러스터 속성")

	다음을 사용하여 이 블레이드의 위쪽과 **필수** 섹션에 있는 아이콘을 이해합니다.

	* **설정** 및 **모든 설정**: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 **설정** 블레이드를 표시합니다.

	* **대시보드**, **클러스터 대시보드** 및 **URL**: 이러한 항목을 통해 클러스터에서 작업을 실행하기 위한 웹 포털인 클러스터 대시보드에 액세스할 수 있습니다.

	* **보안 셸**: SSH를 사용하여 클러스터에 액세스하는 데 필요한 정보입니다.

	* **삭제**: HDInsight 클러스터를 삭제합니다.

	* **빠른 시작(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png))**: HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.

	* **사용자**(![사용자 아이콘](./media/hdinsight-hadoop-provision-linux-clusters/users.png)): Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.

		> [AZURE.IMPORTANT]이는 _오직_ Azure Preview 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.

	* **태그**(![태그 아이콘](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)): 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.

### <a id="cli"></a> Azure CLI 사용

Azure CLI는 Azure 서비스를 관리할 수 있도록 하는 크로스 플랫폼 명령줄 유틸리티입니다. Azure 리소스 관리 템플릿과 함께 사용하여 HDInsight 클러스터를 연결된 저장소 계정 및 기타 서비스와 함께 프로비전할 수 있습니다.

Azure 리소스 관리 템플릿은 __리소스 그룹__ 및 그 안에 모든 리소스를 설명하는 JSON 문서입니다.(예: HDInsight) 이 템플릿 기반 접근 방식을 사용하면 HDInsight에 필요한 모든 리소스를 하나의 템플릿에 정의하고 그룹에 변경 내용을 적용하는 __배포__를 통해 그룹에 대한 변경 내용을 전체적으로 관리할 수 있습니다.

다음 단계는 Azure CLI 및 템플릿을 사용하여 새 HDInsight 클러스터를 만드는 과정을 안내합니다.

1. 아직 설치되지 않은 경우 [Azure CLI 설치 및 구성](../xplat-cli-install.md) 문서의 단계를 수행합니다.

2. 명령줄, 터미널 또는 셸에서 다음 명령을 사용하여 Azure 구독을 인증합니다.

        azure login

    사용자 이름 및 암호를 제공하라는 메시지가 표시됩니다. 여러 Azure 구독이 있는 경우 `azure account set <subscriptionname>`을 사용하여 Azure CLI 명령을 사용하는 구독을 설정할 수 있습니다.

3. 다음 명령을 사용하여 Azure 리소스 관리자 모드로 전환합니다.

        azure config mode arm

4. HDInsight 클러스터에 대한 템플릿을 만듭니다. 다음은 기본 예제 템플릿 일부입니다.

    * [SSH 공개 키를 사용하는 Linux 기반 클러스터](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpublickey)
    * [SSH 계정에 암호를 사용하는 Linux 기반 클러스터](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpassword)

    또한 이러한 템플릿은 모두 HDInsight에서 사용되는 기본 Azure 저장소 계정을 만듭니다.

    필요한 파일은 __azuredeploy.json__ 및 __azuredeploy.parameters.json__입니다.

5. 편집기에서 __azuredeploy.parameters.json__를 열고 `parameters` 섹션에서 항목에 대한 값을 제공합니다.

    * __위치__: 리소스가 생성된 데이터 센터입니다. 허용되는 위치의 목록에 __azuredeploy.json__ 파일에서 `location` 섹션을 확인할 수 있습니다.
    * __clusterName__: HDInsight 클러스터의 이름입니다. 이 이름은 고유해야 합니다. 아니면 배포가 실패합니다.
    * __clusterStorageAccountName__: HDInsight 클러스터에 생성되는 Azure 저장소 계정의 이름입니다. 이 이름은 고유해야 합니다. 아니면 배포가 실패합니다.
    * __clusterLoginPassword__: 클러스터 관리자 사용자의 암호입니다. 클러스터에서 웹 사이트 및 REST 서비스에 액세스하는 데 사용되기 때문에 안전한 암호여야 합니다.
    * __sshUserName__: 클러스터에 만들 첫번째 SSH 사용자의 이름입니다. 이 계정을 사용하여 클러스터에 원격으로 액세스하는 데 SSH를 사용합니다. 이름은 고유해야 하며 클러스터에서 이미 사용 중인 계정의 이름을 쓸 수 없습니다. 잘못된 이름은 루트, storm 및 hbase를 포함합니다.
    * __sshPublicKey__: SSH 공개키를 필요로 하는 템플릿을 사용하는 경우 이 줄에서 공용 키를 추가해야 합니다. 공용 키로 생성하고 작업하는 자세한 내용은 다음 문서를 참조하세요.

        * [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: SSH 암호를 필요로 하는 템플릿을 사용하는 경우 이 줄에 암호를 추가해야 합니다.

    작업을 완료하면 파일을 저장하고 닫습니다.

5. 다음을 사용하여 빈 리소스 그룹을 만듭니다. __RESOURCEGROUPNAME__을 이 그룹에 사용하려는 이름으로 바꿉니다. __위치__를 그룹을 만들어야 하는 데이터 센터로 바꿉니다.

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]위치 이름이 공백을 포함하는 경우 이중 따옴표로 묶습니다. 예를 들어 "미국 중남부"입니다.

6. 다음 명령을 사용하여 이 리소스 그룹에 초기 배포를 만듭니다. __PATHTOTEMPLATE__를 __azuredeploy.json__ 템플릿 파일에 대한 경로로 바꿉니다. __PATHTOPARAMETERSFILE__를 __azuredeploy.parameters.json__ 파일에 대한 경로로 바꿉니다. __RESOURCEGROUPNAME__를 이전 단계에서 만든 그룹의 이름으로 바꿉니다.

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    배포가 수락되면 `group deployment create command ok`과 유사한 메시지가 나타납니다.

7. 배포를 완료하려면 약 15분 정도의 시간이 걸릴 수 있습니다. 다음 명령을 사용하여 배포에 대한 정보를 볼 수 있습니다. __RESOURCEGROUPNAME__를 이전 단계에서 사용한 리소스 그룹의 이름으로 바꿉니다.

        azure group log show -l RESOURCEGROUPNAME

    배포 중에 오류가 발생하는 경우 다음 명령을 사용하여 오류에 대한 자세한 정보를 얻을 수 있습니다.

        azure group log show -l -v RESOURCEGROUPNAME

### <a id="powershell"></a> Azure PowerShell 사용

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 섹션에서는 Azure PowerShell을 사용하여 HDInsight 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. HDInsight Windows PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../install-configure-powershell.md)을 참조하세요. HDInsight에서 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)를 참조하세요. HDInsight Windows PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조](https://msdn.microsoft.com/library/azure/dn858087.aspx)를 참조하세요.


Azure PowerShell을 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

- Azure 리소스 그룹 만들기
- Azure 저장소 계정 만들기
- Azure Blob 컨테이너 만들기
- HDInsight 클러스터 만들기

Linux 클러스터를 프로비전하기 위해 설정해야 하는 두 가지 중요한 매개 변수는 OS 종류를 지정하는 매개 변수와 SSH 사용자 정보를 지정하는 매개 변수입니다.

- **-OSType** 매개 변수는 **Linux**로 지정해야 합니다.
- 클러스터의 원격 세션에 SSH를 사용하려면 SSH 사용자 암호 또는 SSH 공개 키를 지정하면 됩니다. SSH 사용자 암호와 SSH 공개 키를 둘 다 지정한 경우에는 키가 무시됩니다. 원격 세션에 SSH 키를 사용하려면 SSH 암호를 묻는 메시지가 나타날 때 빈 SSH 암호를 지정해야 합니다. HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)


			# Use the new Azure Resource Manager mode
			Switch-AzureMode AzureResourceManager

			###########################################
			# Create required items, if none exist
			###########################################

			# Sign in
			Add-AzureAccount

			# Select the subscription to use
			$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
			Select-AzureSubscription -SubscriptionName $subscriptionName

			# Register your subscription to use HDInsight
			Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

			# Create an Azure Resource Group
			$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
			$location = "<Location>"                        # For example, "West US"
			New-AzureResourceGroup -Name $resourceGroupName -Location $location

			# Create an Azure Storage account
			$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
			New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

			# Create an Azure Blob Storage container
			$containerName = "<ContainerName>"              # Provide a container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
			$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
			New-AzureStorageContainer -Name $containerName -Context $destContext

			###########################################
			# Create an HDInsight Cluster
			###########################################

			# Skip these variables if you just created them
			$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
			$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
			$containerName = "<ContainerName>"              # Provide the container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

			# Set these variables
			$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
			$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
			$credentials = Get-Credential
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]**$clusterCredentials**에 대해 지정한 값은 클러스터의 Hadoop 사용자 계정을 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에 연결합니다. **$sshCredentials**에 대해 지정한 값은 클러스터의 SSH 사용자를 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에서 원격 SSH 세션을 시작하고 작업을 실행합니다. Azure 포털에서 빠른 생성 옵션을 사용하여 클러스터를 프로비전하는 경우 기본 Hadoop 사용자 이름을 "admin"이고 기본 SSH 사용자 이름은 "hdiuser"입니다.

클러스터 프로비전을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

![HDI.CLI.Provision][image-hdi-ps-provision]



###<a id="sdk"></a> HDInsight .NET SDK 사용
HDInsight .NET SDK는 .NET Framework 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 아래 지침에 따라 Visual Studio 콘솔 응용 프로그램을 만들고 클러스터를 만들기 위한 코드를 붙여 넣으세요.

**C# Visual Studio 콘솔 응용 프로그램을 만들려면**

1. Visual Studio 2013 또는 2015 열기
2. 다음 설정으로 새 Visual Studio 프로젝트를 만듭니다.

	|속성|값|
	|--------|-----|
	|Template|Templates/Visual C#/Windows/Console Application|
	|이름|CreateHDICluster|

5. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.
6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	.NET 라이브러리 및 이에 대한 참조가 현재 Visual Studio 프로젝트에 추가됩니다.

6. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 열고 다음 코드를 붙여 넣은 후 변수 값을 제공합니다.

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const string NewClusterVersion = "3.2";
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;

		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

		        private const string NewClusterSshUserName = "sshuser";
		        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
					Comment: ""rsa-key-20150731""
					AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
					gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
					yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
					WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
					pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
					zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
					---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. 클래스 멤버 값을 대체합니다.

7. **F5** 키를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용 프로그램의 상태가 표시되며 또한 Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. HDInsight 클러스터를 만들려면 몇 분정도 걸릴 수 있습니다.



## 다음 단계
이 문서에서는 Linux에서 HDInsight Hadoop 클러스터를 프로비전하는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하십시오.

- [Linux에서 HDInsight 작업](hdinsight-hadoop-linux-information.md): Linux에서 HDInsight 클러스터 관련 작업의 미묘한 차이를 알 수 있습니다.
- [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md): Ambari 웹 또는 Ambari REST API를 사용하여 HDInsight 클러스터의 Linux 기반 Hadoop을 모니터링하고 관리하는 방법에 대해 알아봅니다.
- [HDInsight에서 MapReduce 사용](hdinsight-use-mapreduce.md): 클러스터에서 MapReduce 작업을 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Hive 사용](hdinsight-use-hive.md): 클러스터에서 Hive 쿼리를 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Pig 사용](hdinsight-use-pig.md): 클러스터에서 Pig 작업을 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage.md): HDInsight가 HDInsight 클러스터에서 데이터를 저장하는데 Azure Blob 저장소를 사용하는 방법에 대해 알아봅니다.
- [HDInsight에 데이터 업로드](hdinsight-upload-data.md): HDInsight 클러스터에서 Azure Blob 저장소에 저장된 데이터로 작업하는 방법에 대해 알아봅니다.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

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

<!---HONumber=Oct15_HO2-->