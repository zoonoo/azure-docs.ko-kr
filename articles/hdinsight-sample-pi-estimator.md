<properties 
	pageTitle="HDInsight의 Pi 추정 Hadoop 샘플 | Azure" 
	description="HDInsight에서 Hadoop MapReduce 샘플을 실행하는 방법에 대해 알아봅니다." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

# HDInsight의 Pi 추정 Hadoop 샘플
 
이 항목에서는 Azure Powershell을 사용하여 Azure HDinsight에서 수학 상수 Pi의 값을 추정하는 Hadoop MapReduce 프로그램을 HDInsight에서 실행하는 방법을 보여 줍니다. 또한 Pi 값을 추정하는 데 사용되는 MapReduce 프로그램의 Java 코드도 제공합니다.

프로그램은 통계(준난수 몬테카를로) 방법을 사용하여 Pi의 값을 추정합니다. 단위 정사각형 내에 무작위로 놓인 점은 원의 영역과 같은 확률인 Pi/4로 해당 정사각형 내의 내접원 안에 들어갑니다. Pi의 값은 4R의 값에서 추정할 수 있습니다. 여기에서 R은 정사각형 내에 있는 점의 총수에 대한 원 내부에 있는 점 개수의 비율입니다. 사용한 점 샘플이 크면 클수록 추정이 향상됩니다.

mapper 및 reducer 함수가 포함된 PiEstimator Java 코드를 아래 검사에 사용할 수 있습니다. 매퍼 프로그램은 단위 정사각형 내에 무작위로 놓인 점의 지정된 개수를 생성한 후 원 내부에 있는 해당 점 개수를 계산합니다. 리듀서 프로그램은 매퍼에서 계산된 점을 누적한 후 수식 4R에서 Pi의 값을 추정합니다. 여기에서 R은 정사각형 내에 있는 점의 총수에 대한 원 내부에서 계산된 점 개수의 비율입니다.

이 샘플을 위해 제공된 스크립트는 Hadoop JAR 작업을 제출하고 하나의 값으로 16개의 맵을 실행하도록 설정되며, 각 맵은 매개 변수 값으로 천만 개의 샘플 점을 계산하는 데 필요합니다. 이러한 매개 변수 값은 Pi의 추정값을 개선하기 위해 변경할 수 있습니다. 참고로 Pi의 첫 소수점 이하의 10자리는 3.1415926535입니다.

Azure의 Hadoop에서 응용 프로그램을 배포하는 데 필요한 파일이 포함된 .jar 파일은 .zip 파일이며 다운로드할 수 있습니다. 편한 시간에, 다양한 압축 유틸리티를 사용하여 압축을 푼 후 파일을 탐색할 수 있습니다.

HDInsight를 사용하여 MapReduce 작업을 실행하는 데 도움이 될 수 있는 기타 샘플이 실행 방법 지침에 대한 링크와 함께 [HDInsight 샘플 실행][hdinsight-samples]에 나열되어 있습니다.

**다음 내용을 배웁니다.**
		
* Azure PowerShell을 사용하여 Azure HDInsight에서 Pi 추정 MapReduce 프로그램을 실행하는 방법	
* Java로 작성한 MapReduce 프로그램

**필수 조건**:	

- Azure 계정이 있어야 합니다. 계정 등록 옵션은 [Azure 평가판 사용](http://azure.microsoft.com/pricing/free-trial/) 페이지를 참조하세요.

- HDInsight 클러스터를 미리 프로비전해야 합니다. 이 클러스터를 만드는 여러 방법에 대한 자세한 내용은 [HDInsight 클러스터 프로비전](../hdinsight-provision-clusters/)을 참조하세요.

- Azure PowerShell을 설치하고 계정과 함께 사용하도록 구성해야 합니다. 설치 및 구성 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

## 이 문서의 내용	
이 항목에서는 샘플의 실행 방법을 보여 주고, Pi 추정 MapReduce 프로그램의 Java 코드를 제공하며, 배운 내용을 요약하고, 다음 몇 단계에 대해 설명합니다. 이 항목에는 다음 섹션이 있습니다.
	
1. [Azure PowerShell로 샘플 실행](#run-sample)	
2. [Pi 추정 MapReduce 프로그램의 Java 코드](#java-code)
3. [요약](#summary)	
4. [다음 단계](#next-steps)	

<h2><a id="run-sample"></a>Azure PowerShell로 샘플 실행</h2>

**MapReduce 작업을 제출하려면**

1. Azure PowerShell을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.
2. 다음 명령에서 변수 2개를 설정한 후 실행합니다.
	
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

3. 다음 명령을 실행하여 MapReduce 정의를 만듭니다.	

		$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "pi" -Arguments "16", "10000000" 

	
	첫 번째 인수는 만들 맵의 개수(기본값은 16)를 나타냅니다. 두 번째 인수는 맵별로 만들 샘플 수(기본적으로 천만 개)를 나타냅니다. 따라서 이 프로그램은 "10*1천만 = 1억 6천만" 개의 무작위 점을 사용하여 Pi의 추정치를 만듭니다. 세 번째 인수는 HDInsight 3.0 및 3.1 클러스터에서 샘플을 실행하는 데 사용되는 jar 파일의 위치와 이름을 나타냅니다. 이 파일의 내용은 아래를 참조하세요.

4. 다음 명령을 실행하여 MapReduce 작업을 제출하고 작업이 완료될 때까지 기다립니다.

		# Run the Pi Estimator MapReduce job.
		Select-AzureSubscription $subscriptionName
		$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName
	
		# Wait for the job to complete.  
		$piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

5. 다음 명령을 실행하여 MapReduce 작업 표준 출력을 검색합니다.

		# Print output and standard error file of the MapReduce job
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

	비교를 위해, Pi의 첫 소수점 이하의 10자리는 3.1415926535입니다.


<h2><a id="java-code"></a>Pi 추정 MapReduce 프로그램의 Java 코드</h2>



 	/**	
 	* Licensed to the Apache Software Foundation (ASF) under one	
 	* or more contributor license agreements. See the NOTICE file	
 	* distributed with this work for additional information	
 	* regarding copyright ownership. The ASF licenses this file	
 	* to you under the Apache License, Version 2.0 (the	
 	* "License"); you may not use this file except in compliance	
 	* with the License. You may obtain a copy of the License at	
 	*	
	* http://www.apache.org/licenses/LICENSE-2.0	
 	*	
 	* Unless required by applicable law or agreed to in writing, software	
 	* distributed under the License is distributed on an "AS IS" BASIS,	
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and	
 	* limitations under the License.		
 	*/	

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi	
	//using quasi-Monte Carlo method.	
	//	
	//Mapper:	
	//Generate points in a unit square	
	//and then count points inside/outside of the inscribed circle of the square.	
	//	
	//Reducer:	
	//Accumulate points inside/outside results from the mappers.	
	//Let numTotal = numInside + numOutside.	
	//The fraction numInside/numTotal is a rational approximation of	
	//the value (Area of the circle)/(Area of the square),	
	//where the area of the inscribed circle is Pi/4	
	//and the area of unit square is 1.	
	//Then, Pi is estimated value to be 4(numInside/numTotal). 	
	//	

 	public class PiEstimator extends Configured implements Tool {	
	//tmp directory for input/output	
 	static private final Path TMP_DIR = new Path(	
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");	

	//2-dimensional Halton sequence {H(i)},		
	//where H(i) is a 2-dimensional point and i >= 1 is the index.		
	//Halton sequence is used to generate sample points for Pi estimation.	 
 	private static class HaltonSequence {	
	// Bases	
 	static final int[] P = {2, 3}; 	
	//Maximum number of digits allowed	
 	static final int[] K = {63, 40}; 	

 	private long index;	
 	private double[] x;	
 	private double[][] q;	
 	private int[][] d;	

	//Initialize to H(startindex),	
	//so the sequence begins with H(startindex+1).	
 	HaltonSequence(long startindex) {	
 	index = startindex;	
 	x = new double[K.length];	
 	q = new double[K.length][];	
 	d = new int[K.length][];	
 	for(int i = 0; i < K.length; i++) {	
 	q[i] = new double[K[i]];	
 	d[i] = new int[K[i]];	
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;
	
 	for(int j = 0; j < K[i]; j++) {	
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];	
 	d[i][j] = (int)(k % P[i]);	
 	k = (k - d[i][j])/P[i];	
 	x[i] += d[i][j] * q[i][j];	
 	}	
 	}
 	}

	//Compute next point.	
	//Assume the current point is H(index).	
	//Compute H(index+1).	
	//@return a 2-dimensional point with coordinates in [0,1)^2		
 	double[] nextPoint() {		
 	index++;	
 	for(int i = 0; i < K.length; i++) {	
 	for(int j = 0; j < K[i]; j++) {	
 	d[i][j]++;	
 	x[i] += q[i][j];	
 	if (d[i][j] < P[i]) {	
 	break;		
 	}	
 	d[i][j] = 0;	
 	x[i] -= (j == 0? 1.0: q[i][j-1]);	
 	}	
 	}	
 	return x;	
 	}	
 	}	

	//Mapper class for Pi estimation.	
	//Generate points in a unit square and then		
	//count points inside/outside of the inscribed circle of the square.	
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.	
	//@param offset samples starting from the (offset+1)th sample.	
	//@param size the number of samples for this map	
	//@param out output {ture->numInside, false->numOutside}	
	//@param reporter	
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());		
 	long numInside = 0L;	
 	long numOutside = 0L;	

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.		
	//Accumulate points inside/outside results from the mappers.		
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.	
 	@Override	
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.		
	// @param isInside Is the points inside? 	
	// @param values An iterator to a list of point counts	
	// @param output dummy, not used here.	
	// @param reporter	

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}
	
 	//Reduce task done, write output to a file.	
 	@Override	
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class, 
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}
	
	//Run a map/reduce job for estimating Pi.	
	//@return the estimated value of Pi.		
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	) 	
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());
	
 	jobConf.setInputFormat(SequenceFileInputFormat.class);
	
 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);
	
 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);
	
 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);
	
 	// turn off speculative execution, because DFS doesn't handle		
 	// multiple writers to the same file.		
 	jobConf.setSpeculativeExecution(false);
	
 	//setup input/output directories	
 	final Path inDir = new Path(TMP_DIR, "in");	
 	final Path outDir = new Path(TMP_DIR, "out");	
 	FileInputFormat.setInputPaths(jobConf, inDir);	
 	FileOutputFormat.setOutputPath(jobConf, outDir);	
	
 	final FileSystem fs = FileSystem.get(jobConf);		
 	if (fs.exists(TMP_DIR)) {	
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");	
	 }	
	 if (!fs.mkdirs(inDir)) {	
	 throw new IOException("Cannot create input directory " + inDir);	
	 }	

	 //generate an input file for each map task		
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);	
	 final LongWritable offset = new LongWritable(i * numPoints);	
	 final LongWritable size = new LongWritable(numPoints);	
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();	
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }
	
	 //start a map/reduce job		
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");
	
	 //read outputs		
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }
	
	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }
	
	//Parse arguments and then runs a map/reduce job.	
	//Print output in standard out.		
	//@return a non-zero if there is an error. Otherwise, return 0. 	
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);
	
	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);
	
	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }
	
	 //main method for running it as a stand alone command. 		
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
  

<h2><a id="summary"></a>요약</h2>

이 자습서에서는 HDInsight에서 MapReduce 작업을 실행하는 방법과 이 서비스에서 관리할 수 있는 대량의 데이터 집합을 필요에 따라 생성하는 몬테카를로 방법을 사용하는 방법에 대해 알아보았습니다.

다음은 기본 HDInsight 3.1 또는 3.0 클러스터에서 이 샘플을 실행하는 데 사용되는 전체 스크립트입니다. 

	### Provide the Windows Azure subscription name and the HDInsight cluster name. 
	$subscriptionName = "<SubscriptionName>" 
	$clusterName = "<ClusterName>"  

	###Select the Azure subscription to use.
	Select-AzureSubscription $subscriptionName 

	### Create a MapReduce job definition. 
	$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -ClassName "pi" -Arguments "32", "1000000000" -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar"

	### Run the MapReduce job. 
	$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName
 
	### Wait for the job to complete.  
	$piJob | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600

	### Print the standard error file of the MapReduce job.
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput



<h2><a id="next-steps"></a>다음 단계</h2>

Azure PowerShell로 Azure HDInsight에서 Pig, Hive 및 MapReduce 작업을 사용하는 방법에 대한 지침을 제공하고 다른 샘플을 실행하는 자습서에 대해서는 다음 항목을 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [샘플: 10GB GraySort][hdinsight-sample-10gb-graysort]
* [샘플: 워드 수][hdinsight-sample-wordcount]
* [샘플: C# 스트리밍][hdinsight-sample-csharp-streaming]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [Azure HDInsight SDK 설명서][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
 

<!--HONumber=42-->
