<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

HDInsight용 Java MapReduce 프로그램 개발
========================================

이 자습서에서는 단어 계산 MapReduce 작업을 HDInsight Emulator에서 개발 및 테스트부터 Azure HDInsight에서 배포 및 실행에 이르기까지 전체 시나리오를 단계별로 안내합니다.

**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure HDInsight Emulator 설치. 자세한 내용은 [HDInsight Emulator 사용 시작](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)을 참조하십시오.
-   에뮬레이터 컴퓨터에 Azure PowerShell 설치. 자세한 내용은 [Azure PowerShell 설치 및 구성](/ko-kr/documentation/articles/install-configure-powershell/)을 참조하십시오.
-   Azure 구독. 자세한 내용은 [구매 옵션](https://www.windowsazure.com/en-us/pricing/purchase-options/), [회원 제안](https://www.windowsazure.com/en-us/pricing/member-offers/) 또는 [무료 평가판](https://www.windowsazure.com/en-us/pricing/free-trial/)을 참조하십시오.

이 문서에서는 다음을 수행합니다.
--------------------------------

-   [Java로 단어 계산 MapReduce 프로그램 개발](#develop)
-   [에뮬레이터에서 프로그램 테스트](#test)
-   [Azure Blob 저장소로 데이터 파일 및 응용 프로그램 업로드](#upload)
-   [Azure HDInsight에서 MapReduce 프로그램 실행](#run)
-   [MapReduce 결과 검색](#retrieve)
-   [다음 단계](#nextsteps)

Java로 단어 계산 MapReduce 프로그램 개발
----------------------------------------

단어 계산은 지정된 입력 집합에 나오는 각 단어 수를 계산하는 간단한 응용 프로그램입니다.

**Java로 단어 계산 MapReduce 작업을 작성하려면**

1.  메모장을 엽니다.
2.  다음 프로그램을 복사하여 메모장에 붙여넣습니다.

         package org.apache.hadoop.examples;
            
         import java.io.IOException;
         import java.util.StringTokenizer;
         import org.apache.hadoop.conf.Configuration;
         import org.apache.hadoop.fs.Path;
         import org.apache.hadoop.io.IntWritable;
         import org.apache.hadoop.io.Text;
         import org.apache.hadoop.mapreduce.Job;
         import org.apache.hadoop.mapreduce.Mapper;
         import org.apache.hadoop.mapreduce.Reducer;
         import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
         import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
         import org.apache.hadoop.util.GenericOptionsParser;
            
         public class WordCount {
            
           public static class TokenizerMapper 
                extends Mapper<Object, Text, Text, IntWritable>{
                
             private final static IntWritable one = new IntWritable(1);
             private Text word = new Text();
                  
             public void map(Object key, Text value, Context context
                             ) throws IOException, InterruptedException {
               StringTokenizer itr = new StringTokenizer(value.toString());
               while (itr.hasMoreTokens()) {
                 word.set(itr.nextToken());
                 context.write(word, one);
               }
             }
           }
              
           public static class IntSumReducer 
                extends Reducer<Text,IntWritable,Text,IntWritable> {
             private IntWritable result = new IntWritable();
            
             public void reduce(Text key, Iterable<IntWritable> values, 
                                Context context
                                ) throws IOException, InterruptedException {
               int sum = 0;
               for (IntWritable val : values) {
                 sum += val.get();
               }
               result.set(sum);
               context.write(key, result);
             }
           }
            
           public static void main(String[] args) throws Exception {
             Configuration conf = new Configuration();
             String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
             if (otherArgs.length != 2) {
               System.err.println("Usage: wordcount <in> <out>");
               System.exit(2);
             }
             Job job = new Job(conf, "word count");
             job.setJarByClass(WordCount.class);
             job.setMapperClass(TokenizerMapper.class);
             job.setCombinerClass(IntSumReducer.class);
             job.setReducerClass(IntSumReducer.class);
             job.setOutputKeyClass(Text.class);
             job.setOutputValueClass(IntWritable.class);
             FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
             FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
             System.exit(job.waitForCompletion(true) 
         0 : 1);
           }
         }

    패키지 이름은 **org.apache.hadoop.examples**이며 클래스 이름은 **WordCount**입니다. MapReduce 작업을 제출할 때 이 이름을 사용합니다.

3.  파일을 **c:\\Tutorials\\WordCountJava\\WordCount.java**로 저장합니다. 폴더 구조가 없는 경우 만듭니다.

HDInsight Emulator는 *javac* 컴파일러와 함께 제공됩니다.

**MapReduce 프로그램을 컴파일하려면**

1.  명령 프롬프트를 엽니다.
2.  디렉터리를 **c:\\Tutorials\\WordCountJava**로 변경합니다. 이 디렉터리는 단어 계산 MapReduce 프로그램용 폴더입니다.
3.  다음 명령을 실행하여 두 개의 jar 파일이 있는지 확인합니다.

         dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
         dir %hadoop_home%\lib\commons-cli-1.2.jar

4.  다음 명령을 실행하여 프로그램을 컴파일합니다.

         C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

    javac는 C:\\Hadoop\\java\\bin 폴더에 있습니다. 마지막 매개 변수는 현재 폴더에 있는 Java 프로그램입니다. 컴파일러가 현재 폴더에 클래스 파일을 3개 만듭니다.

5.  다음 명령을 실행하여 jar 파일을 만듭니다.

         C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

    이 명령은 현재 폴더에 WordCount.jar 파일을 만듭니다.

    ![HDI.EMulator.WordCount.컴파일](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png)

에뮬레이터에서 프로그램 테스트
------------------------------

에뮬레이터에서 MapReduce 작업을 테스트하는 과정에는 다음 절차가 포함됩니다.

1.  에뮬레이터에서 HDFS에 데이터 파일 업로드
2.  단어 계산 MapReduce 작업 제출
3.  작업 상태 확인
4.  작업 결과 검색

기본적으로 HDInsight Emulator는 HDFS를 기본 파일 시스템으로 사용합니다. 선택적으로 Azure Blob 저장소를 사용하도록 HDInsight Emulator를 구성할 수 있습니다. 자세한 내용은 [HDInsight Emulator 시작](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage)을 참조하십시오.

이 자습서에서는 HDFS *copyFromLocal* 명령을 사용하여 데이터 파일을 HDFS에 업로드합니다. 다음 섹션에서는 Azure PowerShell을 사용하여 Azure Blob 저장소에 파일을 업로드하는 방법을 보여 줍니다. Azure Blob 저장소에 파일을 업로드하는 다른 방법에 대해서는 [HDInsight에 데이터 업로드](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)를 참조하십시오.

이 자습서에서는 다음 HDFS 폴더 구조를 사용합니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><td data-morhtml="true">폴더</td><td data-morhtml="true">참고</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount</td><td data-morhtml="true">단어 계산 프로젝트의 루트 폴더 </td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Apps</td><td data-morhtml="true">매퍼 및 리듀서 실행 파일 폴더</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Input</td><td data-morhtml="true">MapReduce 원본 파일 폴더</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Output</td><td data-morhtml="true">MapReduce 출력 파일 폴더</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/MRStatusOutput</td><td data-morhtml="true">작업 출력 폴더</td></tr>
</table>


이 자습서에서는 %hadoop\_home% 디렉터리에 있는 .txt 파일을 데이터 파일로 사용합니다.

> [WACOM.NOTE] Hadoop HDFS 명령은 대/소문자를 구분합니다.

**에뮬레이터 HDFS로 데이터 파일을 복사하려면**

1.  데스크톱에서 Hadoop 명령줄을 엽니다. Hadoop 명령줄은 에뮬레이터 설치 관리자에서 설치합니다.
2.  Hadoop 명령줄 창에서 다음 명령을 실행하여 입력 파일용 디렉터리를 만듭니다.

         hadoop fs -mkdir /WordCount/Input

    여기에서 사용한 경로는 상대 경로입니다. 이 경로는 다음과 같습니다.

         hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  다음 명령을 실행하여 HDFS의 입력 폴더로 몇 개의 텍스트 파일을 복사합니다.

         hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

    MapReduce 작업은 이러한 파일의 단어 수를 계산합니다.

4.  다음 명령을 실행하여 업로드된 파일을 나열하고 확인합니다.

         hadoop fs -ls /WordCount/Input

    약 8개의 .txt 파일이 표시됩니다.

**Hadoop 명령줄을 사용하여 MapReduce 작업을 실행하려면**

1.  데스크톱에서 Hadoop 명령줄을 엽니다.
2.  다음 명령을 실행하여 HDFS에서 /WordCount/Output 폴더 구조를 삭제합니다. /WordCount/Output은 단어 계산 MapReduce 작업의 출력 폴더입니다. MapReduce 작업은 해당 폴더가 이미 있으면 실패합니다. 두 번째로 작업을 실행하는 경우 이 단계가 필요합니다.

         hadoop fs -rmr /WordCount/Output

3.  다음 명령을 실행합니다.

         hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    작업이 완료되면 다음 스크린샷과 유사한 결과가 표시됩니다.

    ![HDI.EMulator.WordCount.실행](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png)

    스크린샷에서 100% 완료된 map과 reduce를 모두 확인할 수 있습니다. 또한 스크린샷에서는 작업 ID, job\_201312092021\_0002도 나열합니다. 바탕 화면에서 **Hadoop MapReduce Status** 바로 가기를 열어 작업 ID를 찾아보면 동일한 보고서를 검색할 수 있습니다.

MapReduce 작업을 실행하는 다른 옵션은 Azure PowerShell을 사용하는 것입니다. 자세한 내용은 [HDInsight Emulator 시작](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)을 참조하십시오.

**HDFS에서 출력을 표시하려면**

1.  Hadoop 명령줄을 엽니다.
2.  다음 명령을 실행하여 출력을 표시합니다.

         hadoop fs -ls /WordCount/Output/
         hadoop fs -cat /WordCount/Output/part-00000

    페이지 보기를 가져오기 위해 명령의 끝 부분에 "|more"를 추가할 수 있습니다. 또는 문자열 패턴을 찾는 findstr 명령을 사용할 수 있습니다.

         hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

지금까지는 단어 계산 MapReduce 작업을 개발하여 에뮬레이터에서 테스트해 보았습니다. 다음은 Azure HDInsight에서 해당 작업을 배포하고 실행하는 단계입니다.

Azure Blob 저장소에 데이터 업로드
---------------------------------

Azure HDInsight는 데이터 저장소에 Azure Blob 저장소를 사용합니다. HDInsight 클러스터를 프로비전할 때 시스템 파일을 저장하는 데 Azure Blob 저장소 컨테이너를 사용합니다. 데이터 파일을 저장하는 데 이러한 기본 컨테이너를 사용하거나 다른 컨테이너(동일한 Azure 저장소 계정 또는 클러스터와 동일한 데이터 센터에 있는 다른 저장소 계정의 컨테이너)를 사용할 수 있습니다.

이 자습서에서는 별도의 저장소 계정에 데이터 파일 및 MapReduce 응용 프로그램용 컨테이너를 만듭니다. 데이터 파일은 워크스테이션의 %hadoop\_home% 디렉터리에 있는 텍스트 파일입니다.

**Blob 저장소 및 컨테이너를 만들려면**

1.  Azure PowerShell을 엽니다.
2.  다음과 같이 변수를 설정한 후 명령을 실행합니다.

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"
         $location = "<MicrosoftDataCenter>"  # For example, "East US"

    **\$subscripionName**은 Azure 구독과 연결됩니다. **\$storageAccountName\_Data** 및 **\$containerName\_Data**를 명명해야 합니다. 명명 제한에 대해서는 [컨테이너와 Blob, 메타데이터의 명명 및 참조](http://msdn.microsoft.com/ko-kr/library/windowsazure/dd135715.aspx)를 참조하십시오.

3.  다음 명령을 실행하여 저장소 계정 및 계정의 Blob 저장소 컨테이너를 만듭니다.

         # Select Azure subscription
         Select-AzureSubscription $subscriptionName
            
         # Create a storage account
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
                    
         # Create a Blob storage container
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $destContext = New-AzureStorageContext 
         StorageAccountName $storageAccountName_Data 
         StorageAccountKey $storageAccountKey  
         New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  다음 명령을 실행하여 저장소 계정 및 컨테이너를 확인합니다.

         Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
         Get-AzureStorageContainer -Context $destContext

**데이터 파일을 업로드하려면**

1.  Azure PowerShell을 엽니다.
2.  다음과 같이 첫 3개의 변수를 설정한 후 명령을 실행합니다.

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
         $destFolder = "WordCount/Input"

    **\$storageAccountName\_Data** 및 **\$containerName\_Data**는 마지막 절차에서 정의한 것과 동일합니다.

    원본 파일 폴더는 **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**이며, 대상 폴더는 **WordCount/Input**입니다.

3.  다음 명령을 실행하여 원본 파일 폴더의 txt 파일 목록을 가져옵니다.

         # Get a list of the txt files
         $filesAll = Get-ChildItem $localFolder
         $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  다음 명령을 실행하여 저장소 컨텍스트 개체를 만듭니다.

         # Create a storage context object
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  다음 명령을 실행하여 파일을 복사합니다.

         # Copy the file from local workstation to the Blob container        
         foreach ($file in $filesTxt){
             
             $fileName = "$localFolder\$file"
             $blobName = "$destFolder/$file"
            
             write-host "Copying $fileName to $blobName"
            
             Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
         }

6.  다음 명령을 실행하여 업로드된 파일을 나열합니다.

         # List the uploaded files in the Blob storage container
         Write-Host "The Uploaded data files:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    대략 8개의 텍스트 데이터 파일이 표시됩니다.

**단어 계산 응용 프로그램을 업로드하려면**

1.  Azure PowerShell을 엽니다.
2.  다음과 같이 첫 3개의 변수를 설정한 후 명령을 실행합니다.

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
         $blobFolder = "WordCount/jars"

    **\$storageAccountName\_Data** 및 **\$containerName\_Data**는 마지막 절차에서 정의한 것과 동일합니다. 즉, 데이터 파일과 응용 프로그램을 둘 다 동일한 저장소 계정의 동일 컨테이너에 업로드합니다.

    대상 폴더는 **WordCount/jars**입니다.

3.  다음 명령을 실행하여 저장소 컨텍스트 개체를 만듭니다.

         # Create a storage context object
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  다음 명령을 실행하여 응용 프로그램을 복사합니다.

         Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  다음 명령을 실행하여 업로드된 파일을 나열합니다.

         # List the uploaded files in the Blob storage container
         Write-Host "The Uploaded application file:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    jar 파일 목록이 표시됩니다.

Azure HDInsight에서 MapReduce 작업 실행
---------------------------------------

다음 PowerShell 스크립트는 아래와 같은 작업을 수행합니다.

1.  HDInsight 클러스터 프로비전

    1.  기본 HDInsight 클러스터 파일 시스템으로 사용할 저장소 계정 만들기
    2.  Blob 저장소 컨테이너 만들기
    3.  HDInsight 클러스터 만들기

2.  MapReduce 작업 제출

    1.  MapReduce 작업 정의 만들기
    2.  MapReduce 작업 제출
    3.  작업이 완료될 때까지 대기
    4.  표준 오류 표시
    5.  표준 출력 표시

3.  클러스터 삭제

    1.  HDInsight 클러스터 삭제
    2.  기본 HDInsight 클러스터 파일 시스템으로 사용된 저장소 계정 삭제

**PowerShell 스크립트를 실행하려면**

1.  메모장을 엽니다.
2.  다음 코드를 복사하여 붙여넣습니다.

         # The storage account and the HDInsight cluster variables
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $serviceNameToken = "<ServiceNameTokenString>"
         $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
         $clusterNodes = <NumberOFNodesInTheCluster>

         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
            
         $clusterName = $serviceNameToken + "hdicluster"
            
         $storageAccountName_Default = $serviceNameToken + "hdistore"
         $containerName_Default =  $serviceNameToken + "hdicluster"

         # The MapReduce job variables
         $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
         $className = "org.apache.hadoop.examples.WordCount"
         $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
         $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
         $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
            
         # Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
         $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
            
         Select-AzureSubscription $subscriptionName
            
         #=============================
         # Create a storage account used as the default file system
         Write-Host "Create a storage account" -ForegroundColor Green
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
            
         #=============================
         # Create a Blob storage container used as teh default file system
         Write-Host "Create a Blob storage container" -ForegroundColor Green
         $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
         $destContext = New-AzureStorageContext 
         StorageAccountName $storageAccountName_Default 
         StorageAccountKey $storageAccountKey_Default
            
         New-AzureStorageContainer -Name $containerName_Default -Context $destContext
            
         #=============================
         # Create an HDInsight cluster
         Write-Host "Create an HDInsight cluster" -ForegroundColor Green
         $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
            
         $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
             Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
             Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
            
         New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
            
         #=============================
         # Create a MapReduce job definition
         Write-Host "Create a MapReduce job definition" -ForegroundColor Green
         $mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
            
         #=============================
         # Run the MapReduce job
         Write-Host "Run the MapReduce job" -ForegroundColor Green
         $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
         Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
            
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
                    
         #=============================
         # Delete the HDInsight cluster
         Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
         Remove-AzureHDInsightCluster -Name $clusterName  
            
         # Delete the default file system storage account
         Write-Host "Delete the storage account" -ForegroundColor Green
         Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  스크립트에서 첫 6개의 변수를 설정합니다. **\$serviceNameToken**은 HDInsight 클러스터 이름, 기본 저장소 계정 이름 및 기본 Blob 저장소 컨테이너 이름에 사용됩니다. 서비스 이름이 3 - 24자이어야 하는데 스크립트에서 최대 10자의 문자열을 이름에 추가하므로 문자열을 14자 이하로 제한해야 합니다. \$serviceNameToken은 소문자를 사용해야 합니다. **\$storageAccountName\_Data** 및 **\$containerName\_Data**는 데이터 파일 및 응용 프로그램을 저장하는 데 사용되는 저장소 계정 및 컨테이너입니다. **\$location**은 데이터 저장소 계정 위치와 일치해야 합니다.
4.  나머지 변수를 검토합니다.
5.  스크립트 파일을 저장합니다.
6.  Azure PowerShell을 엽니다.
7.  다음 명령을 실행하여 remotesigned에 대한 실행 정책을 설정합니다.

         PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  메시지가 표시되면 HDInsight 클러스터의 사용자 이름 및 암호를 입력합니다. 스크립트의 끝 부분에서 클러스터를 삭제하여 사용자 이름 및 암호가 더 이상 필요하지 않게 되므로 사용자 이름 및 암호로 어떤 문자열이든 사용할 수 있습니다. 자격 증명을 묻는 메시지를 표시하지 않으려면 [Windows PowerShell에서 암호, 보안 문자열 및 자격 증명 작업](http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx)(영문)을 참조하십시오.

MapReduce 작업 출력 검색
------------------------

이 섹션에서는 출력을 다운로드하고 표시하는 방법을 보여 줍니다. Excel에서 결과를 표시하는 방법에 대한 자세한 내용은 [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/) 및 [HDInsight에 파워 쿼리로 Excel 연결](/en-us/manage/services/hdinsight/connect-excel-with-power-query/)을 참조하십시오.

**출력을 검색하려면**

1.  Azure PowerShell 창을 엽니다.
2.  디렉터리를 **C:\\Tutorials\\WordCountJava**로 변경합니다. 기본 Azure PowerShell 폴더는 **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**입니다. 실행할 cmdlet은 출력 파일을 현재 폴더에 다운로드합니다. 파일을 시스템 폴더에 다운로드할 권한은 없습니다.
3.  다음 명령을 실행하여 값을 설정합니다.

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
         $blobName = "WordCount/Output/part-r-00000"

4.  다음 명령을 실행하여 Azure 저장소 컨텍스트 개체를 만듭니다.

         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $storageContext = New-AzureStorageContext 
         StorageAccountName $storageAccountName_Data 
         StorageAccountKey $storageAccountKey  

5.  다음 명령을 실행하여 출력을 다운로드하고 표시합니다.

         Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
         cat "./$blobName" | findstr "there"

작업이 완료되면 [Sqoop](../hdinsight-use-sqoop/)을 사용하여 데이터를 SQL Server 또는 Azure SQL 데이터베이스로 내보내는 옵션 또는 데이터를 Excel로 내보내는 옵션이 제공됩니다.

다음 단계
---------

이 자습서에서는 Java MapReduce 작업을 개발하는 방법, HDInsight Emulator에서 응용 프로그램을 테스트하는 방법 및 HDInsight 클러스터를 프로비전하고 클러스터에서 MapReduce를 실행하도록 PowerShell 스크립트를 작성하는 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하십시오.

-   [HDInsight용 C\# Hadoop 스트리밍 MapReduce 프로그램 개발](/ko-kr/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [HDInsight Emulator 시작](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)
-   [HDInsight와 함께 Azure Blob 저장소 사용](/en-us/manage/services/hdinsight/howto-blob-store/)
-   [PowerShell을 사용하여 HDInsight 관리](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [HDInsight에 데이터 업로드](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [HDInsight와 함께 Hive 사용](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [HDInsight와 함께 Pig 사용](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [HDInsight에 파워 쿼리로 Excel 연결](/ko-kr/documentation/articles/hdinsight-connect-excel-power-query/)
-   [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결](../hdinsight-connect-excel-hive-ODBC-driver/)

