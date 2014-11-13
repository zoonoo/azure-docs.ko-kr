<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="HDInsight의 Hadoop을 사용하여 비행 지연 데이터 분석 | Azure" metaKeywords="" description="HDInsight에 데이터를 업로드하고 Hive를 사용해 데이터를 처리하고 Sqoop을 사용하여 결과를 SQL 데이터베이스로 내보내는 방법에 대해 알아봅니다." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight의 Hadoop을 사용하여 비행 지연 데이터 분석 " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight의 Hadoop을 사용하여 비행 지연 데이터 분석

Hive에서는 대규모 데이터의 요약, 쿼리, 분석에 적용할 수 있는 SQL 스타일 스크립트 언어인 *[HiveQL][HiveQL]*을 통해 Hadoop MapReduce 작업을 실행할 수 있습니다. 이 자습서에서는 Hive를 사용하여 공항의 평균 지연 시간을 계산하는 방법 및 Sqoop을 사용하여 결과를 Azure SQL 데이터베이스로 내보내는 방법을 보여 줍니다.

HDInsight의 주요 이점 중 하나는 데이터 저장소와 계산 기능을 분리할 수 있다는 것입니다. HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. 일반적인 HDInsight 프로세스는 다음의 세 부분으로 구분할 수 있습니다.

1.  Azure Blob 저장소에 데이터 저장. 이 작업은 연속적인 프로세스일 수 있습니다. 예를 들어 날씨 데이터, 센서 데이터, 웹 로그 및 이 자습서의 경우 비행 지연 데이터를 Blob 저장소에 저장할 수 있습니다.
2.  작업 실행. 데이터를 처리해야 할 때는 PowerShell 스크립트나 클라이언트 응용 프로그램을 실행하여 HDInsight 클러스터를 프로비전하고 작업을 실행한 다음 클러스터를 삭제합니다. 작업의 출력 데이터는 Azure Blob 저장소에 저장되며 클러스터가 삭제된 후에도 그대로 유지됩니다. 따라서 사용한 데이터에 대해서만 비용을 지불하면 됩니다.
3.  Blob 저장소에서 출력을 검색하거나 이 자습서에서 설명하는 것처럼 데이터를 Azure SQL 데이터베이스로 내보냅니다.

아래 다이어그램에는 이 문서에서 설명하는 시나리오와 구조가 나와 있습니다.

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

참고: 다이어그램의 숫자는 섹션 제목에 해당합니다.

**예상 완료 시간:** 30분

## 자습서 내용

-   [필수 조건][필수 조건]
-   [신규 또는 기존 HDInsight 클러스터에서 Hive 작업 실행(M1)][신규 또는 기존 HDInsight 클러스터에서 Hive 작업 실행(M1)]
-   [Sqoop을 사용하여 Azure SQL 데이터베이스로 출력 내보내기(M2)][Sqoop을 사용하여 Azure SQL 데이터베이스로 출력 내보내기(M2)]
-   [다음 단계][다음 단계]
-   [부록 A: Azure Blob 저장소에 비행 지연 데이터 업로드(A1)][부록 A: Azure Blob 저장소에 비행 지연 데이터 업로드(A1)]
-   [부록 B: HiveQL 스크립트 만들기 및 업로드(A2)][부록 B: HiveQL 스크립트 만들기 및 업로드(A2)]

## <span id="prerequisite"></span></a>필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure PowerShell이 설치 및 구성된 워크스테이션. 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요.
-   Azure 구독. \*\*\*

**HDInsight 저장소 이해**

HDInsight의 Hadoop 클러스터는 데이터 저장소로 Azure Blob 저장소를 사용합니다. 이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에서 구현되는 Microsoft의 *HDFS*입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

HDInsight 클러스터를 프로비전하면 HDFS의 경우처럼 Azure 저장소 계정의 Blob 저장소 컨테이너가 기본 파일 시스템으로 지정됩니다. 이 저장소 계정을 *기본 저장소 계정*이라고 하며 Blob 컨테이너를 *기본 Blob 컨테이너* 또는 *기본 컨테이너*라고 합니다. 기본 저장소 계정은 HDInsight 클러스터와 동일한 데이터 센터에 있어야 합니다. HDInsight 클러스터를 삭제해도 기본 컨테이너나 기본 저장소 계정은 삭제되지 않습니다.

기본 저장소 계정 외의 다른 Azure 저장소 계정도 프로비전 프로세스 중에 HDInsight 클러스터에 바인딩할 수 있습니다. 이 바인딩을 통해 저장소 계정 및 저장소 계정 키를 구성 파일에 추가합니다. 그러면 클러스터가 런타임에 해당 저장소 계정에 액세스할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight에서 Hadoop 클러스터 프로비전][HDInsight에서 Hadoop 클러스터 프로비전]을 참조하세요.

WASB 구문은 다음과 같습니다.

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] WASB 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

기본 컨테이너에 저장된 파일은 HDInsight에서 다음 URI를 사용하여 액세스할 수 있습니다(flightdelays.hql을 예로 사용함).

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

저장소 계정에서 파일에 직접 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

    tutorials/flightdelays/flightdelays.hql

Blob 이름 앞에는 "/"가 없습니다.

**이 자습서에서 사용하는 파일**

다음 표는 이 자습서에 사용된 파일을 보여 줍니다.

| 파일                                                                      | 설명                                                                            |
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | 실행할 Hive 작업에 필요한 HiveQL 스크립트 파일입니다.                           |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Hive 작업의 입력 데이터입니다.                                                  |
| \\tutorials\\flightdelays\\output                                         | Hive 작업의 출력 경로입니다. 기본 컨테이너를 사용하여 출력 데이터를 저장합니다. |
| \\tutorials\\flightdelays\\jobstatus                                      | Hive 작업 상태 폴더입니다.                                                      |

이 자습서에서는 [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics](RITA)의 항공사 비행 데이터 운항정시성을 사용합니다. 데이터는 공용 Blob 액세스 권한을 사용하여 Azure Blob 저장소 컨테이너에 업로드되었습니다. 이처럼 공용 Blob 컨테이너를 사용하므로 이 저장소 계정을 HDInsight 클러스터에 바인딩하지 않아도 됩니다. HiveQL 스크립트도 같은 Blob 컨테이너에 업로드합니다. 자체 저장소 계정으로 데이터를 가져오고 업로드하는 방법과 HiveQL 스크립트 파일을 만들고 업로드하는 방법을 알아보려면 [부록][부록]을 참조하세요.

**Hive 내부 테이블 및 외부 테이블 이해**

Hive 내부 테이블 및 외부 테이블에 대해 알아야 할 몇 가지 사항이 있습니다.

-   CREATE TABLE 명령은 내부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너에 위치해야 합니다.
-   CREATE TABLE 명령은 데이터 파일을 /hive/warehouse/<tablename> 폴더로 이동합니다.
-   CREATE EXTERNAL TABLE 명령은 외부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너 외부에 있을 수 있습니다.
-   CREATE EXTERNAL TABLE 명령은 데이터 파일을 이동하지 않습니다.
-   CREATE EXTERNAL TABLE 명령은 LOCATION에 어떤 폴더도 허용하지 않습니다. 이 때문에 자습서에서는 sample.log 파일의 복사본을 만듭니다.

자세한 내용은 [HDInsight: Hive 내부 및 외부 테이블 소개][HDInsight: Hive 내부 및 외부 테이블 소개](영문)를 참조하세요.

> [WACOM.NOTE] HiveQL 문 중 하나는 Hive 외부 테이블을 만듭니다. Hive 외부 테이블은 데이터 파일을 원래 위치에 유지합니다. Hive 내부 테이블은 데이터 파일을 hive\\warehouse로 이동합니다. Hive 내부 테이블에서는 데이터 파일이 기본 컨테이너에 있어야 합니다. 기본 Blob 컨테이너 외부에 저장된 데이터의 경우에는 Hive 외부 테이블을 사용해야 합니다.

## <span id="runjob"></span></a>신규 또는 기존 HDInsight 클러스터에서 Hive 작업 실행

Hadoop에서는 작업을 일괄 처리 방식으로 실행합니다. 비용면에서 가장 효율적으로 Hive 작업을 실행하는 방법은 작업에 대해 클러스터를 프로비전하고 완료된 작업은 삭제하는 것입니다. 다음 스크립트에 전체 프로세스가 나와 있습니다. HDInsight 클러스터를 프로비전하고 Hive 작업을 실행하는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 프로비전][HDInsight에서 Hadoop 클러스터 프로비전] 및 [HDInsight에서 Hive 사용][HDInsight에서 Hive 사용]을 참조하세요.

**PowerShell을 사용하여 Hive 쿼리를 실행하려면**

1.  PowerShell ISE를 엽니다.
2.  다음 스크립트를 복사하여 스크립트 창에 붙여 넣습니다.

        *** add script here

3.  **F5** 키를 눌러 스크립트를 실행합니다. 다음과 유사하게 출력됩니다.

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

    각 도시와 지연 시간 사이에는 PowerShell 출력 창에서는 보이지 않는 구분 기호가 있는데, "\\001"입니다. Sqoop 내보내기를 실행할 때 이 구분 기호를 사용합니다.

## <span id="exportdata"></span></a>Azure SQL 데이터베이스로 Hive 작업 출력 내보내기

마지막 단계는 Sqoop 내보내기를 실행하여 데이터를 SQL 데이터베이스로 내보내는 것입니다. 자습서의 앞부분에서 SQL 데이터베이스 및 AvgDelays 테이블을 만들었습니다.

**데이터를 SQL 데이터베이스로 내보내려면**

1.  Azure PowerShell을 엽니다.
2.  다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

3.  다음과 같이 처음 5개의 변수를 설정한 후 명령을 실행합니다.

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

    <tr>
    <td>
    **변수 이름**

    </td>
    <td>
    **참고**

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    HDInsight 클러스터 이름입니다.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Sqoop에서 데이터를 내보낼 SQL 데이터베이스 서버입니다.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    SQL 데이터베이스 사용자 이름입니다.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    SQL 데이터베이스 사용자 암호입니다.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Sqoop에서 데이터를 내보낼 SQL 데이터베이스입니다. 기본 이름은 \*HDISqoop"입니다.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    Sqoop에서 데이터를 내보낼 SQL 데이터베이스입니다. 기본 이름은 AvgDelays입니다. 이 테이블은 자습서의 앞부분에서 만든 테이블입니다.

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Hive 출력 파일 위치입니다. Sqoop은 이 위치에 있는 파일을 SQL 데이터베이스로 내보냅니다.

    </td>
    </tr>

    </table>
    </p>
4.  다음 명령을 실행하여 데이터를 내보냅니다.

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  SQL 데이터베이스에 연결하고 *AvgDelays* 테이블에서 도시별 평균 비행 지연을 확인합니다.

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>다음 단계

이제 파일을 Blob 저장소로 업로드하는 방법, Blob 저장소의 데이터를 사용하여 Hive 테이블을 채우는 방법, Hive 쿼리를 실행하는 방법, Sqoop을 사용하여 HDFS의 데이터를 Azure SQL 데이터베이스로 내보내는 방법을 익혔습니다. 자세한 내용은 다음 문서를 참조하세요.

-   [HDInsight 시작][HDInsight 시작]
-   [HDInsight에서 Hive 사용][HDInsight에서 Hive 사용]
-   [HDInsight에서 Oozie 사용][HDInsight에서 Oozie 사용]
-   [HDInsight에서 Sqoop 사용][HDInsight에서 Sqoop 사용]
-   [HDInsight에서 Pig 사용][HDInsight에서 Pig 사용]
-   [HDInsight용 Java MapReduce 프로그램 개발][HDInsight용 Java MapReduce 프로그램 개발]
-   [HDInsight용 C# Hadoop 스트리밍 프로그램 개발][HDInsight용 C# Hadoop 스트리밍 프로그램 개발]

## <span id="appendix-a"></span></a>부록 A - Azure Blob 저장소에 비행 지연 데이터 업로드

**비행 데이터를 다운로드하려면**

1.  [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics](RITA)(영문)로 이동합니다.
2.  페이지에서 다음 값을 선택합니다.

    | 이름          | 값                                                                                                                                                                                                                                                                                                                                                                                              |
    |---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filter Year   | 2013                                                                                                                                                                                                                                                                                                                                                                                            |
    | Filter Period | January                                                                                                                                                                                                                                                                                                                                                                                         |
    | Fields:       | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\*(다른 모든 필드는 선택하지 않음) |

3.  **다운로드**를 클릭합니다.
4.  압축 파일을 **C:\\Tutorials\\FlightDelays\\Data** 폴더에 풉니다. 각 파일은 CSV 파일이며 크기는 60GB 정도입니다.
5.  파일 이름을 데이터가 포함된 달로 변경합니다. 예를 들어 1월 데이터가 포함된 파일의 이름은 *January.csv*가 됩니다.
6.  2013년에서 12개월 각각에 대해 파일을 다운로드하려면 2단계와 5단계를 반복합니다. 최소 하나의 파일이 있어야 자습서를 실행할 수 있습니다.

**Azure Blob 저장소에 비행 지연 데이터를 업로드하려면**

1.  PowerShell ISE를 엽니다.
2.  다음 스크립트를 스크립트 창에 붙여 넣습니다.

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }

        # Validate the container
        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion

        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"

                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  **F5** 키를 눌러 스크립트를 실행합니다.

## <span id="appendix-b"></span></a>부록 B - HiveQL 스크립트 만들기 및 업로드

Azure PowerShell을 사용하여 여러 HiveQL 문을 한 번에 하나씩 실행하거나 HiveQL 문을 스크립트 파일에 패키지할 수 있습니다. 이 섹션에서는 HiveQL 스크립트를 만든 다음 PowerShell을 사용하여 Azure Blob 저장소에 업로드하는 방법을 보여 줍니다. Hive에서는 HiveQL 스크립트를 WASB에 저장해야 합니다.

HiveQL 스크립트는 다음을 수행합니다.

1.  **delays\_raw 테이블을 삭제합니다**. 해당 테이블이 이미 있는 경우에 삭제합니다.
2.  **delays\_raw 외부 Hive 테이블을 만듭니다**. 이 테이블은 비행 지연 파일이 있는 WASB 위치를 가리킵니다. 이 쿼리는 필드는 ","로 구분되고 줄은 "\\n"로 끝나도록 지정합니다. Hive가 필드 구분 기호인 쉼표와 필드 값(ORIGIN\_CITY\_NAME 및 DEST\_CITY\_NAME에 대한 필드 값의 경우)의 일부인 쉼표를 구분할 수 없기 때문에 필드 값에 쉼표가 *포함*될 경우 문제가 발생합니다. 이 문제를 해결하기 위해 쿼리는 TEMP 열을 만들어 열에 잘못 분할된 데이터를 저장합니다.
3.  **delays 테이블을 삭제합니다**. 해당 테이블이 이미 있는 경우에 삭제합니다.
4.  **delays 테이블을 만듭니다**. 더 처리하기 전에 데이터를 정리하는 데 도움이 됩니다. 이 쿼리는 *delays\_raw* 테이블에서 새 테이블인 *delays*를 만듭니다. TEMP 열(앞에서 언급함)은 복사되지 않으며, *substring* 함수는 해당 데이터에서 따옴표를 제거하는 데 사용됩니다.
5.  **평균 날씨 지연을 계산하고 그 결과를 도시 이름별로 그룹화합니다.** 또한 결과를 WASB에 출력합니다. 이 쿼리는 데이터에서 아포스트로피를 제거하고 *weather\_delay* 값이 *null*인 행을 제외하며, 이 작업은 이 자습서의 뒷부분에서 사용되는 Sqoop이 기본적으로 이러한 값을 적절하게 처리하지 못하므로 필요합니다.

HiveQL 명령의 전체 목록을 보려면 [Hive 데이터 정의 언어][HiveQL](영문)를 참조하세요. 각 HiveQL 명령은 세미콜론으로 끝나야 합니다.

**HiveQL 스크립트 파일을 만들려면**

1.  Azure PowerShell ISE를 엽니다.
2.  다음 스크립트를 복사하여 스크립트 창에 붙여 넣습니다.

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green

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

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    스크립트에서 사용되는 상수는 다음과 같습니다.

    -   **$hqlLocalFileName**: 스크립트는 HiveSQL 스크립트 파일을 WASB에 업로드하기 전에 로컬에 저장합니다. 이 상수가 해당 파일 이름입니다. 기본값은 <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>입니다.
    -   **$hqlBlobName**: Azure Blob 저장소에서 사용되는 HiveQL 스크립트 파일 Blob 이름입니다. 기본값은 <u>tutorials/flightdelays/flightdelays.hql</u>입니다. 파일은 Azure Blob 저장소에 직접 기록되므로 Blob 이름 앞에는 "/"가 없습니다. WASB에서 파일에 액세스하려면 파일 이름 앞에 "/"를 추가해야 합니다.
    -   **$srcDataFolder** 및 **$dstDataFolder**: = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

4.  다음과 같이 처음 두 개의 변수를 설정한 후 명령을 실행합니다.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    다음은 변수와 그 설명입니다.

    <table border="1">

    <tr>
    <td>
    **변수 이름**

    </td>
    <td>
    **설명**

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    HiveQL 스크립트 파일을 저장하는 데 사용되는 Azure 저장소 계정입니다. 이 자습서에 제공된 PowerShell 스크립트에는 동일한 Azure 저장소 계정 및 Blob 저장소 컨테이너에 있는 스크립트 파일과 비행 데이터 파일이 둘 다 있어야 합니다.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    HiveQL 스크립트 파일을 저장하는 데 사용되는 Azure Blob 저장소 컨테이너입니다. 이 자습서에 제공된 PowerShell 스크립트에는 동일한 Azure 저장소 계정 및 Blob 저장소 컨테이너에 있는 스크립트 파일과 비행 데이터 파일이 둘 다 있어야 합니다.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    WASB로 업로드되기 전 HiveQL 스크립트의 로컬 파일 이름입니다. PowerShell 스크립트를 간소화하려면 파일을 로컬로 쓴 다음 Set-AzureStorageBlobContent cmdlet을 사용하여 스크립트 파일을 HDInsight에 업로드합니다.

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    WASB의 경로를 가진 스크립트 파일 이름입니다.

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    HiveQL 스크립트가 데이터를 가져오는 WASB의 폴더입니다.

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    HiveQL 스크립트가 출력을 내보내는 WASB의 폴더입니다. 자습서의 뒷부분에서 Sqoop을 사용하여 이 폴더의 데이터를 Azure SQL 데이터베이스로 내보냅니다.

    </td>
    </tr>

    </table>
    </p>
5.  다음 명령을 실행하여 HiveQL 문을 정의합니다.

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

6.  다음 명령을 실행하여 Hive 스크립트 파일을 워크스테이션에 쓰고 WASB에 업로드합니다.

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

## SQL 데이터베이스 준비

**SQL 데이터베이스를 준비하려면(이 작업을 Sqoop 스크립트와 통합)**

1.  PowerShell ISE를 엽니다.
2.  다음 스크립트를 복사하여 스크립트 창에 붙여 넣습니다.

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"

        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                    [origin_city_name] ASC
                )
                )"

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
            $sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress   
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
        }
        else
        {
            $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
            if (! $dbServer)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green

            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 

            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
        }
        else
        {
            $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
            if (! $db)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    > WACOM.NOTE 스크립트는 REST 서비스 http://bot.whatismyipaddress.com을 사용하여 외부 IP 주소를 검색합니다. IP 주소는 SQL 데이터베이스 서버용 방화벽 규칙을 만드는 데 사용됩니다.

    스크립트에서 사용되는 몇 가지 상수는 다음과 같습니다.

    -   **$ipAddressRestService**: 기본값은 <u>http://bot.whatismyipaddress.com</u>이며, 외부 IP 주소를 가져오기 위한 공용 IP 주소 REST 서비스입니다. 원하는 경우 다른 서비스를 사용할 수 있습니다. 서비스를 사용하여 검색하는 외부 IP 주소를 사용하여 Azure SQL 데이터베이스 서버용 방화벽 규칙을 만듭니다. 따라서 PowerShell 스크립트를 사용하여 워크스테이션에서 데이터베이스에 액세스할 수 있습니다.
    -   **$fireWallRuleName**: Azure SQL 데이터베이스 서버 방화벽 규칙 이름입니다. 기본 이름은 <u>FlightDelay</u>입니다. 원하는 경우 이름을 바꿀 수 있습니다.
    -   **$sqlDatabaseMaxSizeGB**: 이 값은 새 Azure SQL 데이터베이스 서버를 만들 때만 사용됩니다. 기본 크기는 <u>10GB</u>입니다. 이 자습서에서는 크기가 10GB이면 충분합니다.
    -   **$sqlDatabaseName**: 이 값은 새 Azure SQL 데이터베이스를 만들 때만 사용됩니다. 기본값은 <u>HDISqoop</u>입니다. 이 상수의 이름을 바꾸는 경우에는 Sqoop PowerShell 스크립트도 그에 따라 업데이트해야 합니다.

3.  **F5** 키를 눌러 스크립트를 실행합니다. Azure 구독 자격 증명과 다음 값을 입력해야 합니다.

    -   **sqlDatabaseServer**: Hive 출력을 내보낼 Azure SQL 데이터베이스 서버 이름을 입력합니다. 데이터베이스 서버를 만들려면 아무 내용도 입력하지 않습니다.
    -   **sqlDatabaseUsername**: 데이터베이스 로그인을 입력합니다. 새 데이터베이스 서버를 만들지 아니면 기존 데이터베이스 서버를 사용할지를 지정해야 합니다.
    -   **sqlDatabasePassword**: 데이터베이스 로그인 암호를 입력합니다. 새 데이터베이스 서버를 만들지 아니면 기존 데이터베이스 서버를 사용할지를 지정해야 합니다.
    -   **sqlDatabaseLocation**: 하위 지역을 입력합니다. 새 데이터베이스 서버를 만들려는 경우에만 사용됩니다.
    -   **sqlDatabaseName**: Azure SQL 데이터베이스 이름을 입력합니다. 데이터베이스를 만들려면 아무 내용도 입력하지 않습니다. 기본 데이터베이스 이름은 **HDISqoop**입니다.

4.  스크립트 출력의 유효성을 검사합니다. 스크립트가 정상적으로 실행되었는지 확인합니다.

다른 메서드를 사용하여 파일을 업로드하도록 선택한 경우에는 파일 경로가 *tutorials/flightdelays/data*여야 합니다. 파일을 액세스하는 구문은 다음과 같습니다.

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data*는 파일을 업로드했을 때 만든 가상 폴더입니다. 달마다 하나씩 12개의 파일이 있는지 확인합니다.

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [필수 조건]: #prerequisite
  [신규 또는 기존 HDInsight 클러스터에서 Hive 작업 실행(M1)]: #runjob
  [Sqoop을 사용하여 Azure SQL 데이터베이스로 출력 내보내기(M2)]: #exportdata
  [다음 단계]: #nextsteps
  [부록 A: Azure Blob 저장소에 비행 지연 데이터 업로드(A1)]: #appdendix-a
  [부록 B: HiveQL 스크립트 만들기 및 업로드(A2)]: #appendix-b
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [HDInsight에서 Hadoop 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [부록]: #appendix
  [HDInsight: Hive 내부 및 외부 테이블 소개]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight에서 Oozie 사용]: ../hdinsight-use-oozie/
  [HDInsight에서 Sqoop 사용]: ../hdinsight-use-sqoop/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig/
  [HDInsight용 Java MapReduce 프로그램 개발]: ../hdinsight-develop-deploy-java-mapreduce/
  [HDInsight용 C# Hadoop 스트리밍 프로그램 개발]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
