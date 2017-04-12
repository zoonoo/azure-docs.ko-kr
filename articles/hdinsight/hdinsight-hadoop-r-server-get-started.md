---
title: "HDInsight에서 R Server 시작(미리 보기) | Microsoft 문서"
description: "R Server를 포함하는 HDInsight 클러스터에서 Apache Spark를 만든 다음 클러스터에 R 스크립트를 제출하는 방법을 알아봅니다."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f816a6972c0e80c6a7063705917ecf18debc75f6
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>HDInsight에서 R 서버 사용 시작
HDInsight에는 HDInsight 클러스터에 통합되는 R Server 옵션이 포함되어 있습니다. 이를 통해 R 스크립트에서 Spark 및 MapReduce를 사용하여 분산된 계산을 실행할 수 있습니다. 이 문서에서는 HDInsight 클러스터에서 R Server를 만든 다음 R 스크립트를 실행하여 분산된 R 계산에 Spark를 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**: 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. 자세한 내용은 [Azure 무료 평가판 받기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)로 이동합니다.
* **SSH(보안 셸) 클라이언트**: SSH 클라이언트는 HDInsight 클러스터에 원격으로 연결하여 클러스터에서 직접 명령을 실행하는 데 사용됩니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

  * **SSH 키(선택 사항)**: 암호 또는 공개 키를 사용하여 클러스터에 연결할 때 사용되는 SSH 계정을 보호할 수 있습니다. 암호를 사용하면 작업이 보다 간편하며, 공개/개인 키 쌍을 만들지 않고도 시작할 수 있습니다. 하지만 키를 사용하는 것이 더 안전합니다.

      이 문서의 단계에서는 암호를 사용하는 것으로 가정합니다.

### <a name="access-control-requirements"></a>액세스 제어 요구 사항
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>클러스터 만들기
> [!NOTE]
> 이 문서의 단계에서는 기본 구성 정보를 사용하여 HDInsight 클러스터에서 R Server를 만드는 방법을 안내합니다. 기타 클러스터 구성 설정(예: 저장소 계정 추가, Azure 가상 네트워크 사용 또는 Hive용 Metastore 만들기)에 대한 자세한 내용은 [Linux 기반 HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. Azure 리소스 관리 템플릿을 사용하여 R 서버를 만들려면 [R 서버 HDInsight 클러스터 배포](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/)를 참조하세요.
>
>

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **새로 만들기**, **인텔리전스 + 분석**, **HDInsight**를 차례로 선택합니다.

    ![새 클러스터를 만드는 과정의 이미지](./media/hdinsight-getting-started-with-r/newcluster.png)

3. **빠른 생성** 환경에서 **클러스터 이름** 필드에 클러스터의 이름을 입력합니다. Azure 구독이 여러 개 있는 경우 **구독** 항목을 사용하여 사용할 구독을 선택합니다.

    ![클러스터 이름 및 구독 선택](./media/hdinsight-getting-started-with-r/clustername.png)

4. **클러스터 유형**을 선택하여 **클러스터 구성** 블레이드를 엽니다. **클러스터 구성** 블레이드에서 다음 옵션을 선택합니다.

   * **클러스터 유형**: R Server
   * **버전**: 클러스터에 설치할 R Server의 버전을 선택합니다. 최신 기능의 최신 버전을 선택합니다. 호환성을 위해 필요한 경우 다른 버전을 사용할 수 있습니다. 사용 가능한 각 버전의 릴리스 정보는 [여기](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes)서 제공됩니다.
   * **R Server용 R Studio Community Edition**: 이 브라우저 기반 IDE는 기본적으로 에지 노드에 설치됩니다.  설치하지 않으려면 확인란의 선택을 취소합니다. 설치를 선택하면 만들어진 클러스터의 포털 응용 프로그램 블레이드에서 RStudio Server 로그인에 액세스하기 위한 URL을 찾습니다.

   다른 옵션은 기본값으로 남겨 두고 **선택** 단추를 사용하여 클러스터 유형을 저장합니다.

   ![클러스터 유형 블레이드 스크린샷](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**를 입력합니다.

   **SSH 사용자 이름**을 지정합니다.  SSH는 **SSH(보안 셸)** 클라이언트를 사용하여 클러스터에 원격으로 연결하는 데 사용됩니다. 클러스터(클러스터에 대한 구성 탭)를 만든 후에 이 대화 상자에서 SSH 사용자를 지정할 수 있습니다. R Server는 "remoteuser"라는 **SSH 사용자 이름**를 사용하도록 구성됩니다.  **다른 사용자 이름을 사용하면 클러스터를 만든 후에 추가 단계를 수행해야 합니다.**

   공개 키를 사용하려는 경우가 아니면 상자에서 **클러스터 로그인과 동일한 암호 사용**을 선택하여 **암호**를 인증 유형으로 사용합니다.  RTVS, RStudio 또는 다른 데스크톱 IDE와 같은 원격 클라이언트를 통해 클러스터에서 R Server에 액세스하려는 경우 공개/개인 키 쌍이 필요합니다. RStudio Server Community Edition을 설치하려면 SSH 암호를 선택해야 합니다.     

   공개/개인 키 쌍을 만들고 사용하려면 **클러스터 로그인과 동일한 암호 사용**의 확인을 취소한 다음 **공개 키**를 선택하고 다음과 같이 진행합니다.  이러한 지침에서는 ssh-keygen을 포함한 Cygwin 또는 이와 동등한 프로그램이 설치되어 있다고 가정합니다.

   * 랩톱 컴퓨터의 명령 프롬프트에서 공개/개인 키 쌍을 생성합니다.

   `ssh-keygen -t rsa -b 2048`

   * 프롬프트를 따라서 키 파일 이름을 지정하고 보안 강화를 위해 암호를 입력합니다. 화면은 다음과 유사하게 표시됩니다.

   ![Windows의 SSH 명령줄](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * 이렇게 하면 <private-key-filename>.pub이라는 이름으로 개인 키 파일 및 공개 키 파일이 만들어집니다(예: furiosa 및 furiosa.pub).

   ![SSH 디렉터리](./media/hdinsight-getting-started-with-r/dir.png)

   * 그러면 HDI 클러스터 자격 증명을 할당할 때 공개 키 파일(*.pub)을 지정하고 리소스 그룹 및 지역을 마지막으로 확인한 후 **다음**을 선택합니다.

   ![자격 증명 블레이드](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * 랩톱 컴퓨터의 개인 키 파일에 대한 사용 권한 변경

   `chmod 600 <private-key-filename>`

   * 원격 로그인을 위해 SSH와 함께 개인 키 파일을 사용합니다.

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   또는 클라이언트의 R Server에 대한 Hadoop Spark 계산 컨텍스트 정의의 일부로 사용합니다(온라인 [Apache Spark에서 SacaleR 시작 문서](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)의 [Spark에 대한 계산 컨텍스트 만들기](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) 섹션에 있는 ‘Microsoft R Server를 Hadoop 클라이언트로 사용’참조).

6. 빠른 생성은 사용자를 **저장소** 블레이드로 전환하여 클러스터에서 사용하는 HDFS 파일 시스템의 기본 위치에 사용될 저장소 계정 설정을 선택합니다. 새 또는 기존 Azure Storage 계정이나 기존 Data Lake Storage 계정을 선택합니다.

   1. Azure Storage 계정을 선택하면 **저장소 계정 선택**을 선택한 다음 계정을 선택하여 기존 저장소 계정을 선택할 수 있습니다. 또는 **저장소 계정 선택** 섹션에서 **새로 만들기** 링크를 사용하여 새 계정을 만듭니다.

      > [!NOTE]
      > **새로 만들기**를 선택한 경우 새 저장소 계정의 이름을 입력해야 합니다. 이름이 허용되는 경우 녹색 확인 표시가 나타납니다.

      **기본 컨테이너**는 기본적으로 클러스터 이름으로 설정됩니다. 이 값을 그대로 둡니다.

      새 저장소 계정 옵션을 선택한 경우 **위치**를 선택하라는 메시지가 표시되며, 여기서 저장소 계정을 만들 지역을 선택합니다.  

         ![데이터 원본 블레이드](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > 기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 하위 지역에 있어야 합니다.

   2. 기존 Data Lake Store를 사용하도록 선택하는 경우 사용할 ADLS 저장소 계정을 선택하고 클러스터에 클러스터 ADD ID를 추가하여 저장소에 액세스할 수 있도록 합니다. 이 프로세스에 대한 자세한 내용은 [Azure Portal을 사용하여 Data Lake Store로 HDInsight 클러스터 만들기](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)를 검토하세요.

   **선택** 단추를 사용하여 데이터 원본 구성을 저장합니다.


7. 그러면 **요약** 블레이드가 표시되어 모든 설정의 유효성을 검사합니다. 여기서는 **클러스터 크기**를 변경하여 클러스터에 있는 서버 수를 수정하고 실행하려는 **스크립트 작업**을 지정할 수도 있습니다. 더 큰 클러스터가 필요한 경우가 아니면 작업자 노드 수를 기본값(`4`)으로 그대로 둡니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

   ![클러스터 요약](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > 필요한 경우 나중에 포털(클러스터-> 설정 -> 클러스터 크기 조정)을 통해 다시 클러스터의 크기를 조정하여 작업자 노드 수를 늘리거나 줄일 수 있습니다.  클러스터를 사용하지 않을 때 유휴 상태로 유지하거나 대규모 태스크의 요구에 맞게 용량을 추가하는 데 유용할 수 있습니다.
   >
   >

    클러스터, 데이터 노드 및 에지 노드의 크기를 조정할 때 염두할 몇 가지 요인은 다음과 같습니다.  

   * Spark에서 분산된 R 서버 분석의 성능은 데이터가 클 경우 작업자 노드 수에 비례합니다.  

   * R 서버 분석의 성능은 분석 중인 데이터의 크기에 비례합니다. 예:  

     * 작은 데이터부터 보통 크기의 데이터의 경우 성능은 에지 노드의 로컬 계산 컨텍스트에서 분석할 때 가장 뛰어납니다.  로컬 및 Spark 계산 컨텍스트가 가장 잘 작동하는 시나리오에 대한 자세한 내용은 ‘HDInsight에서 R Server의 계산 컨텍스트 옵션’을 참조하세요.<br>
     * 에지 노드에 로그인하고 거기서 R 스크립트를 실행하는 경우 메모리와 에지 노드의 코어 수를 적절하게 조정하도록 에지 노드에서 ScaleR rx 함수를 제외한 모든 항목을 <strong>로컬로</strong> 실행합니다. HDI의 R Server를 랩톱 컴퓨터의 원격 계산 컨텍스트로 사용할 경우에도 동일하게 적용됩니다.

     ![노드 가격 책정 계층 블레이드](./media/hdinsight-getting-started-with-r/pricingtier.png)

     **선택** 단추를 사용하여 노드 가격 책정 구성을 저장합니다.

   **템플릿 및 매개 변수 다운로드**에 대한 링크 수도 있다는 점에 주의합니다. 이 링크를 클릭하면 선택한 구성으로 클러스터의 생성을 자동화하는 데 사용할 수 있는 스크립트가 표시됩니다. 이러한 스크립트는 만들어진 클러스터의 Azure Portal 항목에서도 사용할 수 있습니다.

   > [!NOTE]
   > 클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 20분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 생성 프로세스를 확인하세요.
   >
   >

## <a name="connect-to-rstudio-server"></a>RStudio Server에 연결

설치 시 RStudio Server Community Edition을 포함하도록 선택한 경우 별도의 두 가지 방법으로 RStudio 로그인에 액세스할 수 있습니다.

1. 다음 URL로 이동하거나(여기서 **CLUSTERNAME**은 만든 클러스터의 이름임)

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Azure Portal에서 클러스터의 항목을 열고 R Server 대시보드 빠른 링크를 선택한 다음 R Studio 대시보드를 선택합니다.

     ![R Studio 대시보드에 액세스](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![R Studio 대시보드에 액세스](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > 방법과 상관없이 처음 로그인 할 때는 두 번 인증해야 합니다.  첫 번째 인증에서 클러스터 관리자 사용자 ID와 암호를 제공하고, 두 번째 프롬프트에서 SSH 사용자 ID와 암호를 제공합니다. 후속 로그인에서는 SSH 사용자 ID와 암호만 필요합니다.

## <a name="connect-to-the-r-server-edge-node"></a>R 서버 가장자리 노드에 연결
SSH를 사용하여 HDInsight 클러스터의 R 서버 가장자리 노드에 연결합니다.

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> 클러스터를 선택한 다음 **모든 설정**, **앱** 및 **RServer**를 차례로 선택하여 Azure Portal에서 `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` 주소를 찾을 수도 있습니다. 그러면 가장자리 노드에 대한 SSH 끝점 정보가 표시됩니다.
>
> ![가장자리 노드에 대한 SSH 끝점의 이미지](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

SSH 사용자 계정을 보호하도록 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우, `-i` 매개 변수를 사용하고 일치하는 개인 키를 지정합니다. 예: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

연결되면 다음과 유사한 프롬프트가 나타납니다.

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>R 콘솔 사용

1. SSH 세션에서 다음 명령을 사용하여 R 콘솔을 시작합니다.  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. `>` 프롬프트에서 R 코드를 입력할 수 있습니다. R 서버에는 Hadoop과 쉽게 상호 작용하고 분산된 계산을 실행할 수 있는 패키지가 포함되어 있습니다. 예를 들어, 다음 명령을 사용하여 HDInsight 클러스터에 대한 기본 파일 시스템의 루트를 볼 수입니다.

`rxHadoopListFiles("/")`

WASB 스타일 주소 지정을 사용할 수도 있습니다.

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Microsoft R Server 또는 Microsoft R 클라이언트의 원격 인스턴스에서 HDI의 R Server 사용
클러스터에 액세스하기 위한 공개/개인 키 쌍 사용과 관련하여 위의 섹션에서 데스크톱 또는 랩톱 컴퓨터 상에 실행되는 Microsoft R Server 또는 Microsoft R 클라이언트의 원격 인스턴스에서 HDI Hadoop Spark 계산 컨텍스트에 대한 액세스를 설정할 수 있습니다(온라인 [RevoScaleR Hadoop Spark 시작 가이드](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)의 [Spark용 계산 컨텍스트 만들기](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) 섹션에서 Hadoop 클라이언트인 Microsoft R Server 만들기를 참조).  이렇게 하려면 랩톱 컴퓨터에서 RxSpark 계산 컨텍스트를 정의할 때 hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches 및 sshProfileScript와 같은 옵션을 지정해야 합니다. 예:

```
    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )
```


## <a name="use-a-compute-context"></a>계산 컨텍스트 사용
계산 컨텍스트를 사용하여 계산을 가장자리 노드에서 로컬로 수행할지 여부 또는 HDInsight 클러스터의 노드 간에 분산할지 여부를 제어할 수 있습니다.

1. RStudio Server 또는 R 콘솔(SSH 세션)에서 다음을 사용하여 HDInsight의 기본 저장소에 예제 데이터를 로드합니다.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 그런 다음 데이터로 작업할 수 있도록 몇 가지 데이터 정보를 만들고 두 개의 데이터 원본을 정의해 보겠습니다.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. 로컬 계산 컨텍스트를 사용하여 데이터에 대해 로지스틱 회귀를 실행해 보겠습니다.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    다음과 유사한 줄로 끝나는 출력이 나타나야 합니다.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. 이번에는 Spark 컨텍스트를 사용하여 동일한 로지스틱 회귀를 실행해 보겠습니다. Spark 컨텍스트는 HDInsight 클러스터의 모든 작업자 노드에서 처리를 분산시킵니다.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > MapReduce를 사용하여 클러스터 노드 간에 계산을 분산시킬 수도 있습니다. 계산 컨텍스트에 대한 자세한 내용은 [HDInsight에서 R Server의 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)을 참조하세요.


## <a name="distribute-r-code-to-multiple-nodes"></a>여러 노드에 R 코드 분산
R Server를 사용하면 손쉽게 기존 R 코드를 가져와 `rxExec`를 통해 클러스터의 여러 노드에서 실행할 수 있습니다. 이는 매개 변수 스윕 또는 시뮬레이션을 수행할 때 유용합니다. 다음은 `rxExec`를 사용하는 방법에 대한 예입니다.

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

여전히 Spark 또는 MapReduce 컨텍스트를 사용하는 경우 코드(`(Sys.info()["nodename"])`)가 실행된 작업자 노드의 nodename 값이 반환됩니다. 예를 들어, 4노드 클러스터의 경우 다음과 유사한 출력이 나타날 수 있습니다.


    ```
    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Hive 및 Parquet의 데이터 액세스
R Server 9.0 이상에서 제공되는 새로운 기능을 사용하면 Spark 계산 컨텍스트의 ScaleR 함수에서 Hive 및 Parquet의 데이터에 직접 액세스하여 사용할 수 있습니다. 이러한 기능은 RxHiveData 및 RxParquetData라는 새로운 ScaleR 데이터 소스 함수를 통해 사용할 수 있습니다. 이 함수는 Spark SQL을 사용하여 Spark DataFrame에 데이터를 직접 로드하여 ScaleR을 통해 분석합니다.  

다음은 새 기능을 사용하는 일부 샘플 코드를 제공합니다.



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

이러한 새 기능의 사용에 대한 자세한 내용은 ?RxHivedata 및 ?RxParquetData 명령을 사용하여 R Server의 온라인 도움말을 참조합니다.  


## <a name="install-r-packages"></a>R 패키지 설치
SSH를 통해 에지 노드에 연결한 경우 에지 노드에 추가 R 패키지를 설치하려면 R 콘솔 내에서 직접 `install.packages()`를 사용할 수 있습니다. 그러나 클러스터의 작업자 노드에 R 패키지를 설치해야 하는 경우에는 스크립트 작업을 사용해야 합니다.

스크립트 작업은 HDInsight 클러스터에 대한 구성을 변경하거나 추가 소프트웨어를 설치하는 데 사용되는 Bash 스크립트입니다. 여기서는 추가 R 패키지를 설치하는 데 사용됩니다. 스크립트 작업을 사용하여 추가 패키지를 설치하려면 다음 단계를 사용하세요.

> [!IMPORTANT]
> 추가 R 패키지를 설치하는 데 스크립트 작업을 사용하려는 경우 클러스터가 만들어진 후에만 사용할 수 있습니다. 클러스터를 만드는 동안에 사용해서는 안 됩니다. 스크립트는 완전히 설치되고 구성된 R 서버에 의존하기 때문입니다.
>
>

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터의 R Server를 선택합니다.

2. **설정** 블레이드에서 **스크립트 동작**을 선택한 후 **새로운 항목 제출**을 선택하여 새 스크립트 동작을 제출합니다.

   ![스크립트 작업 블레이드의 이미지](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. **스크립트 동작 제출** 블레이드에서 다음 정보를 제공합니다.

   * **이름**: 이 스크립트를 식별하는 친숙한 이름

   * **Bash 스크립트 URI**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **헤드**: **선택 취소**

   * **작업자**: **선택**

   * **Edge 노드**: **선택 취소**

   * **Zookeeper**: **선택 취소**

   * **매개 변수**: 설치할 R 패키지 위치(예: `bitops stringr arules`

   * **이 스크립트 유지...**: **선택**  

   > [!NOTE]
   > 1. 기본적으로 모든 R 패키지는 설치된 R Server의 버전과 일치하는 Microsoft MRAN 리포지토리의 스냅숏에서 설치됩니다.  그러나 최신 버전의 패키지를 설치하면 호환되지 않을 위험이 있는 경우 `useCRAN`을 패키지 목록의 첫 번째 요소(예: `useCRAN bitops, stringr, arules`)로 지정하여 수행할 수 있습니다.  
   > 2. 일부 R 패키지에는 추가 Linux 시스템 라이브러리가 필요합니다. 편의상 가장 인기 있는 상위 100개 R 패키지에서 필요한 종속성을 미리 설치했습니다. 그러나 설치한 R 패키지에 더 많은 라이브러리가 필요한 경우 여기에 사용되는 기본 스크립트를 다운로드하고 시스템 라이브러리를 설치하는 단계를 추가해야 합니다. 그런 다음 수정된 스크립트를 Azure 저장소의 공용 blob 컨테이너에 업로드하고 수정된 스크립트를 사용하여 패키지를 설치해야 합니다.
   >    스크립트 작업 개발에 대한 자세한 내용은 [스크립트 작업 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.  
   >
   >

   ![스크립트 작업 추가](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. **만들기** 를 선택하여 스크립트를 실행합니다. 스크립트가 완료되면 모든 작업자 노드에서 R 패키지를 사용할 수 있습니다.

## <a name="using-microsoft-r-server-operationalization"></a>Microsoft R Server 조작화 사용
데이터 모델링이 완료되면 모델 조작화를 수행하여 예측할 수 있습니다. Microsoft R Server 조작화를 구성하려면 다음 단계를 수행합니다.

먼저 ssh를 에지 노드로 실행합니다. 예: ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```

ssh를 사용한 후 디렉터리를 다음 디렉터리로 변경하고 아래와 같이 sudo를 통해 dotnet dll을 실행합니다.

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

One-box 구성으로 Microsoft R Server 조작화를 구성하려면 다음을 수행합니다.

* "1.  Configure R Server for Operationalization"(R Server 조작화 구성)을 선택합니다.
* "A.  One-box (web + compute nodes)"(One-box(웹 + 계산 노드))를 선택합니다.
* **admin**(관리자) 사용자의 암호를 입력합니다.

![one-box 조작화](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

선택적 단계로 아래와 같이 진단 테스트를 실행하여 진단 검사를 수행할 수 있습니다.

* "6.  Run diagnostic tests"(진단 테스트 실행)를 선택합니다.
* "A.  Test configuration"(구성 테스트)을 선택합니다.
* 위의 구성 단계에서 사용자 이름("admin")과 암호를 입력합니다.
* Confirm Overall Health(전체 상태 확인)이 pass(합격)인지 확인합니다.
* 관리 유틸리티를 종료합니다.
* ssh를 종료합니다.

![조작화에 대한 진단](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

이 단계에서 조작화 구성이 완료되었습니다. 이제 RClient의 'mrsdeploy' 패키지를 사용하여 에지 노드의 조작화에 연결하고 [원격 실행](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) 및 [웹 서비스](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette)와 같은 기능을 사용할 수 있습니다. 클러스터가 가상 네트워크에 설정되어 있는지 여부에 따라 아래에서 설명한 대로 SSH 로그인을 통해 포트 전달 터널링을 설정할 필요가 있습니다.

### <a name="rserver-cluster-on-virtual-network"></a>가상 네트워크의 RServer 클러스터

12800 포트를 통해 에지 노드로 트래픽을 허용하도록 해야 합니다. 이렇게 하면 에지 노드를 사용하여 조작화 기능에 연결할 수 있습니다.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

remoteLogin()이 에지 노드에 연결할 수 없지만 에지 노드로 ssh를 실행할 수 있는 경우 12800 포트에서 트래픽을 허용하는 규칙이 올바르게 설정되었는지 확인해야 합니다. 문제가 계속되면 ssh를 통해 포트 전달 터널링을 설정하여 문제를 해결할 수 있습니다.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>가상 네트워크에 설정되지 않은 RServer 클러스터

클러스터가 VNet에 설정되지 않았거나 VNet을 통한 연결에 문제가 있는 경우 아래와 같이 SSH 포트 전달 터널링을 사용할 수 있습니다.

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Putty에서도 이 터널링을 설정할 수 있습니다.

![Putty ssh 연결](./media/hdinsight-hadoop-r-server-get-started/putty.png)

SSH 세션이 활성화되면 컴퓨터의 12800 포트에서 발생한 트래픽은 SSH 세션을 통해 에지 노드의 12800 포트로 전달됩니다. remoteLogin() 메서드에서 `127.0.0.1:12800`을 사용해야 합니다. 이렇게 하면 포트 전달을 통해 에지 노드의 조작화에 로그인됩니다.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>HDInsight 작업자 노드에서 Microsoft R Server 조작화 계산 노드를 확장하는 방법


### <a name="decommission-the-worker-nodes"></a>작업자 노드 서비스 해제
Microsoft R Server는 현재 Yarn을 통해 관리되지 않습니다. 작업자 노드 서비스를 해제하지 않으면 Yarn 리소스 관리자가 서버에서 사용하는 리소스를 인식하지 못하기 때문에 예상대로 작동하지 않습니다. 이 문제를 방지하려면 계산 노드의 크기를 조정할 위치에 있는 작업자 노드의 서비스를 해제하는 것이 좋습니다.

작업자 노드 서비스를 해제하는 단계는 다음과 같습니다.

* HDI 클러스터의 Ambari 콘솔에 로그인하고 "Hosts" 탭을 클릭합니다.
* 서비스를 해제할 작업자 노드를 선택하고 "동작"> "선택한 호스트"> "Hosts"를 차례로 클릭한 다음 "유지 관리 모드 켜기"를 클릭합니다. 예를 들어 아래 이미지에서는 wn3과 wn4를 선택하여 서비스를 해제했습니다.  

   ![작업자 노드 서비스 해제](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* "작업" > "선택한 호스트" > "DataNodes"를 차례로 선택한 다음 "서비스 해제"를 클릭합니다.
* "작업" > "선택한 호스트" > "NodeManagers"를 차례로 선택한 다음 "서비스 해제"를 클릭합니다.
* "작업" > "선택한 호스트" > "DataNodes"를 차례로 선택한 다음 "중지"를 클릭합니다.
* "작업" > "선택한 호스트" > "NodeManagers"를 차례로 선택한 다음 "중지"를 클릭합니다.
* "작업" > "선택한 호스트" > "Hosts"를 차례로 선택한 다음 "모든 구성 요소 중지"를 클릭합니다.
* 작업자 노드를 선택 취소하고 헤드 노드를 선택합니다.
* "작업" > "선택한 호스트" > "Hosts" > "모든 구성 요소 다시 시작"을 차례로 선택합니다.


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>서비스 해제된 작업자 노드 각각에 계산 노드 구성

* ssh를 서비스 해제된 각 작업자 노드로 실행합니다.
* `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`을 사용하여 관리 유틸리티를 실행합니다.
* "1"을 입력하여 "1.  Configure R Server for Operationalization" 옵션을 선택합니다.
* "c"를 입력하여 "C.  Compute node"(계산 노드) 옵션을 선택합니다. 그러면 작업자 노드에 계산 노드가 구성됩니다.
* 관리 유틸리티를 종료합니다.

### <a name="add-compute-nodes-details-on-web-node"></a>웹 노드에 계산 노드 세부 정보 추가
서비스 해제된 모든 작업자 노드에서 계산 노드를 실행하도록 구성한 후에는 에지 노드로 돌아가서 Microsoft R Server 웹 노드 구성에 서비스 해제된 작업자 노드의 IP 주소를 추가합니다.

* ssh를 에지 노드로 실행합니다.
* `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`을 실행합니다.
* "URI" 섹션을 살펴보고 작업자 노드의 IP 및 포트 세부 정보를 추가합니다.

![작업자 노드 서비스 해제 명령줄](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="next-steps"></a>다음 단계
이제 R 서버를 포함하는 새 HDInsight 클러스터를 만드는 방법 및 SSH 세션에서 R 콘솔 사용 기본 사항을 이해했으므로 다음을 사용하여 HDInsight에서 R 서버로 작업하는 다른 방법을 확인할 수 있습니다.

* [HDInsight에 RStudio Server 추가(클러스터 생성 동안 설치되지 않은 경우)](hdinsight-hadoop-r-server-install-r-studio.md)
* [HDInsight의 R 서버에 대한 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)
* [HDInsight에서 R Server의 Azure Storage 옵션](hdinsight-hadoop-r-server-storage.md)

