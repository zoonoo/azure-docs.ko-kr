---
title: Azure HDInsight 만들기-Azure Data Lake Storage Gen2-포털
description: 포털을 사용 하 여 Azure HDInsight 클러스터에서 Azure Data Lake Storage Gen2를 사용 하는 방법을 알아봅니다.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 104424c1e3bd1df69106db7da45b744755b51e82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858794"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Data Lake Storage Gen2를 사용 하 여 클러스터 만들기

Azure 포털은 Microsoft Azure 클라우드에 호스트된 서비스와 리소스에 대한 웹 기반 관리 도구입니다. 이 문서에서는 포털을 사용하여 Linux 기반 Azure HDInsight 클러스터를 만드는 방법에 대해 알아봅니다. [HDInsight 클러스터 만들기](./hdinsight-hadoop-provision-linux-clusters.md)에서 추가 세부 정보를 확인할 수 있습니다.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

저장소에 Data Lake Storage Gen2를 사용 하는 HDInsight 클러스터를 만들려면 다음 단계를 수행 하 여 계층적 네임 스페이스를 포함 하는 저장소 계정을 구성 합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID가 아직 없는 경우 지금 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 위에서 **리소스 만들기**를 클릭 합니다.
1. 검색 상자에 **사용자 할당** 을 입력 하 고 **사용자 할당 관리 id**를 클릭 합니다.
1. **만들기**를 클릭합니다.
1. 관리 id의 이름을 입력 하 고 올바른 구독, 리소스 그룹 및 위치를 선택 합니다.
1. **만들기**를 클릭합니다.

Azure HDInsight에서 관리 id가 작동 하는 방식에 대 한 자세한 내용은 [Azure hdinsight에서 관리 되는 id](hdinsight-managed-identities.md)를 참조 하세요.

![사용자 할당 관리 ID 만들기](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2에 사용할 저장소 계정 만들기

Azure Data Lake Storage Gen2와 함께 사용할 저장소 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 위에서 **리소스 만들기**를 클릭 합니다.
1. 검색 상자에 **저장소** 를 입력 하 고 **저장소 계정**을 클릭 합니다.
1. **만들기**를 클릭합니다.
1. **저장소 계정 만들기** 화면에서 다음을 수행 합니다.
    1. 올바른 구독 및 리소스 그룹을 선택 합니다.
    1. Data Lake Storage Gen2를 사용 하 여 저장소 계정의 이름을 입력 합니다.
    1. **고급** 탭을 클릭 합니다.
    1. **Data Lake Storage Gen2**에서 **계층적 네임 스페이스** 옆에 있는 **사용** 을 클릭 합니다.
    1. **검토 + 만들기**를 클릭합니다.
    1. **만들기**를 클릭합니다.

저장소 계정을 만드는 동안 기타 옵션에 대 한 자세한 내용은 [빠른 시작: Azure Data Lake Storage Gen2에 대 한 저장소 계정 만들기](../storage/blobs/create-data-lake-storage-account.md)를 참조 하세요.

![Azure Portal에서 스토리지 계정을 만드는 과정을 보여 주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Data Lake Storage Gen2에서 관리 되는 id에 대 한 사용 권한 설정

저장소 계정의 **저장소 Blob 데이터 소유자** 역할에 관리 되는 id를 할당 합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 저장소 계정을 선택 하 고 **액세스 제어 (IAM)** 를 선택 하 여 계정에 대 한 액세스 제어 설정을 표시 합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.

    ![스토리지 액세스 제어 설정을 보여 주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. **+ 역할 할당 추가** 단추를 선택 하 여 새 역할을 추가 합니다.
1. **역할 할당 추가** 창에서 **저장소 Blob 데이터 소유자** 역할을 선택 합니다. 그런 다음, 관리 ID 및 스토리지 계정이 있는 구독을 선택합니다. 다음으로, 앞에서 만든 사용자 할당 관리 ID를 검색하여 찾습니다. 마지막으로 관리 되는 id를 선택 하면 **선택한 구성원**아래에 나열 됩니다.

    ![Azure 역할을 할당 하는 방법을 보여 주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. **저장**을 선택합니다. 선택한 사용자 할당 id는 이제 선택한 역할 아래에 나열 됩니다.
1. 이 초기 설정이 완료되면 포털을 통해 클러스터를 만들 수 있습니다. 클러스터가 스토리지 계정과 동일한 Azure 영역에 있어야 합니다. 클러스터 만들기 메뉴의 **저장소** 탭에서 다음 옵션을 선택 합니다.

    * **기본 저장소 형식**으로 **Azure Data Lake Storage Gen2**를 선택 합니다.
    * **기본 저장소 계정**에서 Data Lake Storage Gen2 저장소를 사용 하 여 새로 만든 저장소 계정을 검색 하 고 선택 합니다.

    * **Id**에서 새로 만든 사용자 할당 관리 id를 선택 합니다.

        ![Azure HDInsight에서 Data Lake Storage Gen2를 사용하기 위한 스토리지 설정](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Data Lake Storage Gen2를 사용 하 여 보조 저장소 계정을 추가 하려면 저장소 계정 수준에서 앞에서 만든 관리 되는 id를 추가 하려는 새 Data Lake Storage Gen2에 할당 하면 됩니다. HDInsight에서 "추가 저장소 계정" 블레이드를 통해 Data Lake Storage Gen2를 사용 하 여 보조 저장소 계정을 추가 하는 것은 지원 되지 않습니다.
    > * HDInsight에서 사용 하는 Azure Blob storage 계정에서 RA-GRS 또는 ZRS를 사용 하도록 설정할 수 있습니다. 그러나 RA-GRS 또는 RA-ZRS 보조 끝점에 대 한 클러스터를 만드는 것은 지원 되지 않습니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[브라우저, PowerShell 또는 Azure CLI를 사용 하 여 HDInsight 클러스터 삭제를](./hdinsight-delete-cluster.md)참조 하세요.

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](./hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 성공적으로 만들었습니다. 이제 클러스터를 사용하는 방법을 알아봅니다.

### <a name="apache-spark-clusters"></a>Apache Spark 클러스터

* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](spark/apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](spark/apache-spark-livy-rest-interface.md)
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](spark/apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 클러스터

* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache HBase용 Java 애플리케이션 개발](hbase/apache-hbase-build-java-maven-linux.md)
