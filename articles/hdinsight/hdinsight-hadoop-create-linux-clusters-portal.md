---
title: 웹 브라우저를 사용하여 Apache Hadoop 클러스터 만들기 - Azure HDInsight
description: 웹 브라우저와 Azure Preview 포털을 사용하여 HDInsight용 Linux에서 Apache Hadoop, Apache HBase, Apache Storm 또는 Apache Spark 클러스터를 만드는 방법을 알아봅니다.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: ca8b398a796284e34ce4e0b577c335552ca75a98
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974270"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Azure 포털을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 포털은 Microsoft Azure 클라우드에 호스트된 서비스와 리소스에 대한 웹 기반 관리 도구입니다. 이 문서에서는 포털을 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* **최신 웹 브라우저**. Azure 포털은 HTML5 및 Javascript를 사용하며 이전 웹 브라우저에서 제대로 작동하지 않을 수 있습니다.

## <a name="create-clusters"></a>클러스터 만들기
Azure 포털은 대부분의 클러스터 속성을 노출합니다. Azure Resource Manager 템플릿을 사용하여 많은 세부 정보를 숨길 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 메뉴에서 **+ 리소스 만들기**를 선택합니다.

1.  **Azure Marketplace**에서 **Analytics**를 선택합니다.

1.  **추천**에서 **HDInsight**를 선택합니다.
   
    ![Azure Portal에서 새 클러스터 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Azure Portal에서 새 클러스터 만들기")

1. **HDInsight** 페이지에서 **사용자 지정(크기, 설정, 앱)** 을 선택합니다.

1. **1 기본 사항**을 선택한 후, 다음 정보를 입력합니다.

    ![Azure Portal에서 새 클러스터 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Azure Portal에서 새 클러스터 만들기")

    * **클러스터 이름**을 입력합니다. 이 이름은 전역적으로 고유해야 합니다.

    * **구독** 드롭다운에서 클러스터에 사용할 Azure 구독을 선택합니다.

    * **클러스터 유형**을 선택한 다음, 만들려는 클러스터 유형(Hadoop, Spark 등)을 선택합니다. **운영 체제**는 **Linux**입니다. 그런 다음, 클러스터 유형 및 버전을 선택합니다. 어떤 버전을 선택할지 잘 모르는 경우 기본 버전을 사용합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.
     
        > [!IMPORTANT]  
        > HDInsight 클러스터는 작업 부하 또는 클러스터에 대한 튜닝 기술에 해당하는 다양한 형식을 제공합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다.
        
    * **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**에 관리 사용자의 사용자 이름 및 암호를 입력합니다.

    * **SSH 사용자 이름**을 입력하고 SSH 암호를 이전에 지정한 관리자 암호와 동일하게 하려면 **클러스터 로그인과 동일한 암호 사용** 확인란을 선택합니다. 그렇지 않으면 **암호** 또는 **공개 키**를 제공합니다. 이 항목은 SSH 사용자를 인증하는 데 사용됩니다. 공개 키를 사용하는 것이 권장 방식입니다. 아래쪽의 **선택** 을 클릭하여 자격 증명 구성을 저장합니다.
   
        자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

    * **리소스 그룹**에서 새 리소스 그룹을 만들지 아니면 기존 집합을 사용할지 여부를 지정합니다.

    * 클러스터를 만들 데이터 센터 **위치**를 지정합니다.

    * **다음**을 선택하여 다음 페이지로 이동합니다.

4. **2 보안 + 네트워킹**에서 제공된 드롭다운을 사용하여 가상 네트워크에 클러스터를 연결할 수 있습니다. 클러스터를 가상 네트워크에 배치하려는 경우 Azure Virtual Network 및 서브넷을 선택합니다. Virtual Network에 대한 특정 구성 요구 사항을 포함하여 Virtual Network로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요. **Enterprise Security Package**를 사용하려는 경우 여기의 지침을 따를 수도 있습니다. [Azure Active Directory Domain Services를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)

    **다음**을 선택하여 다음 페이지로 이동합니다.


5. **3 스토리지**에서 Azure Storage(WASB) 또는 Data Lake Storage를 기본 스토리지로 사용할지 지정합니다. 자세한 내용은 아래 테이블을 살펴보세요.

     ![Azure Portal에서 새 클러스터 만들기](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Azure Portal에서 새 클러스터 만들기")

     | Storage                                      | 설명 |
     |----------------------------------------------|-------------|
     | **기본 저장소인 Azure Storage Blob**   | <ul><li>**기본 저장소 형식**으로 **Azure Storage**를 선택합니다. 그런 다음 Azure 구독의 일부인 저장소 계정을 지정하고 저장소 계정을 선택하려는 경우 **선택 방법**에서 **내 구독**을 선택할 수 있습니다. 그렇지 않으면 **선택키**를 클릭하고 Azure 구독 외부에서 선택하려는 저장소 계정에 대한 정보를 제공합니다.</li><li>**기본 컨테이너**의 경우 포털에서 제안한 기본 컨테이너 이름을 사용하거나 고유한 이름을 지정하도록 선택할 수 있습니다.</li><li>WASB를 기본 저장소를 사용하는 경우 선택적으로 **추가 Storage 계정**을 클릭하여 클러스터와 연결할 추가 Storage 계정을 지정합니다. **Azure Storage 키**의 경우 **저장소 키 추가**를 클릭한 다음, Azure 구독 또는 다른 구독에서 저장소 계정 액세스 키를 제공하여 저장소 계정을 제공할 수 있습니다.</li><li>WASB를 기본 스토리지로 사용하는 경우 선택적으로 **Data Lake Storage 액세스**를 클릭하여 Azure Data Lake Storage를 추가 스토리지로 지정할 수 있습니다. 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.</li></ul> |
     | **기본 저장소로 Azure Data Lake Storage** | **기본 스토리지 유형**으로 **Azure Data Lake Storage Gen1** 또는 **Azure Data Lake Storage Gen2**를 선택한 다음, [빠른 시작: HDInsight에서 클러스터 설정](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)에서 지침을 참조하세요. |
     | **외부 Metastore**                      | 필요에 따라 SQL Database를 지정하여 클러스터와 연결된 Hive 및 Oozie 메타데이터를 저장할 수 있습니다. **Hive용 SQL Database 사용**에 대해 SQL Database를 클릭하고 데이터베이스의 사용자 이름/암호를 입력합니다. Oozie 메타데이터에 대해 이러한 단계를 반복합니다.<br><br>metastores에 대한 Azure SQL Database를 사용하는 동안 몇 가지 고려 사항입니다. <ul><li>메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.</li><li>Metastore를 만들 때 클러스터를 만드는 프로세스가 실패할 수 있으므로 대시 또는 하이픈을 포함하는 데이터베이스 이름을 사용하지 않습니다.</li></ul> |

     > [!WARNING]  
     > HDInsight 클러스터와 다른 위치에서는 추가 저장소 계정을 사용할 수 없습니다.

     **다음**을 선택하여 다음 페이지로 이동합니다.


6. **4 애플리케이션((선택 사항))** 에서 원하는 애플리케이션을 선택합니다.  Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 애플리케이션을 개발할 수 있습니다. 자세한 내용은 [HDInsight 애플리케이션 설치](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)를 참조하세요.

    **다음**을 선택하여 다음 페이지로 이동합니다.


6. **5 클러스터 크기**에 이 클러스터에 사용되는 노드에 대한 정보가 표시됩니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터 실행의 예상 비용도 표시됩니다.
   
    ![노드 가격 책정 계층](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "클러스터 노드 수 지정")
   
   > [!IMPORTANT]  
   > 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
   > 
   > 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.
   
    **다음**을 선택하여 다음 페이지로 이동합니다.

8. **6 스크립트 동작**에서 사용자 지정 구성 요소를 설치하도록 클러스터를 사용자 지정할 수 있습니다.  클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우에 이 옵션을 사용합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

   **다음**을 선택하여 다음 페이지로 이동합니다.

9. **7 요약**에서, 이전에 입력한 정보를 확인한 다음, **만들기**를 클릭합니다.

     ![노드 가격 책정 계층](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "클러스터 노드 수 지정")
    
    > [!NOTE]  
    > 클러스터를 만드는 데 약간의 시간이 걸리며, 일반적으로 약 20분이 소요됩니다. **알림**을 모니터링하여 프로비전 프로세스를 확인합니다.

10. 만드는 프로세스가 완료되면 **배포 성공** 알림에서 **리소스로 이동**을 선택합니다. 클러스터 창에서는 다음 정보를 제공합니다.
    
    ![클러스터 인터페이스](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "클러스터 속성")
    
    다음을 사용하여 위쪽에 있는 아이콘을 이해합니다.
    
    * **개요** 탭은 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 모든 필수 정보를 제공합니다.
    * **대시보드**는 사용자를 클러스터와 연결된 Ambari 포털로 다이렉트합니다.
    * **보안 셸**: SSH를 사용하여 클러스터에 액세스하는 데 필요한 정보입니다.
    * **클러스터 크기 조정**을 통해 클러스터와 연결된 작업자 노드 수를 늘릴 수 있습니다.
      * **삭제**: HDInsight 클러스터를 삭제합니다.
    

## <a name="customize-clusters"></a>클러스터 사용자 지정
* [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.
* [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

## <a name="delete-the-cluster"></a>클러스터 삭제
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터
* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hadoop/hdinsight-use-pig.md)
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
* [Machine Learning과 Apache Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](spark/apache-spark-machine-learning-mllib-ipython.md)

