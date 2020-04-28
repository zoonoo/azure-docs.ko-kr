---
title: 웹 브라우저, Azure HDInsight를 사용 하 여 Apache Hadoop 클러스터 만들기
description: HDInsight에서 Apache Hadoop, Apache HBase, Apache Storm 또는 Apache Spark 클러스터를 만드는 방법에 대해 알아봅니다. 웹 브라우저 및 Azure Portal를 사용 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 8d4e9a1e255884b9250879ab486d7a4e2849473a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192096"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 포털은 Microsoft Azure 클라우드에 호스트된 서비스와 리소스에 대한 웹 기반 관리 도구입니다. 이 문서에서는 포털을 사용하여 Linux 기반 Azure HDInsight 클러스터를 만드는 방법에 대해 알아봅니다. [HDInsight 클러스터 만들기](./hdinsight-hadoop-provision-linux-clusters.md)에서 추가 세부 정보를 확인할 수 있습니다.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 포털은 대부분의 클러스터 속성을 노출합니다. Azure Resource Manager 템플릿을 사용하여 많은 세부 정보를 숨길 수 있습니다. 자세한 내용은 [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-clusters"></a>클러스터 만들기

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 상단 메뉴에서 **+ 리소스 만들기**를 선택 합니다.

    ![Azure Portal에서 새 클러스터 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Azure 포털에서 새 클러스터 만들기")

1. **분석** > **Azure hdinsight** 를 선택 하 여 **hdinsight 클러스터 만들기** 페이지로 이동 합니다.

## <a name="basics"></a>기본 사항

![HDInsight 클러스터 기본 사항 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Azure 포털에서 새 클러스터 만들기")

**기본 사항** 탭에서 다음 정보를 제공 합니다.

|속성 |Description |
|---|---|
|Subscription|드롭다운 목록에서 클러스터에 사용할 Azure 구독을 선택합니다.|
|Resource group|드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택합니다.|
|클러스터 이름|전역적으로 고유한 이름을 입력합니다.|
|지역|드롭다운 목록에서 클러스터가 생성 되는 지역을 선택 합니다.|
|클러스터 유형|**클러스터 유형 선택** 을 클릭 하 여 목록을 엽니다. 목록에서 원하는 클러스터 유형을 선택 합니다. HDInsight 클러스터는 다양 한 형식으로 제공 됩니다. 클러스터가 튜닝되는 워크로드 또는 기술에 해당합니다. 여러 유형을 결합한 클러스터를 만드는 데 지원되는 방법은 없습니다.|
|버전|드롭다운 목록에서 **버전**을 선택 합니다. 어떤 버전을 선택할지 잘 모르는 경우 기본 버전을 사용합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.|
|클러스터 로그인 사용자 이름|사용자 이름을 입력합니다. 기본값은 **admin**입니다.|
|클러스터 로그인 암호|암호를 입력합니다.|
|클러스터 로그인 암호 확인|암호 다시 입력|
|SSH(보안 셸) 사용자 이름|사용자 이름을 입력 합니다. 기본값은 **sshuser** 입니다.|
|SSH에 클러스터 로그인 암호 사용|이전에 지정한 관리자 암호와 동일한 SSH 암호를 **사용 하려면 ssh에 대 한 클러스터 로그인 암호 사용** 확인란을 선택 합니다. 그렇지 않으면 SSH 사용자를 인증할 수 있는 **암호** 또는 **공개 키**를 제공합니다. 공개 키는 추천되는 방법입니다. 아래쪽에서 **선택**을 클릭하여 자격 증명 구성을 저장합니다.  자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.|

다음 **: 저장소 >>** 를 선택 하 여 다음 탭으로 이동 합니다.

## <a name="storage"></a>스토리지

![HDInsight 클러스터 저장소 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Azure Portal 저장소에서 새 클러스터 만들기")

### <a name="primary-storage"></a>기본 저장소

기본 **저장소 형식**에 대 한 드롭다운 목록에서 기본 저장소 유형을 선택 합니다. 완료 하는 이후 필드는 선택에 따라 달라 집니다. **Azure Storage**:

1. 선택 **방법**의 경우 **목록에서 선택**을 선택 하거나 선택 **키를 사용**합니다.
    * **Select from list**의 경우 드롭다운 목록에서 **기본 저장소 계정을** 선택 하거나 **새로 만들기**를 선택 합니다.
    * **액세스 키 사용**에 **저장소 계정 이름을**입력 합니다. 그런 다음 **액세스 키**를 제공 합니다.

1. **컨테이너**의 경우 기본값을 그대로 적용 하거나 새 값을 입력 합니다.

### <a name="additional-azure-storage"></a>추가 Azure Storage

선택 사항: 추가 클러스터 저장소에 대 한 **Azure Storage 추가** 를 선택 합니다. HDInsight 클러스터와 다른 지역에서 추가 저장소 계정을 사용 하는 것은 지원 되지 않습니다.

### <a name="metastore-settings"></a>Metastore 설정

선택 사항: 기존 SQL Database를 지정 하 여 클러스터 외부에 Apache Hive, Apache Oozie 및 Apache Ambari 메타 데이터를 저장 합니다. Metastore에 사용 되는 Azure SQL Database는 Azure HDInsight를 비롯 한 다른 Azure 서비스에 대 한 연결을 허용 해야 합니다. metastore를 만드는 경우 데이터베이스 이름은 대시 또는 하이픈을 사용하여 지정하지 않습니다. 이러한 문자로 인해 클러스터 만들기 프로세스가 실패할 수 있습니다.

다음 **: 보안 + 네트워킹 >>** 를 선택 하 여 다음 탭으로 이동 합니다.

## <a name="security--networking"></a>보안 + 네트워킹

![HDInsight 클러스터 보안 네트워킹 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight 클러스터 보안 네트워킹 만들기")

**보안 + 네트워킹** 탭에서 다음 정보를 제공 합니다.

|속성 |Description |
|---|---|
|엔터프라이즈 보안 패키지|선택 사항: **Enterprise Security Package**사용할 확인란을 선택 합니다. 자세한 내용은 [Azure Active Directory Domain Services를 사용 하 여 Enterprise Security Package를 사용 하 여 HDInsight 클러스터 구성](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)을 참조 하세요.|
|TLS|선택 사항: 드롭다운 목록에서 TLS 버전을 선택 합니다. 자세한 내용은 [Transport Layer Security](./transport-layer-security.md)을 참조 하세요.|
|가상 네트워크|선택 사항: 드롭다운 목록에서 기존 가상 네트워크 및 서브넷을 선택 합니다. 자세한 내용은 [Azure HDInsight 클러스터에 대 한 가상 네트워크 배포 계획](hdinsight-plan-virtual-network-deployment.md)을 참조 하세요. 이 문서에는 가상 네트워크에 대한 특정 구성 요구 사항이 포함되어 있습니다.|
|디스크 암호화 설정|선택 사항: 암호화를 사용 하려면이 확인란을 선택 합니다. 자세한 내용은 [고객이 관리 하는 키 디스크 암호화](./disk-encryption.md)를 참조 하세요.|
|Kafka REST 프록시|이 설정은 클러스터 유형 Kafka에 대해서만 사용할 수 있습니다. 자세한 내용은 [REST 프록시 사용](./kafka/rest-proxy.md)을 참조 하세요.|
|ID|선택 사항: 드롭다운 목록에서 기존 사용자 할당 서비스 id를 선택 합니다. 자세한 내용은 [Azure HDInsight에서 관리 되는 id](./hdinsight-managed-identities.md)를 참조 하세요.|

다음 **: 구성 + 가격 책정 >>** 를 선택 하 여 다음 탭으로 이동 합니다.

## <a name="configuration--pricing"></a>구성 + 가격 책정

![HDInsight 클러스터 구성 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "구성 및 가격 책정 탭")

**구성 + 가격 책정** 탭에서 다음 정보를 제공 합니다.

|속성 |Description |
|---|---|
|+ 응용 프로그램 추가|선택 사항: 원하는 응용 프로그램을 선택 합니다. Microsoft, ISV(Independent Software Vendor) 또는 사용자는 이러한 애플리케이션을 개발할 수 있습니다. 자세한 내용은 [클러스터 생성 중에 애플리케이션 설치](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)를 참조하세요.|
|노드 크기|선택 사항: 다른 크기의 노드를 선택 합니다.|
|노드 수|선택 사항: 지정 된 노드 형식에 대 한 노드 수를 입력 합니다. 32 개 이상의 작업자 노드를 계획 하는 경우 최소 8 개의 코어와 14GB RAM을 가진 헤드 노드 크기를 선택 합니다. 클러스터를 만들 때 또는 만든 후에 클러스터를 확장하여 노드를 계획합니다.|
|자동 크기 조정 사용|선택 사항:이 기능을 사용 하도록 설정 하려면 확인란을 선택 합니다. 자세한 내용은 [Azure HDInsight 클러스터 자동 크기 조정](./hdinsight-autoscale-clusters.md)을 참조 하세요.|
|+ 스크립트 동작 추가|선택 사항: 클러스터를 만들 때 사용자 지정 스크립트를 사용 하 여 클러스터를 사용자 지정 하려는 경우이 옵션이 작동 합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.|

**검토 + >>만들기** 를 선택 하 여 클러스터 구성의 유효성을 검사 하 고 최종 탭으로 이동 합니다.

## <a name="review--create"></a>검토 + 만들기

![HDInsight 클러스터 만들기 요약](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "클러스터 노드 수 지정")

설정을 검토 합니다. **만들기**를 선택하여 클러스터를 만듭니다.

클러스터를 만드는 데 약간의 시간이 걸리며, 일반적으로 약 20분이 소요됩니다. **알림**을 모니터링하여 프로비전 프로세스를 확인합니다.

## <a name="post-creation"></a>사후 만들기

만들기 프로세스가 완료되면 **배포 성공** 알림에서 **리소스로 이동**을 선택합니다. 클러스터 창에서는 다음 정보를 제공합니다.

![HDI Azure Portal 클러스터 개요](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "클러스터 속성")

창의 일부 아이콘은 다음과 같이 설명 됩니다.

|속성 | Description |
|---|---|
|개요|클러스터에 대 한 모든 필수 정보를 제공 합니다. 예를 들어 이름, 이 이름이 속한 리소스 그룹, 위치, 운영 체제 및 클러스터 대시보드의 URL이 있습니다.|
|클러스터 대시보드|사용자를 클러스터와 연결 된 Ambari 포털로 보냅니다.|
|SSH + 클러스터 로그인|SSH를 사용 하 여 클러스터에 액세스 하는 데 필요한 정보를 제공 합니다.|
|삭제|HDInsight 클러스터를 삭제합니다.|

## <a name="delete-the-cluster"></a>클러스터 삭제

[브라우저, PowerShell 또는 Azure CLI를 사용 하 여 HDInsight 클러스터 삭제를](./hdinsight-delete-cluster.md)참조 하세요.

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](./hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 성공적으로 만들었습니다. 이제 클러스터를 사용하는 방법을 알아봅니다.

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)
