---
title: 웹 브라우저를 사용하여 Apache Hadoop 클러스터 만들기 - Azure HDInsight
description: HDInsight에서 Apache Hadoop, Apache HBase, Apache Storm 또는 Apache Spark 클러스터를 만드는 방법을 알아봅니다. 웹 브라우저 및 Azure Portal을 사용합니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: fdba94738f31d80667a4f804dbed2586aca9db1d
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282370"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[!INCLUDE [selector](includes/hdinsight-create-linux-cluster-selector.md)]

Azure 포털은 Microsoft Azure 클라우드에 호스트된 서비스와 리소스에 대한 웹 기반 관리 도구입니다. 이 문서에서는 포털을 사용하여 Linux 기반 Azure HDInsight 클러스터를 만드는 방법에 대해 알아봅니다. [HDInsight 클러스터 만들기](./hdinsight-hadoop-provision-linux-clusters.md)에서 추가 세부 정보를 확인할 수 있습니다.

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

Azure 포털은 대부분의 클러스터 속성을 노출합니다. Azure Resource Manager 템플릿을 사용하여 많은 세부 정보를 숨길 수 있습니다. 자세한 내용은 [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-clusters"></a>클러스터 만들기

[!INCLUDE [secure-transfer-enabled-storage-account](includes/hdinsight-secure-transfer.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽 메뉴에서 **+ 리소스 만들기** 를 선택합니다.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png" alt-text="Azure Portal에서 새 클러스터 만들기":::

1. **분석** > **Azure HDInsight** 를 차례로 선택하여  **HDInsight 클러스터 만들기** 페이지로 이동합니다.

## <a name="basics"></a>기본 사항

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png" alt-text="HDInsight 클러스터 만들기 기본 사항":::

**기본** 탭에서 다음 정보를 제공합니다.

|속성 |설명 |
|---|---|
|Subscription|드롭다운 목록에서 클러스터에 사용할 Azure 구독을 선택합니다.|
|Resource group|드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
|클러스터 이름|전역적으로 고유한 이름을 입력합니다.|
|지역|드롭다운 목록에서 클러스터를 만들 지역을 선택합니다.|
|클러스터 유형|**클러스터 유형 선택** 을 클릭하여 목록을 엽니다. 목록에서 원하는 클러스터 유형을 선택합니다. HDInsight 클러스터는 다양한 형식으로 제공됩니다. 클러스터가 튜닝되는 워크로드 또는 기술에 해당합니다. 여러 유형을 결합한 클러스터를 만드는 데 지원되는 방법은 없습니다.|
|버전|드롭다운 목록에서 **버전** 을 선택합니다. 어떤 버전을 선택할지 잘 모르는 경우 기본 버전을 사용합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.|
|클러스터 로그인 사용자 이름|사용자 이름을 입력합니다. 기본값은 **admin** 입니다.|
|클러스터 로그인 암호|암호를 입력합니다.|
|클러스터 로그인 암호 확인|암호를 다시 입력합니다.|
|SSH(보안 셸) 사용자 이름|사용자 이름을 입력합니다. 기본값은 **sshuser** 입니다.|
|SSH에 클러스터 로그인 암호 사용|이전에 지정한 관리자 암호와 동일한 SSH 암호를 사용하려면 **SSH에 클러스터 로그인 암호 사용** 확인란을 선택합니다. 그렇지 않으면 SSH 사용자를 인증할 수 있는 **암호** 또는 **공개 키** 를 제공합니다. 공개 키는 추천되는 방법입니다. 아래쪽에서 **선택** 을 클릭하여 자격 증명 구성을 저장합니다.  자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.|

**다음: 스토리지 >>** 를 선택하여 다음 탭으로 이동합니다.

## <a name="storage"></a>스토리지

> [!WARNING] 
> 2020년 6월 15일부터 고객은 HDInsight를 사용하여 새 서비스 주체를 만들 수 없습니다. Azure Active Directory를 사용하여 [서비스 주체 및 인증서 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png" alt-text="HDInsight 클러스터 스토리지 만들기":::

### <a name="primary-storage"></a>기본 스토리지

**기본 스토리지 유형** 에 대한 드롭다운 목록에서 기본 스토리지 유형을 선택합니다. 완료할 후속 필드는 선택에 따라 달라집니다. **Azure Storage**:

1. **선택 방법** 으로 **목록에서 선택** 또는 **액세스 키 사용** 을 선택합니다.
    * **목록에서 선택** 의 경우 드롭다운 목록에서 **기본 스토리지 계정** 을 선택하거나 **새로 만들기** 를 선택합니다.
    * **액세스 키 사용** 으로 **스토리지 계정 이름** 을 입력합니다. 그런 다음, **액세스 키** 를 제공합니다.

1. **컨테이너** 에 대해 기본값을 그대로 적용하거나 새 값을 입력합니다.

### <a name="additional-azure-storage"></a>추가 Azure Storage

선택 사항: 추가 클러스터 스토리지에 대한 **Azure Storage 추가** 를 선택합니다. HDInsight 클러스터와는 다른 지역에서 추가 스토리지 계정을 사용하는 것은 지원되지 않습니다.

### <a name="metastore-settings"></a>Metastore 설정

선택 사항: 기존 SQL Database를 지정하여 클러스터 외부에 Apache Hive, Apache Oozie 및 Apache Ambari 메타데이터를 저장합니다. metastore에 사용되는 Azure SQL Database는 Azure HDInsight를 포함하여 다른 Azure 서비스에 대한 연결을 허용해야 합니다. metastore를 만드는 경우 데이터베이스 이름은 대시 또는 하이픈을 사용하여 지정하지 않습니다. 이러한 문자로 인해 클러스터 만들기 프로세스가 실패할 수 있습니다.

> [!IMPORTANT]
> metastore를 지원하는 클러스터 셰이프의 경우 기본 metastore는 **기본 계층 5 DTU 제한(업그레이드할 수 없음)** 을 Azure SQL Database에 제공합니다. 기본 테스트 목적으로 적합합니다. 대량 또는 프로덕션 워크로드의 경우 외부 metastore로 마이그레이션하는 것이 좋습니다.

**다음: 보안 + 네트워킹 >>** 을 선택하여 다음 탭으로 이동합니다.

## <a name="security--networking"></a>보안 + 네트워킹

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png" alt-text="HDInsight 클러스터 보안 네트워킹 만들기":::

**보안+네트워킹** 탭에서 다음 정보를 제공합니다.

|속성 |Description |
|---|---|
|엔터프라이즈 보안 패키지|선택 사항: **Enterprise Security Package** 를 사용하기 위한 확인란을 선택합니다. 자세한 내용은 [Azure Active Directory Domain Services를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.|
|TLS|선택 사항: 드롭다운 목록에서 TLS 버전을 선택합니다. 자세한 내용은 [전송 계층 보안](./transport-layer-security.md)을 참조하세요.|
|가상 네트워크|선택 사항: 드롭다운 목록에서 기존 가상 네트워크 및 서브넷을 선택합니다. 자세한 내용은 [Azure HDInsight 클러스터에 대한 가상 네트워크 배포 계획](hdinsight-plan-virtual-network-deployment.md)을 참조하세요. 이 문서에는 가상 네트워크에 대한 특정 구성 요구 사항이 포함되어 있습니다.|
|디스크 암호화 설정|선택 사항: 암호화를 사용하려면 이 확인란을 선택합니다. 자세한 내용은 [고객 관리형 키 디스크 암호화](./disk-encryption.md)를 참조하세요.|
|Kafka REST 프록시|이 설정은 클러스터 유형 Kafka에만 사용할 수 있습니다. 자세한 내용은 [REST 프록시 사용](./kafka/rest-proxy.md)을 참조하세요.|
|ID|선택 사항: 드롭다운 목록에서 기존 사용자 할당 서비스 ID를 선택합니다. 자세한 내용은 [Azure HDInsight의 관리 ID](./hdinsight-managed-identities.md)를 참조하세요.|

**다음: 구성+가격 책정 >>** 을 선택하여 다음 탭으로 이동합니다.

## <a name="configuration--pricing"></a>구성+가격 책정

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png" alt-text="HDInsight 클러스터 구성 만들기":::

**구성+가격 책정** 탭에서 다음 정보를 제공합니다.

|속성 |Description |
|---|---|
|애플리케이션 추가|선택 사항: 원하는 애플리케이션을 선택합니다. Microsoft, ISV(Independent Software Vendor) 또는 사용자는 이러한 애플리케이션을 개발할 수 있습니다. 자세한 내용은 [클러스터 생성 중에 애플리케이션 설치](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)를 참조하세요.|
|노드 크기|선택 사항: 다른 크기의 노드를 선택합니다.|
|노드 수|선택 사항: 지정된 노드 유형에 대한 노드 수를 입력합니다. 32개가 넘는 작업자 노드를 계획하는 경우 코어가 8개 이상이고 RAM이 14GB 이상인 헤드 노드 크기를 선택합니다. 클러스터를 만들 때 또는 만든 후에 클러스터를 확장하여 노드를 계획합니다.|
|자동 크기 조정 사용|선택 사항: 이 기능을 사용하도록 설정하려면 이 확인란을 선택합니다. 자세한 내용은 [Azure HDInsight 클러스터 자동 스케일링](./hdinsight-autoscale-clusters.md)을 참조하세요.|
|+스크립트 동작 추가|선택 사항: 이 옵션은 클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우에 작동합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.|

**검토+만들기 >>** 를 선택하여 클러스터 구성의 유효성을 검사하고 최종 탭으로 이동합니다.

## <a name="review--create"></a>검토 + 만들기

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="HDInsight 클러스터 만들기 요약":::

설정을 검토합니다. **만들기** 를 선택하여 클러스터를 만듭니다.

클러스터를 만드는 데 약간의 시간이 걸리며, 일반적으로 약 20분이 소요됩니다. **알림** 을 모니터링하여 프로비전 프로세스를 확인합니다.

## <a name="post-creation"></a>만들기 후

만들기 프로세스가 완료되면 **배포 성공** 알림에서 **리소스로 이동** 을 선택합니다. 클러스터 창에서는 다음 정보를 제공합니다.

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png" alt-text="HDI Azure Portal 클러스터 개요":::

창의 아이콘은 다음과 같이 설명됩니다.

|속성 | Description |
|---|---|
|개요|클러스터에 대한 모든 필수 정보를 제공합니다. 예를 들어 이름, 이 이름이 속한 리소스 그룹, 위치, 운영 체제 및 클러스터 대시보드의 URL이 있습니다.|
|클러스터 대시보드|사용자를 클러스터와 연결된 Ambari 포털로 다이렉트합니다.|
|SSH+클러스터 로그인|SSH를 사용하여 클러스터에 액세스하는 데 필요한 정보를 제공합니다.|
|DELETE|HDInsight 클러스터를 삭제합니다.|

## <a name="delete-the-cluster"></a>클러스터 삭제

[브라우저, PowerShell 또는 Azure CLI를 사용하는 HDInsight 클러스터 삭제](./hdinsight-delete-cluster.md)를 참조하세요.

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](./hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 성공적으로 만들었습니다. 이제 클러스터를 사용하는 방법을 알아봅니다.

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)