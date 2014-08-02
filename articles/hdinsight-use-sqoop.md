<properties linkid="manage-services-hdinsight-use-sqoop" urlDisplayName="Use Sqoop with HDInsight Samples" pageTitle="Use Sqoop with HDInsight | Azure" metaKeywords="" description="Learn how to use Azure PowerShell from a workstation to run Sqoop import and export between an HDInsight cluster and an Azure SQL database." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Use Sqoop with HDInsight" authors="jgao" />

HDInsight에서 Sqoop 사용
========================

워크스테이션에서 Azure PowerShell 및 HDInsight .NET SDK를 사용하여 HDInsight 클러스터와 Azure SQL 데이터베이스 간에 Sqoop 가져오기 및 내보내기를 실행하는 방법을 알아보십시오.

**예상 완료 시간:** 30분

이 문서에서는 다음을 수행합니다.
--------------------------------

-   [Sqoop 정의](#whatissqoop)
-   [필수 조건](#prerequisites)
-   [자습서 시나리오 이해](#scenario)
-   [자습서 준비](#prepare)
-   [PowerShell을 사용하여 Sqoop 내보내기 실행](#export)
-   [HDInsight SDK를 사용하여 Sqoop 내보내기 실행](#export-sdk)
-   [PowerShell을 사용하여 Sqoop 가져오기 실행](#import)
-   [다음 단계](#nextsteps)

Sqoop 정의
----------

비구조적 및 반구조적 데이터(예: 로그 및 파일)를 처리하기 위해 당연히 Hadoop을 선택하지만 관계형 데이터베이스에 저장된 구조적 데이터를 처리해야 할 경우도 있습니다.

[Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html)은 Hadoop 클러스터와 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL, MySQL, Oracle 등의 RDBMS(관계형 데이터베이스 관리 시스템)에서 HDFS(Hadoop Distributed File System)로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다. 이 자습서에서는 관계형 데이터베이스에 SQL 데이터베이스를 사용합니다.

HDInsight 클러스터에서 지원되는 Sqoop 버전을 보려면 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능](../hdinsight-component-versioning/)을 참조하십시오.

필수 조건
---------

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure PowerShell이 설치 및 구성된 **워크스테이션**. 자세한 내용은 [Azure PowerShell 설치 및 구성](../install-configure-powershell)을 참조하십시오. PowerShell 스크립트를 실행하려면 Azure PowerShell을 관리자 권한으로 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행](http://technet.microsoft.com/en-us/library/ee176949.aspx)을 참조하십시오.

-   **Azure HDInsight 클러스터**. 클러스터 프로비전에 대한 자세한 내용은 [HDInsight를 사용하여 시작](../hdinsight-get-started/) 또는 [HDInsight 클러스터 프로비전](../hdinsight-provision-clusters/)을 참조하십시오. 자습서를 진행하려면 다음 데이터가 있어야 합니다.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">클러스터 속성</th><th data-morhtml="true">PowerShell 변수 이름</th><th data-morhtml="true">값</th><th data-morhtml="true">설명</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 이름</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터의 이름입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure 저장소 계정 이름</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터에 사용할 수 있는 Azure 저장소 계정입니다. 이 자습서에서는 클러스터 프로비전 프로세스 중에 지정된 기본 저장소 계정을 사용합니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure Blob 컨테이너 이름</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">이 예제에서는 기본 HDInsight 클러스터 파일 시스템에 사용된 Azure Blob 저장소 컨테이너를 사용합니다. 기본적으로 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.</td></tr>
  </table>

-   **Azure SQL 데이터베이스**. 워크스테이션으로부터의 액세스를 허용하도록 SQL 데이터베이스 서버의 방화벽 규칙을 구성해야 합니다. SQL 데이터베이스 만들기 및 방화벽 구성에 대한 자세한 내용은 [Azure SQL 데이터베이스를 사용하여 시작](../sql-database-get-started/)을 참조하십시오. 이 문서에서는 이 자습서에 필요한 SQL 데이터베이스 테이블을 만드는 데 PowerShell 스크립트를 제공합니다.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">SQL 데이터베이스 속성</th><th data-morhtml="true">PowerShell 변수 이름</th><th data-morhtml="true">값</th><th data-morhtml="true">설명</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 서버 이름</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">Sqoop에서 데이터를 내보내거나 가져올 SQL 데이터베이스 서버입니다. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 로그인 이름</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">SQL 데이터베이스 로그인 이름입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 로그인 암호</td><td data-morhtml="true">$sqlDatabasePassword</td><td data-morhtml="true"></td><td data-morhtml="true">SQL 데이터베이스 로그인 암호입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 이름</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Sqoop에서 데이터를 내보내거나 가져올 Azure SQL 데이터베이스입니다. </td></tr>
  </table>

    > [WACOM.NOTE]기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure 관리 포털에서 사용하도록 설정해야 합니다. SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성](../sql-database-create-configure/)을 참조하십시오.

> [WACOM.NOTE] 테이블에 값을 채웁니다. 이 자습서를 진행하는 데 도움이 됩니다.

시나리오 이해
-------------

HDInsight 클러스터는 일부 샘플 데이터와 함께 제공됩니다. 다음 두 가지를 사용합니다.

-   */example/data/sample.log*에 있는 log4j 로그 파일. 이 파일에서 다음 로그가 추출됩니다.

          2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
          2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
          2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
          ...

-   */hive/warehouse/hivesampletable*에 있는 데이터 파일을 참조하는 *hivesampletable*이라는 이름의 Hive 테이블. 이 테이블에는 일부 모바일 장치 데이터가 포함되어 있습니다. 다음은 Hive 테이블 스키마입니다.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">필드</th><th data-morhtml="true">데이터 형식</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">clientid</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">querytime</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">market</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">deviceplatform</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">devicemake</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">devicemodel</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">state</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">country</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">querydwelltime</td><td data-morhtml="true">double</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">sessionid</td><td data-morhtml="true">bigint</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">sessionpagevieworder</td><td data-morhtml="true">bigint</td></tr>
  </table>

먼저 sample.log와 hivesampletable을 SQL 데이터베이스에 내보낸 후 모바일 장치 데이터를 포함하는 테이블을 다음 경로를 통해 HDInsight에 다시 가져옵니다.

    /tutorials/usesqoop/importeddata

### HDInsight 저장소 이해

HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에서 HDFS의 Microsoft 구현입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage/)을 참조하십시오.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정 및 그 계정에서의 특정 Blob 저장소 컨테이너는 HDFS에서처럼 기본 파일 시스템으로 지정됩니다. 프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전](../hdinsight-provision-clusters/)을 참조하십시오. 이 자습서에 사용된 PowerShell 스크립트를 간소화하려면 모든 파일이 */tutorials/usesqoop*에 있는 기본 파일 시스템 컨테이너에 저장되어야 합니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다. WASB 구문은 다음과 같습니다.

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서는 지원되지만 HDInsight 3.0 클러스터에서는 지원되지 않으며 그 이상 버전에서도 지원되지 않습니다.

> [WACOM.NOTE] WASB 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage/)을 참조하십시오.

기본 파일 시스템 컨테이너에 저장된 파일은 다음 URI 중 아무거나 사용하여 HDInsight에서 액세스할 수 있습니다(예제로 sample.log 사용).

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

파일을 저장소 계정에서 바로 액세스하는 경우 파일의 Blob 이름은 다음과 같습니다.

    example/data/sample.log

자습서 준비
-----------

자습서의 뒷부분에 나오는 Sqoop 내보내기에서 사용하는 두 가지 SQL 데이터베이스 테이블을 만듭니다. 또한 sample.log 파일을 전처리해야 Sqoop에서 처리할 수 있습니다.

**SQL 데이터베이스 테이블을 만들려면**

1.  Windows PowerShell ISE(Windows 8 시작 화면에서 **PowerShell\_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭합니다. [Windows 8 및 Windows에서 Windows PowerShell 시작](http://technet.microsoft.com/en-us/library/hh847889.aspx)(영문)을 참조하십시오.)를 엽니다.

2.  다음 스크립트를 스크립트 창에 복사한 다음 처음 네 변수를 설정합니다.

         #SQL database variables
         $sqlDatabaseServer = "<SQLDatabaseServerName>" 
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
         $sqlDatabaseName = "<SQLDatabaseName>" 

         $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

    변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하십시오.

3.  다음 스크립트를 스크립트 창에 추가합니다. 이 스크립트에는 두 테이블 및 테이블의 클러스터형 인덱스를 정의하는 SQL 문이 있습니다. SQL 데이터베이스에는 클러스터형 인덱스가 있어야 합니다.

         # SQL query strings for creating tables and clustered indexes
         $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
             [t1] [nvarchar](50), 
             [t2] [nvarchar](50), 
             [t3] [nvarchar](50), 
             [t4] [nvarchar](50), 
             [t5] [nvarchar](50), 
             [t6] [nvarchar](50), 
             [t7] [nvarchar](50))"
            
         $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
            
         $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50), 
         [querytime] [nvarchar](50), 
         [market] [nvarchar](50), 
         [deviceplatform] [nvarchar](50), 
         [devicemake] [nvarchar](50), 
         [devicemodel] [nvarchar](50), 
         [state] [nvarchar](50), 
         [country] [nvarchar](50), 
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])"
            
         $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

4.  다음 스크립트를 SQL 명령을 실행하는 스크립트 창에 추가합니다.

         Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
         $conn = New-Object System.Data.SqlClient.SqlConnection
         $conn.ConnectionString = $sqlDatabaseConnectionString
         $conn.Open()
            
         Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
         $cmd = New-Object System.Data.SqlClient.SqlCommand
         $cmd.Connection = $conn
         $cmd.CommandText = $cmdCreateLog4jTable
         $ret = $cmd.ExecuteNonQuery()
         $cmd.CommandText = $cmdCreateLog4jClusteredIndex
         $cmd.ExecuteNonQuery()
            
         Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
         $cmd.CommandText = $cmdCreateMobileTable
         $cmd.ExecuteNonQuery()
         $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
         $cmd.ExecuteNonQuery()
            
         Write-Host "Close connection ..." -ForegroundColor Green     
         $conn.close()
            
         Write-Host "Done" -ForegroundColor Green

5.  **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6.  [Azure 관리 포털](https://manage.windowsazure.com/)을 사용하여 테이블 및 클러스터형 인덱스를 검사합니다.

이 자습서에서는 log4j 로그 파일(구분된 파일) 및 Hive 테이블을 SQL 데이터베이스로 내보냅니다. 구분된 파일은 */example/data/sample.log*입니다. 자습서의 앞부분에 log4j 로그 샘플이 몇 개 있습니다. 로그 파일에는 일부 빈 줄과 일부 다음과 유사한 줄이 있습니다.

    java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
        at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

*java.lang.Exception:*을 제거할 수도 있습니다. 행에서 레코드가 올바르게 구문 분석될 수 있습니다.

빈 문자열이 있거나 SQL 데이터베이스 테이블에 정의된 필드 수보다 요소 수가 더 적은 줄이 있는 경우 Sqoop 내보내기는 실패합니다. log4jlogs 테이블에는 7가지 문자열 형식 필드가 있습니다.

**sample.log 파일을 전처리하려면**

1.  Windows PowerShell ISE를 엽니다.
2.  아래 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

         Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 이 구독 연결 설치 방법이 시간 초과되었으며 12시간 후에 다시 로그인해야 합니다.

    > [WACOM.NOTE] 여러 Azure 구독이 있으며 기본 구독이 사용할 구독이 아닌 경우 **Select-AzureSubscription** cmdlet을 사용하여 현재 구독을 선택합니다.

3.  다음 스크립트를 스크립트 창에 복사한 다음 처음 두 변수를 설정합니다.

         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<BlobContainerName>"
            
         $sourceBlobName = "example/data/sample.log"
         $destBlobName = "tutorials/usesqoop/data/sample.log"

    변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하십시오.

4.  다음 스크립트를 스크립트 창에 추가합니다.

         # Define the connection string
         $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
            
         # Create block blob objects referencing the source and destination blob.
         $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
         $storageClient = $storageAccount.CreateCloudBlobClient();
         $storageContainer = $storageClient.GetContainerReference($containerName)
         $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
         $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
            
         # Define a MemoryStream and a StreamReader for reading from the source file
         $stream = New-Object System.IO.MemoryStream
         $stream = $sourceBlob.OpenRead()
         $sReader = New-Object System.IO.StreamReader($stream)
            
         # Define a MemoryStream and a StreamWriter for writing into the destination file
         $memStream = New-Object System.IO.MemoryStream
         $writeStream = New-Object System.IO.StreamWriter $memStream
            
         # process the source blob
         $exString = "java.lang.Exception:"
         while(-Not $sReader.EndOfStream){
             $line = $sReader.ReadLine()
             $split = $line.Split(" ")
            
             # remove the "java.lang.Exception" from the first element of the array
             # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
             if ($split[0] -eq $exString){
                 #create a new ArrayList to remove $split[0]
                 $newArray = [System.Collections.ArrayList] $split
                 $newArray.Remove($exString)
            
                 # update $split and $line
                 $split = $newArray
                 $line = $newArray -join(" ")
             }
            
             # remove the lines that has less than 7 elements
             if ($split.count -ge 7){
                 write-host $line
                 $writeStream.WriteLine($line)
             }
         }
            
         # Write to the destination blob
         $writeStream.Flush()
         $memStream.Seek(0, "Begin")
         $destBlob.UploadFromStream($memStream)

5.  **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6.  수정한 데이터 파일을 검사하려면 Azure 관리 포털, Azure 저장소 탐색기 도구 또는 Azure PowerShell을 사용할 수 있습니다. [HDInsight 시작](../hdinsight-get-started/)에는 파일을 다운로드하고 그 파일의 내용을 표시하는 PowerShell 사용에 관한 코드 샘플이 있습니다.

PowerShell을 사용하여 Sqoop 내보내기 실행
-----------------------------------------

이 섹션에서는 Hive 테이블과 데이터 파일을 둘 다 Azure SQL 데이터베이스에 내보내기 위해 Azure PowerShell을 사용하여 Sqoop 내보내기 명령을 실행합니다. 다음 섹션에서는 HDInsight .NET 샘플을 제공합니다.

**log4j 로그 파일을 내보내려면**

1.  Windows PowerShell ISE를 엽니다.
2.  아래 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

         Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  다음 스크립트를 스크립트 창에 복사한 다음 처음 7개의 변수를 설정합니다.

         # Define the cluster variables
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Define the SQL database variables
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "<SQLDatabaseName>"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_log4j = "/tutorials/usesqoop/data"

    변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하십시오.

    $exportDir\_log4j에는 파일 이름이 지정된 sample.log 파일이 없습니다. Sqoop은 해당 폴더 아래에 있는 모든 파일의 데이터를 내보냅니다.

4.  다음 스크립트를 스크립트 창에 추가합니다.

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
	
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

    필드 구분 기호는 **\\0x20**이며 공백입니다. 구분 기호는 sample.log 파일 전처리 PowerShell 스크립트에 정의되어 있습니다. **-m 1**에 대해 자세히 알아보려면 [Sqoop 사용자 가이드](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html)를 참조하십시오.

5.  **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6.  [Azure 관리 포털](https://manage.windowsazure.com/)을 사용하여 내보낸 데이터를 검사합니다.

**hivesampletable Hive 테이블을 내보내려면**

1.  Windows PowerShell ISE를 엽니다.
2.  아래 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

         Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  다음 스크립트를 스크립트 창에 복사한 다음 처음 7개의 변수를 설정합니다.

         # Define the cluster variables
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Define the SQL database variables
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_mobile = "mobiledata"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_mobile = "/hive/warehouse/hivesampletable"

    변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하십시오.

4.  다음 스크립트를 스크립트 창에 추가합니다.

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
            
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6.  [Azure 관리 포털](https://manage.windowsazure.com/)을 사용하여 내보낸 데이터를 검사합니다.

HDInsight .NET SDK를 사용하여 Sqoop 내보내기 실행
-------------------------------------------------

다음은 HDInsight .NET SDK를 사용하여 Sqoop 내보내기를 실행하는 C\# 샘플입니다. HDInsight .NET SDK 사용에 대한 일반적인 정보를 보려면 [프로그래밍 방식으로 Hadoop 작업 제출](../hdinsight-submit-hadoop-jobs-programmatically/)을 참조하십시오.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;

	namespace sqoopSDKSample
	{
		class Program
		{
			static void Main(string[] args)
			{
				// Set the variables
				string subscriptionID = "<WindowsAzureSubscriptionID data-morhtml="true">";
				string clusterName = "<HDInsightClusterName data-morhtml="true">";
				string certFriendlyName = "<WindowsAzureCertificateFriendlyName data-morhtml="true">";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName data-morhtml="true">";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin data-morhtml="true">" + "@" + sqlDatabaseServerName;
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword data-morhtml="true">";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";

	            cmdExport = @"export";
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";

	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
		            {
		                Command = cmdExport,
		                StatusFolder = "/tutorials/usesqoop/jobStatus"
		            };

	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2 data-morhtml="true">().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);

	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);

	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);

	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());

	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }

	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

스크립트 파일을 실행하려면 다음을

    Command = cmdExport,

다음으로 바꿀 수 있습니다.

    File = "/tutorials/usesqoop/sqoopexport.txt",

스크립트 파일은 WASB에 있어야 합니다.

PowerShell을 사용하여 Sqoop 가져오기 실행
-----------------------------------------

이 섹션에서는 SQL 데이터베이스로 내보낸 log4j 로그를 HDInsight로 다시 가져옵니다.

1.  Windows PowerShell ISE를 엽니다.
2.  아래 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

         Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  다음 스크립트를 스크립트 창에 복사한 다음 처음 7개의 변수를 설정합니다.

         # Define the cluster variables
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Define the SQL database variables
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $tableName_mobile = "mobiledata"
         $targetDir_mobile = "/tutorials/usesqoop/importeddata/"

    변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하십시오.

4.  다음 스크립트를 스크립트 창에 추가합니다.

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6.  수정한 데이터 파일을 검사하려면 Azure 관리 포털, Azure 저장소 탐색기 도구 또는 Azure PowerShell을 사용할 수 있습니다. [HDInsight 시작](../hdinsight-get-started/)에는 파일을 다운로드하고 그 파일의 내용을 표시하는 PowerShell 사용에 관한 코드 샘플이 있습니다.

다음 단계
---------

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하십시오.

-   [HDInsight에서 Oozie 사용](../hdinsight-use-oozie/): Oozie 워크플로에서 Sqoop 동작을 사용합니다.
-   [HDInsight를 사용하여 비행 지연 데이터 분석](../hdinsight-analyze-flight-delay-data/): Hive를 사용하여 비행 지연 데이터를 분석한 후 Sqoop을 사용하여 데이터를 SQL 데이터베이스로 내보냅니다.
-   [HDInsight에 데이터 업로드](../hdinsight-upload-data/): HDInsight/Azure Blob 저장소에 데이터를 업로드하는 다른 방법을 찾습니다.

