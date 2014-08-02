<properties linkid="manage-services-hdinsight-howto-mapreduce" urlDisplayName="MapReduce with HDInsight " pageTitle="Use MapReduce with HDInsight | Azure" metaKeywords="" description="Learn how to use HDInsight to execute a simple Hadoop MapReduce job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use MapReduce with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

HDInsight와 함께 MapReduce 사용
===============================

Hadoop MapReduce는 방대한 양의 데이터를 처리하는 응용 프로그램을 작성하기 위한 소프트웨어 프레임워크입니다. 이 자습서에서는 워크스테이션의 Azure PowerShell을 사용하여 텍스트의 단어 발생 수를 계산하는 MapReduce 프로그램을 HDInsight 클러스터로 제출합니다. 단어 수 계산 프로그램은 Java로 작성되었으며 HDInsight 클러스터와 함께 제공됩니다.

**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   HDInsight 클러스터. 이 클러스터를 만드는 여러 방법에 대한 지침은 [HDInsight 클러스터 프로비전](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)을 참조하십시오.

-   Azure PowerShell이 설치 및 구성된 워크스테이션. 자세한 내용은 [Azure PowerShell 설치 및 구성](/en-us/manage/install-and-configure-windows-powershell/)을 참조하십시오.

**예상 완료 시간:** 30분

자습서 내용
-----------

1.  [시나리오 이해](#scenario)
2.  [Azure PowerShell을 사용하여 샘플 실행](#run-sample)
3.  [단어 수 계산 MapReduce 프로그램의 Java 코드](#java-code)
4.  [다음 단계](#next-steps)

시나리오 이해
-------------

다음 다이어그램은 단어 수 시나리오에서 MapReduce가 작동하는 방식을 보여 줍니다.

![HDI.WordCountDiagram](./media/hdinsight-get-started/HDI.WordCountDiagram.gif)

MapReduce 작업의 출력은 키-값 쌍 집합입니다. 키는 단어를 지정하는 문자열이고 값은 텍스트에서 해당 단어의 총 발생 수를 지정하는 정수입니다. 이 작업은 다음 두 단계로 수행됩니다.

-   매퍼가 입력 텍스트의 각 줄을 입력으로 사용하고 단어로 나눕니다. 단어 뒤에 1이 표시되는 작업이 발생할 때마다 키/값 쌍을 내보냅니다. 리듀서로 보내기 전에 출력이 정렬됩니다.

-   리듀서가 각 단어의 개별 발생 수를 합산하고 단어 뒤에 발생 합계가 포함된 단일 키/값 쌍을 내보냅니다.

MapReduce 작업을 실행하려면 다음 요소가 필요합니다.

-   MapReduce 프로그램. 이 자습서에서는 직접 작성할 필요가 없도록 HDInsight 클러스터와 함께 제공된 단어 수 계산 샘플을 사용합니다. 이 샘플은 */example/jars/hadoop-examples.jar*에 있습니다. 버전 3.0 HDInsight 클러스터에서는 파일 이름이 *hadoop-mapreduce-examples.jar*입니다. 고유한 MapReduce 작업을 작성하는 방법에 대한 지침은 [HDInsight용 Java MapReduce 프로그램 개발](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)을 참조하십시오.
-   입력 파일. */example/data/gutenberg/davinci.txt*를 입력 파일로 사용합니다. 파일 업로드에 대한 자세한 내용은 [HDInsight에 데이터 업로드](/en-us/documentation/articles/hdinsight-upload-data/)를 참조하십시오.
-   출력 파일 폴더. */example/data/WordCountOutput*을 출력 파일 폴더로 사용합니다. 폴더가 없는 경우 시스템에서 새로 만듭니다. 해당 폴더가 있으면 MapReduce 작업이 실패합니다. MapReduce 작업을 두 번째 실행하려면 출력 폴더를 삭제하거나 다른 출력 폴더를 지정해야 합니다.

Azure PowerShell을 사용하여 샘플 실행
-------------------------------------

1.  **Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성](/en-us/manage/install-and-configure-windows-powershell/)을 참조하십시오.

2.  다음 명령에 있는 변수 2개를 설정한 후 실행합니다.

         $subscriptionName = "<SubscriptionName>"   # Azure subscription name
         $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  다음 명령을 실행하고 Azure 계정 정보를 제공합니다.

         Add-AzureAccount

4.  다음 명령을 실행하여 MapReduce 작업 정의를 만듭니다.

         # Define the MapReduce job
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar*는 버전 2.1 HDInsight 클러스터와 함께 제공됩니다. 버전 3.0 HDInsight 클러스터에서는 파일 이름이 *hadoop-mapreduce.jar*로 바뀌었습니다.

    hadoop-examples.jar 파일은 HDInsight 클러스터 배포와 함께 제공됩니다. MapReduce 작업에 대한 두 개의 인수가 있습니다. 첫 번째 인수는 원본 파일 이름이고, 두 번째 인수는 출력 파일 경로입니다. 원본 파일은 HDInsight 클러스터 배포와 함께 제공되고 출력 파일 경로는 런타임 시 만들어집니다.

5.  다음 명령을 실행하여 MapReduce 작업을 제출합니다.

         # Submit the job
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    MapReduce 작업 정의뿐 아니라 MapReduce 작업을 실행하려는 HDInsight 클러스터 이름 및 자격 증명도 제공합니다. Start-AzureHDInsightJob은 비동기 호출입니다. 작업 완료를 확인하려면 *Wait-AzureHDInsightJob* cmdlet을 사용합니다.

6.  다음 명령을 실행하여 MapReduce 작업 완료를 확인합니다.

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

7.  다음 명령을 실행하여 MapReduce 작업 실행 오류를 확인합니다.

         # Get the job output
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**MapReduce 작업 결과를 검색하려면**

1.  **Azure PowerShell**을 엽니다.
2.  다음 명령을 실행하여 디렉터리를 c:\\ 루트로 변경합니다.

         cd \

    기본 Azure Powershell 디렉터리는 *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*입니다. 기본적으로 이 폴더에 대한 쓰기 권한은 없습니다. C:\\ 루트 디렉터리 또는 쓰기 권한이 있는 폴더로 디렉터리를 변경해야 합니다.

3.  다음 명령에 있는 변수 3개를 설정한 후 실행합니다.

         $subscriptionName = "<SubscriptionName>"       # Azure subscription name
            
         $storageAccountName = "<StorageAccountName>"   # Azure storage account name
         $containerName = "<ContainerName>"              # Blob storage container name

         Azure 저장소 계정은 자습서의 앞부분에서 만든 계정입니다. 저장소 계정은 기본 HDInsight 클러스터 파일 시스템으로 사용되는 Blob 컨테이너를 호스트하는 데 사용됩니다.  일반적으로 Blob 저장소 컨테이너 이름은 클러스터를 프로비전할 때 다른 이름을 지정하지 않을 경우 HDInsight 클러스터와 동일한 이름을 공유합니다.

4.  다음 명령을 실행하여 Azure 저장소 컨텍스트 개체를 만듭니다.

         # Select the current subscription
         Select-AzureSubscription $subscriptionName

         # Create the storage account context object
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription*은 여러 구독이 있을 경우 현재 구독을 설정하는 데 사용되며, 기본 구독은 사용할 구독이 아닙니다.

5.  다음 명령을 실행하여 Blob 컨테이너의 MapReduce 작업 출력을 워크스테이션으로 다운로드합니다.

         # Download the job output to the workstation
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    */example/data/WordCountOutput* 폴더는 MapReduce 작업을 실행할 때 지정된 출력 폴더입니다. *part-r-00000*은 MapReduce 작업 출력의 기본 파일 이름입니다. 로컬 폴더의 동일한 폴더 구조에 파일이 다운로드됩니다. 예를 들어 다음 스크린샷에서 현재 폴더는 C 루트 폴더입니다. \*C:\\example\\data\\WordCountOutput\* 폴더에 파일이 다운로드됩니다.

6.  다음 명령을 실행하여 MapReduce 작업 출력 파일을 인쇄합니다.

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    MapReduce 작업에서 단어와 개수를 사용하여 *part-r-00000*이라는 파일을 생성합니다. 스크립트는 findstr 명령을 사용하여 *"there"*가 포함된 모든 단어를 나열합니다.

MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.

단어 수 계산 MapReduce 프로그램의 Java 코드
-------------------------------------------

다음은 단어 수 계산 Java MapReduce 프로그램의 소스 코드입니다.

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

      public static void main(String[] args) throws Exception{
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

다음 단계
---------

MapReduce는 강력한 진단 기능을 제공하는 반면 익히기 어려울 수 있습니다. Pig, Hive 등의 기타 언어를 사용하면 HDInsight에 저장된 데이터로 더 쉽게 작업할 수 있습니다. 자세한 내용은 다음 문서를 참조하십시오.

-   [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [HDInsight용 Java MapReduce 프로그램 개발](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [HDInsight용 C\# Hadoop 스트리밍 MapReduce 프로그램 개발](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [HDInsight와 함께 Hive 사용](/en-us/documentation/articles/hdinsight-use-hive/)
-   [HDInsight와 함께 Pig 사용](/en-us/documentation/articles/hdinsight-use-pig/)
-   [HDInsight 샘플 실행](/en-us/documentation/articles/hdinsight-run-samples/)

