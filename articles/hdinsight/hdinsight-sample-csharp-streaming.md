<properties
	pageTitle="C# 스트리밍 단어 수 Hadoop 샘플 | Microsoft Azure"
	description="Hadoop 스트리밍 인터페이스를 사용하는 MapReduce 프로그램을 C#으로 작성하는 방법과 PowerShell cmdlet을 사용하여 HDInsight에서 프로그램을 실행하는 방법"
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
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>

# HDInsight의 Hadoop에서 C# 스트리밍 단어 수 MapReduce 샘플

Hadoop은 맵을 작성하고 Java가 아닌 다른 언어의 함수를 줄일 수 있는 스트리밍 API를 MapReduce에 제공합니다. 이 자습서에서는 Hadoop 스트리밍 인터페이스를 사용하는 MapReduce 프로그램을 C#으로 작성하는 방법 및 Azure PowerShell cmdlet을 사용하여 Azure HDInsight에서 프로그램을 실행하는 방법을 보여 줍니다.

> [AZURE.NOTE]이 자습서의 단계는 Windows 기반 HDInsight 클러스터에만 적용됩니다. Linux 기반 HDInsight 클러스터를 스트리밍하는 예제는 [HDInsight용 Python 스트리밍 프로그램 개발](hdinsight-hadoop-streaming-python.md)을 참조하세요.

예제에서 mapper 및 reducer는 [stdin][stdin-stdout-stderr]에서 입력을 줄 단위로 읽고 출력을 [stdout][stdin-stdout-stderr]으로 내보내는 실행 파일입니다. 프로그램은 텍스트의 모든 단어 수를 계산합니다.

**매퍼**에 대해 실행 파일이 지정된 경우 각 매퍼 작업은 매퍼가 초기화될 때 개별 프로세스로 실행 파일을 시작합니다. 실행 시 mapper 작업은 입력을 줄로 변환하고 프로세스의 [stdin][stdin-stdout-stderr]에 줄을 공급합니다.

그동안 mapper는 프로세스의 stdout에서 줄 기반 출력을 수집합니다. 각 줄을 mapper의 출력으로 수집되는 키/값 쌍으로 변환합니다. 기본적으로 첫 번째 탭 문자까지 줄의 접두사는 키이고 줄의 나머지(탭 문자 제외)는 값입니다. 줄에 탭 문자가 없는 경우에는 전체 줄이 키로 간주되고 값은 null입니다.

**리듀서**에 대해 실행 파일이 지정된 경우 각 리듀서 작업은 리듀서가 초기화될 때 개별 프로세스로 실행 파일을 시작합니다. 실행 시 reducer 작업은 입력 키/값 쌍을 줄로 변환하고 프로세스의 [stdin][stdin-stdout-stderr]에 줄을 공급합니다.

그동안 reducer는 프로세스의 [stdout][stdin-stdout-stderr]에서 줄 기반 출력을 수집합니다. 각 줄을 reducer의 출력으로 수집되는 키/값 쌍으로 변환합니다. 기본적으로 첫 번째 탭 문자까지 줄의 접두사는 키이고 줄의 나머지(탭 문자 제외)는 값입니다.

Hadoop 스트리밍 인터페이스에 대한 자세한 내용은 [Hadoop 스트리밍][hadoop-streaming]을 참조하세요.

**이 자습서에서는 다음 방법에 대해 알아봅니다.**

* Azure PowerShell에서 C# 스트리밍 프로그램을 실행하여 HDInsight의 파일에 포함된 데이터를 분석합니다.
* Hadoop 스트리밍 인터페이스를 사용하는 C# 코드를 작성합니다.


**필수 조건**:

시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **HDInsight 클러스터**. 이 클러스터를 만드는 여러 방법에 대한 자세한 내용은 [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)(영문)을 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.



## <a id="run-sample"></a>Azure PowerShell을 사용하여 샘플 실행

**MapReduce 작업을 실행하려면**

1.	**Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

3. 다음 명령에 있는 변수 2개를 설정한 후 실행합니다.

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. 다음 명령을 실행하여 MapReduce 작업을 정의합니다.

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	매개 변수는 mapper 및 reducer 함수와 입력 파일 및 출력 파일을 지정합니다.

5. 다음 명령을 실행하여 MapReduce 작업을 실행하고, 작업이 완료될 때까지 기다린 다음 표준 오류 메시지를 출력합니다.

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. 다음 명령을 실행하여 단어 수 결과를 표시합니다.

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.


## <a id="java-code"></a>Hadoop 스트리밍의 C# 코드


MapReduce 프로그램은 cat.exe 응용 프로그램을 매핑 인터페이스로 사용하여 텍스트를 콘솔로 스트리밍하고 wc.exe 응용 프로그램을 리듀싱 인터페이스로 사용하여 문서에서 스트리밍되는 단어 수를 계산합니다. mapper와 reducer는 둘 다 표준 입력 스트림(stdin)에서 문자를 줄 단위로 읽고 표준 출력 스트림(stdout)에 씁니다.



	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



cat.cs 파일의 mapper 코드는 [StreamReader][streamreader] 개체를 사용하여 들어오는 스트림의 문자를 콘솔로 읽어오며, 콘솔에서 정적 [Console.Writeline][console-writeline] 메서드를 사용하여 스트림을 표준 출력 스트림에 씁니다.


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


wc.cs 파일의 리듀서 코드는 [StreamReader][streamreader] 개체를 사용하여 cat.exe 매퍼에 의해 출력된 표준 입력 스트림에서 문자를 읽습니다. [Console.Writeline][console-writeline] 메서드를 사용하여 문자를 읽고 공백 및 각 단어 끝에 있는 줄의 끝 문자를 계산하여 단어 수를 셉니다. 그런 다음 [Console.Writeline][console-writeline] 메서드를 사용하여 합계를 표준 출력 스트림에 작성합니다.


## <a id="summary"></a>요약

이 자습서에서는 Hadoop 스트리밍을 사용하여 HDInsight에 MapReduce 작업을 배포하는 방법을 살펴보았습니다.

## <a id="next-steps"></a>다음 단계


다른 샘플을 실행하고 Azure PowerShell을 통해 Azure HDInsight에서 Pig, Hive 및 MapReduce 작업을 실행하는 방법에 대한 지침을 제공하는 자습서는 다음 문서를 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [샘플: Pi 추정][hdinsight-sample-pi-estimator]
* [샘플: 단어 개수][hdinsight-sample-wordcount]
* [샘플: 10GB GraySort][hdinsight-sample-10gb-graysort]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [Azure HDInsight SDK 설명서][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=August15_HO8-->