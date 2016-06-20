<properties
   pageTitle="HDInsight의 R 서버에 대한 Azure 저장소 옵션(미리 보기) | Azure"
   description="HDInsight의 R 서버 사용자가 사용할 수 있는 다양한 저장소 옵션을 알아봅니다(미리 보기)."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="jeffstok"
/>

# HDInsight의 R 서버에 대한 Azure 저장소 옵션(미리 보기)

HDInsight의 R 서버(미리 보기)는 데이터, 코드, 분석의 결과 개체 등을 유지하기 위해 Azure Blob과 [Azure Data Lake 저장소](https://azure.microsoft.com/services/data-lake-store/) 모두에 액세스할 수 있습니다.

HDInsight의 Hadoop 클러스터를 만들 때 Azure 저장소 계정을 지정합니다. 해당 계정은 특정 Blob 저장소 컨테이너는 사용자가 만든 클러스터의 파일 시스템, 즉 HDFS(Hadoop Distributed File System)를 유지하도록 지정됩니다. 성능을 위해 HDInsight 클러스터는 사용자가 지정한 기본 저장소 계정과 동일한 데이터 센터에 만들어집니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md "HDInsight에서 Azure Blob 저장소 사용")을 참조하세요.


## 여러 Azure Blob 저장소 계정 사용

필요한 경우 HDI 클러스터에서 여러 Azure 저장소 계정 또는 컨테이너에 액세스할 수 있습니다. 이렇게 하려면 클러스터를 만들 때 UI에서 추가 저장소 계정을 지정하고 다음 단계에 따라 R에서 사용해야 합니다.

1.	기본 컨테이너 "container1"과 함께 저장소 계정 이름 "storage1"을 사용하여 HDInsight 클러스터를 만든다고 가정해 보겠습니다. 또한 추가 저장소 계정 "storage2"를 지정합니다.  
2.	이제 “mycsv.csv” 파일을 “/share” 디렉터리에 복사하고 이 파일에서 분석을 수행하려고 합니다.  

````
hadoop fs –mkdir /share
hadoop fs –copyFromLocal myscsv.scv /share  
````

3.	R 코드에서 이름 노드를 "default"를 설정하고 처리할 디렉터리 및 파일을 설정합니다.  

````
myNameNode <- "default"
myPort <- 0
````

  캐시의 위치

    bigDataDirRoot <- "/share"  

  Spark 계산 컨텍스트 정의

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  계산 컨텍스트 설정

    rxSetComputeContext(mySparkCluster)

  HDFS 파일 시스템 정의

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  분석할 HDFS의 입력 파일 지정

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
모든 디렉터리 및 파일 참조는 저장소 계정 wasb://container1@storage1.blob.core.windows.net을 가리킵니다. 이 계정은 HDInsight 클러스터와 연결된 **기본 저장소 계정**이기 때문입니다.

이제 저장소 계정 이름 “storage2”에서 “container2” 컨테이너의 “/private” 디렉터리에 있는 “mySpecial.csv” 파일을 처리하려고 합니다.

R 코드에서 이름 노드 참조를 "storage2" 저장소 계정으로 변경합니다.

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  캐시의 위치

    bigDataDirRoot <- "/private"

  Spark 계산 컨텍스트 정의

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  계산 컨텍스트 설정

    rxSetComputeContext(mySparkCluster)

  HDFS 파일 시스템 정의

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  분석할 HDFS의 입력 파일 지정

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
이제 모든 디렉터리 및 파일 참조가 저장소 계정 wasb://container2@storage2.blob.core.windows.net을 가리킵니다. 이 계정은 지정한 **이름 노드**이기 때문입니다.

저장소 계정 "storage2"에서 /User/RevoShare/<SSH username> 디렉터리를 구성해야 합니다.

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## Azure 데이터 레이크 저장소 사용

HDInsight 계정으로 Azure 데이터 레이크 저장소를 사용하려면 사용하려는 각 Azure 데이터 레이크 저장소에 대해 클러스터 액세스 권한을 부여한 다음 위에 설명된 보조 Azure 저장소 계정을 사용하는 경우와 비슷한 방식으로 R 스크립트에서 해당 저장소를 참조해야 합니다.

## Azure 데이터 레이크 저장소에 대한 클러스터 액세스 추가

Azure 데이터 레이크 저장소에 대한 액세스는 HDInsight 클러스터와 연결된 AAD(Azure Active Directory) 서비스 사용자를 사용하여 설정됩니다. HDInsight 클러스터를 만들 때 서비스 사용자를 추가하려면 데이터 원본 탭에서 "클러스터 AAD ID" 옵션을 클릭하고 "새로 만들기" 서비스 사용자를 클릭합니다. 이름 및 암호를 제공하면 서비스 사용자를 Azure 데이터 레이크 저장소에 연결할 수 있는 새 탭이 열립니다.

Azure 포털에서 Azure 데이터 레이크 스토어를 열고 "데이터 탐색기-> 액세스"로 이동하여 나중에 Azure 데이터 레이크 저장소에 액세스를 추가할 수도 있습니다. 서비스 사용자를 만들고 "rkadl11" Azure 데이터 레이크 저장소와 연결하는 방법을 보여 주는 샘플 대화 상자는 다음과 같습니다.

![ADL 저장소 서비스 사용자 1 만들기](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![ADL 저장소 서비스 사용자 2 만들기](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## R 서버에서 Azure 데이터 레이크 저장소 사용
클러스터 서비스 사용자를 사용하여 Azure 데이터 레이크 저장소에 대한 액세스 권한을 받으면 보조 Azure 저장소 계정의 경우와 같은 방식으로 HDInsight의 R 서버에서 해당 저장소를 사용할 수 있습니다. 유일한 차이점은 wasb:// 접두사가 adl://로 변경된다는 것입니다. 예를 들면 다음과 같습니다.

````
# point to the ADL store (e.g. ADLtest) 
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account) 
bigDataDirRoot <- "/share"  

# define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE)

# set compute context
rxSetComputeContext(mySparkCluster)

# define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# create Factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# define the data source 
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

> [AZURE.NOTE] 다음은 RevoShare 디렉터리로 storage account 저장소 계정을 구성하고 위 예제의 샘플 CSV 파일을 추가하는 데 사용되는 명령입니다.

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## 에지 노드에서 Azure 파일 사용 

[Azure 파일](../storage/storage-how-to-use-files-linux.md "Azure 파일")이라는 에지 노드에서 사용할 편리한 데이터 저장소 옵션이 있습니다. 이 옵션을 통해 Azure 저장소 파일 공유를 Linux 파일 시스템에 마운트할 수 있습니다. 이는 나중에 HDFS 대신 에지 노드의 기본 파일 시스템을 사용하는 것이 유용할 때 필요할 수 있는 데이터 파일, R 스크립트 및 결과 개체를 저장하는 데 편리합니다. Azure 파일을 사용할 경우의 이점은 파일 공유를 마운트하여 지원되는 OS(Win, Linux)가 있는 모든 시스템(예: 사용자 또는 팀 구성원의 다른 HDInsight 클러스터, Azure VM, 온-프레미스 시스템 등)에서 사용할 수 있다는 것입니다.


## 다음 단계

이제 R 서버를 포함하는 새 HDInsight 클러스터를 만드는 방법 및 SSH 세션에서 R 콘솔 사용 기본 사항을 이해했으므로 다음을 사용하여 HDInsight에서 R 서버로 작업하는 다른 방법을 확인할 수 있습니다.

- [Hadoop에서 R 서버 개요](hdinsight-hadoop-r-server-overview.md)
- [Hadoop에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)
- [HDInsight Premium에 RStudio 서버 추가](hdinsight-hadoop-r-server-install-r-studio.md)
- [HDInsight Premium의 R 서버에 대한 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0608_2016-->