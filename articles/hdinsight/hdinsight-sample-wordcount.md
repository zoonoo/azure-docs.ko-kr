<properties
	pageTitle="HDInsight에서 Hadoop MapReduce 단어 개수 예제 | Microsoft Azure"
	description="HDInsight의 Hadoop 클러스터에서 MapReduce 단어 개수 예제를 실행합니다. Java로 작성된 프로그램이 텍스트 파일에 있는 단어 수를 계산합니다."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>

#HDInsight에서 Java로 작성된 Hadoop 클러스터에서 MapReduce 단어 개수 예제를 실행합니다.

이 자습서에서는 HDInsight의 Hadoop 클러스터에서 MapReduce 단어 개수 예제를 실행하는 방법을 보여 줍니다. 프로그램은 Java로 작성되었습니다. 이 프로그램에서는 텍스트 파일에서 단어 발생 수를 계산하고 발생 빈도와 쌍을 이룬 각 단어가 포함된 새 텍스트 파일을 출력합니다. 이 샘플에서 분석한 텍스트 파일은 "The Notebooks of Leonardo Da Vinci"의 Project Gutenberg 전자책 버전입니다.

> [AZURE.NOTE]이 문서의 단계에서는 Windows 클라이언트가 필요합니다. Linux 기반 HDInsight 클러스터와 함께 Linux, OS X 또는 Unix 클라이언트에서 단어 개수 예제를 사용하는 단계는 [SSH와 함께 HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-hadoop-use-mapreduce-ssh.md) 또는 [Curl을 사용하여 HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-hadoop-use-mapreduce-curl.md)을 참조하세요.

**다음 내용을 배웁니다.**

* Azure PowerShell을 사용하여 HDInsight 클러스터에서 MapReduce 프로그램을 실행하는 방법
* Java로 MapReduce 프로그램을 작성하는 방법


**필수 조건**:

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **HDInsight 클러스터**. 이 클러스터를 만드는 여러 방법에 대한 자세한 내용은 [Azure HDInsight 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.



## <a id="run-sample"></a>Azure PowerShell을 사용하여 샘플 실행</h2>

**MapReduce 작업을 제출하려면**

1.	**Azure PowerShell** 콘솔을 엽니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

3. 다음 명령에 있는 변수 2개를 설정한 후 실행합니다.

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. 다음 명령을 실행하여 MapReduce 작업 정의를 만듭니다.

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]*hadoop-examples.jar*은 HDInsight 버전 2.1 클러스터와 함께 제공됩니다. HDInsight 버전 3.0 클러스터에서는 파일 이름이 *hadoop-mapreduce.jar*로 바뀌었습니다.

	hadoop-mapreduce-examples.jar 파일은 HDInsight 클러스터와 함께 제공됩니다. MapReduce 작업에 대한 두 개의 인수가 있습니다. 첫 번째 인수는 원본 파일 이름이고, 두 번째 인수는 출력 파일 경로입니다. 원본 파일은 HDInsight 클러스터와 함께 제공되고 출력 파일 경로는 런타임에 만들어집니다.

6. 다음 명령을 실행하여 MapReduce 작업을 제출합니다.

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	MapReduce 작업 정의뿐 아니라 MapReduce 작업을 실행하려는 HDInsight 클러스터 이름도 제공해야 합니다.

8. 다음 명령을 실행하여 MapReduce 작업 실행 오류를 확인합니다.

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**MapReduce 작업 결과를 검색하려면**

1. **Azure PowerShell** 콘솔을 엽니다.
2. 다음 명령에 있는 변수 3개를 설정한 후 실행합니다.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Azure 저장소 계정은 자습서의 앞부분에서 만든 계정입니다. 저장소 계정은 기본 HDInsight 클러스터 파일 시스템으로 사용되는 Blob을 호스트하는 데 사용됩니다. 일반적으로 Azure Blob 저장소 컨테이너 이름은 클러스터를 프로비전할 때 다른 이름을 지정하지 않을 경우 HDInsight 클러스터와 동일한 이름을 공유합니다.

3. 다음 명령을 실행하여 Azure 저장소 컨텍스트 개체를 만듭니다.

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription**은 여러 구독이 있을 경우 현재 구독을 설정하는 데 사용되며, 기본 구독은 사용하지 않습니다.

4. 다음 명령을 실행하여 Blob의 MapReduce 작업 출력을 워크스테이션으로 다운로드합니다.

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*/example/data/WordCountOutput* 폴더는 MapReduce 작업을 실행할 때 지정된 출력 폴더입니다. *part-r-00000*은 MapReduce 작업 출력의 기본 파일 이름입니다. 로컬 폴더의 동일한 폴더 구조에 파일이 다운로드됩니다. 예를 들어 다음 스크린샷에서 현재 폴더는 C 루트 폴더입니다. *C:\\example\\data\\WordCountOutput* 폴더에 파일이 다운로드됩니다.

5. 다음 명령을 실행하여 MapReduce 작업 출력 파일을 인쇄합니다.

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	MapReduce 작업에서 *part-r-00000*이라는 파일을 생성하며 단어와 개수를 포함합니다. 스크립트는 **findstr** 명령을 사용하여 *"there"*가 포함된 모든 단어를 나열합니다.

WordCount 스크립트의 출력이 명령 창에 표시됩니다.

![PowerShell에서 HDInsight의 Hadoop MapReduce 단어 개수 예제를 실행하여 얻은 단어 빈도 결과입니다.][image-hdi-sample-wordcount-output]

MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.

## <a id="java-code"></a>WordCount MapReduce 프로그램의 Java 코드</h2>



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
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  	}
  	}



이 자습서에서는 Azure PowerShell을 사용하여 HDInsight에서 텍스트 파일에 나타나는 단어 수를 계산하는 MapReduce 프로그램의 실행 방법을 알아보았습니다.

## <a id="next-steps"></a>다음 단계</h2>

다른 샘플을 실행하고 Azure PowerShell을 사용하여 Azure HDInsight에서 Pig, Hive 및 MapReduce 작업을 사용하는 방법에 대한 지침을 제공하는 자습서는 다음 항목을 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [샘플: 10GB GraySort][hdinsight-sample-10gb-graysort]
* [샘플: Pi 추정][hdinsight-sample-pi-estimator]
* [샘플: C# 스트리밍][hdinsight-sample-cs-streaming]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [Azure HDInsight SDK 설명서][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO3-->