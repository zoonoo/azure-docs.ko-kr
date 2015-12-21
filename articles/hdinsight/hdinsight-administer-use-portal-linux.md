<properties
	pageTitle="Azure 포털을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 관리 | Microsoft Azure"
	description="Azure 포털을 사용하여 Linux 기반 HDInsight 클러스터를 만들고 관리하는 방법에 대해 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="larryfr"/>

# Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리

[AZURE.INCLUDE [선택기](../../includes/hdinsight-portal-management-selector.md)]


[Azure 포털][preview-portal]을 사용하여 Azure HDInsight에서 Linux 기반 Hadoop 클러스터를 프로비전 및 관리할 수 있습니다.

> [AZURE.NOTE]이 문서의 단계는 Linux 기반 Hadoop 클러스터를 사용한 작업을 대상으로 합니다. Windows 기반 클러스터를 사용한 작업에 대한 자세한 내용은 [Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md)를 참조하세요.


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## HDInsight를 관리하기 위한 기타 도구
Azure 포털뿐 아니라 HDInsight 관리에 사용할 수 있는 기타 도구도 있습니다.

- [Azure CLI를 사용하여 HDInsight 관리](hdinsight-administer-use-command-line.md): Azure CLI는 Azure 서비스를 관리할 수 있도록 하는 크로스 플랫폼 명령줄 도구입니다.

- [Azure PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md): Azure PowerShell은 Azure 서비스를 관리하기 위한 PowerShell cmdlet을 제공합니다.

## 필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

## HDInsight 클러스터 프로비전

다음 단계를 사용하여 Azure 포털에서 HDInsight 클러스터를 프로비전할 수 있습니다.

1. [Azure 포털][preview-portal]에 로그인합니다.

2. **새로 만들기**를 선택하고 __데이터 분석__, __HDInsight__를 차례로 선택합니다.

	![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. __클러스터 이름__을 입력한 다음 만들려는 __클러스터 유형__을 선택합니다. 클러스터 이름을 사용할 수 있는 경우 __클러스터 이름__ 옆에 녹색 확인 표시가 나타납니다.

	![클러스터 이름, 클러스터 유형 및 OS 유형](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. 둘 이상의 구독이 있는 경우 __구독__ 항목을 선택하여 클러스터에 사용할 Azure 구독을 선택합니다.

5. __리소스 그룹__의 경우 기존 리소스 그룹 목록을 표시할 항목을 선택한 다음 클러스터를 만들 리소스 그룹을 선택할 수 있습니다. 또는 __새로 만들기__를 선택한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

	> [AZURE.NOTE]사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. __자격 증명__을 선택한 다음 __클러스터 로그인 사용자 이름__에 대한 __클러스터 로그인 암호__를 입력합니다. __SSH 사용자 이름__과 __암호__ 또는 __공개 키__(SSH 사용자를 인증하는 데 사용됨)도 입력해야 합니다. 마지막으로 __선택__ 단추를 사용하여 자격 증명을 설정합니다. 이 문서에서는 원격 데스크톱을 사용하지 않으므로 사용 안함으로 둡니다.

	![클러스터 자격 증명 블레이드](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE]SSH는 명령줄을 사용하여 HDInsight 클러스터에 원격으로 액세스하는 데 사용됩니다. 사용자 이름 및 암호 또는 여기에 사용되는 공용 키는 SSH 통해 클러스터에 연결할 때 사용됩니다.

    HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

7. __데이터 원본__의 경우 항목을 선택하여 기존 데이터 원본을 선택하거나 새로 만듭니다.

	![데이터 원본 블레이드](./media/hdinsight-administer-use-portal-linux/datasource.png)

	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 __데이터 원본__ 블레이드의 항목을 이해합니다.

	- __선택 방법__: 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 __From all subscriptions(모든 구독에서)__로 설정합니다. 기존 저장소 계정의 __저장소 이름__ 및 __액세스 키__를 입력하려면 __액세스 키__로 설정합니다.

	- __새로 만들기__: 새 저장소 계정을 만들려면 사용합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.

	- __기본 컨테이너 선택__: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.

	- __위치__: 저장소 계정이 있거나 저장소 계정을 만들 지리적 지역입니다.

		> [AZURE.IMPORTANT]기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.

	- __선택__: 데이터 원본 구성을 저장하려면 이 항목을 사용합니다.

	
8. __노드 가격 책정 계층__을 선택하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 기본적으로 작업자 노드 수는 __4__로 설정됩니다.


	클러스터의 예상 비용이 이 블레이드의 맨 아래에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

	__선택__ 단추를 사용하여 __노드 가격 책정 계층__ 정보를 저장합니다.

9. __선택적 구성__을 선택합니다. 이 블레이드를 사용하면 다음과 같은 항목을 구성할 수 있습니다.

	* __HDInsight 버전__: 클러스터에 사용되는 HDInsight의 버전입니다. HDInsight 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전](hdinsight-component-versioning.md)을 참조하세요.

	* __외부 Metastore__: Oozie 및 Hive에 대한 구성 정보를 저장하는 데 사용되는 SQL 데이터베이스를 선택할 수 있습니다. 이를 통해 Hive 및 Oozie 구성을 다시 만들 필요 없이 클러스터를 삭제하고 다시 만들 때 구성을 다시 사용할 수 있습니다.

	* __가상 네트워크__: SQL 데이터베이스 또는 Azure 가상 컴퓨터와 같은 다른 리소스와 동일한 가상 네트워크에 HDInsight 클러스터를 배치할 수 있습니다. 가상 네트워크에 리소스를 배치하면 인터넷에서 들어오는 트래픽을 처리하는 공용 게이트웨이를 무시하여 서로 직접 통신할 수 있습니다. Azure 가상 네트워크에서 HDInsight의 이점을 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

		> [AZURE.IMPORTANT]HDInsight 구성에서 새 네트워크를 만들 수 없으므로 HDInsight 클러스터를 만들기 전에 Azure 가상 네트워크를 만들어야 합니다.
		>
		> 현재(2015/8/25) 하나의 Linux 기반 HDInsight 클러스터만 Azure 가상 네트워크에 있을 수 있다는 제한 사항이 있습니다.
        >
        > Linux 기반 HDInsight로 v1(클래식) Azure 가상 네트워크를 사용할 수 없습니다. 가상 네트워크가 v2(Azure 리소스 관리자)여야만 Azure 포털에서 HDInsight 클러스터를 만드는 과정 동안 옵션으로 나열하거나 Azure CLI 또는 Azure PowerShell에서 클러스터를 만들 때 사용할 수 있습니다.
        >
        > v1 네트워크에 리소스가 있고 HDInsight가 가상 네트워크를 통해 이러한 리소스에 직접 액세스할 수 있도록 하려면 [클래식 VNet을 새 VNet에 연결](../virtual-network/virtual-networks-arm-asm-s2s.md)을 참조하여 v2 가상 네트워크를 v1 가상 네트워크에 연결하는 방법에 대한 정보를 확인하세요. 이 연결이 설정되면 v2 가상 네트워크에 HDInsight 클러스터를 만들 수 있습니다.

	* __스크립트 동작__: 프로비전 중 HDInsight 클러스터를 사용자 지정하는 Bash 스크립트를 지정할 수 있습니다. 예를 들어 [Hue를 설치하는 스크립트](hdinsight-hadoop-hue-linux.md)(Hadoop과 함께 작업하기 위한 그래픽 클라이언트)가 있습니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

	* __Azure 저장소 키__: 추가 저장소 계정을 HDInsight 서버와 연결할 수 있습니다.

		> [AZURE.NOTE]HDInsight는 기본 데이터 저장소로 사용되고 이 구성 섹션을 통해 추가되거나 공개적으로 액세스할 수 있는 Azure 저장소 계정에만 액세스할 수 있습니다.

	![선택적 구성 블레이드](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. __시작 보드에 고정__이 선택되어 있는지 확인한 다음 __만들기__를 선택합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 프로비전 중임을 나타내고 프로비전이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

	| 프로비전 중 | 프로비전 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 프로비전](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![프로비전된 클러스터 타일](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 __알림__ 항목을 사용하여 프로비전 프로세스를 확인하세요.

## 클러스터 관리

Azure 포털에서 클러스터를 선택하면 이름, 리소스 그룹, 운영 체제 및 클러스터 대시보드(Linux 클러스터용 Ambari 웹에 액세스하는데 사용됨)에 대한 URL과 같은 클러스터에 대한 중요한 정보를 표시합니다.

![클러스터 세부 정보](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

다음을 사용하여 이 블레이드의 위쪽과 __필수__ 및 __빠른 링크__ 섹션에 있는 아이콘을 이해합니다.

* __설정__ 및 __모든 설정__: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 __설정__ 블레이드를 표시합니다.

* __대시보드__, __클러스터 대시보드__ 및 __URL__: 이러한 항목을 통해 Linux 기반 클러스터용 Ambari 웹인 클러스터 대시보드에 액세스할 수 있습니다.

* __보안 셸__: SSH를 사용하여 클러스터에 액세스하는 데 필요한 정보입니다.

* __클러스터 크기 조정__: 이 클러스터의 작업자 노드 수를 변경할 수 있습니다.

* __삭제__: HDInsight 클러스터를 삭제합니다.

* __빠른 시작(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-administer-use-portal-linux/quickstart.png))__: HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.

* __사용자(![사용자 아이콘](./media/hdinsight-administer-use-portal-linux/users.png))__: Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.

	> [AZURE.IMPORTANT]이는 _오직_ Azure 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.

* __태그(![태그 아이콘](./media/hdinsight-administer-use-portal-linux/tags.png))__: 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.

* __설명서__: Azure HDInsight에 대한 설명서 링크를 제공합니다.

> [AZURE.IMPORTANT]HDInsight 클러스터에 의해 제공되는 서비스를 관리하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

### <a name="scaling"></a>크기 조정

포털을 사용하여 클러스터의 크기를 조정하려면 HDInsight 클러스터를 선택한 다음 __클러스터 크기 조정__을 선택합니다. 클러스터에 설정하려는 __작업자 노드 수__를 입력한 다음 __저장__을 클릭합니다.

![크기 조정 ui의 이미지](./media/hdinsight-administer-use-portal-linux/scaling.png)

크기 조정 작업에 대한 자세한 내용은 [Linux에서 HDInsight 사용에 관한 정보](hdinsight-hadoop-linux-information.md#scaling)를 참조하세요.

## 클러스터 모니터링

HDInsight 클러스터 블레이드의 __사용량__ 섹션은 해당 클러스터에 할당된 코어 수 및 이 클러스터 내에서 노드에 할당되는 방법뿐만 아니라 HDInsight와 함께 사용하기 위해 구독에 사용할 수 있는 코어 수에 대한 정보도 표시합니다.

![사용 정보](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]HDInsight 클러스터에 의해 제공되는 서비스를 모니터링하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## 다음 단계
이 문서에서는 Azure 포털을 사용하여 HDInsight 클러스터를 만드는 방법 및 Hadoop 명령줄 도구를 여는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)
* [Azure CLI를 사용하여 HDInsight 관리](hdinsight-administer-use-command-line.md)
* [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight에 포함된 Hadoop 버전](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_1210_2015-->