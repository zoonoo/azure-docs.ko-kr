<properties linkid="manage-services-hdinsight-sample-csharp-streaming" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight C# streaming wordcount sample | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a sample TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight C# streaming wordcount sample" authors="bradsev" />

HDInsight C\# 스트리밍 단어 수 샘플
===================================

Hadoop는 맵을 작성하고 Java가 아닌 다른 언어의 함수를 줄일 수 있는 스트리밍 API를 MapReduce에 제공합니다. 이 자습서에서는 Hadoop 스트리밍 인터페이스를 사용하는 MapReduce 프로그램을 C\#으로 작성하는 방법 및 Azure PowerShell을 사용하여 Azure HDInsight에서 프로그램을 실행하는 방법을 보여 줍니다.

예제에서 매퍼 및 리듀서는 모두 [stdin](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx)에서 입력을 줄 단위로 읽고 출력을 [stdout](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx)으로 내보내는 실행 파일입니다. 프로그램은 텍스트의 모든 단어 수를 계산합니다.

**매퍼**에 대해 실행 파일이 지정된 경우 각 매퍼 작업은 매퍼가 초기화될 때 개별 프로세스로 실행 파일을 시작합니다. 실행 시 매퍼 작업은 입력을 줄로 변환하고 프로세스의 [stdin](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx)에 줄을 공급합니다. 그 동안 매퍼는 프로세스의 stdout에서 줄 지향 출력을 수집하고 각 줄을 키/값 쌍으로 변환합니다. 키/값 쌍은 매퍼의 출력으로 수집됩니다. 기본적으로 첫 번째 탭 문자까지 줄의 접두사는 키이고 줄의 나머지(탭 문자 제외)는 값입니다. 줄에 탭 문자가 없는 경우에는 전체 줄이 키로 간주되고 값은 null입니다.

**리듀서**에 대해 실행 파일이 지정된 경우 각 리듀서 작업은 리듀서가 초기화될 때 개별 프로세스로 실행 파일을 시작합니다. 실행 시 리듀서 작업은 입력 키/값 쌍을 줄로 변환하고 프로세스의 [stdin](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx)에 줄을 공급합니다. 그 동안 리듀서는 프로세스의 [stdout](http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx)에서 줄 지향 출력을 수집하고 각 줄을 키/값 쌍으로 변환합니다. 키/값 쌍은 리듀서의 출력으로 수집됩니다. 기본적으로 첫 번째 탭 문자까지 줄의 접두사는 키이고 줄의 나머지(탭 문자 제외)는 값입니다.

Hadoop 스트리밍 인터페이스에 대한 자세한 내용은 [Hadoop 스트리밍](http://wiki.apache.org/hadoop/HadoopStreaming)(영문)을 참조하십시오.

**다음 내용을 배웁니다.**

-   Azure PowerShell을 통해 C\# 스트리밍 프로그램을 실행하여 HDInsight의 파일에 포함된 데이터를 분석하는 방법
-   Hadoop 스트리밍 인터페이스를 사용하는 C\# 코드를 작성하는 방법

**필수 조건**:

-   Azure 계정이 있어야 합니다. 계정 등록 옵션은 [Azure 평가판 사용](http://www.windowsazure.com/en-us/pricing/free-trial/) 페이지를 참조하십시오.

-   HDInsight 클러스터를 미리 프로비전해야 합니다. 이 클러스터를 만드는 여러 방법에 대한 지침은 [HDInsight 클러스터 프로비전](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)을 참조하십시오.

-   Azure PowerShell을 설치하고 계정과 함께 사용하도록 구성해야 합니다. 설치 방법에 대한 지침은 [Azure PowerShell 설치 및 구성](/en-us/documentation/articles/install-configure-powershell/)을 참조하십시오.

이 문서의 내용
--------------

이 항목에서는 샘플을 실행하는 방법을 보여 주고, MapReduce 프로그램의 Java 코드를 제공하며, 배운 내용을 요약하고, 다음 몇 가지 단계를 간략하게 설명합니다. 다음 섹션이 포함되어 있습니다.

1.  [Azure PowerShell을 사용하여 샘플 실행](#run-sample)
2.  [Hadoop 스트리밍의 C\# 코드](#java-code)
3.  [요약](#summary)
4.  [다음 단계](#next-steps)

Azure PowerShell을 사용하여 샘플 실행
-------------------------------------

**MapReduce 작업을 실행하려면**

1.  **Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성](/en-us/documentation/articles/install-configure-powershell/)을 참조하십시오.

2.  다음 명령에 있는 변수 2개를 설정한 후 실행합니다.

         $subscriptionName = "<SubscriptionName>"   # Azure subscription name
         $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  다음 명령을 실행하여 MapReduce 작업을 정의합니다.

         # Create a MapReduce job definition for the streaming job.
         $streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

    매개 변수는 매퍼 및 리듀서 함수와 입력 파일 및 출력 파일을 지정합니다.

4.  다음 명령을 실행하여 MapReduce 작업을 실행하고, 작업이 완료될 때까지 기다린 다음 표준 오류를 출력합니다.

         # Run the C# Streaming MapReduce job.
         # Wait for the job to complete.
         # Print output and standard error file of the MapReduce job
         Select-AzureSubscription $subscriptionName
         $streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5.  다음 명령을 실행하여 단어 수 결과를 표시합니다.

         $subscriptionName = "<SubscriptionName>"   
         $storageAccountName = "<StorageAccountName>" 
         $containerName = "<ContainerName>

    "

         # Select the current subscription
         Select-AzureSubscription $subscriptionName
                  
         # Blob storage container and account name

    \$storageAccountKey = Get-AzureStorageKey -StorageAccountName \$storageAccountName | %{ \$\_.Primary } \$storageContext = New-AzureStorageContext -StorageAccountName \$storageAccountName -StorageAccountKey \$storageAccountKey

         # Retrieve the output
         Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

         # The number of words in the text is:
         cat ./example/data/StreamingOutput/wc.txt/part-00000

    MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.

Hadoop 스트리밍의 C\# 코드
--------------------------

MapReduce 프로그램은 cat.exe 응용 프로그램을 매핑 인터페이스로 사용하여 텍스트를 콘솔로 스트리밍하고 wc.exe 응용 프로그램을 리듀서 인터페이스로 사용하여 문서에서 스트리밍되는 단어 수를 계산합니다. 매퍼와 리듀서는 둘 다 표준 입력 스트림(stdin)에서 문자를 줄 단위로 읽고 표준 출력 스트림(stdout)에 씁니다.

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

cat.cs 파일의 매퍼 코드는 StreamReader 개체를 사용하여 들어오는 스트림의 문자를 콘솔로 읽어오며, 콘솔에서 정적 Console.Writeline 메서드를 사용하여 스트림을 표준 출력 스트림에 씁니다.

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

wc.cs 파일의 리듀서 코드는 [StreamReader](http://msdn.microsoft.com/en-us/library/system.io.streamreader.aspx) 개체를 사용하여 cat.exe 매퍼에 의해 출력된 표준 입력 스트림에서 문자를 읽습니다. [Console.Writeline](http://msdn.microsoft.com/en-us/library/system.console.writeline) 메서드를 사용하여 문자를 읽는 경우 각 단어의 끝에 있는 공백과 줄의 끝 문자를 포함하여 단어 수를 계산한 다음 [Console.Writeline](http://msdn.microsoft.com/en-us/library/system.console.writeline) 메서드를 사용하여 합계를 표준 출력 스트림에 씁니다.

요약
----

이 자습서에서는 Hadoop 스트리밍을 사용하여 HDInsight에 MapReduce 작업을 배포하는 방법을 살펴보았습니다.

다음 단계
---------

기타 샘플을 실행하고 Azure PowerShell을 통해 Azure HDInsight에서 Pig, Hive 및 MapReduce 작업을 사용하는 방법에 대한 지침을 제공하는 자습서는 다음 항목을 참조하십시오.

-   [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [샘플: Pi Estimator](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [샘플: Wordcount](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [샘플: 10GB GraySort](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [HDInsight와 함께 Pig 사용](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [HDInsight와 함께 Hive 사용](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Azure HDInsight SDK 문서](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

