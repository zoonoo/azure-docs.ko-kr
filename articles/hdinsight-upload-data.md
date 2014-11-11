<properties linkid="manage-services-hdinsight-upload-data-for-hadoop-jobs-in-hdinsight" urlDisplayName="Upload Data" pageTitle="Upload data for Hadoop jobs in HDInsight | Azure" metaKeywords="" description="Learn how to upload and access data in HDInsight using Azure Storage Explorer, Azure PowerShell, the Hadoop command line, or Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data for Hadoop jobs in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight에서 Hadoop 작업용 데이터 업로드

Azure HDInsight는 Azure Blob 저장소를 통해 모든 기능을 갖춘 HDFS(Hadoop Distributed File System)를 제공합니다. HDFS 확장으로 설계된 Azure HDInsight는 관리 중인 데이터에 Hadoop 에코시스템의 모든 구성 요소가 직접 작동하도록 설정하여 고객에게 능률적인 환경을 제공합니다. Azure Blob 저장소와 HDFS는 데이터의 저장소 및 해당 데이터의 계산을 최적화하는 별개의 파일 시스템입니다. Azure Blob 저장소 사용의 이점에 대해 알아보려면 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

Azure HDInsight 클러스터는 일반적으로 MapReduce 작업 실행을 위해 배포되며 이러한 작업이 완료되면 삭제됩니다. 계산이 완료된 후 HDFS 클러스터에 데이터를 유지하면 데이터 저장에 비용이 많이 들 수 있습니다. Azure Blob 저장소는 HDInsight를 사용하여 처리할 데이터를 위한 옵션으로서 확장성, 가용성, 용량이 뛰어나고 경제적이며 공유할 수 있습니다. Blob에 데이터를 저장하면 계산에 사용할 HDInsight 클러스터를 데이터 손실 없이 안전하게 릴리스할 수 있습니다.

[AzCopy][AzCopy], [Azure PowerShell][Azure PowerShell], [Azure Storage Client Library for .NET][Azure Storage Client Library for .NET] 또는 탐색 도구를 통해 Azure Blob 저장소에 액세스할 수 있습니다. 사용 가능한 도구는 다음과 같습니다.

-   [Azure 저장소 탐색기][Azure 저장소 탐색기]
-   [Cloud Storage Studio 2][Cloud Storage Studio 2]
-   [CloudXplorer][CloudXplorer]
-   [Azure Explorer][Azure Explorer]
-   [Azure Explorer PRO][Azure Explorer PRO]

**필수 조건**

이 문서를 시작하기 전에 다음 요구 사항을 확인하세요.

-   Azure HDInsight 클러스터. 자세한 내용은 [Azure HDInsight 시작][Azure HDInsight 시작] 또는 [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]을 참조하세요.

## 이 문서에서는 다음을 수행합니다.

-   [AzCopy를 사용하여 Blob 저장소에 데이터 업로드][AzCopy를 사용하여 Blob 저장소에 데이터 업로드]
-   [Azure PowerShell을 사용하여 Blob 저장소에 데이터 업로드][Azure PowerShell을 사용하여 Blob 저장소에 데이터 업로드]
-   [Azure 저장소 탐색기를 사용하여 Blob 저장소에 데이터 업로드][Azure 저장소 탐색기를 사용하여 Blob 저장소에 데이터 업로드]
-   [Hadoop 명령줄을 사용하여 Blob 저장소에 데이터 업로드][Hadoop 명령줄을 사용하여 Blob 저장소에 데이터 업로드]
-   [Sqoop을 사용하여 Azure SQL 데이터베이스에서 Blob 저장소로 데이터 가져오기][Sqoop을 사용하여 Azure SQL 데이터베이스에서 Blob 저장소로 데이터 가져오기]

## <span id="azcopy"></span></a>AzCopy를 사용하여 Blob 저장소에 데이터 업로드

AzCopy는 Azure 저장소 계정과 데이터를 주고받는 작업을 간소화하도록 설계된 명령줄 유틸리티입니다. 이 유틸리티는 독립 실행형 도구로 사용할 수도 있고 기존 응용 프로그램에 통합할 수도 있습니다. [AzCopy를 다운로드][AzCopy를 다운로드]하세요.

AzCopy 구문은 다음과 같습니다.

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

자세한 내용은 [AzCopy - Azure Blob용 파일 업로드/다운로드][AzCopy](영문)를 참조하세요.

## <span id="powershell"></span></a>Azure PowerShell을 사용하여 Blob 저장소에 데이터 업로드

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. Azure PowerShell을 사용하면, MapReduce 작업에서 처리할 수 있도록 데이터를 Blob 저장소에 업로드할 수 있습니다. Azure PowerShell을 실행하기 위해 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요.

**Blob 저장소에 로컬 파일을 업로드하려면**

1.  [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]의 지침에 따라 Azure PowerShell 콘솔 창을 엽니다.
2.  다음 스크립트에서 처음 5개의 변수 값을 설정합니다.

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}

        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container        
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3.  Azure PowerShell 콘솔 창에 스크립트를 붙여넣고 실행합니다.

Blob 저장소 컨테이너는 키/값 쌍으로 데이터를 저장하며, 디렉터리 계층 구조는 없습니다. 그러나 파일이 디렉터리 구조 내에 저장된 것처럼 보이도록 키 이름에 "/" 문자를 사용할 수 있습니다. 예를 들어 Blob의 키 이름을 *input/log1.txt*로 지정할 수 있습니다. 실제로 "input" 디렉터리는 없지만 키 이름에 "/" 문자가 있으므로 파일 경로처럼 보입니다. 이전 스크립트에서는 $blobname 변수를 설정하여 파일에 폴더 구조를 제공할 수 있습니다. 예를 들면 *$blobname="myfolder/myfile.txt"*와 같습니다.

Azure 탐색기 도구를 사용하면 몇몇 0바이트 파일을 발견할 수 있습니다. 이러한 파일의 목적은 두 가지입니다.

-   빈 폴더의 경우 폴더가 존재함을 표시하는 역할을 합니다. Blob 저장소는 지능적이어서, foo/bar라는 Blob이 있으면 foo라는 폴더도 있다는 사실을 압니다. 그러나 foo라는 빈 폴더를 가지려는 경우 유일한 방법은 이 특별한 0바이트 파일을 만들어 두는 것입니다.
-   Hadoop 파일 시스템에 필요한 몇몇 특수 메타데이터(예: 폴더에 대한 사용 권한 및 소유자)를 보유합니다.

## <span id="storageexplorer"></span></a>Azure 저장소 탐색기를 사용하여 Blob 저장소에 데이터 업로드

*Azure 저장소 탐색기*는 Azure 저장소의 데이터를 검사하고 변경하기 위한 유용한 도구로서 무료이며 [][]<http://azurestorageexplorer.codeplex.com/></a>에서 다운로드할 수 있습니다.

이 도구를 사용하기 전에 Azure 저장소 계정 이름과 계정 키를 알아야 합니다. 이 정보를 확인하는 방법을 알아보려면 [저장소 계정 관리][저장소 계정 관리]의 "방법: 저장소 액세스 키 보기, 복사 및 다시 생성" 섹션을 참조하세요.

1.  Azure 저장소 탐색기를 실행합니다.

    ![HDI.AzureStorageExplorer][HDI.AzureStorageExplorer]

2.  **계정 추가**를 클릭합니다. Azure 저장소 탐색기에 계정을 추가한 후에는 이 단계를 다시 수행할 필요가 없습니다.

    ![HDI.ASEAddAccount][HDI.ASEAddAccount]

3.  **저장소 계정 이름** 및 **저장소 계정 키**를 입력한 다음 **저장소 계정 추가**를 클릭합니다. 저장소 계정을 여러 개 추가할 수 있으며 각 계정은 각 탭에 표시됩니다.
4.  **저장소 형식**에서 **Blob**을 선택합니다.

    ![HDI.ASEBlob][HDI.ASEBlob]

5.  **컨테이너**에서 HDInsight 클러스터에 연결된 컨테이너를 클릭합니다. HDInsight 클러스터를 만들 때 컨테이너를 지정해야 합니다. 그렇게 하지 않은 경우 클러스터 만들기 과정에서 자동으로 생성됩니다.
6.  **Blob**에서 **업로드**를 클릭합니다.
7.  업로드할 파일을 지정한 다음 **열기**를 클릭합니다.

## <span id="commandline"></span></a> Hadoop 명령줄을 사용하여 Blob 저장소에 데이터 업로드

Hadoop 명령줄을 사용하려면 먼저 원격 데스크톱을 사용하여 클러스터에 연결해야 합니다.

1.  [관리 포털][관리 포털]에 로그인합니다.
2.  **HDINSIGHT**를 클릭합니다. 배포된 Hadoop 클러스터 목록이 표시됩니다.
3.  데이터를 업로드할 HDInsight 클러스터를 클릭합니다.
4.  페이지 위쪽에서 **구성**을 클릭합니다.
5.  원격 데스크톱을 사용하도록 설정하지 않은 경우 **원격 사용**을 클릭하고 지침을 따릅니다. 그렇지 않은 경우 다음 단계로 건너뜁니다.
6.  페이지 맨 아래에서 **연결**을 클릭합니다.
7.  **열기**를 클릭합니다.
8.  자격 증명을 입력한 다음 **확인**을 클릭합니다.
9.  **예**를 클릭합니다.
10. 데스크톱에서 **Hadoop 명령줄**을 클릭합니다.
11. 다음 샘플은 HDInsight 헤드 노드의 로컬 파일 시스템에서 /example/data 디렉터리로 davinci.txt 파일을 복사하는 방법을 보여 줍니다.

        hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

    기본 파일 시스템은 Azure Blob 저장소에 있으므로 /example/datadavinci.txt는 실제로 Azure Blob 저장소에 있습니다. 파일이 다음과 같을 수도 있습니다.

        wasb:///example/data/davinci.txt 

    또는

        wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

    *wasbs*를 사용할 때에는 정규화된 도메인 이름이 필요합니다.

12. 다음 명령을 사용하여 업로드된 파일을 나열합니다.

        hadoop dfs -lsr /example/data

## <span id="sqoop"></span></a> Sqoop을 사용하여 SQL 데이터베이스/SQL Server에서 HDFS로 데이터 가져오기

Sqoop은 Hadoop과 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL, MySQL, Oracle 등의 RDBMS(관계형 데이터베이스 관리 시스템)에서 HDFS(Hadoop Distributed File System)로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변형한 다음, 데이터를 RDBMS로 다시 내보낼 수 있습니다. 자세한 내용은 [Sqoop 사용자 가이드][Sqoop 사용자 가이드](영문)를 참조하세요.

데이터를 가져오기 전에 Azure SQL 데이터베이스 서버 이름, 데이터베이스 계정 이름, 계정 암호 및 데이터베이스 이름을 알아야 합니다.

기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure 관리 포털에서 사용하도록 설정해야 합니다. SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성][SQL 데이터베이스 만들기 및 구성]을 참조하세요.

다음 절차에서는 PowerShell을 사용하여 Sqoop 작업을 제출합니다.

**Sqoop 및 PowerShell을 사용하여 HDInsight로 데이터를 가져오려면**

1.  [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]의 지침에 따라 Azure PowerShell 콘솔 창을 엽니다.
2.  다음 스크립트에서 처음 8개의 변수 값을 설정합니다.

        $subscriptionName = "<AzureSubscriptionName>"
        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
        $tableName = "<SQLDatabaseTableName>"

        $hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".

        Select-AzureSubscription $subscriptionName      
        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3.  Azure PowerShell 콘솔 창에 스크립트를 붙여넣고 실행합니다. Azure Blob 저장소에서 데이터 파일을 검색하기 위한 PowerShell 샘플을 보려면 [HDInsight 시작][Azure HDInsight 시작]을 참조하세요.

Sqoop 사용에 대한 자세한 내용은 [HDInsight와 함께 Sqoop 사용][HDInsight와 함께 Sqoop 사용]을 참조하세요.

## 다음 단계

이제 HDInsight로 데이터를 가져오는 방법을 익혔으니 다음 문서를 사용하여 분석을 수행하는 방법을 알아보세요.

-   [Azure HDInsight 시작][Azure HDInsight 시작]
-   [프로그래밍 방식으로 Hadoop 작업 제출][프로그래밍 방식으로 Hadoop 작업 제출]
-   [HDInsight에서 Hive 사용][HDInsight에서 Hive 사용]
-   [HDInsight에서 Pig 사용][HDInsight에서 Pig 사용]

  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [AzCopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
  [Azure PowerShell]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj152841.aspx
  [Azure Storage Client Library for .NET]: /ko-kr/develop/net/how-to-guides/blob-storage/
  [Azure 저장소 탐색기]: http://azurestorageexplorer.codeplex.com/
  [Cloud Storage Studio 2]: http://www.cerebrata.com/Products/CloudStorageStudio/
  [CloudXplorer]: http://clumsyleaf.com/products/cloudxplorer
  [Azure Explorer]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
  [Azure Explorer PRO]: http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx
  [Azure HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [AzCopy를 사용하여 Blob 저장소에 데이터 업로드]: #azcopy
  [Azure PowerShell을 사용하여 Blob 저장소에 데이터 업로드]: #powershell
  [Azure 저장소 탐색기를 사용하여 Blob 저장소에 데이터 업로드]: #storageexplorer
  [Hadoop 명령줄을 사용하여 Blob 저장소에 데이터 업로드]: #commandline
  [Sqoop을 사용하여 Azure SQL 데이터베이스에서 Blob 저장소로 데이터 가져오기]: #sqoop
  [AzCopy를 다운로드]: http://aka.ms/WaCopy
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  []: http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer"
  [저장소 계정 관리]: ../storage-manage-storage-account/
  [HDI.AzureStorageExplorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
  [HDI.ASEAddAccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
  [HDI.ASEBlob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
  [관리 포털]: https://manage.windowsazure.com
  [Sqoop 사용자 가이드]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
  [SQL 데이터베이스 만들기 및 구성]: ../sql-database-create-configure/
  [HDInsight와 함께 Sqoop 사용]: ../hdinsight-use-sqoop/
  [프로그래밍 방식으로 Hadoop 작업 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig
