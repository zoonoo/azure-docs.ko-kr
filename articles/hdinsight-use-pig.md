<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Pig with HDInsight" pageTitle="Use Pig with HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Pig with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

HDInsight와 함께 Pig 사용
=========================

[Apache *Pig*](http://pig.apache.org/)는 Java 코드를 작성하는 대신 *MapReduce* 작업을 실행하는 스크립트 언어를 제공합니다. 이 자습서에서는 PowerShell을 사용하여 일부 Pig Latin 문을 실행하여 Apache log4j 로그 파일을 분석하고 데이터에 대해 다양한 쿼리를 실행하여 출력을 생성합니다. 이 자습서에서는 Pig의 장점 및 Pig를 사용하여 MapReduce 작업을 간소화할 수 있는 방법을 보여 줍니다.

Pig의 스크립트 언어는 *Pig Latin*입니다. Pig Latin 문은 다음과 같은 일반적인 흐름을 따릅니다.

-   **로드**: 파일 시스템에서 조작할 데이터 읽기
-   **변환**: 데이터 조작
-   **덤프 또는 저장**: 데이터를 화면에 출력하거나 처리를 위해 저장

Pig Latin에 대한 자세한 내용은 [Pig Latin 참조 설명서 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html)(영문) 및 [Pig Latin 참조 설명서 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html)(영문)를 참조하십시오.

**필수 조건**

이 문서를 시작하기 전에 다음 요구 사항을 확인하십시오.

-   Azure HDInsight 클러스터를 만듭니다. 자세한 내용은 [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/) 또는 [HDInsight 클러스터 프로비전](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)을 참조하십시오.
-   Azure PowerShell을 설치하고 구성합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/ko-kr/documentation/articles/install-configure-powershell/)을 참조하십시오.

**예상 완료 시간:** 30분

이 문서에서는 다음을 수행합니다.
--------------------------------

-   [Pig 사용 사례](#usage)
-   [Azure Blob 저장소에 데이터 파일 업로드](#uploaddata)
-   [Pig Latin 이해](#understand)
-   [PowerShell을 사용하여 Pig Latin 실행](#powershell)
-   [다음 단계](#nextsteps)

Pig 사용 사례
-------------

데이터베이스는 작은 데이터 집합과 대기 시간이 짧은 쿼리에 매우 적합합니다. 그러나 빅데이터와 테라바이트 규모의 대용량 데이터 집합의 경우에는 기존의 SQL 데이터베이스가 이상적인 솔루션이 아닙니다. 역사적으로 데이터베이스 로드가 증가하고 성능이 떨어지면서, 데이터베이스 관리자는 더 큰 하드웨어를 구입해야 했습니다.

일반적으로 모든 응용 프로그램은 오류, 예외 및 기타 코딩 문제를 로그 파일에 저장하므로, 관리자는 문제를 검토하거나 로그 파일 데이터에서 특정 메트릭을 생성할 수 있습니다. 이러한 로그 파일은 대개 크기가 상당히 크며, 처리 및 마이닝을 거쳐야 하는 풍부한 데이터를 포함합니다.

로그 파일은 빅데이터의 좋은 예입니다. 빅데이터는 관계형 데이터베이스 및 통계/시각화 패키지를 사용하여 작업하기가 어렵습니다. 데이터 양이 많고 이 데이터를 계산하기 위해 수십, 수백, 심지어 수천 대의 서버에서 병렬로 실행하는 소프트웨어가 있어야 합당한 시간 내에 이 데이터를 계산할 수 있는 경우가 많습니다. Hadoop의 MapReduce 프레임워크에서는 대규모 컴퓨터 클러스터에서 구조화된 데이터 및 구조화되지 않은 대량의 데이터를 안정성과 내결함성이 높은 방식으로 병렬 처리하는 응용 프로그램을 작성할 수 있습니다.

Pig를 사용하면 매퍼 및 리듀서 프로그램을 작성하는 데 필요한 시간이 줄어듭니다. 다시 말해서 Java가 필요 없으며 상용구 코드도 필요 없습니다. Java 코드를 Pig와 유연하게 결합할 수도 있습니다. 사람이 인식할 수 있는 Pig 코드 다섯 줄 미만으로 많은 복잡한 알고리즘을 작성할 수 있습니다.

다음 두 그림은 이 자습서에서 수행할 내용을 시각적으로 표현합니다. 이 그림은 스크립트에서 Pig 코드 줄을 진행함에 따른 데이터의 흐름과 변환을 표현하는 데이터 집합의 전형적인 샘플을 보여 줍니다. 첫 번째 그림은 log4j 파일 샘플을 보여 줍니다.

![전체 파일 샘플](./media/hdinsight-use-pig/HDI.wholesamplefile.png)

두 번째 그림은 데이터 변환을 보여 줍니다.

![HDI.PIG.Data.Transformation](./media/hdinsight-use-pig/HDI.DataTransformation.gif)



Blob 저장소에 데이터 파일 업로드
--------------------------------

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. 자세한 내용은 [HDInsight와 함께 Azure Blob 저장소 사용](/en-us/manage/services/hdinsight/howto-blob-store/)을 참조하십시오. 이 문서에서는 HDInsight 클러스터로 배포한 log4j 샘플 파일(*\\example\\data\\sample.log*에 저장됨)을 사용합니다. 데이터 업로드에 대한 자세한 내용은 [HDInsight에 데이터 업로드](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)를 참조하십시오.

파일에 액세스하려면 다음 구문을 사용합니다.

     wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

예를 들면 다음과 같습니다.

     wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

*mycontainer*를 컨테이너 이름으로, *mystorage*를 Blob 저장소 계정 이름으로 교체합니다.

파일은 기본 파일 시스템에 저장되므로 다음을 사용해 파일에 액세스할 수도 있습니다.

     wasb:///example/data/sample.log
     /example/data/sample.log


Pig Latin 이해
--------------

이 세션에서는 일부 Pig Latin 문과 이 문을 실행한 후의 결과를 검토합니다. 다음 세션에서는 PowerShell을 실행하여 Pig 문을 실행합니다.

1.  파일 시스템에서 데이터를 로드하고 결과를 표시합니다.

         LOGS = LOAD 'wasb:///example/data/sample.log';
         DUMP LOGS;

    다음과 유사하게 출력됩니다.

         (2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
         (2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
         (2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
         (2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
         (2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
         (2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
         ...

2.  데이터 파일에서 각 줄을 검토하여 6개의 로그 수준에서 일치 항목을 찾습니다.

         LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  일치 항목이 없는 행을 필터링합니다. 빈 행을 예로 들 수 있습니다.

         FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
         DUMP FILTEREDLEVELS;

    다음과 유사하게 출력됩니다.

         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         ...

4.  모든 로그 수준을 고유 행으로 그룹화합니다.

         GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
         DUMP GROUPEDLEVELS;

    다음과 유사하게 출력됩니다.

         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE), ...

5.  각 그룹에서 로그 수준의 수를 계산합니다. 각 로그 수준의 빈도입니다.

         FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
         DUMP FREQUENCIES;

    다음과 유사하게 출력됩니다.

         (INFO,3355)
         (WARN,361)
         (DEBUG,15608)
         (ERROR,181)
         (FATAL,37)
         (TRACE,29950)

6.  빈도를 내림차순으로 정렬합니다.

         RESULT = order FREQUENCIES by COUNT desc;
         DUMP RESULT;   

    다음과 유사하게 출력됩니다.

         (TRACE,29950)
         (DEBUG,15608)
         (INFO,3355)
         (WARN,361)
	     (ERROR,181)
         (FATAL,37)

PowerShell을 사용하여 Pig Latin 실행
------------------------------------

이 섹션에서는 PowerShell cmdlet 사용 지침을 설명합니다. 이 섹션을 진행하기 전에 먼저 로컬 환경을 설정하고 Azure 연결을 구성해야 합니다. 자세한 내용은 [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/) 및 [PowerShell을 사용하여 HDInsight 관리](/en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/)를 참조하십시오.

**PowerShell을 사용하여 Pig Latin을 실행하려면**

1.  Azure PowerShell 콘솔 창을 엽니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/ko-kr/documentation/articles/install-configure-powershell/)을 참조하십시오.
2.  다음 명령을 실행하여 Azure 구독에 연결합니다.

         Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  다음 스크립트에서 변수를 설정하고 실행합니다.

         $clusterName = "<HDInsightClusterName>" 

4.  다음 명령을 실행하여 Pig Latin 쿼리 문자열을 정의합니다.

         # Create the Pig job definition
         $0 = '$0';
         $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                         "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                         "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                         "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                         "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                         "RESULT = order FREQUENCIES by COUNT desc;" +
                         "DUMP RESULT;" 
            
         $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

    -File 스위치를 사용하여 HDFS에서 Pig 스크립트 파일을 지정할 수도 있습니다.

5.  다음 스크립트를 실행하여 Pig 작업을 제출합니다.

         # Submit the Pig job
         Select-AzureSubscription $subscriptionName
         $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

6.  다음 스크립트를 실행하여 Pig 작업이 완료되기를 기다립니다.

         # Wait for the Pig job to complete
         Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  다음 스크립트를 실행하여 Pig 작업 출력을 인쇄합니다.

         # Print the standard error and the standard output of the Pig job.
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    ![HDI.Pig.PowerShell](./media/hdinsight-use-pig/hdi.pig.powershell.png)

    Pig 작업은 다양한 로그 종류의 빈도를 계산합니다.



다음 단계
---------

Pig를 사용하여 데이터를 분석할 수 있지만, HDInsight에 포함된 다른 언어도 유용할 수 있습니다. Hive는 HDInsight에 저장된 데이터를 손쉽게 쿼리할 수 있는 SQL과 유사한 쿼리 언어를 제공하며, Java로 작성된 MapReduce 작업은 복잡한 데이터를 분석할 수 있게 합니다. 자세한 내용은 다음을 참조하십시오.

-   [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [HDInsight에 데이터 업로드](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [HDInsight와 함께 Hive 사용](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)


[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: /en-us/manage/services/hdinsight/howto-blob-store/
[hdinsight-upload-data]: /en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-getting-started]: /en-us/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-admin-powershell]: /en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/

[hdinsight-using-hive]: /en-us/manage/services/hdinsight/using-hive-with-hdinsight/

[hdinsight-provision]: /en-us/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-configure-powershell]: /en-us/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 

[Powershell-install-configure]: /ko-kr/documentation/articles/install-configure-powershell/

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png  
