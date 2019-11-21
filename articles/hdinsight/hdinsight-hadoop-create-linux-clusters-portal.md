---
title: Create Apache Hadoop clusters using web browser, Azure HDInsight
description: Learn how to create Apache Hadoop, Apache HBase, Apache Storm, or Apache Spark clusters on Linux for HDInsight by using a web browser and the Azure portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215896"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 포털은 Microsoft Azure 클라우드에 호스트된 서비스와 리소스에 대한 웹 기반 관리 도구입니다. 이 문서에서는 포털을 사용하여 Linux 기반 Azure HDInsight 클러스터를 만드는 방법에 대해 알아봅니다.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>전제 조건

* **Azure 구독** - [HDInsight에서 Hadoop을 테스트하기 위한 Azure 평가판을 가져오는 방법](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* **최신 웹 브라우저**. Azure Portal에서 HTML5와 JavaScript를 사용합니다. 이전 웹 브라우저에서는 제대로 작동하지 않을 수 있습니다.

## <a name="create-clusters"></a>클러스터 만들기

Azure 포털은 대부분의 클러스터 속성을 노출합니다. Azure Resource Manager 템플릿을 사용하여 많은 세부 정보를 숨길 수 있습니다. 자세한 내용은 [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. [Azure portal](https://portal.azure.com)에 로그인합니다.

1. From the left menu, navigate to **+ Create a resource** >  **Analytics** > **Azure HDInsight**.

    ![Create a new cluster in the Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Azure 포털에서 새 클러스터 만들기")

1. From the **Create HDInsight cluster** page, select **Go to classic create experience**.

    ![Go to classic create experience](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. **HDInsight** 페이지에서 **사용자 지정(크기, 설정, 앱)** 을 선택합니다.

1. **1 기본 사항**을 선택합니다. Then enter the following information:

    |자산 |설명 |
    |---|---|
    |클러스터 이름|이 이름은 전역적으로 고유해야 합니다.|
    |Subscription|From the drop-down list, select the Azure subscription that's used for the cluster.|
    |클러스터 유형|Select the type of cluster you want to create. 예를 들어 Hadoop과 Apache Spark가 있습니다. **운영 체제**는 **Linux**입니다. 다음으로, 클러스터 유형과 버전을 선택합니다. 어떤 버전을 선택할지 잘 모르는 경우 기본 버전을 사용합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.|
    |클러스터 로그인 사용자 이름|Provide the username, default is **admin**.|
    |클러스터 로그인 암호|Provide the password.|
    |SSH(보안 셸) 사용자 이름|Default is **sshuser**. If you want the same SSH password as the admin password you specified earlier, select the **Use cluster login password for SSH** check box. 그렇지 않으면 SSH 사용자를 인증할 수 있는 **암호** 또는 **공개 키**를 제공합니다. 공개 키는 추천되는 방법입니다. 아래쪽에서 **선택**을 클릭하여 자격 증명 구성을 저장합니다.  자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.|
    |Resource group|새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다.|
    |위치|Specify a datacenter where the cluster is created.|

    ![HDInsight create cluster basics](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Azure 포털에서 새 클러스터 만들기")

    > [!IMPORTANT]  
    > HDInsight 클러스터는 다양한 유형으로 제공됩니다. 클러스터가 튜닝되는 워크로드 또는 기술에 해당합니다. 여러 유형을 결합한 클러스터를 만드는 데 지원되는 방법은 없습니다. 예를 들어 한 클러스터에 Storm과 HBase가 있습니다.

    **다음**을 선택하여 다음 페이지로 이동합니다.

1. **2 보안 + 네트워킹**에서 제공된 드롭다운 메뉴를 사용하여 클러스터를 가상 네트워크에 연결할 수 있습니다. 클러스터를 가상 네트워크에 배치하려는 경우 Azure Virtual Network 및 서브넷을 선택합니다. For information on using HDInsight with a virtual network, see [Plan a virtual network deployment for Azure HDInsight clusters](hdinsight-plan-virtual-network-deployment.md). 이 문서에는 가상 네트워크에 대한 특정 구성 요구 사항이 포함되어 있습니다.

    If you want to use the **Enterprise Security Package**, follow these instructions: [Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    **다음**을 선택하여 다음 페이지로 이동합니다.

1. From **3 Storage**, for **Storage Account Settings**, specify whether you want Azure Storage or Azure Data Lake Storage as your default storage. 자세한 내용은 다음 표를 참조하세요.

    | Primary Storage type | 설명 |
    |------------------|-------------|
    | Azure Storage   |  * For **Selection method**, choose **My subscriptions** if you want to specify a storage account that's part of your Azure subscription. 그런 다음, 스토리지 계정을 선택합니다. 그렇지 않으면 **액세스 키**를 선택합니다. 그런 다음, Azure 구독 외부에서 선택하려는 스토리지 계정에 대한 정보를 제공합니다.</br></br> * For **Default container**, choose the default container name suggested by the portal or specify your own.</br></br> * If Azure Blob storage is your default storage, you can also select **Additional Storage Accounts** to specify additional storage accounts to associate with the cluster. **Azure Storage 키**에 대해 **스토리지 키 추가**를 선택합니다. 그런 다음, Azure 구독 또는 다른 구독의 스토리지 계정을 제공할 수 있습니다. 스토리지 계정 액세스 키를 제공합니다.</br></br> * If Blob storage is your default storage, you can also select **Data Lake Storage access** to specify Azure Data Lake Storage as additional storage. 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.</li></ul> |
    | Azure Data Lake Storage | Select **Azure Data Lake Storage Gen1** or **Azure Data Lake Storage Gen2**. Then refer to the article [Quickstart: Set up clusters in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) for instructions. |

    **Metastore Settings (optional)**

    필요에 따라 SQL 데이터베이스를 지정하여 클러스터와 연결된 Apache Hive 및 Apache Oozie 메타데이터를 저장합니다. **Hive용 SQL 데이터베이스 선택**에 대해 SQL 데이터베이스를 선택합니다. 그런 다음, 데이터베이스에 대한 사용자 이름과 암호를 제공합니다. Oozie 메타데이터에 대해 이러한 단계를 반복합니다.

    metastore에 Azure SQL 데이터베이스를 사용할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.
    * metastore에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 포함하여 다른 Azure 서비스에 대한 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 선택합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 선택합니다. 그런 다음, **Azure Services**에 대해 **예**를 선택합니다. 그런 다음 **저장**을 선택합니다.
    * metastore를 만드는 경우 데이터베이스 이름은 대시 또는 하이픈을 사용하여 지정하지 않습니다. 이러한 문자로 인해 클러스터 만들기 프로세스가 실패할 수 있습니다.

    ![HDInsight create cluster storage](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Azure 포털에서 새 클러스터 만들기")

    > [!WARNING]  
    > HDInsight 클러스터와 다른 위치에서는 추가 스토리지 계정을 사용할 수 없습니다.

    **다음**을 선택하여 다음 페이지로 이동합니다.

1. **4 애플리케이션(선택 사항)** 에서 원하는 애플리케이션을 선택합니다. Microsoft, ISV(Independent Software Vendor) 또는 사용자는 이러한 애플리케이션을 개발할 수 있습니다. 자세한 내용은 [클러스터 생성 중에 애플리케이션 설치](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)를 참조하세요.

    **다음**을 선택하여 다음 페이지로 이동합니다.

1. **5 클러스터 크기**에 이 클러스터에 사용되는 노드에 대한 정보가 표시됩니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터 실행의 예상 비용도 표시됩니다.

    ![HDInsight create cluster nodes](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Specify number of cluster nodes")

   > [!IMPORTANT]  
   > 32개가 넘는 작업자 노드를 계획하는 경우 코어가 8개 이상이고 RAM이 14GB 이상인 헤드 노드 크기를 선택합니다. 클러스터를 만들 때 또는 만든 후에 클러스터를 확장하여 노드를 계획합니다.
   >
   > 노드 크기 및 관련 비용에 대한 자세한 내용은 [Azure HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

    **다음**을 선택하여 다음 페이지로 이동합니다.

1. **6 스크립트 동작**에서 사용자 지정 구성 요소를 설치하도록 클러스터를 사용자 지정할 수 있습니다. 이 옵션은 클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우에 작동합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

   **다음**을 선택하여 다음 페이지로 이동합니다.

1. **7 요약**에서 이전에 입력한 정보를 확인합니다. 그런 다음 **만들기**를 선택합니다.

    ![HDInsight create cluster summary](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Specify number of cluster nodes")

    > [!NOTE]  
    > 클러스터를 만드는 데 약간의 시간이 걸리며, 일반적으로 약 20분이 소요됩니다. **알림**을 모니터링하여 프로비전 프로세스를 확인합니다.

1. 만들기 프로세스가 완료되면 **배포 성공** 알림에서 **리소스로 이동**을 선택합니다. 클러스터 창에서는 다음 정보를 제공합니다.

    ![HDI Azure portal cluster overview](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "클러스터 속성")

    Some of the icons in the window are explained as follows:

    |자산 | 설명 |
    |---|---|
    |개요|Provides all the essential information about the cluster. 예를 들어 이름, 이 이름이 속한 리소스 그룹, 위치, 운영 체제 및 클러스터 대시보드의 URL이 있습니다.|
    |클러스터 대시보드|Directs you to the Ambari portal associated with the cluster.|
    |SSH + Cluster login|Provides information needed to access the cluster by using SSH.|
    |삭제|HDInsight 클러스터를 삭제합니다.|

## <a name="customize-clusters"></a>클러스터 사용자 지정

* [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 성공적으로 만들었습니다. 이제 클러스터를 사용하는 방법을 알아봅니다.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 클러스터

* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache HBase용 Java 애플리케이션 개발](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 클러스터

* [HDInsight에서 Apache Storm용 Java 토폴로지 개발](storm/apache-storm-develop-java-topology.md)
* [HDInsight의 Apache Storm에서 Python 구성 요소 사용](storm/apache-storm-develop-python-topology.md)
* [HDInsight에서 Apache Storm을 사용하는 토폴로지 배포 및 모니터링](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 클러스터

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](spark/apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](spark/apache-spark-livy-rest-interface.md)
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](spark/apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](spark/apache-spark-machine-learning-mllib-ipython.md)
