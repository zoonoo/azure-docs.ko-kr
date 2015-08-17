<properties
	pageTitle="Hadoop용 Java MapReduce 프로그램 개발 | Microsoft Azure"
	description="HDInsight Emulator에서 Java MapReduce 프로그램을 개발하여 HDInsight로 배포하는 방법에 대해 알아봅니다."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="nitinme"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="07/11/2015"
	ms.author="nitinme"/>

# HDInsight의 Hadoop용 Java MapReduce 프로그램 개발

[AZURE.INCLUDE [pig-선택기](../../includes/hdinsight-maven-mapreduce-selector.md)]

이 자습서에서는 Apache Maven을 사용하여 Java에서 단어 계산 Hadoop MapReduce 작업을 개발하는 전체 시나리오를 안내합니다. 또한 HDInsight Emulator for Azure에서 응용 프로그램을 테스트한 다음 Windows 기반 HDInsight 클러스터에서 배포 및 실행하는 방법도 설명합니다.

##<a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 작업을 완료해야 합니다.

- HDInsight Emulator를 설치합니다. 자세한 내용은 [HDInsight Emulator 사용 시작][hdinsight-emulator]을 참조하세요. 필요한 모든 서비스가 실행 중인지 확인합니다. HDInsight Emulator가 설치된 컴퓨터의 바탕 화면 바로 가기에서 Hadoop 명령줄을 실행하고 **C:\\hdp**로 이동하여 **start\_local\_hdp\_services.cmd** 명령을 실행합니다.
- 에뮬레이터 컴퓨터에 Azure PowerShell 설치. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.
- 에뮬레이터 컴퓨터에 Java 플랫폼 JDK 7 이상 설치. 이 버전은 에뮬레이터 컴퓨터에 이미 제공되어 있습니다.
- [Apache Maven](http://maven.apache.org/) 설치 및 구성
- Azure 구독. 자세한 내용은 [구매 옵션][azure-purchase-options], [회원 제안][azure-member-offers] 또는 [무료 평가판][azure-free-trial]을 참조하세요.


##<a name="develop"></a>Apache Maven을 사용하여 Java에서 MapReduce 프로그램 만들기

지정된 입력 집합에 나오는 각 단어 수를 계산하는 간단한 단어 계산 MapReduce 응용 프로그램을 만듭니다. 이 섹션에서는 다음 작업을 수행합니다.

1. Apache Maven을 사용하여 프로젝트 만들기
2. POM(프로젝트 개체 모델) 업데이트
3. 단어 계산 MapReduce 응용 프로그램 만들기
4. 응용 프로그램 빌드 및 패키지화

**Maven을 사용하여 프로젝트 만들기**

1. **C:\\Tutorials\\WordCountJava** 디렉터리를 만듭니다.
2. 개발 환경의 명령줄에서 방금 만든 위치로 디렉터리를 변경합니다.
3. Maven과 함께 설치되는 __mvn__ 명령을 사용하여 프로젝트용 스캐폴딩을 생성합니다.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	이 명령은 __artifactID__ 매개 변수로 지정된 이름(이 예제에서는 **wordcountjava**)을 사용하여 현재 디렉터리에 새 디렉터리를 만듭니다. 이 디렉터리에는 다음 항목이 포함됩니다.

	* __pom.xml__ - [프로젝트 개체 모델(POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.

	* __src__ - __main\\java\\org\\apache\\hadoop\\examples__ 디렉터리를 포함하는 디렉터리이며, 여기서 응용 프로그램을 작성합니다.
3. __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__ 파일은 이 예제에서 사용되지 않으므로 삭제합니다.

**POM 업데이트**

1. __pom.xml__ 파일을 편집하고 `<dependencies>` 섹션 내에 다음을 추가합니다.

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-mapreduce-client-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>

	이 코드를 통해 Maven은 프로젝트에 특정 버전(<version>에 나열됨)의 라이브러리(<artifactId> 내에 나열됨)가 필요하다는 것을 인식합니다. 컴파일 시간에 이 파일이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 리포지토리 검색](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar)을 사용하여 자세한 정보를 볼 수 있습니다.

2. __pom.xml__ 파일에 다음을 추가합니다. 이 코드는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어`</dependencies>`과 `</project>` 사이에 있어야 합니다.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
  		  </plugins>
	    </build>

	이 코드는 Maven으로 빌드된 JAR에서 라이선스 중복을 방지하는 데 사용되는 [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/)을 구성합니다. 이 플러그 인을 사용하는 이유는 중복 라이선스 파일이 HDInsight 클러스터에서 런타임으로 오류를 일으키기 때문입니다. `ApacheLicenseResourceTransformer` 구현과 함께 Maven Shade Plugin을 사용하면 이 오류가 방지됩니다.

	또한 Maven Shade Plugin에서는 응용 프로그램에 필요한 모든 종속성을 포함하는 uberjar(또는 fatjar이라고도 함)도 생성합니다.

3. __pom.xml__ 파일을 저장합니다.

**단어 계산 응용 프로그램 만들기**

1. __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ 디렉터리로 이동하여 __app.java__ 파일의 이름을 __WordCount.java__로 바꿉니다.
2. 메모장을 엽니다.
2. 다음 프로그램을 복사하여 메모장에 붙여넣습니다.

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

	패키지 이름은 **org.apache.hadoop.examples**이며 클래스 이름은 **WordCount**입니다. MapReduce 작업을 제출할 때 이 이름을 사용합니다.

3. 파일을 저장합니다.

**응용 프로그램 빌드 및 패키지**

1. 명령 프롬프트를 열고 디렉터리를 __wordcountjava__로 변경합니다.

2. 다음 명령을 사용하여 응용 프로그램을 포함하는 JAR 파일을 빌드합니다.

		mvn clean package

	이 코드는 이전 빌드 아티팩트를 정리하고, 아직 설치되지 않은 모든 종속성을 다운로드한 후 응용 프로그램을 빌드 및 패키지화합니다.

3. 명령이 완료되면 __wordcountjava\\target__ 디렉터리에 __wordcountjava-1.0-SNAPSHOT.jar__라는 파일이 포함됩니다.

	> [AZURE.NOTE]__wordcountjava-1.0-SNAPSHOT.jar__ 파일이 uberjar입니다.


##<a name="test"></a>에뮬레이터에서 프로그램 테스트

HDInsight Emulator에서 MapReduce 작업을 테스트하는 과정에는 다음 절차가 포함됩니다.

1. 에뮬레이터에서 Hadoop 분산 파일 시스템(HDFS)에 데이터 파일 업로드
2. 로컬 사용자 그룹 만들기
3. 단어 계산 MapReduce 작업 실행
4. 작업 결과 검색

기본적으로 HDInsight Emulator는 HDFS를 파일 시스템으로 사용합니다. 선택적으로 Azure Blob 저장소를 사용하도록 HDInsight Emulator를 구성할 수 있습니다. 자세한 내용은 [HDInsight Emulator 시작][hdinsight-emulator-wasb]을 참조하세요.

이 자습서에서는 HDFS **copyFromLocal** 명령을 사용하여 데이터 파일을 HDFS에 업로드합니다. 다음 섹션에서는 Azure PowerShell을 사용하여 Azure Blob 저장소에 파일을 업로드하는 방법을 보여줍니다. Azure Blob 저장소에 파일을 업로드하는 다른 방법에 대해서는 [HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요.

이 자습서에서는 다음 HDFS 폴더 구조를 사용합니다.

<table border="1">
<tr><td>폴더</td><td>참고</td></tr>
<tr><td>/WordCount</td><td>단어 계산 프로젝트의 루트 폴더 </td></tr>
<tr><td>/WordCount/Apps</td><td>매퍼 및 리듀서 실행 파일 폴더</td></tr>
<tr><td>/WordCount/Input</td><td>MapReduce 원본 파일 폴더</td></tr>
<tr><td>/WordCount/Output</td><td>MapReduce 출력 파일 폴더</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>작업 출력 폴더</td></tr>
</table>

이 자습서에서는 %hadoop\_home% 디렉터리에 있는 .txt 파일을 데이터 파일로 사용합니다.

> [AZURE.NOTE]Hadoop HDFS 명령은 대/소문자를 구분합니다.

**에뮬레이터 HDFS로 데이터 파일 복사**

1. 데스크톱에서 Hadoop 명령줄을 엽니다. Hdoop 명령줄은 에뮬레이터 설치 관리자를 통해 설치합니다.
2. Hadoop 명령줄 창에서 다음 명령을 실행하여 입력 파일용 디렉터리를 만듭니다.

		hadoop fs -mkdir /WordCount
		hadoop fs -mkdir /WordCount/Input

	여기에서 사용한 경로는 상대 경로입니다. 이 경로는 다음과 같습니다.

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3. 다음 명령을 실행하여 HDFS의 입력 폴더로 몇 개의 텍스트 파일을 복사합니다.

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /WordCount/Input

	MapReduce 작업은 이러한 파일의 단어 수를 계산합니다.

4. 다음 명령을 실행하여 업로드된 파일을 나열하고 확인합니다.

		hadoop fs -ls /WordCount/Input

**로컬 사용자 그룹 만들기**

클러스터에서 MapReduce 작업을 올바르게 실행하려면 hdfs라는 사용자 그룹을 만들어야 합니다. 또한 에뮬레이터에 로그온하는 데 사용하는 로컬 사용자와 hadoop이라는 사용자를 이 그룹에 추가해야 합니다. 이렇게 하려면 관리자 권한 명령 프롬프트에서 다음 명령을 사용합니다.

		# Add a user group called hdfs
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**Hadoop 명령줄을 사용하여 MapReduce 작업 실행**

1. 데스크톱에서 Hadoop 명령줄을 엽니다.
2. 다음 명령을 실행하여 HDFS에서 /WordCount/Output 폴더 구조를 삭제합니다. /WordCount/Output은 단어 계산 MapReduce 작업의 출력 폴더입니다. MapReduce 작업은 해당 폴더가 이미 있으면 실패합니다. 두 번째로 작업을 실행하는 경우 이 단계가 필요합니다.

		hadoop fs -rm - r /WordCount/Output

2. 다음 명령을 실행합니다.

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	작업이 완료되면 다음 스크린샷과 유사한 결과가 표시됩니다.

	![HDI.EMulator.WordCount.실행][image-emulator-wordcount-run]

	스크린샷에서 100% 완료된 map과 reduce를 모두 확인할 수 있습니다. 또한 스크린샷에는 작업 ID도 나와 있습니다. 바탕 화면에서 **Hadoop MapReduce 상태** 바로 가기를 열어 같은 작업 ID를 찾아보면 동일한 보고서를 검색할 수 있습니다.

MapReduce 작업을 실행하는 다른 옵션은 Azure PowerShell을 사용하는 것입니다. 자세한 내용은 [HDInsight Emulator 시작][hdinsight-emulator]을 참조하세요.

**HDFS에서 출력 표시**

1. Hadoop 명령줄을 엽니다.
2. 다음 명령을 실행하여 출력을 표시합니다.

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	페이지 보기를 가져오기 위해 명령의 끝 부분에 "|more"를 추가할 수 있습니다. 또는 문자열 패턴을 찾는 **findstr** 명령을 사용할 수 있습니다.

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

지금까지는 단어 계산 MapReduce 작업을 개발하여 에뮬레이터에서 테스트해 보았습니다. 다음은 Azure HDInsight에서 해당 작업을 배포하고 실행하는 단계입니다.



##<a id="upload"></a>Azure Blob 저장소에 데이터 및 응용 프로그램 업로드
Azure HDInsight는 데이터 저장소에 Azure Blob 저장소를 사용합니다. HDInsight 클러스터를 프로비전할 때 시스템 파일을 저장하는 데 Azure Blob 저장소 컨테이너를 사용합니다. 데이터 파일을 저장하는 데 이러한 기본 컨테이너를 사용하거나 다른 컨테이너(동일한 Azure 저장소 계정 또는 클러스터와 동일한 데이터 센터에 있는 다른 저장소 계정의 컨테이너)를 사용할 수 있습니다.

이 자습서에서는 별도의 저장소 계정에 데이터 파일 및 MapReduce 응용 프로그램용 컨테이너를 만듭니다. 데이터 파일은 에뮬레이터 워크스테이션의 **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** 디렉터리에 있는 텍스트 파일입니다.

**Blob 저장소 계정 컨테이너 만들기**

1. Azure PowerShell을 엽니다.
2. 다음과 같이 변수를 설정한 후 명령을 실행합니다.

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	**$subscripionName** 변수는 Azure 구독과 연결됩니다. **$storageAccountName\_Data** 및 **$containerName\_Data**를 명명해야 합니다. 명명 제한에 대해서는 [컨테이너와 Blob, 메타데이터의 명명 및 참조](http://msdn.microsoft.com/library/windowsazure/dd135715.aspx)를 참조하세요.

3. 다음 명령을 실행하여 저장소 계정 및 계정의 Blob 저장소 컨테이너를 만듭니다.

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. 다음 명령을 실행하여 저장소 계정 및 컨테이너를 확인합니다.

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**데이터 파일 업로드**

1. Azure PowerShell을 엽니다.
2. 다음과 같이 첫 3개의 변수를 설정한 후 명령을 실행합니다.

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	**$storageAccountName\_Data** 및 **$containerName\_Data** 변수는 마지막 절차에서 정의한 것과 동일합니다.

	원본 파일 폴더는 **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**이며, 대상 폴더는 **WordCount/Input**입니다.

3. 다음 명령을 실행하여 원본 파일 폴더의 .txt 파일 목록을 가져옵니다.

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4. 다음 명령을 실행하여 저장소 컨텍스트 개체를 만듭니다.

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. 다음 명령을 실행하여 파일을 복사합니다.

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. 다음 명령을 실행하여 업로드된 파일을 나열합니다.

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	업로드된 텍스트 데이터 파일에 대한 정보가 표시됩니다.

**단어 계산 응용 프로그램 업로드**

1. Azure PowerShell을 엽니다.
2. 다음과 같이 첫 3개의 변수를 설정한 후 명령을 실행합니다.

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar"
		$blobFolder = "WordCount/jars"

	**$storageAccountName\_Data** 및 **$containerName\_Data** 변수는 마지막 절차에서 정의한 것과 동일합니다. 즉, 데이터 파일과 응용 프로그램을 둘 다 동일한 저장소 계정의 동일 컨테이너에 업로드합니다.

	대상 폴더는 **WordCount/jars**입니다.

4. 다음 명령을 실행하여 저장소 컨텍스트 개체를 만듭니다.

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. 다음 명령을 실행하여 응용 프로그램을 복사합니다.

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. 다음 명령을 실행하여 업로드된 파일을 나열합니다.

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	JAR 파일 목록이 표시되어야 합니다.

##<a name="run"></a>Azure HDInsight에서 MapReduce 작업 실행

이 섹션에서는 다음 작업을 수행하는 Azure PowerShell 스크립트를 만듭니다.

1. HDInsight 클러스터 프로비전

	1. 기본 HDInsight 클러스터 파일 시스템으로 사용할 저장소 계정 만들기
	2. Blob 저장소 컨테이너 만들기
	3. HDInsight 클러스터 만들기

2. MapReduce 작업 제출

	1. MapReduce 작업 정의 만들기
	2. MapReduce 작업 제출
	3. // 작업이 완료될 때까지 대기
	4. 표준 오류 표시
	5. 표준 출력 표시

3. 클러스터 삭제

	1. HDInsight 클러스터 삭제
	2. 기본 HDInsight 클러스터 파일 시스템으로 사용된 저장소 계정 삭제


**Azure PowerShell 스크립트 실행**

1. 메모장을 엽니다.
2. 다음 코드를 복사하여 붙여넣습니다.

		# The Storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data Storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		# Create a PSCredential object. The user name and password are hardcoded here. You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password)

		Select-AzureSubscription $subscriptionName

		#=============================
		# Create a Storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#=============================
		# Create a Blob storage container used as the default file system
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

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

		# Delete the default file system Storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. 스크립트에서 첫 6개의 변수를 설정합니다. **$stringPrefix** 변수는 HDInsight 클러스터 이름, 저장소 계정 이름 및 Blob 저장소 컨테이너 이름에 지정된 문자열을 접두사로 추가하는 데 사용됩니다. 이러한 항목의 이름은 3\~24자여야 하므로 지정하는 문자열과 이 스크립트가 사용하는 이름을 합한 길이가 이름의 문자 제한을 초과하지 않는지 확인해야 합니다. 모두 **$stringPrefix**의 소문자를 사용해야 합니다.

	**$storageAccountName\_Data** and **$containerName\_Data** 변수는 데이터 파일 및 응용 프로그램을 저장하는 데 사용되는 저장소 계정 및 컨테이너입니다. **$location** 변수는 데이터 저장소 계정 위치와 일치해야 합니다.

4. 나머지 변수를 검토합니다.
5. 스크립트 파일을 저장합니다.
6. Azure PowerShell을 엽니다.
7. 다음 명령을 실행하여 RemoteSigned에 대한 실행 정책을 설정합니다.

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. 메시지가 표시되면 HDInsight 클러스터의 사용자 이름 및 암호를 입력합니다. 스크립트의 끝 부분에서 클러스터를 삭제하여 사용자 이름 및 암호가 더 이상 필요하지 않게 되므로 사용자 이름 및 암호로 어떤 문자열이든 사용할 수 있습니다. 자격 증명을 묻는 메시지를 표시하지 않으려면 [Windows PowerShell에서 암호, 보안 문자열 및 자격 증명 작업][powershell-PSCredential](영문)을 참조하세요.


##<a name="retrieve"></a>MapReduce 작업 출력 검색
이 섹션에서는 출력을 다운로드하고 표시하는 방법을 보여 줍니다. Excel에서 결과를 표시하는 방법에 대한 자세한 내용은 [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결][hdinsight-ODBC] 및 [HDInsight에 파워 쿼리로 Excel 연결][hdinsight-power-query]을 참조하세요.


**출력 검색**

1. Azure PowerShell 창을 엽니다.
2. 디렉터리를 **C:\\Tutorials\\WordCountJava**로 변경합니다. 기본 Azure PowerShell 폴더는 **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**입니다. 실행할 cmdlet은 출력 파일을 현재 폴더에 다운로드합니다. 파일을 시스템 폴더에 다운로드할 권한은 없습니다.
2. 다음 명령을 실행하여 값을 설정합니다.

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"

3. 다음 명령을 실행하여 Azure 저장소 컨텍스트 개체를 만듭니다.

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. 다음 명령을 실행하여 출력을 다운로드하고 표시합니다.

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

작업이 완료되면 [Sqoop][hdinsight-use-sqoop]을 사용하여 SQL Server 또는 Azure SQL 데이터베이스로 데이터를 내보내거나 데이터를 Excel로 내보내는 옵션이 제공됩니다.

##<a id="nextsteps"></a>다음 단계
이 자습서에서는 Java MapReduce 작업을 개발하는 방법, HDInsight Emulator에서 응용 프로그램을 테스트하는 방법 및 HDInsight 클러스터를 프로비전하고 클러스터에서 MapReduce 작업을 실행하도록 Azure PowerShell 스크립트를 작성하는 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

- [HDInsight용 C# Hadoop 스트리밍 MapReduce 프로그램 개발][hdinsight-develop-streaming]
- [Azure HDInsight 시작][hdinsight-get-started]
- [HDInsight Emulator 시작][hdinsight-emulator]
- [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]
- [Azure PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
- [HDInsight에 데이터 업로드][hdinsight-upload-data]
- [HDInsight에서 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Pig 사용][hdinsight-use-pig]
- [파워 쿼리로 HDInsight에 Excel 연결][hdinsight-power-query]
- [Microsoft Hive ODBC 드라이버로 HDInsight에 Excel 연결][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: ../install-configure-powershell.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png

<!---HONumber=August15_HO6-->