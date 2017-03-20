---
title: "Azure Portal을 사용하여 Data Lake Store로 Azure HDInsight 클러스터 만들기 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure Data Lake Store로 HDInsight Hadoop 클러스터 만들기 및 사용"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: d1d780eb2c635f60409396804c0b8b706e59127b
ms.lasthandoff: 03/07/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Data Lake Store로 HDInsight 클러스터 만들기 | Azure
> [!div class="op_single_selector"]
> * [Azure Portal 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 사용(기본 저장소의 경우)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 사용(추가 저장소의 경우)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

이 문서에서는 Azure Portal을 사용하여 Azure Data Lake Store에 대한 액세스 권한이 있는 HDInsight 클러스터를 만드는 방법을 설명합니다. 지원되는 클러스터 유형의 경우 Data Lake Store를 기본 저장소 또는 추가 저장소 계정으로 사용할 수 있습니다. 

Data Lake Store를 추가 저장소로 사용하여 클러스터에 대한 기본 저장소 계정이 Azure Blob Storage로 유지되고 클러스터 관련 파일(예: 로그)이 기본 저장소에 계속 기록됩니다. 그러나 처리하려는 데이터는 Data Lake Store 계정에 저장될 수 있습니다. Data Lake Store를 추가 저장소 계정으로 사용하면 클러스터에서 저장소로 읽거나 쓰는 성능 또는 기능에 영향을 주지 않습니다.

Data Lake Store에서 HDInsight를 사용하는 몇 가지 중요한 고려 사항은 다음과 같습니다.

* 기본 저장소인 Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.5에서 사용할 수 있습니다.

* 기본 저장소인 Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight Premium 클러스터에서 *사용할 수 없습니다*.

* 추가 저장소로 Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.2, 3.4 및 3.5에서 사용할 수 있습니다.

* HBase 클러스터(Windows 및 Linux)의 경우 Data Lake Store는 기본 또는 추가 저장소 중 하나에 대해서도 저장소 옵션으로 *지원되지 않습니다*.

* Storm 클러스터(Windows 및 Linux)의 경우 Data Lake Store를 사용하여 Storm 토폴로지에서 데이터를 작성할 수 있습니다. Storm 토폴로지에서 읽을 수 있는 참조 데이터에 Data Lake Store를 사용할 수도 있습니다. 자세한 내용은 [Storm 토폴로지에서 Data Lake 저장소 사용](#use-data-lake-store-in-a-storm-topology)을 참조하세요.


## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음 요구 사항을 충족하는지 확인합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 이동합니다.

* **Azure 데이터 레이크 저장소 계정**. [Azure Portal을 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.

* **Azure Active Directory 서비스 주체** 이 자습서에서는 Azure AD(Azure Active Directory)에서 서비스 주체를 만드는 방법에 대한 지침을 제공합니다. 그러나 서비스 주체를 만들려면 Azure AD 관리자여야 합니다. 관리자인 경우 이 필수 요소를 건너뛰고 자습서를 진행할 수 있습니다.

 >[!NOTE]
 >Azure AD 관리자인 경우에만 서비스 주체를 만들 수 있습니다. Azure AD 관리자가 서비스 주체를 만들어야 Data Lake Store와 HDInsight 클러스터를 만들 수 있습니다. 또한 [인증서를 사용하여 서비스 주체 만들기](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)에 설명된 대로 인증서를 사용하여 서비스 주체를 만들어야 합니다.

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>Data Lake Store에 대한 액세스 권한을 가진 HDInsight 클러스터 만들기
이 섹션에서는 Data Lake Store를 추가 저장소로 사용하는 HDInsight Hadoop 클러스터를 만듭니다. 이 릴리스에서 Hadoop 클러스터의 경우 Data Lake Store를 클러스터의 추가 저장소로만 사용할 수 있습니다. 기본 저장소는 Azure Blob Storage로 유지됩니다. 따라서 먼저 클러스터에 필요한 저장소 계정 및 저장소 컨테이너를 만듭니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. HDInsight 클러스터를 프로비전하기 시작하려면 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-provision-clusters.md)의 단계를 따릅니다.

3. **저장소** 블레이드에서 Blob Storage 또는 Data Lake Store를 기본 저장소로 사용하려는지 지정하고 다음 중 하나를 수행합니다.

 * Azure Data Lake Store를 기본 저장소로 사용하려면 4단계로 건너뜁니다.

 * Blob Storage를 기본 저장소로 사용합니다.

    a. **기본 저장소 형식** 아래에서 **Azure Storage**를 선택합니다.

    b. **선택 방법**에서 다음 중 하나를 수행합니다.      * Azure 구독의 일부인 저장소 계정을 지정하고 **내 구독**을 선택한 다음 저장소 계정을 선택합니다.      * Azure 구독 외부에 있는 저장소 계정을 지정하려면 **선택키**를 선택한 다음 외부 저장소 계정에 정보를 제공합니다.

    c. **기본 컨테이너**에서 포털에서 제안한 기본 컨테이너 이름을 입력하거나 고유한 이름을 지정합니다.

 Blob Storage를 기본 저장소로 사용하는 경우 Data Lake Store를 클러스터에 대한 추가 저장소로 계속 사용할 수 있습니다. 이렇게 하려면 **Data Lake Store 액세스**를 클릭한 다음 5단계로 건너뜁니다.

 ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight 클러스터에 서비스 주체 추가")

4. Data Lake Store를 기본 저장소로 사용하려면:

 a. **기본 저장소 형식**에서 **Data Lake Store**를 클릭합니다.

 b. 기존 Data Lake Store 계정을 선택하고 클러스터 특정 파일을 저장할 루트 폴더 경로를 입력하고 **위치**를 **미국 동부 2**로 지정한 다음 **Data Lake Store 액세스**를 클릭합니다.

 >[!NOTE]
 >이 옵션은 HDInsight 3.5 클러스터에서만 사용할 수 있습니다(Standard 버전). HDInsight 3.5 클러스터 내의 HBase 클러스터 유형에 이 옵션을 사용할 수 없습니다.

 다음 스크린샷에서 루트 폴더 경로는 /clusters/myhdiadlcluster이며, 여기서 *myhdiadlcluster*는 만들려는 클러스터의 이름입니다. 이 경우 */clusters* 폴더가 Data Lake Store 계정에 존재하는지 확인합니다. 클러스터를 만드는 동안에 *myhdiadlcluster* 폴더가 만들어집니다. 마찬가지로 루트 경로가 */hdinsight/clusters/data/myhdiadlcluster*로 설정된 경우 */hdinsight/clusters/data/*가 Data Lake Store 계정에 존재하는지 확인합니다.

 ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "HDInsight 클러스터에 서비스 주체 추가")

5. **Data Lake Store 액세스** 블레이드에서 다음 중 하나를 수행합니다.

 * 기존 서비스 주체를 사용하려면 6단계로 건너뜁니다.
 * 서비스 주체를 만들려면:

    a. **Data Lake Store 액세스** 블레이드에서 **새로 만들기**를 클릭한 다음 **서비스 주체**를 클릭합니다.

    b. **서비스 주체 만들기** 블레이드에서 필수 값을 입력합니다.  

    c. **만들기**를 클릭합니다.  
    인증서 및 Azure AD 응용 프로그램은 자동으로 생성됩니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight 클러스터에 서비스 주체 추가")

    **인증서 다운로드**를 클릭하여 만든 서비스 주체와 연결된 인증서를 다운로드합니다. 추가 HDInsight 클러스터를 만들 때 동일한 서비스 주체를 사용하려면 인증서를 다운로드하는 것이 유용합니다. **선택**을 클릭합니다.

6. 기존 서비스 주체를 사용하려면:

 a. **Data Lake Store 액세스** 블레이드에서 **기존 항목 사용**을 클릭한 다음 **서비스 주체**를 클릭합니다.

 b. **서비스 주체 선택** 블레이드에서 기존 서비스 주체를 검색합니다. 사용하려는 서비스 주체 이름을 클릭한 다음 **선택**을 클릭합니다.

 c. **Data Lake Store 액세스** 블레이드에서 선택한 서비스 주체와 연결된 인증서(.pfx 파일)를 업로드한 다음 인증서 암호를 입력합니다.

 ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight 클러스터에 서비스 주체 추가")

7. **Data Lake Store 액세스** 블레이드에서 **액세스**를 클릭합니다.  
기본적으로 **파일 사용 권한 선택** 블레이드가 열립니다. 구독에서 모든 Data Lake Store 계정을 나열합니다.

8. 클러스터와 연결하려는 Data Lake Store 계정을 선택합니다.
 해당 계정에 있는 모든 파일 및 폴더가 나열됩니다. 그런 다음 파일 또는 폴더 수준에서 권한을 할당할 수 있습니다. 계정의 루트 수준에서 사용 권한을 할당하려면 계정 이름 옆에 있는 확인란을 선택합니다.

 ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight 클러스터에 서비스 주체 추가")

 > [!NOTE]
 > Data Lake Store 계정을 클러스터의 기본 저장소로 사용하는 경우 Data Lake Store 계정의 루트 수준에 있는 서비스 주체에 권한을 할당합니다.

9. 계정 내에서 파일이나 폴더에 대한 사용 권한을 할당하려면 **파일 사용 권한 선택** 창에서 Data Lake Store 계정을 선택합니다.

10. 오른쪽 창에서 사용 권한을 할당하려는 파일 및 폴더를 선택하고 권한(읽기/쓰기/실행)을 선택하고 사용 권한이 하위 항목에도 반복적으로 적용되는지 지정한 다음 **선택**을 클릭합니다.

    ![HDInsight 클러스터에 서비스 주체 사용 권한 할당](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "HDInsight 클러스터에 서비스 주체 사용 권한 할당")

11. **선택한 사용 권한 할당** 창에서 선택한 계정, 파일, 폴더의 Azure Active Directory 서비스 주체에 대한 사용 권한을 할당하려면 **실행**을 클릭합니다.

    ![HDInsight 클러스터에 서비스 주체 사용 권한 할당](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "HDInsight 클러스터에 서비스 주체 사용 권한 할당")

12. 권한이 성공적으로 할당된 후에 모든 열린 블레이드에서 **완료**를 클릭하여 **Data Lake Store 액세스** 블레이드로 돌아갑니다.

13. **Data Lake Store 액세스**에서 **선택**을 클릭한 다음 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터 만들기를 계속 진행합니다.

14. 클러스터 설정을 완료한 후에 클러스터 블레이드에서 다음 중 하나 또는 모두를 수행하여 결과를 확인합니다.

 * 클러스터에 대한 연결된 저장소가 지정한 Data Lake Store 계정인지 확인하려면 왼쪽 창에서 **저장소 계정**을 클릭합니다.

        ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Add service principal to HDInsight cluster")

 * 서비스 주체가 HDInsight 클러스터와 올바르게 연결되었는지 확인하려면 왼쪽 창에서 **Data Lake Store 액세스**를 클릭합니다.

       ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="examples"></a>예

Data Lake Store를 저장소로 사용하여 클러스터를 설정한 후에 HDInsight 클러스터를 사용하여 Data Lake Store에 저장된 데이터를 분석하는 방법에 대한 다음과 같은 예제를 참조합니다.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>기본 저장소인 Data Lake Store에서 데이터에 대한 Hive 쿼리 실행

Hive 쿼리를 실행하려면 Ambari 포털에서 [Hive 보기] 인터페이스를 사용합니다. [Ambari Hive 보기]를 사용하는 방법에 대한 지침은 [ HDInsight에서 Hadoop을 사용하여 Hive 보기 사용](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)을 참조하세요.

Data Lake Store에서 데이터를 사용하는 경우 다음과 같은 몇 가지 문자열을 변경합니다.

예를 들어, Data Lake Store를 주 저장소로 사용하여 만든 클러스터를 사용하는 경우 데이터의 경로는 *adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file*입니다. Data Lake Store 계정에 저장된 샘플 데이터에서 테이블을 만드는 Hive 쿼리는 다음과 같이 표시됩니다.

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

설명:
* `adl://hdiadlstorage.azuredatalakestore.net/`은 Data Lake Store 계정의 루트입니다.
* `/clusters/myhdiadlcluster`는 클러스터를 만드는 동안에 지정한 클러스터 데이터의 루트입니다.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`는 쿼리에서 사용하는 샘플 파일의 위치입니다.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>추가 저장소인 Data Lake Store에 저장된 데이터에 대한 Hive 쿼리 실행

사용자가 만든 클러스터에서 Blob Storage를 기본 저장소로 사용하는 경우 샘플 데이터는 추가 저장소로 사용된 Azure Data Lake Store 계정에 포함되지 않습니다. 이와 같은 경우에는 먼저 Blob Storage에서 Data Lake Store로 데이터를 전송하고 앞의 예제와 같이 쿼리를 실행합니다.

Blob Storage에서 Azure Data Lake Store로 데이터를 복사하는 방법에 대한 내용은 다음 문서를 참조하세요.

* [Distcp를 사용하여 Azure Storage Blob과 Data Lake Store 간에 데이터 복사](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy를 사용하여 Azure Storage Blob에서 Data Lake Store로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Spark 클러스터에서 Data Lake Store 사용
Spark 클러스터를 사용하여 Data Lake Store에 저장된 데이터에서 Spark 작업을 실행할 수 있습니다. 자세한 내용은 [HDInsight Spark 클러스터를 사용하여 Data Lake Store의 데이터 분석](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)을 참조하세요.


### <a name="use-data-lake-store-in-a-storm-topology"></a>Storm 토폴로지에서 Data Lake 저장소 사용
데이터 레이크 저장소를 사용하여 Storm 토폴로지에서 데이터를 쓸 수 있습니다. 이 시나리오를 수행하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm에 Azure Data Lake Store 사용](../hdinsight/hdinsight-storm-write-data-lake-store.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [PowerShell: HDInsight 클러스터를 만들어 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

