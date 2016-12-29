---
title: "포털을 사용하여 Azure Data Lake Store를 포함한 HDInsight 클러스터 만들기 | Microsoft 문서"
description: "Azure 포털을 사용하여 Azure 데이터 레이크 저장소로 HDInsight Hadoop 클러스터 만들기 및 사용"
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
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3a9a4df2d260e8eebd8621b22efdb2927e9f5ecf
ms.openlocfilehash: a4fb47f9f517d66cf0ff9fde039d7bfd8edc29eb


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Azure 포털을 사용하여 데이터 레이크 저장소로 HDInsight 클러스터 만들기
> [!div class="op_single_selector"]
> * [포털 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure Portal을 사용하여 Azure Data Lake Store에 대한 액세스 권한이 있는 HDInsight 클러스터를 만드는 방법에 대해 알아봅니다. 지원되는 클러스터 유형의 경우 Data Lake Store는 기본 저장소 또는 추가 저장소 계정으로 사용됩니다. Data Lake Store를 추가 저장소로 사용하는 경우 클러스터의 기본 저장소 계정은 여전히 Azure Storage Blob(WASB)이고 클러스터 관련 파일(예: 로그 등)은 여전히 기본 저장소에 기록되지만 처리하려는 데이터는 Data Lake Store 계정에 저장될 수 있습니다. Data Lake 저장소를 추가 저장소 계정으로 사용하면 클러스터에서 저장소로 읽고 쓰는 성능 또는 기능에 영향을 주지 않습니다.

몇 가지 중요한 고려 사항

* 기본 저장소로 Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.5에서 사용할 수 있습니다.

* 추가 저장소로 Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.2, 3.4 및 3.5에서 사용할 수 있습니다.

* HBase 클러스터(Windows 및 Linux)의 경우 Data Lake Store는 기본 저장소뿐만 아니라 추가 저장소에 대해서도 저장소 옵션으로 **지원되지 않습니다**.

* Storm 클러스터(Windows 및 Linux)의 경우 Data Lake Store는 Storm 토폴로지에서 데이터를 쓰는 데 사용될 수 있습니다. 데이터 레이크 저장소는 Storm 토폴로지에서 읽을 수 있는 참조 데이터를 저장하는 데도 사용될 수 있습니다. 자세한 내용은 [Storm 토폴로지에서 Data Lake 저장소 사용](#use-data-lake-store-in-a-storm-topology)을 참조하세요.


## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Data Lake Store 계정**. [Azure Portal을 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.

* **Azure Active Directory 서비스 사용자**. 이 자습서의 단계에서는 Azure AD에서 서비스 사용자를 만드는 방법에 대한 지침을 제공합니다. 그러나 서비스 사용자를 만들려면 Azure AD 관리자여야 합니다. Azure AD 관리자인 경우 이 필수 조건을 건너뛰고 자습서를 진행할 수 있습니다.

    **Azure AD 관리자가 아닌 경우** 서비스 사용자를 만드는 데 필요한 단계를 수행할 수 없습니다. 이 경우 먼저 Azure AD 관리자가 서비스 사용자를 만들어야 Data Lake Store와 HDInsight 클러스터를 만들 수 있습니다. 또한 [인증서를 사용하여 서비스 사용자 만들기](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)에 설명된 대로 인증서를 사용하여 서비스 사용자를 만들어야 합니다.

## <a name="do-you-learn-faster-with-videos"></a>비디오를 통해 보다 빠르게 사용 방법을 익힐 수 있습니다.
Data Lake 저장소에 대한 액세스와 HDInsight 클러스터를 프로비전하는 방법을 이해하려면 다음 비디오를 시청하세요.

* [Data Lake 저장소에 대한 액세스와 HDInsight 클러스터 만들기](https://mix.office.com/watch/l93xri2yhtp2)
* 클러스터가 설정된 후, [Hive 및 Pig 스크립트를 사용하여 Data Lake 저장소의 데이터에 액세스](https://mix.office.com/watch/1n9g5w0fiqv1q)

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Azure 데이터 레이크 저장소 계정에 액세스할 수 있는 HDInsight 클러스터를 만듭니다.
이 섹션에서는 추가 저장소로 데이터 레이크 저장소를 사용하는 HDInsight Hadoop 클러스터를 만듭니다. 이 릴리스에서 Hadoop 클러스터의 경우 데이터 레이크 저장소는 클러스터에 대해 추가 저장소로만 사용될 수 있습니다. 기본 저장소는 여전히 Azure 저장소 Blob(WASB)이 됩니다. 따라서 먼저 클러스터에 필요한 저장소 계정 및 저장소 컨테이너를 만들어 보겠습니다.

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-provision-clusters.md) 에서 단계를 따라 HDInsight 클러스터 프로비전을 시작합니다.

3. **데이터 원본** 블레이드에서 Azure Storage(WASB) 또는 Data Lake Store를 기본 저장소로 사용할지 여부를 지정합니다. Azure Data Lake Store를 기본 저장소로 사용하려면 다음 단계로 건너뜁니다. 

    Azure Storage Blob을 기본 저장소로 사용하려면 **기본 저장소 유형**에 대해 **Azure Storage**를 클릭합니다. 저장소 계정 및 저장소 컨테이너에 대한 세부 정보를 지정하고 **위치**를 **미국 동부 2**로 지정한 다음 **클러스터 AAD ID**를 클릭합니다.
    
    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Add service principal to HDInsight cluster")


4. Azure Data Lake Store를 기본 저장소로 사용하려면 **기본 저장소 유형**에 대해 **Data Lake Store**를 클릭합니다. 이미 존재하는 Data Lake Store 계정을 선택하고 클러스터 특정 파일을 저장할 루트 폴더 경로를 제공하고(아래 참고 참조) **위치**를 **미국 동부 2**로 지정한 다음 **클러스터 AAD ID**를 클릭합니다. 이 옵션은 HDInsight 3.5 클러스터에서만 사용할 수 있습니다. HDInsight 3.5 클러스터에서 이 옵션은 HBase 클러스터 유형에 사용할 수 없습니다.

    아래 화면 캡처에서 루트 폴더 경로는 /clusters/myhdiadlcluster이며, 여기서 **myhdiadlcluster**는 만들려는 클러스터의 이름입니다. 이 경우 **/clusters** 폴더가 이미 Data Lake Store 계정에 존재하는지 확인합니다. 클러스터를 만드는 동안에 **myhdiadlcluster** 폴더가 만들어집니다. 마찬가지로 루트 경로가 /hdinsight/clusters/data/myhdiadlcluter로 설정된 경우 **/hdinsight/clusters/data/**가 이미 Data Lake Store 계정에 존재하는지 확인해야 합니다.
        
    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Add service principal to HDInsight cluster")

5. **클러스터 AAD ID** 블레이드에서 기존 서비스 주체를 선택하거나 새로 만들기를 선택할 수 있습니다. 기존 서비스 주체를 사용하려면 다음 단계로 건너뜁니다.

    새 서비스 주체를 만들려면 **클러스터 AAD ID** 블레이드에서 **새로 만들기**, **서비스 주체**를 차례로 클릭한 다음 **서비스 주체 만들기** 블레이드에서 값을 제공하여 새 서비스 주체를 만듭니다. 이 작업의 일부로 인증서와 Azure Active Directory 응용 프로그램도 생성됩니다. **만들기**를 클릭합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Add service principal to HDInsight cluster")

    **인증서 다운로드**를 클릭하여 만든 서비스 주체와 연결된 인증서를 다운로드합니다. 추가 HDInsight 클러스터를 만드는 동안 나중에 동일한 서비스 주체를 사용하려는 경우에 유용합니다. **선택**을 클릭합니다.

6. 기존 서비스 주체를 사용하려면 **클러스터 AAD ID** 블레이드에서 **기존 정보 사용**, **서비스 주체**를 차례로 클릭한 다음 **서비스 주체 선택** 블레이드에서 기존 서비스 주체를 검색합니다. 서비스 주체 이름을 클릭한 다음 **선택**을 클릭합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Add service principal to HDInsight cluster")

    **클러스터 AAD ID** 블레이드에서 선택한 서비스 주체와 연결된 인증서(.pfx)를 업로드한 다음 인증서 암호를 제공합니다.

6. **클러스터 AAD ID** 블레이드에서 **ADLS 액세스 관리**를 클릭합니다. 다음 창에서 **파일 권한 선택**이 기본적으로 선택되어 있으며 구독에 있는 모든 Data Lake Store 계정을 나열합니다. 해당 계정의 파일과 폴더를 나열하려면 클러스터와 연결할 Data Lake Store 계정을 클릭합니다. 그런 다음 파일 또는 폴더 수준에서 권한을 할당할 수 있습니다. 계정의 루트 수준에서 권한을 연결하려면 계정 이름 옆에 있는 확인란을 선택합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Add service principal to HDInsight cluster")

    > [!NOTE]
    > Data Lake Store 계정을 클러스터의 기본 저장소로 사용하는 경우 Data Lake Store 계정의 루트 수준에 있는 서비스 주체에 권한을 **할당해야** 합니다.

7. 계정에 있는 파일이나 폴더에 대한 권한을 할당하려면 Data Lake Store 계정을 선택하여 다음 창에서 파일/폴더를 확인합니다. 파일/폴더를 선택하고 할당할 권한(읽기/쓰기/실행)을 선택하고 권한이 하위 항목에도 반복적으로 적용되는지 지정한 다음 **선택**을 클릭합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Add service principal to HDInsight cluster")

8. 다음 화면에서 **실행**을 클릭하여 선택한 계정, 파일, 폴더에서 Azure Active Directory 서비스 주체에 대한 권한을 할당합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Add service principal to HDInsight cluster")

9. 권한이 성공적으로 할당되면 **클러스터 AAD ID** 블레이드로 돌아갈 때까지 모든 블레이드에서 **완료**를 클릭합니다.

4. **클러스터 AAD ID** 블레이드에서 **선택**을 클릭한 다음 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터 만들기를 계속 진행합니다.

10. 클러스터가 프로비전된 후 서비스 주체가 HDInsight 클러스터와 연결되어 있음을 확인할 수 있습니다. 이렇게 하려면 클러스터 블레이드에서 **클러스터 AAD ID** 를 클릭하여 연결된 서비스 주체를 확인합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="show-me-some-examples"></a>몇 가지 예제 보기

저장소로 Data Lake Store를 사용하여 클러스터를 프로비전한 후에 HDInsight 클러스터를 사용하여 Data Lake Store에 저장된 데이터를 분석하는 방법에 대한 몇 가지 예가 여기에 있습니다.

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store"></a>Data Lake Store에 저장된 데이터에 대한 Hive 쿼리 실행

Hive 쿼리를 실행하려면 Ambari 포털에서 제공하는 [Hive 보기] 인터페이스를 사용할 수 있습니다. [Ambari Hive 보기]를 사용하는 방법에 대한 지침은 [ HDInsight에서 Hadoop을 사용하여 Hive 보기 사용](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)을 참조하세요. Data Lake Store에서 데이터로 작업할 때 변경해야 할 몇 가지 사항이 있습니다.

* 위에서 만든 클러스터의 예제를 사용하는 경우 데이터 경로는 `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file`이 됩니다. Data Lake Store 계정에 저장된 샘플 데이터에서 테이블을 만드는 Hive 쿼리는 다음과 같이 표시됩니다.

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

위의 쿼리에 대해 설명하면 다음과 같습니다.

* `adl://hdiadlstorage.azuredatalakestore.net`은 Data Lake Store 계정의 루트입니다.
* `/clusters/myhdiadlcluster`는 클러스터를 만드는 동안에 지정한 클러스터 데이터의 루트입니다.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`는 쿼리에서 사용하는 샘플 파일의 위치입니다.


### <a name="use-data-lake-store-with-spark-cluster"></a>Spark 클러스터로 Data Lake 저장소 사용
Spark 클러스터를 사용하여 Data Lake Store에 저장된 데이터에서 Spark 작업을 실행할 수 있습니다. 이에 대한 지침은 [HDInsight Spark 클러스터를 사용하여 Data Lake Store의 데이터 분석](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)을 참조하세요.


### <a name="use-data-lake-store-in-a-storm-topology"></a>Storm 토폴로지에서 Data Lake 저장소 사용
데이터 레이크 저장소를 사용하여 Storm 토폴로지에서 데이터를 쓸 수 있습니다. 이 시나리오를 수행하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm에 Azure Data Lake Store 사용](../hdinsight/hdinsight-storm-write-data-lake-store.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [PowerShell: HDInsight 클러스터를 만들어 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx



<!--HONumber=Nov16_HO4-->


