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
	ms.date="10/15/2015"
	ms.author="jgao"/>

#HDInsight의 Hadoop 클러스터에서 워드카운트 MapReduce 프로그램 실행

Azure PowerShell을 사용하여 HDInsight의 Hadoop 클러스터에서 MapReduce 프로그램을 실행하는 방법을 알아봅니다. Java로 작성된 이 프로그램에서는 텍스트 파일에서 단어 발생 수를 계산하고 발생 빈도와 쌍을 이룬 각 단어가 포함된 새 텍스트 파일을 출력합니다.

이 프로그램은 클러스터에 설치됩니다. 이 자습서에서 분석한 텍스트 파일은 The Notebooks of Leonardo Da Vinci의 Project Gutenberg 전자책 버전입니다.

> [AZURE.NOTE]이 문서의 단계에서는 Windows 클라이언트가 필요합니다. Linux 기반 HDInsight 클러스터와 함께 Linux, OS X 또는 Unix 클라이언트에서 단어 개수 예제를 사용하는 단계는 [SSH와 함께 HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-hadoop-use-mapreduce-ssh.md) 또는 [Curl을 사용하여 HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-hadoop-use-mapreduce-curl.md)을 참조하세요.

**다른 관련 문서:**

* [Azure HDInsight 시작][hdinsight-get-started]
* [HDInsight의 Hadoop용 Java MapReduce 프로그램 개발](hdinsight-develop-deploy-java-mapreduce.md)
* [HDInsight에서 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)
* [샘플: 10GB GraySort][hdinsight-sample-10gb-graysort]
* [샘플: Pi 추정][hdinsight-sample-pi-estimator]
* [샘플: C# 스트리밍][hdinsight-sample-cs-streaming]

**필수 조건**:

- **HDInsight 클러스터**. 이 클러스터를 만드는 여러 방법에 대한 자세한 내용은 [Azure HDInsight 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.

## Azure PowerShell을 사용하여 샘플 실행

**MapReduce 작업을 제출하려면**

1. **Windows PowerShell ISE**를 엽니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.
2. 다음 PowerShell 스크립트를 붙여 넣습니다.

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. 처음 3개 변수를 설정하고 스크립트를 실행합니다.
		
**MapReduce 작업 결과를 검색하려면**

1. **Windows PowerShell ISE**를 엽니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.
2. 다음 PowerShell 스크립트를 붙여 넣습니다.

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	MapReduce 작업에서 *part-r-00000*이라는 파일을 생성하며 단어와 개수를 포함합니다. 스크립트는 **findstr** 명령을 사용하여 *"there"*가 포함된 모든 단어를 나열합니다.

WordCount 스크립트의 출력이 명령 창에 표시됩니다.

![PowerShell에서 HDInsight의 Hadoop MapReduce 단어 개수 예제를 실행하여 얻은 단어 빈도 결과입니다.][image-hdi-sample-wordcount-output]

MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.

##Java 소스 코드

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

## 다음 단계

* [Azure HDInsight 시작][hdinsight-get-started]
* [HDInsight의 Hadoop용 Java MapReduce 프로그램 개발](hdinsight-develop-deploy-java-mapreduce.md)
* [HDInsight에서 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)
* [샘플: 10GB GraySort][hdinsight-sample-10gb-graysort]
* [샘플: Pi 추정][hdinsight-sample-pi-estimator]
* [샘플: C# 스트리밍][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->