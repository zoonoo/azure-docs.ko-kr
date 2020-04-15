---
title: 웹 브라우저, Azure HDInsight를 사용하여 아파치 하두롭 클러스터 만들기
description: HDInsight에서 아파치 하두프, 아파치 HBase, 아파치 스톰 또는 아파치 스파크 클러스터를 만드는 방법을 배웁니다. 웹 브라우저 및 Azure 포털을 사용합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a5f6ac76d509a0a63c2d641f91cd91cdb2e0d19d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313817"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 포털은 Microsoft Azure 클라우드에 호스트된 서비스와 리소스에 대한 웹 기반 관리 도구입니다. 이 문서에서는 포털을 사용하여 Linux 기반 Azure HDInsight 클러스터를 만드는 방법에 대해 알아봅니다. 추가 세부 정보는 [HDInsight 만들기 클러스터에서](./hdinsight-hadoop-provision-linux-clusters.md)확인할 수 있습니다.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 포털은 대부분의 클러스터 속성을 노출합니다. Azure Resource Manager 템플릿을 사용하여 많은 세부 정보를 숨길 수 있습니다. 자세한 내용은 [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-clusters"></a>클러스터 만들기

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 상단 메뉴에서 **+ 리소스 만들기를 선택합니다.**

    ![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Azure 포털에서 새 클러스터 만들기")

1. **분석** > **Azure HDInsight를** 선택하여 **HDInsight 생성 클러스터** 페이지로 이동합니다.

## <a name="basics"></a>기본 사항

![HDInsight는 클러스터 기본을 만듭니다.](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Azure 포털에서 새 클러스터 만들기")

**기본** 탭에서 다음 정보를 제공합니다.

|속성 |Description |
|---|---|
|Subscription|드롭다운 목록에서 클러스터에 사용되는 Azure 구독을 선택합니다.|
|Resource group|드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새**.|
|클러스터 이름|전역적으로 고유한 이름을 입력합니다.|
|지역|드롭다운 목록에서 클러스터가 생성되는 영역을 선택합니다.|
|클러스터 유형|**클러스터 유형 선택을** 클릭하여 목록을 엽니다. 목록에서 원하는 클러스터 유형을 선택합니다. HDInsight 클러스터는 다양한 유형으로 제공됩니다. 클러스터가 튜닝되는 워크로드 또는 기술에 해당합니다. 여러 유형을 결합한 클러스터를 만드는 데 지원되는 방법은 없습니다.|
|버전|드롭다운 목록에서 **버전을**선택합니다. 어떤 버전을 선택할지 잘 모르는 경우 기본 버전을 사용합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.|
|클러스터 로그인 사용자 이름|사용자 이름을 제공하면 기본값이 **관리자입니다.**|
|클러스터 로그인 암호|암호를 입력합니다.|
|클러스터 로그인 암호 확인|암호 다시 입력|
|SSH(보안 셸) 사용자 이름|사용자 이름 제공, 기본값은 **sshuser**|
|SSH에 클러스터 로그인 암호 사용|앞에서 지정한 관리자 암호와 동일한 SSH 암호를 사용하려면 **SSH의 클러스터 로그인 암호 사용 확인란을** 선택합니다. 그렇지 않으면 SSH 사용자를 인증할 수 있는 **암호** 또는 **공개 키**를 제공합니다. 공개 키는 추천되는 방법입니다. 아래쪽에서 **선택**을 클릭하여 자격 증명 구성을 저장합니다.  자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.|

**다음: 저장소 >>** 선택하여 다음 탭으로 이동합니다.

## <a name="storage"></a>스토리지

![HDInsight, 클러스터 스토리지 구축](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Azure 포털 - 저장소에서 새 클러스터 만들기")

### <a name="primary-storage"></a>기본 스토리지

**기본 저장소 유형에**대한 드롭다운 목록에서 기본 저장소 유형을 선택합니다. 완료할 이후 필드는 선택한 필드에 따라 다릅니다. **Azure 저장소의**경우:

1. **선택 방법의**경우 **목록에서 선택**또는 액세스 키 **사용**중 하나를 선택합니다.
    * **목록에서 선택하려면**드롭다운 목록에서 **기본 저장소 계정을** 선택하거나 새 **.**
    * **액세스 키 를 사용하려면**저장소 계정 **이름을**입력합니다. 그런 다음 **액세스 키를**제공합니다.

1. **컨테이너의**경우 기본값을 수락하거나 새 값을 입력합니다.

### <a name="additional-azure-storage"></a>추가 Azure 저장소

선택 사항: 추가 클러스터 저장소를 위해 **Azure 저장소 추가를** 선택합니다. HDInsight 클러스터가 아닌 다른 지역에서 추가 저장소 계정을 사용하는 것은 지원되지 않습니다.

### <a name="metastore-settings"></a>메타스토어 설정

선택 사항: 기존 SQL 데이터베이스를 지정하여 아파치 하이브, 아파치 Oozie 및 클러스터 외부에 아파치 암바리 메타데이터를 저장합니다. 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스에 대한 연결을 허용해야 합니다. metastore를 만드는 경우 데이터베이스 이름은 대시 또는 하이픈을 사용하여 지정하지 않습니다. 이러한 문자로 인해 클러스터 만들기 프로세스가 실패할 수 있습니다.

**다음 을 선택: 보안 + 네트워킹 >>** 다음 탭으로 진행합니다.

## <a name="security--networking"></a>보안 + 네트워킹

![HDInsight, 클러스터 보안 네트워킹 생성](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight, 클러스터 보안 네트워킹 생성")

보안 **+ 네트워킹** 탭에서 다음 정보를 제공합니다.

|속성 |설명 |
|---|---|
|엔터프라이즈 보안 패키지|선택 사항: **엔터프라이즈 보안 패키지를**사용하려면 확인란을 선택합니다. 자세한 내용은 [Azure Active Directory 도메인 서비스를 사용하여 엔터프라이즈 보안 패키지를 사용하여 HDInsight 클러스터 구성을](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)참조하십시오.|
|TLS|선택 사항: 드롭다운 목록에서 TLS 버전을 선택합니다. 자세한 내용은 [전송 계층 보안](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)을 참조하십시오.|
|가상 네트워크|선택 사항: 드롭다운 목록에서 기존 가상 네트워크 및 서브넷을 선택합니다. 자세한 내용은 [Azure HDInsight 클러스터에 대한 가상 네트워크 배포 계획을](hdinsight-plan-virtual-network-deployment.md)참조하십시오. 이 문서에는 가상 네트워크에 대한 특정 구성 요구 사항이 포함되어 있습니다.|
|디스크 암호화 설정|선택 사항: 암호화를 사용할 확인란을 선택합니다. 자세한 내용은 [고객이 관리하는 키 디스크 암호화](./disk-encryption.md)를 참조하십시오.|
|Kafka REST 프록시|이 설정은 클러스터 유형 Kafka에서만 사용할 수 있습니다. 자세한 내용은 [REST 프록시 사용을](./kafka/rest-proxy.md)참조하십시오.|
|ID|선택 사항: 드롭다운 목록에서 기존 사용자가 할당한 서비스 ID를 선택합니다. 자세한 내용은 [Azure HDInsight의 관리되는 ID를](./hdinsight-managed-identities.md)참조하십시오.|

**다음 을 선택: 구성 + 가격 책정 >>** 다음 탭으로 진행합니다.

## <a name="configuration--pricing"></a>구성 + 가격 책정

![HDInsight, 클러스터 구성 생성](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "구성 및 가격 책정 탭")

구성 **+ 가격 책정** 탭에서 다음 정보를 제공합니다.

|속성 |설명 |
|---|---|
|+ 응용 프로그램 추가|선택 사항: 원하는 응용 프로그램을 선택합니다. Microsoft, ISV(Independent Software Vendor) 또는 사용자는 이러한 애플리케이션을 개발할 수 있습니다. 자세한 내용은 [클러스터 생성 중에 애플리케이션 설치](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)를 참조하세요.|
|노드 크기|선택 사항: 다른 크기의 노드를 선택합니다.|
|노드 수|선택 사항: 지정된 노드 유형에 대한 노드 수를 입력합니다. 32개 이상의 작업자 노드를 계획하는 경우 8개 이상의 코어와 14GB RAM이 있는 헤드 노드 크기를 선택합니다. 클러스터를 만들 때 또는 만든 후에 클러스터를 확장하여 노드를 계획합니다.|
|자동 크기 조정 사용|선택 사항: 기능을 활성화하려면 확인란을 선택합니다. 자세한 내용은 [Azure HDInsight 클러스터의 자동 배율 조정을](./hdinsight-autoscale-clusters.md)참조하십시오.|
|+ 스크립트 작업 추가|선택 사항: 이 옵션은 클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우에 작동합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.|

**검토 + 만들기 >>** 선택하여 클러스터 구성의 유효성을 검사하고 최종 탭으로 이동합니다.

## <a name="review--create"></a>검토 + 만들기

![HDInsight는 클러스터 요약을 만듭니다.](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "클러스터 노드 수 지정")

설정을 검토합니다. **만들기**를 선택하여 클러스터를 만듭니다.

클러스터를 만드는 데 약간의 시간이 걸리며, 일반적으로 약 20분이 소요됩니다. **알림**을 모니터링하여 프로비전 프로세스를 확인합니다.

## <a name="post-creation"></a>포스트 생성

만들기 프로세스가 완료되면 **배포 성공** 알림에서 **리소스로 이동**을 선택합니다. 클러스터 창에서는 다음 정보를 제공합니다.

![HDI Azure 포털 클러스터 개요](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "클러스터 속성")

창의 아이콘 중 일부는 다음과 같이 설명되어 있습니다.

|속성 | 설명 |
|---|---|
|개요|클러스터에 대한 모든 필수 정보를 제공합니다. 예를 들어 이름, 이 이름이 속한 리소스 그룹, 위치, 운영 체제 및 클러스터 대시보드의 URL이 있습니다.|
|클러스터 대시보드|클러스터와 연결된 Ambari 포털로 이동합니다.|
|SSH + 클러스터 로그인|SSH를 사용하여 클러스터에 액세스하는 데 필요한 정보를 제공합니다.|
|DELETE|HDInsight 클러스터를 삭제합니다.|

## <a name="customize-clusters"></a>클러스터 사용자 지정

* [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>클러스터 삭제

[브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제를](./hdinsight-delete-cluster.md)참조하십시오.

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](./hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 성공적으로 만들었습니다. 이제 클러스터를 사용하는 방법을 알아봅니다.

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache Storm용 Java 토폴로지 개발](storm/apache-storm-develop-java-topology.md)
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](spark/apache-spark-create-standalone-application.md)
