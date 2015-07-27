<properties
	pageTitle="HDInsight에서 Hadoop Sqoop 사용 | Microsoft Azure"
	description="워크스테이션에서 Azure PowerShell을 사용하여 Hadoop 클러스터와 Azure SQL 데이터베이스 간에 Sqoop 가져오기 및 내보내기를 실행하는 방법에 대해 알아봅니다."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/11/2015"
	ms.author="jgao"/>

#HDInsight에서 Hadoop과 Sqoop 사용(Windows)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

워크스테이션에서 Azure PowerShell 및 HDInsight .NET SDK를 사용하여 HDInsight 클러스터와 Azure SQL 데이터베이스 또는 SQL Server 데이터베이스 간에 Sqoop 가져오기 및 내보내기를 실행하는 방법에 대해 알아봅니다.

> [AZURE.NOTE]이 문서의 단계는 Windows 기반 또는 Linux 기반 HDInsight 클러스터와 사용할 수 있지만 이 단계에서만 Windows 클라이언트에서 작동합니다.
>
> Linux, OS X 또는 Unix 클라이언트 및 Linux 기반 HDInsight 서버를 사용하는 경우 [HDInsight에서 Hadoop과 함께 Sqoop 사용(SSH)](hdinsight-use-sqoop-mac-linux.md)을 참조하세요.

##Sqoop 정의

비구조적 및 반구조적 데이터(예: 로그 및 파일)를 처리하기 위해 당연히 Hadoop을 선택하지만 관계형 데이터베이스에 저장된 구조적 데이터를 처리해야 할 경우도 있습니다.

[Sqoop][sqoop-user-guide-1.4.4]은 Hadoop 클러스터와 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL Server, MySQL, Oracle 등의 RDBMS(관계형 데이터베이스 관리 시스템)에서 HDFS(Hadoop Distributed File System)로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다. 이 자습서에서는 관계형 데이터베이스에 SQL Server 데이터베이스를 사용합니다.

HDInsight 클러스터에서 지원되는 Sqoop 버전을 보려면 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능][hdinsight-versions]을 참조하세요.

##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요. Azure PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행][powershell-script](영문)을 참조하세요.

- **Azure HDInsight 클러스터**: 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 사용 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 자습서를 완료하려면 다음 데이터가 필요합니다.

	<table border="1">
<tr><th>클러스터 속성</th><th>Azure PowerShell 변수 이름</th><th>값</th><th>설명</th></tr>
<tr><td>HDInsight 클러스터 이름</td><td>$clusterName</td><td></td><td>HDInsight 클러스터 이름</td></tr>
<tr><td>Azure 저장소 계정 이름</td><td>$storageAccountName</td><td></td><td>Azure 저장소 계정은 HDInsight 클러스터에서 사용할 수 있습니다. 이 자습서의 경우 클러스터 프로비전 프로세스 도중에 지정된 기본 저장소 계정을 사용합니다.</td></tr>
<tr><td>Azure Blob 컨테이너 이름</td><td>$containerName</td><td></td><td>이 예에서는 기본 HDInsight 클러스터 파일 시스템에 사용되는 Blob 이름을 사용합니다. 기본적으로 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.</td></tr>
</table>

- **Azure SQL 데이터베이스**: 워크스테이션에서 액세스할 수 있도록 Azure SQL 데이터베이스 서버의 방화벽 규칙을 구성해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 구성에 대한 자세한 내용은 [Azure SQL 데이터베이스 사용 시작][sqldatabase-get-started]을 참조하세요. 이 문서에는 이 자습서에 필요한 Azure SQL 데이터베이스 테이블을 만들기 위한 Windows PowerShell 스크립트가 있습니다.

	<table border="1">
<tr><th>Azure SQL 데이터베이스 속성</th><th>Azure PowerShell 변수 이름</th><th>값</th><th>설명</th></tr>
<tr><td>Azure SQL 데이터베이스 서버 이름</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop에서 데이터를 내보내거나 가져올 Azure SQL 데이터베이스 서버입니다. </td></tr>
<tr><td>Azure SQL 데이터베이스 로그인 이름</td><td>$sqlDatabaseLogin</td><td></td><td>Azure SQL 데이터베이스 로그인 이름입니다.</td></tr>
<tr><td>Azure SQL 데이터베이스 로그인 암호</td><td>$sqlDatabasePassword</td><td></td><td>Azure SQL 데이터베이스 로그인 암호입니다.</td></tr>
<tr><td>Azure SQL 데이터베이스 이름</td><td>$sqlDatabaseName</td><td></td><td>Sqoop에서 데이터를 내보내거나 가져올 Azure SQL 데이터베이스입니다. </td></tr>
</table>> [AZURE.NOTE]기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure 포털에서 사용하도록 설정해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성][sqldatabase-create-configue]을 참조하세요.

* **SQL Server**: HDInsight 클러스터가 SQL Server와 같은 Azure의 가상 네트워크에 있으면 이 문서의 단계를 사용하여 SQL Server 데이터베이스에 대해 데이터 가져오기 및 내보내기를 수행할 수 있습니다.

	> [AZURE.NOTE]HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹 기반 가상 네트워크와는 연동되지 않습니다.

	* 가상 네트워크를 만들고 구성하려면 [가상 네트워크 구성 작업](http://msdn.microsoft.com/library/azure/jj156206.aspx)을 참조하세요.

		* 데이터 센터에서 SQL Server를 사용할 때는 가상 네트워크를 *사이트 간* 또는 *지점 및 사이트 간*으로 구성해야 합니다.

			> [AZURE.NOTE]**지점 및 사이트 간** 가상 네트워크의 경우 SQL Server가 VPN 클라이언트 구성 응용 프로그램을 실행해야 합니다. 이 응용 프로그램은 Azure 가상 네트워크 구성의 **대시보드**에서 사용 가능합니다.

		* Azure 가상 컴퓨터에서 SQL Server를 사용할 때는 SQL Server를 호스트하는 가상 컴퓨터가 HDInsight와 같은 가상 네트워크의 멤버이면 모든 가상 네트워크 구성을 사용할 수 있습니다.

	* 가상 네트워크에 HDInsight 클러스터를 프로비전하려면 [사용자 지정 옵션을 사용하여 HDInsight의 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.

	> [AZURE.NOTE]SQL Server는 인증도 허용해야 합니다. 이 문서의 단계를 완료하려면 SQL 서버 로그인을 사용해야 합니다.

	<table border="1">
<tr><th>SQL Server 데이터베이스 속성</th><th>Azure PowerShell 변수 이름</th><th>값</th><th>설명</th></tr>
<tr><td>SQL Server 이름</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop에서 데이터를 내보내거나 가져올 SQL Server입니다. </td></tr>
<tr><td>SQL Server 로그인 이름</td><td>$sqlDatabaseLogin</td><td></td><td>SQL Server에 대한 로그인 이름입니다.</td></tr>
<tr><td>SQL Server 로그인 암호</td><td>$sqlDatabasePassword</td><td></td><td>SQL Server에 대한 로그인 암호입니다.</td></tr>
<tr><td>SQL Server 데이터베이스 이름</td><td>$sqlDatabaseName</td><td></td><td>Sqoop에서 데이터를 내보내거나 가져올 SQL Server 데이터베이스입니다. </td></tr>
</table>


> [AZURE.NOTE]이전 테이블의 채우기 값입니다. 이 자습서를 완료하는 데 유용합니다.

##시나리오 이해
HDInsight 클러스터는 일부 샘플 데이터와 함께 제공됩니다. 다음 두 예제를 사용합니다.

- */example/data/sample.log*에 있는 log4j 로그 파일. 이 파일에서 다음 로그가 추출됩니다.

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- */hive/warehouse/hivesampletable*에 있는 데이터 파일을 참조하는 *hivesampletable*이라는 이름의 Hive 테이블. 이 테이블에는 일부 모바일 장치 데이터가 포함되어 있습니다. 다음은 Hive 테이블 스키마입니다.

	<table border="1">
<tr><th>필드</th><th>데이터 형식</th></tr>
<tr><td>clientid</td><td>string</td></tr>
<tr><td>querytime</td><td>string</td></tr>
<tr><td>market</td><td>string</td></tr>
<tr><td>deviceplatform</td><td>string</td></tr>
<tr><td>devicemake</td><td>string</td></tr>
<tr><td>devicemodel</td><td>string</td></tr>
<tr><td>state</td><td>string</td></tr>
<tr><td>country</td><td>string</td></tr>
<tr><td>querydwelltime</td><td>double</td></tr>
<tr><td>sessionid</td><td>bigint</td></tr>
<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
</table>

먼저 *sample.log*와 *hivesampletable*을 Azure SQL 데이터베이스 또는 SQL Server에 내보낸 후 모바일 장치 데이터를 포함하는 테이블을 다음 경로를 통해 HDInsight에 다시 가져옵니다.

	/tutorials/usesqoop/importeddata

###HDInsight 저장소 이해

HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정 및 이 계정에서 오는 특정 Blob 저장소 컨테이너가 HDFS의 경우와 같이 기본 파일 시스템으로 지정됩니다. 프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다.

저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 이 자습서에 사용된 Windows PowerShell 스크립트를 간소화하려면 모든 파일이 */tutorials/usesqoop*에 있는 기본 파일 시스템 컨테이너에 저장되어야 합니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다. 구문은 다음과 같습니다:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.0 클러스터 이상 버전에서는 지원되지 않습니다.

> [AZURE.NOTE]*wasb://* 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

기본 파일 시스템 Blob에 저장된 파일은 다음 URI 중 아무거나 사용하여 HDInsight에서 액세스할 수 있습니다(다음 예제는 sample.log 사용).

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

저장소 계정에서 바로 파일에 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

	example/data/sample.log


##자습서 준비

Azure SQL 데이터베이스 또는 SQL Server에 테이블 두 개를 만듭니다. 이러한 테이블은 자습서 뒷부분의 Sqoop 내보내기에 사용됩니다. 또한 Sqoop에서 처리하기 전에 sample.log 파일을 처리해야 합니다.

###SQL 테이블 만들기

**Azure SQL 데이터베이스의 경우**

1. Windows PowerShell ISE를 엽니다. Windows 8 시작 화면에서 **PowerShell_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. [Windows 8 및 Windows에서 Windows PowerShell 시작][powershell-start](영문)을 참조하세요.

2. 다음 스크립트를 스크립트 창에 복사한 다음 처음 네 변수를 설정합니다.

		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하세요.

3. 다음 스크립트를 스크립트 창에 추가합니다. 이 스크립트에는 두 테이블 및 테이블의 클러스터형 인덱스를 정의하는 SQL 문이 있습니다. Azure SQL 데이터베이스에는 클러스터형 인덱스가 있어야 합니다.

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

4. 다음 스크립트를 SQL 명령을 실행하는 스크립트 창에 추가합니다.

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

5. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6. [Azure 포털][azure-management-portal]을 사용하여 테이블 및 클러스터형 인덱스를 검사합니다.

**SQL Server의 경우**

1. **SQL Server Management Studio**를 열고 SQL Server에 연결합니다.

2. **sqoopdb**라는 새 데이터베이스를 만듭니다.

3. **sqoopdb** 데이터베이스를 선택하고 SQL Server Management Studio 위쪽의 리본 메뉴에서 **새 쿼리**를 선택합니다.

4. 쿼리 창에 다음 정보를 입력합니다.

		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50),
		 [t2] [nvarchar](50),
		 [t3] [nvarchar](50),
		 [t4] [nvarchar](50),
		 [t5] [nvarchar](50),
		 [t6] [nvarchar](50),
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
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
		 [sessionpagevieworder][bigint])

5. **F5**를 클릭하거나 **을(를) 선택합니다. 실행**을 선택하여 쿼리를 실행합니다. 그러면 쿼리 아래에 다음 메시지가 표시됩니다.

		Command(s) completed successfully.

6. SQL Server Management Studio를 닫습니다.

###데이터 생성

이 자습서에서는 log4j 로그 파일(구분된 파일) 및 Hive 테이블을 Azure SQL 데이터베이스로 내보냅니다. 구분된 파일의 이름은 */example/data/sample.log*입니다. 자습서의 앞부분에 log4j 로그 샘플이 몇 개 있습니다. 로그 파일에는 일부 빈 줄과 일부 다음과 유사한 줄이 있습니다.

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

이 데이터를 사용하는 다른 예제에서는 이 줄이 있어도 관계없지만 데이터를 Azure SQL 데이터베이스 또는 SQL Server로 가져오려면 이러한 예외를 제거해야 합니다. 빈 문자열이 있거나 Azure SQL 데이터베이스 테이블에 정의된 필드 수보다 요소 수가 더 적은 줄이 있는 경우 Sqoop 내보내기는 실패합니다. log4jlogs 테이블에는 7가지 문자열 형식 필드가 있습니다.

**sample.log 파일을 처리하려면**

1. Windows PowerShell ISE를 엽니다.
2. 아래쪽 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

		Add-AzureAccount

	Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 이 구독 연결 설치 방법이 시간 초과되었으며 12시간 후에 다시 로그인해야 합니다.

	> [AZURE.NOTE]여러 Azure 구독이 있는 경우 기본 구독이 사용하려는 구독이 아니면 <strong>Select-AzureSubscription</strong> cmdlet을 사용하여 현재 구독을 선택합니다.

3. 다음 스크립트를 스크립트 창에 복사한 다음 처음 두 변수를 설정합니다.

		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	변수에 대한 자세한 내용은 이 자습서의 [필수 조건](#prerequisites) 섹션을 참조하세요.

4. 다음 스크립트를 스크립트 창에 추가합니다.

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

5. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6. 수정한 데이터 파일을 검사하려면 Azure 포털, Azure 저장소 탐색기 도구 또는 Azure PowerShell을 사용할 수 있습니다. [HDInsight 시작][hdinsight-get-started]에는 파일을 다운로드하고 그 파일의 내용을 표시하는 Azure PowerShell 사용에 관한 코드 샘플이 있습니다.


##PowerShell을 사용하여 Sqoop 내보내기 실행

이 섹션에서는 Hive 테이블과 데이터 파일을 Azure SQL 데이터베이스 또는 SQL Server에 내보내기 위해 Azure PowerShell을 사용하여 Sqoop 내보내기 명령을 실행합니다. 다음 섹션에서는 HDInsight .NET 샘플을 제공합니다.

> [AZURE.NOTE]연결 문자열 정보를 제외하면 이 섹션의 단계는 Azure SQL 데이터베이스 또는 SQL Server에 대해 작동합니다. 이러한 단계는 다음 구성을 사용하여 테스트했습니다.
>
> * **Azure 가상 네트워크 지점 및 사이트 간 구성**: 개인 데이터 센터에서 HDInsight 클러스터를 SQL Server에 연결하는 가상 네트워크입니다. 자세한 내용은 [관리 포털에서 지점 및 사이트 간 VPN 구성](http://msdn.microsoft.com/library/azure/dn133792.aspx)을 참조하세요.
> * **Azure HDInsight 3.1**: 가상 네트워크에서 클러스터를 만드는 방법에 대한 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight의 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.
> * **SQL Server 2014**: 인증을 허용하고 VPN 클라이언트 구성 패키지를 실행하여 가상 네트워크에 안전하게 연결할 수 있도록 구성됩니다.

**Log4j 로그 파일을 내보내려면**

1. Windows PowerShell ISE를 엽니다.
2. 아래쪽 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

		Add-AzureAccount

	Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3. 다음 스크립트를 스크립트 창에 복사한 다음 처음 7개의 변수를 설정합니다.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_log4j = "/tutorials/usesqoop/data"

	변수에 대한 자세한 내용은 이 자습서의 [필수 조건](#prerequisites) 섹션을 참조하세요.

	$exportDir_log4j에는 파일 이름이 지정된 sample.log 파일이 없습니다. Sqoop은 해당 폴더 아래에 있는 모든 파일의 데이터를 내보냅니다.

4. 다음 스크립트를 스크립트 창에 추가합니다.

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	필드 구분 기호는 **\\0x20**이며 공백입니다. 구분 기호는 sample.log 파일 Azure PowerShell 스크립트에 정의되어 있습니다. **-m 1**에 대해 자세히 알아보려면 [Sqoop 사용자 가이드][sqoop-user-guide-1.4.4]를 참조하세요.

5. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6. [Azure 포털][azure-management-portal]을 사용하여 내보낸 데이터를 검사합니다.

**hivesampletable Hive 테이블을 내보내려면**

1. Windows PowerShell ISE를 엽니다.
2. 아래쪽 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

		Add-AzureAccount

	Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3. 다음 스크립트를 스크립트 창에 복사한 다음 처음 7개의 변수를 설정합니다.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_mobile = "/hive/warehouse/hivesampletable"

	변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하세요.

4. 다음 스크립트를 스크립트 창에 추가합니다.

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"


		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6. [Azure 포털][azure-management-portal]을 사용하여 내보낸 데이터를 검사합니다.



##HDInsight .NET SDK를 사용하여 Sqoop 내보내기 실행

다음은 HDInsight .NET SDK를 사용하여 Sqoop 내보내기를 실행하는 C# 샘플입니다. HDInsight .NET SDK 사용에 대한 일반적인 정보를 보려면 [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]을 참조하세요.


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
	            string subscriptionID = "<AzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<AzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>";
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";

	            cmdExport = @"export";
				// Connection string for using Azure SQL Database.
				// Comment if using SQL Server
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
				// Connection string for using SQL Server.
				// Uncomment if using SQL Server
				//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
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
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
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

스크립트 파일은 Azure Blob 저장소에 있어야 합니다.




##Azure PowerShell을 사용하여 Sqoop 가져오기 실행

이 섹션에서는 Azure SQL 데이터베이스로 내보낸 log4j 로그를 HDInsight로 다시 가져옵니다.

1. Windows PowerShell ISE를 엽니다.
2. 아래쪽 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

		Add-AzureAccount

	Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3. 다음 스크립트를 스크립트 창에 복사한 다음 처음 7개의 변수를 설정합니다.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

	변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하세요.

4. 다음 스크립트를 스크립트 창에 추가합니다.

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.
6. 수정한 데이터 파일을 검사하려면 Azure 포털, Azure 저장소 탐색기 도구 또는 Azure PowerShell을 사용할 수 있습니다. [HDInsight 시작][hdinsight-get-started]에는 파일을 다운로드하고 그 파일의 내용을 표시하는 Azure PowerShell 사용에 관한 코드 샘플이 있습니다.

##다음 단계

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight와 함께 Oozie 사용][hdinsight-use-oozie]: Oozie 워크플로에서 Sqoop 작업을 사용합니다.
- [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-data]: Hive를 사용하여 비행 지연 데이터를 분석한 후 Sqoop을 사용하여 데이터를 Azure SQL 데이터베이스로 내보냅니다.
- [HDInsight에 데이터 업로드][hdinsight-upload-data]: HDInsight/Azure Blob 저장소에 데이터를 업로드하는 다른 방법을 찾습니다.




[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
 

<!---HONumber=July15_HO3-->