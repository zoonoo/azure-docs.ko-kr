<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyze flight delay data using Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

#HDInsight의 Hadoop을 사용하여 비행 지연 데이터 분석

Hive에서는 대규모 데이터의 요약, 쿼리, 분석에 적용할 수 있는 SQL 같은 스크립트 언어인 *[HiveQL][]*(영문)을 통해 Hadoop MapReduce 작업을 실행할 수 있습니다. 이 자습서에서는 Hive를 사용하여 공항의 평균 지연 시간을 계산하는 방법 및 Sqoop을 사용하여 계산 결과를 SQL 데이터베이스로 내보내는 방법을 보여 줍니다.

**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

* Azure HDInsight 클러스터. HDInsight 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 시작][] 또는 [HDInsight 클러스터 프로비전][]을 참조하세요.
* Azure PowerShell이 설치 및 구성된 워크스테이션. 자세한 내용은 [Azure PowerShell 설치 및 구성][]을 참조하세요.

**예상 완료 시간:** 30분

## 자습서 내용

* [자습서 준비][]
* [HiveQL 스크립트 만들기 및 업로드][]
* [HiveQL 스크립트 실행][]
* [Azure SQL 데이터베이스로 출력 내보내기][]
* [다음 단계][]

## <a id="prepare"></a>자습서 준비
이 자습서에서는 워크스테이션에 [Research and Innovative Technology Administration, Bureau of Transportation Statistics][](RITA)(영문)의 항공사 비행 데이터 운항정시성을 사용합니다. 다음을 수행합니다.

1. 웹 브라우저를 사용하여 워크스테이션에 RITA의 운항정시성 데이터 다운로드
2. Azure PowerShell을 사용하여 HDInsight에 데이터 업로드
3. Azure PowerShell을 사용하여 데이터 내보내기를 위한 SQL 데이터베이스 준비

**HDInsight 저장소 이해**

HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. 이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][]을 참조하세요.

HDInsight 클러스터를 프로비전하면 HDFS의 경우처럼 Blob 저장소 컨테이너가 기본 파일로 지정됩니다. 프로비전 프로세스 중에 이 컨테이너 외에도 동일한 Azure 저장소 계정 또는 다른 Azure 저장소 계정에서 컨테이너를 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전][]을 참조하세요.

이 자습서에 사용된 PowerShell 스크립트를 간소화하려면 모든 파일이 */tutorials/flightdelays*에 있는 기본 파일 시스템 컨테이너에 저장되어야 합니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.

WASB 구문은 다음과 같습니다.

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만 HDInsight 3.0 클러스터에서는 지원되지 않으며 이후 버전에서도 지원되지 않을 것입니다.

> WASB 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

기본 파일 시스템 컨테이너에 저장된 파일은 다음 URI 중 아무거나 사용하여 HDInsight에서 액세스할 수 있습니다(예제로 flightdelays.hql 사용).

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

저장소 계정에서 바로 파일에 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

    tutorials/flightdelays/flightdelays.hql

다음 표는 이 자습서에 사용된 파일을 보여 줍니다.

<table border="1">
<tr><td><strong>**파일**</strong></td><td><strong>**설명**</strong></td></tr>
<tr><td>\tutotirals\flightdelays\data</td><td> Hive의 입력 비행 데이터</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Hive의 출력</td></tr>
<tr><td>\tutorials\flightdelays\flightdelays.hql</td><td>HiveQL 스크립트 파일    </td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Hadoop 작업 상태</td></tr>
</table>

**Hive 내부 테이블 및 외부 테이블 이해**

Hive 내부 테이블 및 외부 테이블에 대해 알아야 할 몇 가지 사항이 있습니다.

- CREATE TABLE 명령은 내부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너에 위치해야 합니다.
- CREATE TABLE 명령은 데이터 파일을 /hive/warehouse/<tablename> 폴더로 이동합니다.
- CREATE EXTERNAL TABLE 명령은 외부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너 외부에 있을 수 있습니다.
- CREATE EXTERNAL TABLE 명령은 데이터 파일을 이동하지 않습니다.
- CREATE EXTERNAL TABLE 명령은 LOCATION에 어떤 폴더도 허용하지 않습니다. 이 때문에 자습서에서는 sample.log 파일의 복사본을 만듭니다.

자세한 내용은 [HDInsight: Hive 내부 및 외부 테이블 설명][cindygross-hive-tables]을 참조하세요.

> [WACOM.NOTE] HiveQL 문 중 하나는 Hive 외부 테이블을 만듭니다. Hive 외부 테이블은 데이터 파일을 원래 위치에 유지합니다. Hive 내부 테이블은 데이터 파일을 hive\\warehouse로 이동합니다. Hive 외부 테이블은 데이터 파일이 기본 파일 시스템 WASB 컨테이너에 있어야 합니다. 기본 Blob 컨테이너가 아닌 다른 컨테이너에 비행 데이터 파일을 저장하도록 선택한 경우에는 Hive 내부 테이블을 사용해야 합니다.


**비행 데이터를 다운로드하려면**

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics][](RITA)(영문)로 이동합니다.
2. 페이지에서 다음 값을 선택합니다.

	<table border="1">
	<tr><th>이름</th><th>값</th></tr>  
                                                                                                                                                                                                                                                                                                                                                                                   	<tr><td>Filter Year</td><td>2012</td></tr>                                                                                                                                                                                                                                                                                                                                                                                  
	<tr><td>Filter Period</td><td>January</td></tr>                                                                                                                                                                                                                                                                                                                                                                                    
	<tr><td>Fields:</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (다른 모든 필드는 선택하지 않음)</td></tr>
	</table>

3. **다운로드**를 클릭합니다. 각 파일을 다운로드하는 데 최대 15분 정도 소요될 수 있습니다.
4. 압축 파일을 **C:\\Tutorials\\FlightDelays\\Data** 폴더에 풉니다. 각 파일은 CSV 파일이며 크기는 60GB 정도입니다.
5. 파일 이름을 데이터가 포함된 달로 변경합니다. 예를 들어 1월 데이터가 포함된 파일의 이름은 *January.csv*가 됩니다.
6. 2012년에서 12개월 각각에 대해 파일을 다운로드하려면 2단계와 5단계를 반복합니다. 최소 하나의 파일이 있어야 자습서를 실행할 수 있습니다.

**Azure Blob 저장소에 비행 지연 데이터를 업로드하려면**

1. Azure PowerShell을 엽니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][]을 참조하세요.
2. 다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  다음과 같이 처음 3개의 변수를 설정한 후 명령을 실행합니다.

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $ContainerName = "<BlobStorageContainerName>"

        $localFolder = "C:\Tutorials\FlightDelays\Data"
        $destFolder = "tutorials/flightdelays/data"

        $month = "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    다음은 변수와 그 설명입니다.
	<table border="1">
	<tr><td><strong>변수 이름</strong></td><td><strong>설명</strong></td></tr>
	<tr><td>$subscriptionName</td><td>Azure 구독 이름입니다.</td></tr>
    <tr><td>$storageAccountName</td><td>비행 데이터 파일을 저장하는 데 사용되는 Azure 저장소 계정입니다. 기본 저장소 계정을 사용하는 것이 좋습니다.</td></tr>
	<tr><td>$containerName</td><td>비행 데이터 파일을 저장하는 데 사용되는 Azure Blob 저장소 컨테이너입니다. 기본 HDInsight 클러스터 파일 시스템 Blob 컨테이너를 사용하는 것이 좋습니다. 기본적으로 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.</td></tr>
	<tr><td>$localFolder</td><td>비행 지연 파일이 저장되는 워크스테이션의 폴더입니다.</td></tr>
	<tr><td>$destFolder</td><td>비행 지연 데이터를 업로드할 WASB 경로입니다. Hadoop(HDInsight) 경로는 대/소문자를 구분합니다.</td></tr>
	<tr><td>$month</td><td>12개의 파일을 모두 다운로드하지 못했으면 이 변수를 업데이트해야 합니다.</td></tr>
	</table>


4. 다음 명령을 실행하여 파일을 업로드하고 나열합니다.

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB        
        foreach ($item in $month){
            $fileName = "$localFolder\$item.csv"
            $blobName = "$destFolder/$item.csv"

            Write-Host "Copying $fileName to $blobName" -BackgroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder






다른 메서드를 사용하여 파일을 업로드하도록 선택한 경우에는 파일 경로가 *tutorials/flightdelays/data*여야 합니다. 파일을 액세스하는 구문은 다음과 같습니다.

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data*는 파일을 업로드했을 때 만든 가상 폴더입니다. 달마다 하나씩 12개의 파일이 있는지 확인합니다.

**SQL 데이터베이스를 준비하려면**

1. Azure PowerShell을 엽니다.
2. 다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3. 다음 스크립트에서 처음 6개의 변수를 설정한 후 명령을 실행합니다.

        # Azure subscription name
        $subscriptionName = "<AzureSubscriptionName>"

        # SQL database server variables
        $sqlDatabaseServer = ""  # specify the Azure SQL database server name if you have one created. Otherwise use "".
        $sqlDatabaseUsername = "<SQLDatabaseUserName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #For example, West US.

        # SQL database variables
        $sqlDatabaseName = ""  # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        $sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        $sqlDatabaseTableName = "AvgDelays"
        $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
            (
                [origin_city_name] ASC
            )
            )"

    다음은 변수와 그 설명입니다.
	<table border="1">
	<tr><td><strong>변수 이름</strong></td><td><strong>설명</strong></td></tr>
	<tr><td>$subscriptionName</td><td>Azure 구독 이름입니다.</td></tr>
	<tr><td>$sqlDatabaseServer</td><td>Sqoop에서 데이터를 내보내는 데 사용하는 SQL 데이터베이스 서버 이름입니다. 그대로 놔두면 스크립트가 이름을 하나 만듭니다. 그렇지 않은 경우 기존 SQL 데이터베이스나 SQL 서버를 지정합니다.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>SQL 데이터베이스/SQL 서버 사용자 이름입니다.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>SQL 데이터베이스/SQL 서버 사용자 암호입니다.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>스크립트에서 SQL 데이터베이스 서버를 만들도록 할 경우에만 사용됩니다.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Sqoop에서 데이터를 내보내는 데 사용하는 SQL 데이터베이스 이름입니다. 그대로 놔두면 스크립트가 이름을 하나 만듭니다. 그렇지 않은 경우 기존 SQL 데이터베이스나 SQL 서버를 지정합니다.</td></tr>
	<tr><td>$sqlDatabaseMaxSizeGB</td><td>스크립트에서 SQL 데이터베이스를 만들도록 할 경우에만 사용됩니다.</td></tr>
	</table>

4. 다음 명령을 실행하여 SQL 데이터베이스 서버/데이터베이스/테이블을 만듭니다.

        # Select the current Azure subscription in case there are multiple subscriptions
        Select-AzureSubscription $subscriptionName

        # create a new Azure SQL Database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
            Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
        }

        # Create a new SQL database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB

            Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
        }

        #Create AvgDelays table
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()












## <a id="createscript"></a>HiveQL 스크립트 만들기 및 업로드

Azure PowerShell을 사용하여 여러 HiveQL 문을 한 번에 하나씩 실행하거나 HiveQL 문을 스크립트 파일에 패키지할 수 있습니다. 이 자습서에서는 HiveQL 스크립트를 만듭니다. 스크립트 파일은 WASB에 업로드해야 합니다. 다음 섹션에서는 Azure PowerShell을 사용하여 스크립트 파일을 실행합니다.

HiveQL 스크립트는 다음을 수행합니다.

1. **delays_raw 테이블을 삭제합니다**. 해당 테이블이 이미 있는 경우에 삭제합니다.
2. **delays_raw 외부 Hive 테이블을 만듭니다**. 이 테이블은 비행 지연 파일이 있는 WASB 위치를 가리킵니다. 이 쿼리는 필드는 ","로 구분되고 줄은 "\\n"로 끝나도록 지정합니다. Hive가 필드 구분 기호인 쉼표와 필드 값(ORIGIN\_CITY\_NAME 및 DEST\_CITY\_NAME에 대한 필드 값의 경우)의 일부인 쉼표를 구분할 수 없기 때문에 필드 값에 쉼표가 *포함*될 경우 문제가 발생합니다. 이 문제를 해결하기 위해 쿼리는 TEMP 열을 만들어 열에 잘못 분할된 데이터를 저장합니다.
3. **delays 테이블을 삭제합니다**. 해당 테이블이 이미 있는 경우에 삭제합니다.
4. **delays 테이블을 만듭니다**. 더 처리하기 전에 데이터를 정리하는 데 도움이 됩니다. 이 쿼리는 *delays\_raw* 테이블에서 새 테이블인 *delays*를 만듭니다. TEMP 열(앞에서 언급함)은 복사되지 않으며, *substring* 함수는 해당 데이터에서 따옴표를 제거하는 데 사용됩니다.
5. **평균 날씨 지연을 계산하고 그 결과를 도시 이름별로 그룹화합니다.** 또한 결과를 WASB에 출력합니다. 이 쿼리는 데이터에서 아포스트로피를 제거하고 *weather\_delay* 값이 *null*인 행을 제외하며, 이 작업은 이 자습서의 뒷부분에서 사용되는 Sqoop이 기본적으로 이러한 값을 적절하게 처리하지 못하므로 필요합니다.





HiveQL 명령의 전체 목록을 보려면 [Hive 데이터 정의 언어][HiveQL](영문)를 참조하세요. 각 HiveQL 명령은 세미콜론으로 끝나야 합니다.


**HiveQL 스크립트 파일을 만들려면**

1. Azure PowerShell을 엽니다.
2. 다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

3. 다음과 같이 처음 두 개의 변수를 설정한 후 명령을 실행합니다.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"


    다음은 변수와 그 설명입니다.
	<table border="1">
	<tr><td><strong>변수 이름</strong></td><td><strong>설명</strong></td></tr>
	<tr><td>$storageAccountName</td><td>HiveQL 스크립트 파일을 저장하는 데 사용되는 Azure 저장소 계정입니다. 이 자습서에 제공된 PowerShell 스크립트에는 동일한 Azure 저장소 계정 및 Blob 저장소 컨테이너에 있는 스크립트 파일과 비행 데이터 파일이 둘 다 있어야 합니다</td></tr>
	<tr><td>$containerName</td><td>HiveQL 스크립트 파일을 저장하는 데 사용되는 Azure Blob 저장소 컨테이너입니다. 이 자습서에 제공된 PowerShell 스크립트에는 동일한 Azure 저장소 계정 및 Blob 저장소 컨테이너에 있는 스크립트 파일과 비행 데이터 파일이 둘 다 있어야 합니다.</td></tr>
	<tr><td>$hqlLocalFileName</td><td>WASB로 업로드되기 전 HiveQL 스크립트의 로컬 파일 이름입니다. PowerShell 스크립트를 간소화하려면 파일을 로컬로 쓴 다음 Set-AzureStorageBlobContent cmdlet을 사용하여 스크립트 파일을 HDInsight에 업로드합니다.</td></tr>
	<tr><td>$hqlBlobName</td><td>This is the script file name with path on WASB</td></tr>
	<tr><td>$srcDataFolder</td><td>WASB의 경로를 가진 스크립트 파일 이름입니다.</td></tr>
	<tr><td>$dstDataFolder </td><td>HiveQL 스크립트가 데이터를 가져오는 WASB의 폴더입니다.</td></tr>
	</table>

4. 다음 명령을 실행하여 HiveQL 문을 정의합니다.

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5. 다음 명령을 실행하여 Hive 스크립트 파일을 워크스테이션에 쓰고 WASB에 업로드합니다.

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    다음과 유사하게 출력됩니다.

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

















## <a id="executehqlscript"></a>HiveQL 스크립트 실행

Hive를 실행하는 데 사용할 수 있는 몇 가지 Azure PowerShell cmdlet이 있습니다. 이 자습서에서는 Invoke-Hive를 사용합니다. 다른 방법에 대해서는 [HDInsight에서 Hive 사용][]을 참조하세요. Invoke-Hive를 사용하여 HiveQL 문이나 HiveQL 스크립트 중 하나를 실행할 수 있습니다. 만들어서 Azure Blob 저장소에 업로드한 HiveQL 스크립트를 사용합니다.

알려진 Hive 경로 문제가 있습니다. 이 문제를 해결하기 위한 지침은 [TechNet Wiki][](영문)에서 확인할 수 있습니다.

**PowerShell을 사용하여 Hive 쿼리를 실행하려면**

1. Azure PowerShell을 엽니다.
2. 다음 명령을 실행하여 현재 디렉터리를 변경합니다.

    cd \\Tutorials\\FlightDelays\\

    Hive 출력의 복사본을 워크스테이션에 다운로드하므로 이 단계가 필요합니다. 기본적으로 PowerShell 폴더에 대한 쓰기 권한은 없습니다.

3. 다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

4. 다음과 같이 처음 3개의 변수를 설정한 후 명령을 실행합니다.

        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureBlobStorageContainerName>"
        $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
        $outputBlobName = "tutorials/flightdelays/output/000000_0"

    다음은 변수와 그 설명입니다.
    <table border="1">
	<tr><td><strong>변수 이름</strong></td><td><strong>설명</strong></td></tr>
	<tr><td>$clusterName</td><td>Hive 스크립트와 Sqoop 내보내기를 실행할 HDInsight 클러스터입니다.</td></tr>
	<tr><td>$storageAccountName</td><td>HiveQL 스크립트를 저장하는 데 사용되는 Azure 저장소 계정입니다. <a href = "#createScript">Create and upload HiveQL 스크립트 만들기 및 업로드</a>를 참조하세요.
	<tr><td>$containerName</td><td>HiveQL 스크립트를 저장하는 데 사용되는 Azure Blob 저장소 컨테이너입니다. <a href = "#createScript">Create and upload HiveQL 스크립트 만들기 및 업로드</a>를 참조하세요.
	<tr><td>$hqlScriptFile</td><td>HiveQL 스크립트 파일의 URI입니다.</td></tr>
	<tr><td>$outputBlobName</td><td>HiveQL 스크립트 출력 파일입니다. 기본 이름은 *000000\_0*입니다.</td></tr>
	</table>

5. 다음 명령을 실행하여 Hive를 호출합니다.

        Use-AzureHDInsightCluster $clusterName

        Invoke-Hive –File $hqlScriptFile

6. 다음 명령을 실행하여 결과를 확인합니다.

        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 

        cat ".\$outputBlobName" | findstr /B "Ch"

    다음과 유사하게 출력됩니다.

        Champaign/Urbana 19.023255813953487
        Charleston 24.93975903614458
        Charleston/Dunbar 16.954545454545453
        Charlotte 16.88616981831665
        Charlotte Amalie 12.88888888888889
        Charlottesville 25.444444444444443
        Chattanooga 19.883561643835616
        Cheyenne 9.875
        Chicago 16.77321649680922
        Chico 12.340909090909092
        Christiansted 12.318181818181818

    각 도시와 지연 시간 사이에는 PowerShell 출력 창에서는 보이지 않는 구분 기호가 있는데, "\001"입니다. Sqoop 내보내기를 실행할 때 이 구분 기호를 사용합니다.


## <a id="exportdata"></a>Azure SQL 데이터베이스로 Hive 작업 출력 내보내기
마지막 단계는 Sqoop 내보내기를 실행하여 데이터를 SQL 데이터베이스로 내보내는 것입니다. 자습서의 앞부분에서 SQL 데이터베이스 및 AvgDelays 테이블을 만들었습니다.

**데이터를 SQL 데이터베이스로 내보내려면**

1. Azure PowerShell을 엽니다.
2. 다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

3. 다음과 같이 처음 5개의 변수를 설정한 후 명령을 실행합니다.

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    다음은 변수와 그 설명입니다.
	<table border="1">
	<tr><td><strong>변수 이름</strong></td><td><strong>참고</strong></td></tr>
	<tr><td>$clusterName</td><td>HDInsight 클러스터 이름입니다.</td></tr>
	<tr><td>$sqlDatabaseServer</td><td>Sqoop에서 데이터를 내보낼 SQL 데이터베이스 서버입니다.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>SQL 데이터베이스 사용자 이름입니다.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>SQL 데이터베이스 사용자 암호입니다.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Sqoop에서 데이터를 내보낼 SQL 데이터베이스입니다. 기본 이름은 \*HDISqoop"입니다. </td></tr>
	<tr><td>$sqlDatabaseTableName</td><td>Sqoop에서 데이터를 내보낼 SQL 데이터베이스입니다. 기본 이름은 AvgDelays입니다. 이 테이블은 자습서의 앞부분에서 만든 테이블입니다.</td></tr>
	<tr><td>$exportDir</td><td>Hive 출력 파일 위치입니다. Sqoop은 이 위치에 있는 파일을 SQL 데이터베이스로 내보냅니다.</td></tr>
	</table>
	
4. 다음 명령을 실행하여 데이터를 내보냅니다.

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. SQL 데이터베이스에 연결하고 *AvgDelays* 테이블에서 도시별 평균 비행 지연을 확인합니다.

    ![HDI.FlightDelays.AvgDelays.Dataset][]


## <a id="nextsteps"></a> 다음 단계

이제 파일을 Blob 저장소로 업로드하는 방법, Blob 저장소의 데이터를 사용하여 Hive 테이블을 채우는 방법, Hive 쿼리를 실행하는 방법, Sqoop을 사용하여 HDFS의 데이터를 Azure SQL 데이터베이스로 내보내는 방법을 익혔습니다. 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 시작][]
* [HDInsight에서 Hive 사용][]
* [HDInsight에서 Oozie 사용][]
* [HDInsight에서 Sqoop 사용][]
* [HDInsight에서 Pig 사용][]
* [HDInsight용 Java MapReduce 프로그램 개발][]
* [HDInsight용 C# Hadoop 스트리밍 프로그램 개발][]

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [자습서 준비]: #prepare
  [HiveQL 스크립트 만들기 및 업로드]: #createscript
  [HiveQL 스크립트 실행]: #executehqlscript
  [Azure SQL 데이터베이스로 출력 내보내기]: #exportdata
  [다음 단계]: #nextsteps
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [TechNet Wiki]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [Create and upload HiveQL 스크립트 만들기 및 업로드]: #createScript
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [HDInsight에서 Oozie 사용]: ../hdinsight-use-oozie/
  [HDInsight에서 Sqoop 사용]: ../hdinsight-use-sqoop/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig/
  [HDInsight용 Java MapReduce 프로그램 개발]: ../hdinsight-develop-deploy-java-mapreduce/
  [HDInsight용 C# Hadoop 스트리밍 프로그램 개발]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
