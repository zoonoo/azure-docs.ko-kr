<properties  linkid="manage-services-hdinsight-submit-hadoop-jobs-programmatically" urlDisplayName="HDInsight Administration" pageTitle="Submit Hadoop jobs programmatically | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Learn how to programmatically submit Hadoop jobs to Azure HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit Hadoop jobs programmatically" authors="jgao" />

# 프로그래밍 방식으로 Hadoop 작업 제출

이 문서에서는 PowerShell과 HDInsight .NET SDK를 사용하여 MapReduce 및 Hive 작업을 제출하는
방법에 대해 설명합니다.

**필수 조건:**

이 문서를 시작하기 전에 다음이 있어야 합니다.

* Azure HDInsight 클러스터를 만듭니다. 자세한 내용은 [HDInsight
  시작](/en-us/manage/services/hdinsight/get-started-hdinsight/) 또는
  [HDInsight 클러스터
  프로비전](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)을
  참조하십시오.
* Azure PowerShell을 설치하고 구성합니다. 자세한 내용은 [Azure PowerShell 설치 및
  구성](/en-us/documentation/articles/install-configure-powershell/)을
  참조하십시오.

## 이 문서에서는 다음을 수행합니다.

* [PowerShell을 사용하여 MapReduce 작업 제출](#mapreduce-powershell)
* [PowerShell을 사용하여 Hive 작업 제출](#hive-powershell)
* [PowerShell을 사용하여 Sqoop 작업 제출](#sqoop-powershell)
* [HDInsight .NET SDK를 사용하여 MapReduce 작업 제출](#mapreduce-sdk)
* [HDInsight .NET SDK를 사용하여 Hive 작업 제출](#hive-sdk)
* [다음 단계](#nextsteps)

## <a id="mapreduce-powershell" ></a> PowerShell을 사용하여 MapReduce 작업 제출

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅
환경입니다. HDInsight와 함께 PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여
HDInsight
관리](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)를
참조하십시오.

Hadoop MapReduce는 방대한 양의 데이터를 처리하는 응용 프로그램을 작성하기 위한 소프트웨어 프레임워크입니다. HDInsight 클러스터는 여러 MapReduce 예제가 포함된 jar
파일(*\example\jars\hadoop-examples.jar*)과 함께 제공됩니다. 버전 3.0 HDInsight
클러스터에서는 파일 이름이 hadoop-mapreduce-examples.jar로 변경되었습니다. 예제 중 하나는 소스 파일에서
단어의 빈도수를 계산하는 것입니다. 이 세션에서는 단어 개수 샘플을 실행하기 위해 워크스테이션에서 PowerShell을 사용하는
방법에 대해 알아봅니다. MapReduce 작업의 개발 및 실행에 대한 자세한 내용은 [HDInsight와 함께 MapReduce
사용](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)을
참조하십시오.

**PowerShell을 사용하여 단어 개수 MapReduce 프로그램을 실행하려면**

1.  **Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은
    [Azure PowerShell 설치 및
    구성](/en-us/documentation/articles/install-configure-powershell/)을
    참조하십시오.

2.  다음 PowerShell 명령을 실행하여 두 변수를 설정합니다.
    
         $subscriptionName = "<SubscriptionName>"   
         $clusterName = "<HDInsightClusterName>"    
    
    구독(subscription)은 HDInsight 클러스터를 만드는 데 사용한 것입니다. HDInsight 클러스터는
    MapReduce 작업 실행에 사용하려는 것입니다.

3.  다음 명령을 실행하여 MapReduce 작업 정의를 만듭니다.

         # Define the word count MapReduce job
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
    
    두 개의 인수가 있습니다. 첫 번째 인수는 원본 파일 이름이고, 두 번째 인수는 출력 파일 경로입니다. wasb 접두사에
    대한 자세한 내용은 [HDInsight와 함께 Azure Blob 저장소
    사용](/en-us/manage/services/hdinsight/howto-blob-store/)을 참조하십시오.

4.  다음 명령을 실행하여 MapReduce 작업을 실행합니다.

         # Submit the MapReduce job
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
    
    MapReduce 작업 정의뿐 아니라 MapReduce 작업을 실행하려는 HDInsight 클러스터 이름도 제공해야
    합니다.

5.  다음 명령을 실행하여 MapReduce 작업 완료를 확인합니다.

         # Wait for the job to complete
         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  다음 명령을 실행하여 MapReduce 작업 실행 오류를 확인합니다.

         # Get the job standard error output
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
    
    다음 스크린샷은 성공한 실행 출력을 보여 줍니다. 그렇지 않으면 오류 메시지가 표시됩니다.
    
    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png)

**MapReduce 작업 결과를 검색하려면**

1.  **Azure PowerShell**을 엽니다. 2.  다음 PowerShell 명령을 실행하여 세 변수를 설정합니다.
    
         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"
         $containerName = "<ContainerName>"			
    
    Azure 저장소 계정은 HDInsight 클러스터 프로비전 중에 지정한 것입니다. 저장소 계정은 기본 HDInsight
    클러스터 파일 시스템으로 사용되는 Blob 컨테이너를 호스트하는 데 사용됩니다. 일반적으로 Blob 저장소 컨테이너 이름은
    클러스터를 프로비전할 때 다른 이름을 지정하지 않을 경우 HDInsight 클러스터와 동일한 이름을 공유합니다.

3.  다음 명령을 실행하여 Azure 저장소 컨텍스트 개체를 만듭니다.

         # Create the storage account context object
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Select-AzureSubscription은 여러 구독이 있을 경우 현재 구독을 설정하는 데 사용되며, 기본 구독은
    사용할 구독이 아닙니다.

4.  다음 명령을 실행하여 Blob 컨테이너의 MapReduce 작업 출력을 워크스테이션으로 다운로드합니다.

         # Get the blob content
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
    
    *example/data/WordCountOutput* 폴더는 MapReduce 작업을 실행할 때 지정된 출력 폴더입니다.
    *part-r-00000*은 MapReduce 작업 출력의 기본 파일 이름입니다. 로컬 폴더의 동일한 폴더 구조에 파일이
    다운로드됩니다. 예를 들어 다음 스크린샷에서 현재 폴더는 C 루트 폴더입니다.
    *C:\example\data\WordCountOutput* 폴더에 파일이 다운로드됩니다.

5.  다음 명령을 실행하여 MapReduce 작업 출력 파일을 인쇄합니다.
    
         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    
    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png)
    
    MapReduce 작업에서 단어와 개수를 사용하여 *part-r-00000*이라는 파일을 생성합니다. 스크립트는
    findstr 명령을 사용하여 "there"가 포함된 모든 단어를 나열합니다.

> [WACOM.NOTE] MapReduce 작업의 여러 줄 출력인
> ./example/data/WordCountOutput/part-r-00000을 메모장에서 열면 줄 바꿈이 제대로 렌더링되지
> 않는 것을 확인할 수 있습니다. 예상된 동작입니다.

## <a id="hive-powershell" ></a> PowerShell을 사용하여 Hive 작업 제출

Apache [Hive][1]는 대규모 데이터의 요약, 쿼리 및 분석에 적용할 수 있는 SQL 같은 스크립팅 언어인
*HiveQL*을 통해 MapReduce 작업을 실행하는 수단을 제공합니다.

HDInsight 클러스터는 *hivesampletable*이라는 샘플 Hive 테이블과 함께 제공됩니다. 이 세션에서는 PowerShell을 사용하여 Hive 테이블의 일부 데이터를 나열하기 위한 Hive 작업을 실행합니다.

**PowerShell을 사용하여 Hive 작업을 실행하려면**

1.  **Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은
    [Azure PowerShell 설치 및
    구성](/en-us/documentation/articles/install-configure-powershell/)을
    참조하십시오.

2.  다음 명령에서 처음 2개 변수를 설정한 후 명령을 실행합니다.
    
         $subscriptionName = "<SubscriptionName>"   
         $clusterName = "<HDInsightClusterName>"             
         $querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"
    
    $querystring은 HiveQL 쿼리입니다.

3.  다음 명령을 실행하여 Hive 작업을 실행할 Azure 구독과 클러스터를 선택합니다.
    
         Select-AzureSubscription -SubscriptionName $subscriptionName

4.  Hive 작업을 제출합니다.
    
         Use-AzureHDInsightCluster $clusterName
         Invoke-Hive -Query $queryString
    
    -File 스위치를 사용하여 HDFS에서 HiveQL 스크립트 파일을 지정할 수 있습니다.

Hive에 대한 자세한 내용은 [HDInsight와 함께 Hive
사용](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)을
참조하십시오.

## <a  ></a>PowerShell을 사용하여 Sqoop 작업 제출

[HDInsight와 함께 Sqoop 사용](../hdinsight-use-sqoop/)을 참조하십시오.

## <a id="mapreduce-sdk" ></a> HDInsight .NET SDK를 사용하여 MapReduce 작업 제출

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트
라이브러리를 제공합니다. HDInsight 클러스터는 여러 MapReduce 예제가 포함된 jar
파일(*\example\jars\hadoop-examples.jar*)과 함께 제공됩니다. 예제 중 하나는 소스 파일에서 단어의
빈도수를 계산하는 것입니다. 이 세션에서는 단어 개수 샘플을 실행하는 .NET 응용 프로그램을 만드는 방법에 대해 알아봅니다.
MapReduce 작업의 개발 및 실행에 대한 자세한 내용은 [HDInsight와 함께 MapReduce
사용](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)을
참조하십시오.

SDK를 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

* HDInsight .NET SDK 설치
* 콘솔 응용 프로그램 만들기
* 응용 프로그램 실행

**HDInsight .NET SDK를 설치하려면** [NuGet][2]에서 가장 최근에 게시된 SDK 빌드를 설치할 수
있습니다. 지침은 다음 절차에서 설명합니다.

**Visual Studio 콘솔 응용 프로그램을 만들려면**

1.  Visual Studio 2012를 엽니다.

2.  파일 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

3.  새 프로젝트에서 다음 값을 입력하거나 선택합니다.
    
    <table  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
     <tr>
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">속성</th>
    
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">값</th>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td>
    </tr>
    
     </table>

4.  **확인**을 클릭하여 프로젝트를 만듭니다.

5.  **도구** 메뉴에서 **라이브러리 패키지 관리자**를 클릭한 다음 **패키지 관리자 콘솔**을 클릭합니다.

6.  콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.
    
         Install-Package Microsoft.WindowsAzure.Management.HDInsight
    
    .NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다. 버전은 0.11.0.1
    이상이어야 합니다.

7.  솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.

8.  파일 맨 위에 다음 using 문을 추가합니다.
    
         using System.IO;
         using System.Threading;
         using System.Security.Cryptography.X509Certificates;
        	
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Blob;
        	
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.Hadoop.Client;

9.  클래스에 다음의 함수 정의를 추가합니다. 이 함수는 Hadoop 작업의 완료를 기다리는 데 사용됩니다.
    
         private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
         {
             JobDetails jobInProgress = client.GetJob(jobResults.JobId);
             while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
             {
                 jobInProgress = client.GetJob(jobInProgress.JobId);
                 Thread.Sleep(TimeSpan.FromSeconds(10));
             }
         }

10. Main() 함수에서 다음 코드를 복사하여 붙여넣습니다.
    
        // Set the variables
        string subscriptionID = "<Azure  subscription ID>";
        string certFriendlyName = "<certificate  friendly name>";
        
        string clusterName = "<HDInsight  cluster name>";
        	
        string storageAccountName = "<Azure  storage account name>";
        string storageAccountKey = "<Azure  storage account key>";
        string containerName = "<Blob  container name>";
    
    이러한 모든 변수를 프로그램에 대해 설정해야 합니다. [Azure 관리 포털][3]에서 Azure 구독 이름을 가져올 수
    있습니다.
    
    인증서에 대한 정보는 [Azure용 관리 인증서 만들기 및 업로드][4]를 참조하십시오. 인증서를 구성하는 손쉬운 방법은
    *Get-AzurePublishSettingsFile* 및 *Import-AzurePublishSettingsFile*
    PowerShell cmdlet을 실행하는 것입니다. 그러면 관리 인증서가 자동으로 생성되어 업로드됩니다.
    PowerShell cmdlet을 실행한 후 워크스테이션에서 *certmgr.msc*를 열고,
    *Personal/Certificates*를 확장하여 인증서를 찾을 수 있습니다. PowerShell cmdlet으로 만든
    인증서에는 *발급 대상* 및 *발급자* 필드 모두에 사용할 수 있는 *Azure Tools*가 있습니다.
    
    Azure 저장소 계정 이름은 HDInsight 클러스터를 프로비전할 때 지정하는 계정입니다. 기본 컨테이너 이름은
    HDInsight 클러스터 이름과 동일합니다.

11. Main() 함수에서 다음 코드를 추가하여 MapReduce 작업을 정의합니다.
    
        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };
        
        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");
    
    두 개의 인수가 있습니다. 첫 번째 인수는 원본 파일 이름이고, 두 번째 인수는 출력 파일 경로입니다. wasb 접두사에
    대한 자세한 내용은 [HDInsight와 함께 Azure Blob 저장소
    사용](/en-us/manage/services/hdinsight/howto-blob-store/)을 참조하십시오.

12. Main() 함수에서 다음 코드를 추가하여 JobSubmissionCertificateCredential 개체를 만듭니다.
    
         // Get the certificate object from certificate store using the
         friendly name to identify it X509Store store = new X509Store();
         store.Open(OpenFlags.ReadOnly); X509Certificate2 cert =
         store.Certificates.Cast<x509certificate2
         ().First(item => item.FriendlyName ==
         certFriendlyName); JobSubmissionCertificateCredential creds = new
         JobSubmissionCertificateCredential(new Guid(subscriptionID), cert,
         clusterName);</x509certificate2>

13. Main() 함수에서 다음 코드를 추가하여 작업을 실행하고 작업이 완료되기를 기다립니다.
    
        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        
        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);
        
        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Main() 함수에서 다음 코드를 추가하여 MapReduce 작업 출력을 인쇄합니다.
    
        // Print the MapReduce job output
        Stream stream = new MemoryStream();
        	
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");
        	
        blockBlob.DownloadToStream(stream);
        stream.Position = 0;
        	
        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());
        	
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();
    
    MapReduce 작업을 정의할 때 출력 폴더가 지정됩니다. 기본 파일 이름은 *part-r-00000*입니다.

**응용 프로그램을 실행하려면**

Visual Studio에 응용 프로그램이 열려 있을 때 **F5**를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용
프로그램의 상태 및 응용 프로그램 출력이 표시됩니다.

## <a id="hive-sdk" ></a> HDInsight .NET SDK를 사용하여 Hive 작업 제출

HDInsight 클러스터는 *hivesampletable*이라는 샘플 Hive 테이블과 함께 제공됩니다. 이 세션에서는 HDInsight 클러스터에서 만든 Hive 테이블을 나열하기 위한 Hive 작업을 실행하는 .NET 응용 프로그램을 만듭니다. Hive 사용에 대한 자세한 내용은 [HDInsight와 함께 Hive
사용](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)을
참조하십시오.

SDK를 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

* HDInsight .NET SDK 설치
* 콘솔 응용 프로그램 만들기
* 응용 프로그램 실행

**HDInsight .NET SDK를 설치하려면** [NuGet][2]에서 가장 최근에 게시된 SDK 빌드를 설치할 수
있습니다. 지침은 다음 절차에서 설명합니다.

**Visual Studio 콘솔 응용 프로그램을 만들려면**

1.  Visual Studio 2012를 엽니다.

2.  파일 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

3.  새 프로젝트에서 다음 값을 입력하거나 선택합니다.
    
    <table  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
     <tr>
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">속성</th>
    
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">값</th>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td>
    </tr>
    
     </table>

4.  **확인**을 클릭하여 프로젝트를 만듭니다.

5.  **도구** 메뉴에서 **라이브러리 패키지 관리자**를 클릭한 다음 **패키지 관리자 콘솔**을 클릭합니다.

6.  콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.
    
         Install-Package Microsoft.WindowsAzure.Management.HDInsight
    
    .NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다.

7.  솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.

8.  파일 맨 위에 다음 using 문을 추가합니다.
    
         using System.IO;
         using System.Threading;
         using System.Security.Cryptography.X509Certificates;
        
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.Hadoop.Client;

9.  클래스에 다음의 함수 정의를 추가합니다. 이 함수는 Hadoop 작업의 완료를 기다리는 데 사용됩니다.
    
         private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
         {
             JobDetails jobInProgress = client.GetJob(jobResults.JobId);
             while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
             {
                 jobInProgress = client.GetJob(jobInProgress.JobId);
                 Thread.Sleep(TimeSpan.FromSeconds(10));
             }
         }

10. Main() 함수에서 다음 코드를 복사하여 붙여넣습니다.
    
        // Set the variables
        string subscriptionID = "<Azure  subscription ID>";
        string clusterName = "<HDInsight  cluster name>";
        string certFriendlyName = "<certificate  friendly name>";		
    
    이러한 모든 변수를 프로그램에 대해 설정해야 합니다. 시스템 관리자로부터 Azure 구독 ID를 얻을 수 있습니다.
    
    인증서에 대한 정보는 [Azure용 관리 인증서 만들기 및 업로드][4]를 참조하십시오. 인증서를 구성하는 손쉬운 방법은
    *Get-AzurePublishSettingsFile* 및 *Import-AzurePublishSettingsFile*
    PowerShell cmdlet을 실행하는 것입니다. 그러면 관리 인증서가 자동으로 생성되어 업로드됩니다.
    PowerShell cmdlet을 실행한 후 워크스테이션에서 *certmgr.msc*를 열고,
    *Personal/Certificates*를 확장하여 인증서를 찾을 수 있습니다. PowerShell cmdlet으로 만든
    인증서에는 *발급 대상* 및 *발급자* 필드 모두에 사용할 수 있는 *Azure Tools*가 있습니다.

11. Main() 함수에서 다음 코드를 추가하여 Hive 작업을 정의합니다.
    
        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };
    
    File 매개 변수를 사용하여 HDFS에서 HiveQL 스크립트 파일을 지정할 수도 있습니다. 예를 들면 다음과 같습니다.
    
        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

12. Main() 함수에서 다음 코드를 추가하여 JobSubmissionCertificateCredential 개체를 만듭니다.
    
        // Get the certificate object from certificate store using the
        friendly name to identify it X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly); X509Certificate2 cert =
        store.Certificates.Cast<x509certificate2
        ().First(item => item.FriendlyName ==
        certFriendlyName); JobSubmissionCertificateCredential creds = new
        JobSubmissionCertificateCredential(new Guid(subscriptionID), cert,
        clusterName);</x509certificate2>

13. Main() 함수에서 다음 코드를 추가하여 작업을 실행하고 작업이 완료되기를 기다립니다.
    
        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);
        
        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);

14. Main() 함수에서 다음 코드를 추가하여 Hive 작업 출력을 인쇄합니다.
    
        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);
        
        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());
        
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**응용 프로그램을 실행하려면**

Visual Studio에 응용 프로그램이 열려 있을 때 **F5**를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용
프로그램의 상태가 표시되며 다음이 출력됩니다.

    hivesampletable

## <a id="nextsteps" ></a>다음 단계

이 문서에서는 HDInsight 클러스터를 프로비전하는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를
참조하십시오.

* [Azure HDInsight
  시작](/en-us/manage/services/hdinsight/get-started-hdinsight/)
* [HDInsight 클러스터
  프로비전](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)
* [PowerShell을 사용하여 HDInsight
  관리](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
* [HDInsight Cmdlet 참조 문서][5]
* [HDInsight와 함께 Hive
  사용](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
* [HDInsight와 함께 Pig
  사용](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)



[1]: http://hive.apache.org/
[2]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
[3]: http://manage.windowsazure.com/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx