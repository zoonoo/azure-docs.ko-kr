<properties urlDisplayName="" pageTitle="HDInsight용 C# Hadoop 스트리밍 프로그램 개발 | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="C#에서 Hadoop 스트리밍 MapReduce 프로그램을 개발한 다음 Azure HDInsight에 배포하는 방법에 대해 알아봅니다." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight용 C# Hadoop 스트리밍 프로그램 개발" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# HDInsight용 C# Hadoop 스트리밍 프로그램 개발

Hadoop은 MapReduce용 스트리밍 API를 제공합니다. 이 API를 사용하여 Java 이외의 언어로 map 및 reduce 함수를 작성할 수 있습니다. 이 자습서에서는 사용자가 제공하는 입력 데이터 내의 지정된 단어 수를 계산하는 단어 계산 시나리오를 안내합니다. 다음 그림에는 MapReduce 프레임워크에서 단어 계산을 수행하는 방법이 나와 있습니다.

![HDI.WordCountDiagram][HDI.WordCountDiagram]

이 항목에서는 다음을 수행하는 방법에 대해 설명합니다.

-   HDInsight Emulator에서 C#을 사용하여 Hadoop 스트리밍 MapReduce 프로그램 개발 및 테스트
-   Azure HDInsight에서 동일한 MapReduce 작업 실행
-   MapReduce 작업 결과 검색

**필수 조건:**

이 자습서를 시작하기 전에 다음을 완료해야 합니다.

-   Azure HDInsight Emulator 설치. 자세한 내용은 [HDInsight Emulator 사용 시작][HDInsight Emulator 사용 시작]을 참조하세요.
-   에뮬레이터 컴퓨터에 Azure PowerShell 설치. 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요.
-   Azure 구독. 자세한 내용은 [구매 옵션][구매 옵션], [회원 제안][회원 제안] 또는 [무료 평가판][무료 평가판]을 참조하세요.

## 이 문서에서는 다음을 수행합니다.

-   [C#으로 단어 계산 Hadoop 스트리밍 프로그램 개발][C#으로 단어 계산 Hadoop 스트리밍 프로그램 개발]
-   [에뮬레이터에서 프로그램 테스트][에뮬레이터에서 프로그램 테스트]
-   [Azure Blob 저장소로 데이터 및 응용 프로그램 업로드][Azure Blob 저장소로 데이터 및 응용 프로그램 업로드]
-   [Azure HDInsight에서 MapReduce 프로그램 실행][Azure HDInsight에서 MapReduce 프로그램 실행]
-   [MapReduce 결과 검색][MapReduce 결과 검색]
-   [다음 단계][다음 단계]

## <a name="develop"></a>C#으로 단어 계산 Hadoop 스트리밍 프로그램 개발

단계 계산 솔루션에는 콘솔 응용 프로그램 프로젝트인 매퍼 및 리듀서가 포함되어 있습니다. 매퍼 응용 프로그램은 각 단어를 콘솔로 스트림하며, 리듀서 응용 프로그램은 문서에서 스트림된 단어 수를 계산합니다. 매퍼와 리듀서는 모두 표준 입력 스트림(stdin)에서 문자를 한 줄씩 읽어 표준 출력 스트림(stdout)에 기록합니다.

**C# 콘솔 응용 프로그램을 만들려면**

1.  Visual Studio 2013을 엽니다.
2.  **파일**, **새로 만들기**를 클릭한 후 **프로젝트**를 클릭합니다.
3.  다음 값을 입력하거나 선택합니다.

    |-------------|----------------------------------------|
    | 필드        | 값                                     |
    | 템플릿      | Visual C#/Windows/Console Application |
    | 이름        | WordCountMapper                        |
    | Location    | C:\\Tutorials                          |
    | 솔루션 이름 | WordCount                              |

4.  **확인**을 클릭하여 프로젝트를 만듭니다.

**매퍼 프로그램을 만들려면**

1.  솔루션 탐색기에서 **Program.cs**를 마우스 오른쪽 단추로 클릭한 후 **이름 바꾸기**를 클릭합니다.
2.  파일의 이름을 **WordCountMapper.cs**로 바꾼 후 **ENTER** 키를 누릅니다.
3.  **예**를 클릭하여 모든 참조의 이름 바꾸기를 확인합니다.
4.  **WordCountMapper.cs**를 두 번 클릭하여 엽니다.
5.  다음 using 문을 추가합니다.

        using System.IO;

6.  Main() 함수를 다음으로 바꿉니다.

        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            string[] words;

            while ((line = Console.ReadLine()) != null)
            {
                words = line.Split(' ');

                foreach (string word in words)
                    Console.WriteLine(word.ToLower());
            }
        }

7.  **빌드**를 클릭한 후 **솔루션 빌드**를 클릭하여 매퍼 프로그램을 컴파일합니다.

**리듀서 프로그램을 만들려면**

1.  Visual Studio 2013에서 **파일**, **추가**, **새 프로젝트**를 차례로 클릭합니다.
2.  다음 값을 입력하거나 선택합니다.

    |----------|----------------------------------------|
    | 필드     | 값                                     |
    | 템플릿   | Visual C#/Windows/Console Application |
    | 이름     | WordCountReducer                       |
    | Location | C:\\Tutorials\\WordCount               |

3.  **솔루션용 디렉터리 만들기** 확인란 선택을 취소하고 **확인**을 클릭하여 프로젝트를 만듭니다.
4.  솔루션 탐색기에서 **Program.cs**를 마우스 오른쪽 단추로 클릭한 후 **이름 바꾸기**를 클릭합니다.
5.  파일의 이름을 **WordCountReducer.cs**로 바꾼 후 **Enter** 키를 누릅니다.
6.  **예**를 클릭하여 모든 참조의 이름 바꾸기를 확인합니다.
7.  **WordCountReducer.cs**를 두 번 클릭하여 엽니다.
8.  다음 using 문을 추가합니다.

        using System.IO;

9.  Main() 함수를 다음으로 바꿉니다.

        static void Main(string[] args)
        {
            string word, lastWord = null;
            int count = 0;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            while ((word = Console.ReadLine()) != null)
            {
                if (word != lastWord)
                {
                    if(lastWord != null)
                        Console.WriteLine("{0}[{1}]", lastWord, count);

                    count = 1;
                    lastWord = word;
                }
                else
                {
                    count += 1; 
                }
            }
            Console.WriteLine(count);
        }

10. **빌드**를 클릭한 후 **솔루션 빌드**를 클릭하여 리듀서 프로그램을 컴파일합니다.

매퍼 및 리듀서 실행 파일의 경로는 다음과 같습니다.

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>에뮬레이터에서 프로그램 테스트

에뮬레이터에서 다음을 수행하여 프로그램을 테스트합니다.

1.  에뮬레이터 HDFS에 데이터 업로드
2.  에뮬레이터 HDFS에 매퍼 및 리듀서 응용 프로그램 업로드
3.  단어 계산 MapReduce 작업 제출
4.  작업 상태 확인
5.  작업 결과 검색

기본적으로 HDInsight Emulator는 HDFS를 기본 파일 시스템으로 사용합니다. 선택적으로 Azure Blob 저장소를 사용하도록 HDInsight Emulator를 구성할 수 있습니다. 자세한 내용은 [HDInsight Emulator 시작][HDInsight Emulator 시작]을 참조하세요. 이 섹션에서는 HDFS *copyFromLocal* 명령을 사용하여 파일을 업로드합니다. 다음 섹션에서는 Azure PowerShell을 사용하여 파일을 업로드하는 방법을 보여 줍니다. 다른 방법에 대해서는 [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]를 참조하세요.

이 자습서에서는 다음 폴더 구조를 사용합니다.

|-----------------------------|--------------------------------|
| 폴더                        | 참고                           |
| \\WordCount                 | 단어 계산 프로젝트의 루트 폴더 |
| \\WordCount\\Apps           | 매퍼 및 리듀서 실행 파일 폴더  |
| \\WordCount\\Input          | MapReduce 원본 파일 폴더       |
| \\WordCount\\Output         | MapReduce 출력 파일 폴더       |
| \\WordCount\\MRStatusOutput | 작업 출력 폴더                 |

</br>

이 자습서에서는 %hadoop\_home% 디렉터리에 있는 .txt 파일을 사용합니다.

> [WACOM.NOTE] Hadoop HDFS 명령은 대/소문자를 구분합니다.

**에뮬레이터 HDFS로 텍스트 파일을 복사하려면**

1.  Hadoop 명령줄 창에서 다음 명령을 실행하여 입력 파일용 디렉터리를 만듭니다.

        hadoop fs -mkdir /WordCount/
        hadoop fs -mkdir /WordCount/Input

    여기에서 사용한 경로는 상대 경로입니다. 이 경로는 다음과 같습니다.

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  다음 명령을 실행하여 HDFS의 입력 폴더로 몇 개의 텍스트 파일을 복사합니다.

        hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common\*.txt \WordCount\Input

3.  다음 명령을 실행하여 업로드된 파일을 나열합니다.

        hadoop fs -ls \WordCount\Input

**에뮬레이터 HDFS에 매퍼 및 리듀서를 배포하려면**

1.  바탕 화면에서 Hadoop 명령줄을 열고 다음과 같이 HDFS에 /Apps 폴더를 만듭니다.
     hadoop fs -mkdir /WordCount/Apps

2.  다음 명령을 실행합니다.

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  다음 명령을 실행하여 업로드된 파일을 나열합니다.

        hadoop fs -ls /WordCount/Apps

    두 개의 .exe 파일이 표시됩니다.

**HDInsight PowerShell을 사용하여 MapReduce 작업을 실행하려면**

1.  Azure PowerShell을 엽니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요.
2.  다음 명령을 실행하여 변수를 설정합니다.

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    HDInsight Emulator 클러스터 이름은 "http://localhost:50111"입니다.

3.  다음 명령을 실행하여 스트리밍 작업을 정의합니다.

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

4.  다음 명령을 실행하여 자격 증명 개체를 만듭니다.

        $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    암호를 입력하라는 메시지가 표시됩니다. 암호는 아무 문자열이나 사용할 수 있습니다. 사용자 이름은 "hadoop"이어야 합니다.

5.  다음 명령을 실행하여 MapReduce 작업을 제출하고 작업이 완료될 때까지 기다립니다.

        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
        Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

    작업이 완료되면 다음과 유사한 출력이 표시됩니다.

        StatusDirectory : /WordCount/MRStatusOutput
        ExitCode        : 
        Name            : mrWordCountStreamingJob
        Query           : 
        State           : Completed
        SubmissionTime  : 11/15/2013 7:18:16 PM
        Cluster         : http://localhost:50111
        PercentComplete : map 100%  reduce 100%
        JobId           : job_201311132317_0034

    출력에서 *job-201311132317-0034*와 같은 작업 ID를 확인할 수 있습니다.

**작업 상태를 확인하려면**

1.  바탕 화면에서 **Hadoop YARN 상태**를 클릭하거나 **http://localhost:50030/jobtracker.jsp**로 이동합니다.
2.  **실행 중** 또는 **완료됨** 범주 아래의 작업 ID를 사용하여 작업을 찾습니다.
3.  작업이 실패한 경우 **실패** 범주에서 작업 ID를 찾을 수 있습니다. 작업 세부 정보를 열어 디버깅에 유용한 몇몇 정보를 찾아볼 수도 있습니다.

**HDFS에서 출력을 표시하려면**

1.  Hadoop 명령줄을 엽니다.
2.  다음 명령을 실행하여 출력을 표시합니다.

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    페이지 보기를 가져오기 위해 명령의 끝 부분에 "|more"를 추가할 수 있습니다.

## <span id="upload"></span></a>Azure Blob 저장소에 데이터 업로드

Azure HDInsight는 Azure Blob 저장소를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 구성하여 데이터 파일에 추가 Blob 저장소를 사용할 수도 있습니다. 이 섹션에서는 저장소 계정을 만들어 Blob 저장소에 데이터 파일을 업로드합니다. 데이터 파일은 %hadoop\_home%\\share\\doc\\hadoop\\common 디렉터리에 있는 .txt 파일입니다.

**Blob 저장소 및 컨테이너를 만들려면**

1.  Azure PowerShell을 엽니다.
2.  다음과 같이 변수를 설정한 후 명령을 실행합니다.

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  다음 명령을 실행하여 저장소 계정 및 계정의 Blob 저장소 컨테이너를 만듭니다.

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  다음 명령을 실행하여 저장소 계정 및 컨테이너를 확인합니다.

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**데이터 파일을 업로드하려면**

1.  Azure PowerShell 창에서 로컬 폴더 및 대상 폴더의 값을 설정합니다.

        $localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
        $destFolder = "WordCount/Input"

    로컬 원본 파일 폴더는 **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common**이고 대상 폴더는 **WordCount/Input**입니다. 원본 위치는 HDInsight Emulator의 .txt 파일 위치입니다. 대상은 Azure Blob 컨테이너 아래에 반영되는 폴더 구조입니다.

2.  다음 명령을 실행하여 원본 파일 폴더의 .txt 파일 목록을 가져옵니다.

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

3.  다음 코드 조각을 실행하여 파일을 복사합니다.

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

4.  다음 명령을 실행하여 업로드된 파일을 나열합니다.

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**단어 계산 응용 프로그램을 업로드하려면**

1.  Azure PowerShell 창에서 다음 변수를 설정합니다.

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    대상 폴더는 Azure Blob 컨테이너에 반영되는 구조인 **WordCount/Apps**입니다.

2.  다음 명령을 실행하여 응용 프로그램을 복사합니다.

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3.  다음 명령을 실행하여 업로드된 파일을 나열합니다.

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    두 응용 프로그램 파일의 목록이 모두 표시됩니다.

## <a name="run"></a>Azure HDInsight에서 MapReduce 작업 실행

이 섹션에서는 MapReduce 작업 실행과 관련된 모든 작업을 수행하는 PowerShell 스크립트를 제공합니다. 작업 목록에는 다음이 포함됩니다.

1.  HDInsight 클러스터 프로비전

    1.  기본 HDInsight 클러스터 파일 시스템으로 사용할 저장소 계정 만들기
    2.  Blob 저장소 컨테이너 만들기
    3.  HDInsight 클러스터 만들기

2.  MapReduce 작업 제출

    1.  스트리밍 MapReduce 작업 정의 만들기
    2.  MapReduce 작업 제출
    3.  작업 완료 대기
    4.  표준 오류 표시
    5.  표준 출력 표시

3.  클러스터 삭제

    1.  HDInsight 클러스터 삭제
    2.  기본 HDInsight 클러스터 파일 시스템으로 사용된 저장소 계정 삭제

**PowerShell 스크립트를 실행하려면**

1.  메모장을 엽니다.
2.  다음 코드를 복사하여 붙여넣습니다.

        # ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
        $subscriptionName = "<AzureSubscriptionName>"
        $stringPrefix = "<StringForPrefix>"     ### prefix to cluster, storage account, and container names
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $stringPrefix + "hdicluster"

        $storageAccountName_Default = $stringPrefix + "hdistore"
        $containerName_Default =  $stringPrefix + "hdicluster"

        # ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #====== CREATE A STORAGE ACCOUNT ======
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #====== CREATE A BLOB STORAGE CONTAINER ======
        Write-Host "Create a Blob storage container" -ForegroundColor Green
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

        New-AzureStorageContainer -Name $containerName_Default -Context $destContext

        #====== CREATE AN HDINSIGHT CLUSTER ======
        Write-Host "Create an HDInsight cluster" -ForegroundColor Green
        $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

        Select-AzureSubscription $subscriptionName
        New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config

        #====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #====== RUN A STREAMING MAPREDUCE JOB ======
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        Select-AzureSubscription $subscriptionName
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

        #====== DELETE THE HDINSIGHT CLUSTER ======
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Select-AzureSubscription $subscriptionName
        Remove-AzureHDInsightCluster -Name $clusterName 

        #====== DELETE THE STORAGE ACCOUNT ======
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  스크립트에서 첫 4개의 변수를 설정합니다. **$stringPrefix**은 HDInsight 클러스터 이름, 저장소 계정 이름 및 Blob 저장소 컨테이너 이름에 지정된 문자열을 접두사로 추가하는 데 사용됩니다. 이러한 항목의 이름은 3~24자여야 하므로 지정하는 문자열과 이 스크립트가 사용하는 이름을 합한 길이가 이름의 문자 제한을 초과하지 않는지 확인해야 합니다. $stringPrefix에는 모두 소문자를 사용해야 합니다.

    **$storageAccountName\_Data** 및 **$containerName\_Data**는 이전 단계에서 이미 만든 저장소 계정과 컨테이너입니다. 따라서 해당 이름을 제공해야 합니다. 이 두 항목은 데이터 파일과 응용 프로그램을 저장하는 데 사용됩니다. $location은 데이터 저장소 계정 위치와 일치해야 합니다.

4.  나머지 변수를 검토합니다.
5.  스크립트 파일을 저장합니다.
6.  Azure PowerShell을 엽니다.
7.  다음 명령을 실행하여 remotesigned에 대한 실행 정책을 설정합니다.

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  메시지가 표시되면 HDInsight 클러스터의 사용자 이름 및 암호를 입력합니다. 암호가 10자 이상이고 대문자, 소문자, 숫자 및 특수 문자를 하나씩 포함하는지 확인합니다. 자격 증명을 묻는 메시지를 표시하지 않으려면 [Windows PowerShell에서 암호, 보안 문자열 및 자격 증명 작업][Windows PowerShell에서 암호, 보안 문자열 및 자격 증명 작업](영문)을 참조하세요.

Hadoop 스트리밍 작업 제출에 대한 HDInsight .NET SDK 샘플을 보려면 [프로그래밍 방식으로 Hadoop 작업 제출][프로그래밍 방식으로 Hadoop 작업 제출](영문)을 참조하세요.

## <a name="retrieve"></a>MapReduce 작업 출력 검색

이 섹션에서는 출력을 다운로드하고 표시하는 방법을 보여 줍니다. Excel에서 결과를 표시하는 방법에 대한 자세한 내용은 [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결][HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결] 및 [HDInsight에 파워 쿼리로 Excel 연결][HDInsight에 파워 쿼리로 Excel 연결]을 참조하세요.

**출력을 검색하려면**

1.  Azure PowerShell 창을 엽니다.
2.  다음과 같이 값을 설정한 후 명령을 실행합니다.

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  다음 명령을 실행하여 Azure 저장소 컨텍스트 개체를 만듭니다.

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  다음 명령을 실행하여 출력을 다운로드하고 표시합니다.

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>다음 단계

이 자습서에서는 Hadoop 스트리밍 MapReduce 작업을 개발하는 방법, HDInsight Emulator에서 응용 프로그램을 테스트하는 방법 및 HDInsight 클러스터를 프로비전하고 클러스터에서 MapReduce를 실행하도록 PowerShell 스크립트를 작성하는 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

-   [Azure HDInsight 시작][Azure HDInsight 시작]
-   [HDInsight Emulator 시작][HDInsight Emulator 사용 시작]
-   [HDInsight용 Java MapReduce 프로그램 개발][HDInsight용 Java MapReduce 프로그램 개발]
-   [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]
-   [PowerShell을 사용하여 HDInsight 관리][PowerShell을 사용하여 HDInsight 관리]
-   [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]
-   [HDInsight에서 Hive 사용][HDInsight에서 Hive 사용]
-   [HDInsight에서 Pig 사용][HDInsight에서 Pig 사용]

  [HDI.WordCountDiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "MapReduce 단어 계산 응용 프로그램 흐름"
  [HDInsight Emulator 사용 시작]: ../hdinsight-get-started-emulator/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [구매 옵션]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
  [회원 제안]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
  [무료 평가판]: http://azure.microsoft.com/ko-kr/pricing/free-trial/
  [C#으로 단어 계산 Hadoop 스트리밍 프로그램 개발]: #develop
  [에뮬레이터에서 프로그램 테스트]: #test
  [Azure Blob 저장소로 데이터 및 응용 프로그램 업로드]: #upload
  [Azure HDInsight에서 MapReduce 프로그램 실행]: #run
  [MapReduce 결과 검색]: #retrieve
  [다음 단계]: #nextsteps
  [HDInsight Emulator 시작]: ../hdinsight-get-started-emulator/#blobstorage
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
  [Windows PowerShell에서 암호, 보안 문자열 및 자격 증명 작업]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [프로그래밍 방식으로 Hadoop 작업 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [HDInsight에 파워 쿼리로 Excel 연결]: ../hdinsight-connect-excel-power-query/
  [Azure HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight용 Java MapReduce 프로그램 개발]: ../hdinsight-develop-deploy-java-mapreduce/
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [PowerShell을 사용하여 HDInsight 관리]: ../hdinsight-administer-use-powershell/
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig/
