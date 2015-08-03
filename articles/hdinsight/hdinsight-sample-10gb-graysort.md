
<properties
	pageTitle="10GB GraySort Hadoop MapReduce 샘플 | Microsoft Azure"
	description="HDInsight의 Hadoop에서 보통 최소 100TB 정도 되는 매우 많은 양의 데이터에 대해 Azure PowerShell을 사용하여 일반적인 목적의 GraySort를 실행하는 방법에 대해 알아봅니다."
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
	ms.date="07/09/2015"
	ms.author="jgao"/>

# HDInsight의 10GB GraySort Hadoop MapReduce 샘플

이 샘플 항목은 Azure PowerShell을 사용하여 Azure HDInsight에서 범용 GraySort Hadoop MapReduce 프로그램을 실행하는 방법을 보여 줍니다. GraySort는 메트릭이 대량의 데이터를 정렬하는 동안 도달하는 정렬 속도(TB/분)가 일반적으로 최소 100TB인 벤치마크 정렬입니다.

> [AZURE.NOTE]이 문서의 단계에는 Windows 기반 HDInsight 클러스터가 필요합니다. Linux 기반 클러스터에서 이 샘플 및 다른 샘플을 실행하는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 샘플 실행](hdinsight-hadoop-run-samples-linux.md)을 참조하세요.

이 샘플에서는 비교적 빠르게 실행할 수 있도록 적절한 10GB의 데이터를 사용합니다. 또한 2009년에 0.578TB/분(173분에 100TB)의 속도로 연간 범용("daytona") 테라바이트 정렬 벤치마크를 획득한 Owen O'Malley 및 Arun Murthy가 개발한 MapReduce 응용 프로그램을 사용합니다. 이 정렬 벤치마크 및 다른 정렬 벤치마크에 대한 자세한 내용은 [정렬 벤치마크](http://sortbenchmark.org/)(영문) 사이트를 참조하십시오.

이 샘플에서는 세 가지 집합의 MapReduce 프로그램을 사용합니다.

1. **TeraGen**은 정렬할 데이터의 행을 생성하는 데 사용할 수 있는 MapReduce 프로그램입니다.
2. **TeraSort**는 입력 데이터를 샘플링하여 데이터를 전체 순서로 정렬하는 데 MapReduce를 사용합니다. TeraSort는 각 reduce의 키 범위를 정의하는 N-1 샘플 키의 정렬된 목록을 사용하는 사용자 지정 파티셔너를 제외하면 MapReduce 함수의 표준 정렬입니다. 특히, sample[i-1] <= key < sample[i]와 같은 모든 키는 reduce i로 전송됩니다. 이는 reduce i의 출력이 모두 reduce i+1의 출력보다 작다는 것을 보증합니다.
3. **TeraValidate**는 출력이 전역으로 정렬되는지 확인하는 MapReduce 프로그램입니다. 이 프로그램은 출력 디렉터리에 파일당 하나의 맵을 만들며 각 맵에서 각 키가 이전 키보다 작거나 같은지 확인합니다. 또한 map 함수는 각 파일의 첫 번째와 마지막 키의 레코드를 생성하며, reduce 함수는 파일 i의 첫 번째 키가 파일 i-1의 마지막 키보다 큰지 확인합니다. 모든 문제가 순서에서 벗어난 키와 함께 reduce의 출력으로 보고됩니다.

세 응용 프로그램 모두에 사용된 입력 및 출력 형식은 올바른 형식의 텍스트 파일을 읽고 씁니다. 벤치마크 콘테스트에서는 출력 데이터를 여러 노드로 복제할 필요가 없으므로 reduce의 출력에서 복제를 기본값인 3 대신 1로 설정했습니다.


**다음 내용을 배웁니다.** * Azure PowerShell을 사용하여 Azure HDInsight에서 일련의 MapReduce 프로그램을 실행하는 방법 * Java로 작성한 MapReduce 프로그램


**필수 조건:**

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **HDInsight 클러스터**. 이 클러스터를 만드는 여러 방법에 대한 자세한 내용은 [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)(영문)을 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.



##Azure PowerShell로 샘플 실행

소개 부분에서 설명한 각 MapReduce 프로그램에 해당하는 샘플에는 다음과 같은 세 가지 작업이 필요합니다.

1. **TeraGen** MapReduce 작업을 실행하여 정렬할 데이터를 생성합니다.
2. **TeraSort** MapReduce 작업을 실행하여 데이터를 정렬합니다.
3. **TeraValidate** MapReduce 작업을 실행하여 데이터가 올바르게 정렬되었는지 확인합니다.


**TeraGen 프로그램을 실행하려면**

1. Azure PowerShell을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.
2. 다음 명령에서 변수 2개를 설정한 후 실행합니다.

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

4. 다음 명령을 실행하여 MapReduce 작업 정의를 만듭니다.

		# Create a MapReduce job definition for the TeraGen MapReduce program
		$teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

	*"-Dmapred.map.tasks=50"* 인수는 50개의 맵을 만들어 작업을 실행하도록 지정합니다. *100000000* 인수는 생성할 데이터의 양을 지정합니다. 마지막 인수, */example/data/10GB-sort-input*은 결과가 저장되는(그리고 다음 정렬 단계의 입력을 포함하는) 출력 디렉터리를 지정합니다.

5. 다음 명령을 실행하여 작업을 제출하고 작업이 완료될 때까지 기다린 후 표준 오류를 인쇄합니다.

		# Run the TeraGen MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**TeraSort 프로그램을 실행하려면**

1. Azure PowerShell을 엽니다.
2. 다음 명령에서 변수 2개를 설정한 후 실행합니다.

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. 다음 명령을 실행하여 MapReduce 작업을 정의합니다.

		# Create a MapReduce job definition for the TeraSort MapReduce program
		$terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

	*"-Dmapred.map.tasks=50"* 인수는 50개의 맵을 만들어 작업을 실행하도록 지정합니다. *100000000* 인수는 생성할 데이터의 양을 지정합니다. 마지막 두 개의 인수는 입력 및 출력 디렉터리를 지정합니다.

4. 다음 명령을 실행하여 작업을 제출하고 작업이 완료될 때까지 기다린 후 표준 오류를 인쇄합니다.

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**TeraValidate 프로그램을 실행하려면**

1. Azure PowerShell을 엽니다.
2. 다음 명령에서 변수 2개를 설정한 후 실행합니다.

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. 다음 명령을 실행하여 MapReduce 작업을 정의합니다.

		#	Create a MapReduce job definition for the TeraValidate MapReduce program
		$teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"

	*"-Dmapred.map.tasks=50"* 인수는 50개의 맵을 만들어 작업을 실행하도록 지정합니다. *"-Dmapred.reduce.tasks=25"* 인수는 25개의 reduce 작업을 만들어 작업을 실행하도록 지정합니다. 마지막 두 개의 인수는 입력 및 출력 디렉터리를 지정합니다.


4. 다음 명령을 실행하여 MapReduce 작업을 제출하고 작업이 완료될 때까지 기다린 후 표준 오류를 인쇄합니다.

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


##TeraSort MapReduce 프로그램의 Java 코드

이 섹션에서는 TeraSort MapReduce 프로그램 코드가 검사용으로 제시되었습니다.


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one
	 * or more contributor license agreements.  See the NOTICE file
	 * distributed with this work for additional information
	 * regarding copyright ownership.  The ASF licenses this file
	 * to you under the Apache License, Version 2.0 (the
	 * "License"); you may not use this file except in compliance
	 * with the License.  You may obtain a copy of the License at
	 *
	 *     http://www.apache.org/licenses/LICENSE-2.0
	 *
	 * Unless required by applicable law or agreed to in writing, software
	 * distributed under the License is distributed on an "AS IS" BASIS,
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	 * See the License for the specific language governing permissions and
	 * limitations under the License.
	 */

	package org.apache.hadoop.examples.terasort;

	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;

	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;

	/**
	 * Generates the sampled split points, launches the job,
	 * and waits for it to finish.
	 * <p>
	 * To run the program:
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
	 */

	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);

	  /**
	   * A partitioner that splits text keys into roughly equal
	   * partitions in a global sorted order.
	   */

	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;

	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }

	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];

	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }

	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }


	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p,
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }

	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }

	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }

	    public TotalOrderPartitioner() {
	    }

	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }

	  }

	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }

	  /**
	   * @param args
	   */

	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }

	}


##요약

이 샘플에서는 Azure HDInsight를 사용하여 한 작업의 데이터 출력이 계열 내 다음 작업의 입력이 되는 일련의 MapReduce 작업을 실행하는 방법에 대해 설명했습니다.

##다음 단계

다른 샘플 실행 과정을 안내하고 Azure PowerShell을 통해 Azure HDInsight에서 Pig, Hive 및 MapReduce 작업을 사용하는 방법에 대한 지침을 제공하는 자습서는 다음 항목을 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [샘플: Pi 추정][hdinsight-sample-pi-estimator]
* [샘플: 단어 개수][hdinsight-sample-wordcount]
* [샘플: C# 스트리밍][hdinsight-sample-csharp-streaming]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [Azure HDInsight SDK 설명서][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=July15_HO4-->