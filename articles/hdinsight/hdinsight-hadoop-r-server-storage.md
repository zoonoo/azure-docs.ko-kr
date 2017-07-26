---
title: "HDInsight의 R Server에 대한 Azure Storage 옵션 - Azure | Microsoft Docs"
description: "HDInsight의 R Server에서 사용할 수 있는 다양한 저장소 옵션에 대해 알아봅니다."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 017a66ae1ade5e0f64fc799b7bb6aa97b67791a8
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017


---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>HDInsight의 R Server에 대한 Azure Storage 솔루션

HDInsight의 Microsoft R Server에는 데이터, 코드, 또는 분석 결과가 포함된 개체를 유지할 수 있는 다양한 저장 솔루션이 있습니다. 여기에는 다음 옵션이 포함됩니다.

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure 데이터 레이크 저장소](https://azure.microsoft.com/services/data-lake-store/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

필요한 경우 HDI 클러스터가 있는 여러 Azure Storage 계정 또는 컨테이너에 액세스할 수도 있습니다. Azure File Storage는 Azure Storage 파일 공유를 Linux 파일 시스템 등에 마운팅할 수 있도록 에지 노드에서 사용할 수 있는 편리한 데이터 저장 옵션입니다. 하지만, Azure File 공유는 마운팅이 가능하고 Windows 또는 Linux 등 지원되는 OS가 있는 모든 시스템에서 사용할 수 있습니다. 

HDInsight에서 Hadoop 클러스터를 만들 때 **Azure Storage** 계정 또는 **Data Lake Store**를 지정합니다. 해당 계정의 특정 저장소 컨테이너에는 사용자가 만든 클러스터의 파일 시스템(예: Hadoop 분산 파일 시스템)이 있습니다. 자세한 내용 및 지침은 다음을 참조하세요.

- [HDInsight에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)
- [Azure HDInsight 클러스터에 Data Lake Store 사용](hdinsight-hadoop-use-data-lake-store.md) 

Azure Storage 솔루션에 대한 자세한 내용은 [Microsoft Azure Storage 소개](../storage/storage-introduction.md)를 참조하세요. 

시나리오에 사용할 가장 적합한 저장소 옵션을 선택하는 방법에 대한 지침은 [Azure Blob, Azure File 또는 Azure Data Disk 사용 시기 결정](../storage/storage-decide-blobs-files-disks.md)을 참조하세요. 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>R Server에 Azure Blob Storage 계정 사용

필요한 경우 HDI 클러스터가 있는 여러 Azure 저장소 계정 또는 컨테이너에 액세스할 수 있습니다. 이렇게 하려면 클러스터를 만들 때 UI에서 추가 저장소 계정을 지정하고 다음 단계에 따라 R Server에서 사용해야 합니다.

> [!WARNING]
> 성능을 위해 HDInsight 클러스터는 사용자가 지정한 기본 저장소 계정과 동일한 데이터 센터에 만들어집니다. HDInsight 클러스터와 다른 위치에서는 저장소 계정을 사용할 수 없습니다.

1. 저장소 계정 이름이 **storage1**이고 기본 컨테이너가 **container1**인 HDInsight 클러스터를 만듭니다.
2. **storage2**라는 추가 저장소 계정을 지정합니다.  
3. /share 디렉터리로 mycsv.csv 파일을 복사하고 해당 파일에 대한 분석을 수행합니다.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. R 코드에서 이름 노드를 **default** 로 설정하고 처리할 디렉터리 및 파일을 설정합니다.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

모든 디렉터리와 파일 참조는 저장소 계정 wasbs://container1@storage1.blob.core.windows.net을 지정합니다. 이는 HDInsight 클러스터와 연결된 **기본 저장소 계정**입니다.

**storage2**에 있는 **container2**의 /private 디렉터리에 있는 mySpecial.csv 파일을 처리한다고 가정합니다.

R 코드에서 이름 노드 참조를 **storage2** 저장소 계정으로 지정합니다.


    myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

모든 디렉터리와 파일 참조는 이제 저장소 계정 wasbs://container2@storage2.blob.core.windows.net을 지정합니다. 지정한 **이름 노드**입니다.

다음과 같이 **storage2**에서 /user/RevoShare/<SSH username> 디렉터리를 구성해야 합니다.


    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>R Server에서 Azure Data Lake Store 사용

HDInsight 계정과 함께 Data Lake 저장소를 사용하려면 사용하려는 Azure Data Lake 저장소 각각에 클러스터 액세스 권한을 부여해야 합니다. Azure Portal에서 기본 저장소 또는 추가 저장소로 Azure Data Lake Store 계정을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure Portal을 사용하여 Data Lake Store를 포함한 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.

그런 다음, 보조 Azure Storage 계정을 사용하는 것처럼 R 스크립트에서 저장소를 사용합니다(이전 절차에서 설명한 대로).

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Azure Data Lake 저장소에 클러스터 액세스 추가
HDInsight 클러스터와 연결된 Azure AD(Azure Active Directory) 서비스 주체를 사용하여 Azure Data Lake 저장소에 액세스합니다.

Azure AD 서비스 사용자 추가

1. HDInsight 클러스터를 만들 때 **데이터 원본** 탭에서 **클러스터 AAD ID**를 선택합니다.

2. **클러스터 AAD ID** 대화 상자의**AD 서비스 주체 선택**에서 **새로 만들기**를 선택합니다.

서비스 사용자에 이름을 지정하고 암호를 만든 후에 **ADLS 액세스 관리**를 클릭하여 Data Lake Store와 서비스 사용자를 연결합니다.

클러스터를 만든 후 하나 이상의 Data Lake Store에 클러스터 액세스를 추가할 수도 있습니다. Data Lake Store에 대한 Azure Portal 항목을 열고 **데이터 탐색기 > 액세스 > 추가**로 이동합니다. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>R Server에서 Data Lake Store에 액세스하는 방법

Data Lake 저장소에 액세스 권한을 부여하면 보조 Azure 저장소 계정과 동일한 방식으로 HDInsight의 R 서버에서 저장소를 사용할 수 있습니다. 유일한 차이점은 다음과 같이 **wasb://** 접두사가 **adl://**로 변경된다는 것입니다.


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


다음 명령은 RevoShare 디렉터리로 Data Lake Store 계정을 구성하고 이전 예제의 샘플 .csv 파일을 추가하는 데 사용합니다.


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>R Server에서 Azure File Storage 사용

에지 노드에 사용할 수 있는 [Azure 파일]이라고 하는 편리한 데이터 저장소 옵션도 있습니다(https://azure.microsoft.com/services/storage/files/). Azure 파일을 사용하면 Linux 파일 시스템에서 Azure 저장소 파일 공유를 탑재할 수 있습니다. 이 옵션은 특히 나중에 HDFS 대신 에지 노드의 원시 파일 시스템을 사용하는 것이 유용할 때 필요할 수 있는 데이터 파일, R 스크립트 및 결과 개체를 저장하는 데 편리합니다. 

Azure 파일의 장점은 파일 공유가 탑재되고 Windows 또는 Linux 등 지원되는 OS가 있는 모든 시스템에서 사용할 수 있다는 점입니다. 예를 들어, 사용자 또는 팀의 다른 사용자가 보유한 HDInsight 클러스터, Azure VM 또는 온-프레미스 시스템에서도 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Linux에서 Azure File Storage 사용 방법](../storage/storage-how-to-use-files-linux.md)
- [Windows에서 Azure File Storage 사용 방법](../storage/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>다음 단계

Azure Storage 옵션을 이해했으므로, 이제 다음 링크를 사용하여 HDInsight의 R Server로 데이터 과학 작업을 수행하는 방법을 검색합니다.

* [HDInsight의 R 서버 개요](hdinsight-hadoop-r-server-overview.md)
* [Hadoop에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)
* [HDInsight에 RStudio Server 추가(클러스터를 만드는 중에 추가되지 않은 경우)](hdinsight-hadoop-r-server-install-r-studio.md)
* [HDInsight의 R 서버에 대한 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)


