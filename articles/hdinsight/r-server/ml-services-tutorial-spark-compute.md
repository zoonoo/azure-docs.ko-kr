---
title: '자습서: Azure HDInsight에서 Spark 컴퓨팅 컨텍스트에 R 사용'
description: 자습서 - Azure HDInsight Machine Learning Service 클러스터에서 R 및 Spark 시작
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "71121930"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>자습서: Azure HDInsight에서 Spark 컴퓨팅 컨텍스트에 R 사용

이 자습서에서는 Azure HDInsight의 Machine Learning Service 클러스터에서 실행되는 Apache Spark에서 R 함수를 사용하는 방법을 단계별로 소개합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 로컬 스토리지에 샘플 데이터 다운로드
> * 기본 스토리지에 데이터 복사
> * 데이터 세트 설정
> * 데이터 원본 만들기
> * Spark에 대한 컴퓨팅 컨텍스트 만들기
> * 선형 모델 맞춤
> * 복합 XDF 파일 사용
> * XDF를 CSV로 변환

## <a name="prerequisites"></a>사전 요구 사항

* Azure HDInsight Machine Learning Service 클러스터. [Azure Portal을 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)로 이동한 후 **클러스터 유형**으로 **ML Services**를 선택합니다.

## <a name="connect-to-rstudio-server"></a>RStudio Server에 연결

RStudio Server는 클러스터의 에지 노드에서 실행됩니다. 다음 사이트로 이동합니다(여기서 URL의 *CLUSTERNAME*은 사용자가 만든 HDInsight Machine Learning Servoce 클러스터의 이름임).

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

처음 로그인할 때는 두 번 인증합니다. 첫 번째 인증 프롬프트에서는 클러스터 관리자 사용자 이름 및 암호(기본값 *admin*)를 입력합니다. 두 번째 인증 프롬프트에서는 SSH 사용자 이름 및 암호(기본값 *sshuser*)를 입력합니다. 그 다음부터는 로그인할 때 SSH 자격 증명만 필요합니다.

## <a name="download-the-sample-data-to-local-storage"></a>로컬 스토리지에 샘플 데이터 다운로드

*Airline 2012 On-Time Data Set*는 미국 내 모든 민간 항공사의 2012년 항공편 도착 및 출발 세부 정보가 들어 있는 12개의 쉼표로 구분된 파일로 구성되어 있습니다. 이 데이터 세트는 600만 가지가 넘는 관찰 데이터를 포함하므로 매우 큽니다.

1. 몇 가지 환경 변수를 초기화합니다. RStudio Server 콘솔에서 다음 코드를 입력합니다.

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. 오른쪽 창에서 **환경** 탭을 선택합니다. 변수는 **값** 아래에 표시됩니다.

    ![HDInsight R Studio 웹 콘솔](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. 로컬 디렉터리를 만들고 샘플 데이터를 다운로드합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    다운로드는 약 9분 30초 후에 완료됩니다.

## <a name="copy-the-data-to-default-storage"></a>기본 스토리지에 데이터 복사

HDFS(Hadoop 분산 파일 시스템) 위치는 `airDataDir` 변수를 사용하여 지정합니다. RStudio에서 다음 코드를 입력합니다.

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

이 단계는 약 10초 후에 완료됩니다.

## <a name="set-up-a-dataset"></a>데이터 세트 설정

1. 기본값을 사용하는 파일 시스템 개체를 만듭니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. 원본 CSV 파일의 변수 이름은 다루기 불편하기 때문에, 보다 편하게 관리할 수 있도록 *colInfo* 목록을 제공합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>데이터 원본 만들기

Spark 컴퓨팅 컨텍스트에서 다음 함수를 사용하여 데이터 원본을 만들 수 있습니다.

|함수 | Description |
|---------|-------------|
|`RxTextData` | 쉼표로 구분된 텍스트 데이터 원본입니다. |
|`RxXdfData` | XDF 데이터 파일 형식의 데이터입니다. RevoScaleR에서 XDF 파일 형식은 단일 파일이 아닌 복합 파일 세트에 데이터를 저장하도록 Hadoop에 맞게 수정됩니다. |
|`RxHiveData` | Hive 데이터 원본 개체를 생성합니다.|
|`RxParquetData` | Parquet 데이터 원본 개체를 생성합니다.|
|`RxOrcData` | Orc 데이터 원본 개체를 생성합니다.|

HDFS로 복사한 파일을 사용하여 [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) 개체를 만듭니다. RStudio에서 다음 코드를 입력합니다.

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Spark에 대한 컴퓨팅 컨텍스트 만들기

데이터를 로드하고 작업자 노드에 대한 분석을 실행할 수 있도록 스크립트에서 컴퓨팅 컨텍스트를 [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark)로 설정합니다. 이 컨텍스트에서 R 함수는 워크로드를 모든 작업자 노드에 자동 분산하며, 작업 또는 큐를 관리하기 위한 기본 요구 사항은 없습니다. Spark 컴퓨팅 컨텍스트는 `RxSpark` 또는 `rxSparkConnect()`를 통해 설정되어 Spark 컴퓨팅 컨텍스트를 만들고, `rxSparkDisconnect()`를 사용하여 로컬 컴퓨팅 컨텍스트로 돌아갑니다. RStudio에서 다음 코드를 입력합니다.

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>선형 모델 맞춤

1. [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) 함수를 사용하여 `airDS` 데이터 원본을 사용하는 선형 모델에 맞춥니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    이 단계는 2~3분 이내에 완료됩니다.

1. 결과를 확인합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    summary(delayArr)
    ```

    다음 결과가 보일 것입니다.

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    결과를 보면 모든 데이터를 처리했고, 6백만 건의 관찰 기록이 있고, 모두 지정된 디렉터리에 있는 .csv 파일을 사용합니다. 또한 `cube = TRUE`를 지정했기 때문에 각 요일의 예상 계수가 있습니다(절편은 없음).

## <a name="use-composite-xdf-files"></a>복합 XDF 파일 사용

앞서 살펴본 것처럼 Hadoop에서 R을 사용하여 CSV 파일을 직접 분석할 수 있습니다. 그러나 더 효율적인 형식으로 데이터를 저장하는 경우 분석을 더 빠르게 수행할 수 있습니다. R XDF 파일 형식은 효율적이지만, 개별 파일을 단일 HDFS 블록 내에 유지하기 위해 HDFS에 맞게 약간 수정됩니다. (HDFS 블록 크기는 설치 방법에 따라 다르지만, 일반적으로 64MB 또는 128MB입니다.) 

Hadoop에서 [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport)를 사용하여 복합 .xdf 파일 세트를 만드는 경우 `AirDS` 같은 `RxTextData` 데이터 원본을 inData로 지정하고, fileSystem이 HDFS 파일 시스템으로 설정된 `RxXdfData` 데이터 원본을 outFile 인수로 지정합니다. 그러면 후속 R 분석에서 `RxXdfData` 개체를 데이터 인수로 사용할 수 있습니다.

1. `RxXdfData` 개체를 정의합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. 블록 크기를 250000행으로 설정하고 모든 데이터를 읽는 것으로 지정합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. `rxImport`를 사용하여 데이터를 가져옵니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    이 단계는 몇 분 내에 완료됩니다.

1. 더 빠른 새 데이터 원본을 사용하여 동일한 선형 모델을 다시 예측합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    이 단계는 1분 내에 완료됩니다.

1. 결과를 확인합니다. 결과는 CSV 파일의 결과와 동일합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF를 CSV로 변환

### <a name="in-a-spark-context"></a>Spark 컨텍스트에서

이전에는 분석을 실행하는 동안 효율성 향상을 위해 CSV 파일을 XDF 파일 형식으로 변환했지만 이제 데이터를 CSV로 다시 변환하려면 [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep)을 사용하면 됩니다.

CSV 파일의 폴더를 만들려면 먼저 디렉터리 이름을 file 인수로 사용하여 `RxTextData` 개체를 만듭니다. 이 개체는 CSV 파일을 만들 폴더를 나타냅니다. 이 디렉터리는 `rxDataStep`을 실행할 때 만들어집니다. 그런 다음, `rxDataStep`의 `outFile` 인수에서 `RxTextData` 개체를 가리킵니다. 생성된 각 CSV의 이름은 디렉터리 이름 뒤에 숫자를 붙여 지정합니다.

새 CSV 파일에 예상 값과 잔차가 포함되도록, 로지스틱 회귀와 예측을 수행한 후 `airDataXdf` 복합 XDF에서 HDFS의 CSV 폴더를 작성하려 한다고 가정해 봅시다. RStudio에서 다음 코드를 입력합니다.

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

이 단계는 약 2분 30초 후에 완료됩니다.

`rxDataStep`은 입력 복합 XDF 파일의 모든 XDFD 파일에 대한 하나의 CSV를 작성했습니다. 이는 컴퓨팅 컨텍스트가 `RxSpark`로 설정된 경우 복합 XDF 파일에서 HDFS로 CSV 파일을 작성하기 위한 기본 동작입니다.

### <a name="in-a-local-context"></a>로컬 컨텍스트에서

또는 분석이 완료되었을 때 컴퓨팅 컨텍스트를 다시 `local`로 전환하면 HDFS에 CSV 파일을 작성할 때 약간 더 정교하게 제어할 수 있는 두 인수 `createFileSet` 및 `rowsPerOutFile`을 `RxTextData` 내에서 활용할 수 있습니다. `createFileSet`를 `TRUE`로 설정하면 사용자가 지정하는 디렉터리에 CSV 파일의 폴더가 작성됩니다. `createFileSet`를 `FALSE`로 설정하면 단일 CSV 파일이 작성됩니다. 두 번째 인수 `rowsPerOutFile`은 `createFileSet`가 `TRUE`일 때 각 CSV 파일에 쓸 수 있는 행 수를 나타내는 정수로 설정할 수 있습니다.

RStudio에서 다음 코드를 입력합니다.

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

이 단계는 약 10분 후에 완료됩니다.

`RxSpark` 컴퓨팅 컨텍스트를 사용하는 경우 `createFileSet`는 기본적으로 `TRUE`로 설정되고 `rowsPerOutFile`은 아무 영향도 주지 않습니다. 따라서 단일 CSV를 만들거나 파일당 행 수를 사용자 지정하려면 `local` 컴퓨팅 컨텍스트에서 `rxDataStep`을 수행합니다(데이터는 여전히 HDFS일 수 있음).

## <a name="final-steps"></a>최종 단계

1. 데이터를 정리합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. 원격 Spark 애플리케이션을 중지합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. R 세션을 종료합니다. RStudio에서 다음 코드를 입력합니다.

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

클러스터를 삭제하려면 [브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제](../hdinsight-delete-cluster.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 HDInsight Machine Learning Service 클러스터에서 실행되는 Apache Spark에서 R 함수를 사용하는 방법을 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight Machine Learning Service 클러스터에 대한 컴퓨팅 컨텍스트 옵션](r-server-compute-contexts.md)
* [Hadoop의 Spark용 R 함수](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
