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
	ms.date="03/30/2016"
	ms.author="larryfr"/>

#Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리

[AZURE.INCLUDE [선택기](../../includes/hdinsight-portal-management-selector.md)]


[Azure 포털][preview-portal]을 사용하여 Azure HDInsight에서 Linux 기반 Hadoop 클러스터를 프로비전 및 관리할 수 있습니다.

> [AZURE.NOTE] 이 문서의 단계는 Linux 기반 Hadoop 클러스터를 사용한 작업을 대상으로 합니다. Windows 기반 클러스터를 사용한 작업에 대한 자세한 내용은 [Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md)를 참조하세요.


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## HDInsight를 관리하기 위한 기타 도구
Azure 포털뿐 아니라 HDInsight 관리에 사용할 수 있는 기타 도구도 있습니다.

- [Azure CLI를 사용하여 HDInsight 관리](hdinsight-administer-use-command-line.md): Azure CLI는 Azure 서비스를 관리할 수 있도록 하는 크로스 플랫폼 명령줄 도구입니다.

- [Azure PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md): Azure PowerShell은 Azure 서비스를 관리하기 위한 PowerShell cmdlet을 제공합니다.

## 필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

## HDInsight 클러스터 만들기

Azure 포털에서 HDInsight 클러스터를 만들 수 있습니다. 자세한 내용은 [Azure 포털을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md) 문서를 참조하세요.

## 클러스터 관리

Azure 포털에서 클러스터를 선택하면 이름, 리소스 그룹, 운영 체제 및 클러스터 URL(Linux 기반 클러스터용 Ambari 웹에 액세스하는데 사용됨)과 같은 클러스터에 대한 중요한 정보를 표시합니다.

![클러스터 세부 정보](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

다음을 사용하여 이 블레이드의 위쪽과 __필수__ 및 __빠른 링크__ 섹션에 있는 아이콘을 이해합니다.

* __설정__ 및 __모든 설정__: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 __설정__ 블레이드를 표시합니다.

* __대시보드__, __클러스터 대시보드__ 및 __URL__: 이러한 항목을 통해 클러스터 대시보드에 액세스할 수 있습니다. 클러스터 유형에 따라 클러스터의 대시보드 목록이 제공될 수 있습니다. 예를 들어 Spark 클러스터 유형은 __대시보드__ 아이콘을 선택한 경우 대시보드 목록을 표시하는 반면, Hadoop 클러스터는 Ambari 웹 UI를 엽니다.

* __보안 셸__: SSH를 사용하여 클러스터 헤드 노드에 액세스하는 데 필요한 정보입니다.

* __클러스터 크기 조정__: 이 클러스터의 작업자 노드 수를 변경할 수 있습니다.

* __삭제__: HDInsight 클러스터를 삭제합니다.

* __빠른 시작(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-administer-use-portal-linux/quickstart.png))__: HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.

* __사용자(![사용자 아이콘](./media/hdinsight-administer-use-portal-linux/users.png))__: Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.

	> [AZURE.IMPORTANT] 이는 _오직_ Azure 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.

* __태그(![태그 아이콘](./media/hdinsight-administer-use-portal-linux/tags.png))__: 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.

* __설명서__: Azure HDInsight에 대한 설명서 링크를 제공합니다.

> [AZURE.IMPORTANT] HDInsight 클러스터에 의해 제공되는 서비스를 관리하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

### 설정

__설정__ 아이콘 또는 __모든 설정__ 링크를 선택하면 설정 블레이드가 표시됩니다. 크기 조정 또는 보안 셸 정보 등 이전에 설명한 __Essentials__ 영역에서 사용 가능한 여러 기능을 여기에서도 사용할 수 있습니다. 또한 설정에서 다음에 액세스할 수도 있습니다.

* __감사 로그__: 클러스터 상태 문제를 진단하는 데 유용한 기록된 정보입니다.

* __클러스터 로그인__: HTTPS를 사용하여 클러스터에 액세스하는 데 사용할 수 있는 __클러스터 로그인 사용자 이름__ 및 __원격 주소__를 표시합니다.

* __외부 메타스토어__: 클러스터에서 사용되는 외부 메타스토어(있는 경우)에 대한 정보를 표시합니다. 클러스터를 구성하는 동안 사용자 지정 메타스토어를 구성하지 않은 경우에는 정보가 표시되지 않습니다.

* __스크립트 동작__: 이 클러스터에서 실행된 스크립트 동작에 대한 정보를 표시합니다. 또한 새 스크립트 동작을 실행할 수 있으며, 이전에 실행된 스크립트 동작을 보존하거나 삭제할 수 있습니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

* __앱__: 클러스터에 설치된 앱에 대한 정보를 표시하고, Azure 마켓플레이스에서 클러스터에 새 응용 프로그램을 추가할 수 있도록 합니다.

* __Azure 저장소 키__: 클러스터에서 사용되는 Azure 저장소 계정에 대한 정보를 표시합니다. 저장소 계정을 선택하면 선택한 계정의 저장소 계정 블레이드가 로드됩니다.

* __클러스터 AAD ID__: HDInsight 클러스터의 서비스 사용자에 대한 정보를 표시합니다. 서비스 사용자는 Azure Data Lake 저장소에 액세스하는 데 사용됩니다. 클러스터를 만드는 동안 클러스터를 Azure Data Lake 저장소에 연결하지 않은 경우 이 블레이드의 항목에는 __구성 되지 않음__이 표시됩니다. HDInsight에서 Azure Data Lake 저장소를 사용하는 방법에 대한 자세한 내용은 [Azure Data Lake 저장소를 사용하는 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.

### <a name="scaling"></a>크기 조정

포털을 사용하여 클러스터의 크기를 조정하려면 HDInsight 클러스터를 선택한 다음 __클러스터 크기 조정__을 선택합니다. 클러스터에 설정하려는 __작업자 노드 수__를 입력한 다음 __저장__을 클릭합니다.

![크기 조정 ui의 이미지](./media/hdinsight-administer-use-portal-linux/scaling.png)

크기 조정 작업에 대한 자세한 내용은 [Linux에서 HDInsight 사용에 관한 정보](hdinsight-hadoop-linux-information.md#scaling)를 참조하세요.

## 클러스터 모니터링

HDInsight 클러스터 블레이드의 __사용량__ 섹션은 해당 클러스터에 할당된 코어 수 및 이 클러스터 내에서 노드에 할당되는 방법뿐만 아니라 HDInsight와 함께 사용하기 위해 구독에 사용할 수 있는 코어 수에 대한 정보도 표시합니다.

![사용 정보](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] HDInsight 클러스터에 의해 제공되는 서비스를 모니터링하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## 다음 단계
이 문서에서는 Azure 포털을 사용하여 HDInsight 클러스터를 만드는 방법 및 Hadoop 명령줄 도구를 여는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)
* [Azure CLI를 사용하여 HDInsight 관리](hdinsight-administer-use-command-line.md)
* [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight에 포함된 Hadoop 버전](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->