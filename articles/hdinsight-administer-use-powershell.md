<properties linkid="manage-services-hdinsight-administer-hdinsight-using-powershell" urlDisplayName="HDInsight Administration" pageTitle="Administer HDInsight using PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to perform administrative tasks for the HDInsight clusters using PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer HDInsight using PowerShell" authors="bradsev" />

PowerShell을 사용하여 HDInsight 관리
====================================

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 문서에서는 Windows PowerShell을 통해 로컬 Azure PowerShell 콘솔을 사용하여 HDInsight 클러스터를 관리하는 방법을 알아보겠습니다. HDInsight PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx)를 참조하십시오.

**필수 조건:**

이 문서를 시작하기 전에 다음이 있어야 합니다.

-   Azure 구독. Azure는 구독 기반 플랫폼입니다. HDInsight PowerShell cmdlet은 구독을 사용하여 작업을 수행합니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션](https://www.windowsazure.com/en-us/pricing/purchase-options/), [구성원 제공 항목](https://www.windowsazure.com/en-us/pricing/member-offers/) 또는 [무료 평가판](https://www.windowsazure.com/en-us/pricing/free-trial/)을 참조하십시오.

-   Azure PowerShell이 포함된 워크스테이션. 자세한 내용은 [Azure PowerShell 설치 및 구성](/en-us/documentation/articles/install-configure-powershell/)을 참조하십시오.

이 문서의 내용
--------------

-   [클러스터 프로비전](#provision)
-   [클러스터 나열 및 표시](#listshow)
-   [클러스터 삭제](#delete)
-   [HTTP 서비스 액세스 권한 부여/해지](#httpservices)
-   [MapReduce 작업 제출](#mapreduce)
-   [Hive 작업 제출](#hive)
-   [Blob 저장소에 데이터 업로드](#upload)
-   [Blob 저장소에서 MapReduce 출력 데이터 다운로드](#download)

HDInsight 클러스터 프로비전
---------------------------

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정 및 저장소 컨테이너가 필요합니다.

**Azure 저장소 계정을 만들려면**

게시 설정 파일을 가져온 후 다음 명령을 사용하여 저장소 계정을 만들 수 있습니다.

    # Create an Azure storage account
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # For example, "West US"

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] 저장소 계정은 HDInsight 클러스터와 동일한 데이터 센터에 있어야 합니다. 현재 다음 데이터 센터에서만 HDInsight 클러스터를 프로비전할 수 있습니다.

><ul>
<li>동남아시아</li>
<li>북유럽</li>
<li>서유럽</li>
<li>미국 동부</li>
<li>미국 서부</li>
</ul>

관리 포털을 사용하여 Azure 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정을 만드는 방법](/en-us/manage/services/storage/how-to-create-a-storage-account/)을 참조하십시오.

저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 명령을 사용하여 정보를 검색할 수 있습니다.

    # List storage accounts for the current subscription
    Get-AzureStorageAccount
    # List the keys for a storage account
    Get-AzureStorageKey <StorageAccountName>

관리 포털을 사용하여 정보를 가져오는 방법에 대한 자세한 내용은 *방법: 저장소 액세스 키 보기, 복사 및 다시 생성* 섹션([저장소 계정을 관리하는 방법](/en-us/manage/services/storage/how-to-manage-a-storage-account/))을 참조하십시오.

**Azure 저장소 컨테이너를 만들려면**

PowerShell은 HDInsight 프로비전 중 Blob 컨테이너를 만들 수 없습니다. 다음 스크립트를 사용하여 컨테이너를 만들 수 있습니다.

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Create a storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext

**클러스터를 프로비전하려면**

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Get the storage account key
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Create a new HDInsight cluster
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

다음 스크린샷은 스크립트 실행을 보여 줍니다.

![HDI.PS.Provision](./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png)

클러스터 세부 정보 나열 및 표시
-------------------------------

클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

**현재 구독의 클러스터를 모두 나열하려면**

    Get-AzureHDInsightCluster 

**현재 구독의 특정 클러스터 세부 정보를 표시하려면**

    Get-AzureHDInsightCluster -Name <ClusterName> 

클러스터 삭제
-------------

클러스터를 삭제하려면 다음 명령을 사용합니다.

    Remove-AzureHDInsightCluster -Name <ClusterName> 

HTTP 서비스 액세스 권한 부여/해지
---------------------------------

HDInsight 클러스터에는 다음과 같은 HTTP 웹 서비스가 있습니다(이러한 모든 서비스에 RESTful 끝점이 있음).

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

이러한 서비스에는 기본적으로 액세스 권한이 부여됩니다. 액세스 권한을 해지/부여할 수 있습니다. 샘플은 다음과 같습니다.

    Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

샘플에서 *hdiv2*는 HDInsight 클러스터 이름입니다.

> [WACOM.NOTE] 액세스 권한을 부여/해지하여 클러스터 사용자 이름 및 암호를 다시 설정합니다.

Azure 관리 포털을 사용하여 이 작업을 수행할 수도 있습니다. [관리 포털을 사용하여 HDInsight 관리](/en-us/documentation/articles/hdinsight-administer-use-management-portal/)를 참조하십시오.

MapReduce 작업 제출
-------------------

HDInsight 클러스터 배포는 몇 가지 MapReduce 샘플과 함께 제공됩니다. 샘플 중 하나는 소스 파일에서 단어의 빈도수를 계산하는 것입니다.

**MapReduce 작업을 제출하려면**

다음 PowerShell 스크립트는 단어 수 샘플 작업을 제출합니다.

    $clusterName = "<HDInsightClusterName>"            

    # Define the MapReduce job
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Run the job and show the standard error 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] *hadoop-examples.jar*는 버전 2.1 HDInsight 클러스터와 함께 제공됩니다. 버전 3.0 HDInsight 클러스터에서는 파일 이름이 *hadoop-mapreduce.jar*로 바뀌었습니다.

WASB 접두사에 대한 자세한 내용은 [Use Azure Blob storage for HDInsight][hdinsight- storage]을 참조하십시오.

**MapReduce 작업 출력을 다운로드하려면**

다음 PowerShell 스크립트는 마지막 절차의 MapReduce 작업 출력을 검색합니다.

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Create the storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Download the output to local computer
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

MapReduce 작업 개발 및 실행에 대한 자세한 내용은 [HDInsight와 함께 MapReduce 사용](/en-us/documentation/articles/hdinsight-use-mapreduce/)을 참조하십시오.

Hive 작업 제출
--------------

HDInsight 클러스터 배포는 *hivesampletable*이라는 샘플 Hive 테이블과 함께 제공됩니다. HiveQL "show tables;"을 사용하여 클러스터의 Hive 테이블을 나열할 수 있습니다.

**Hive 작업을 제출하려면**

다음 스크립트는 Hive 테이블을 나열하는 hive 작업을 제출합니다.

    $clusterName = "<HDInsightClusterName>"               

    # HiveQL query
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
    "@

    Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

Hive 작업은 먼저 클러스터에 생성된 Hive 테이블을 표시한 후 hivesampletable에서 반환된 데이터를 표시합니다.

Hive 사용에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용](/en-us/documentation/articles/hdinsight-use-hive/)을 참조하십시오.

Blob 저장소에 데이터 업로드
---------------------------

[HDInsight에 데이터 업로드](/en-us/documentation/articles/hdinsight-upload-data/)를 참조하십시오.

Blob 저장소에서 MapReduce 출력 다운로드
---------------------------------------

이 문서의 [MapReduce 작업 제출](#mapreduce) 세션을 참조하십시오.

참고 항목
---------

-   [HDInsight Cmdlet 참조 문서](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx)
-   [관리 포털을 사용하여 HDInsight 관리](/en-us/documentation/articles/hdinsight-administer-use-management-portal/)
-   [명령줄 인터페이스를 사용하여 HDInsight 관리](/en-us/documentation/articles/hdinsight-administer-use-command-line/)
-   [HDInsight 클러스터 프로비전](/en-us/documentation/articles/hdinsight-provision-clusters/)
-   [HDInsight에 데이터 업로드](/en-us/documentation/articles/hdinsight-upload-data/)
-   [프로그래밍 방식으로 Hadoop 작업 제출](/en-us/documentation/articles/hdinsight-submit-hadoop-jobs-programmatically/)
-   [Azure HDInsight 시작](/en-us/documentation/articles/hdinsight-get-started/)

