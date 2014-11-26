<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Interactive Console" pageTitle="How to work with the interactive console in HDInsight | Azure" metaKeywords="" description="In this guide, you'll learn how to perform common tasks such as file upload, run jobs, and visualize data using the interactive console for HDInsight Service." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight Interactive JavaScript and Hive Consoles" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

HDInsight 대화형 JavaScript 및 Hive 콘솔
========================================

Microsoft Azure HDInsight Service는 JavaScript 및 Hive용 대화형 콘솔과 함께 제공됩니다. 이러한 콘솔은 간단한 대화형 REPL(Read-Evaluate-Print Loop) 환경을 제공하여 사용자가 식을 입력하고 평가한 후 MapReduce 작업의 결과를 쿼리하여 즉시 표시할 수 있습니다. JavaScript 콘솔은 Pig Latin 문을 실행합니다. Hive 콘솔은 Hive QL(Hive 쿼리 언어) 문을 평가합니다. 두 가지 유형의 문은 MapReduce 프로그램으로 컴파일됩니다. 이러한 콘솔에서 Hadoop 작업을 관리하는 방법이 Hadoop 클러스터의 헤드 노드에 원격으로 연결하여 MapReduce 프로그램으로 직접 작업하는 방법보다 훨씬 간단합니다.

**JavaScript 콘솔**: Hadoop 에코시스템에 대한 흐름 인터페이스를 제공하는 명령 셸입니다. 흐름 인터페이스는 특정 시퀀스의 한 호출 컨텍스트를 해당 시퀀스의 후속 호출로 릴레이하는 명령 연결 방법을 사용합니다. JavaScript 콘솔은 다음을 제공합니다.

-   Hadoop 클러스터, 리소스 및 HDFS(Hadoop Distributed File System) 명령에 대한 액세스
-   Hadoop 클러스터로 들어오거나 나가는 데이터의 관리 및 조작
-   일련의 MapReduce 프로그램을 정의하여 데이터 처리 워크플로를 만드는 Pig Latin 및 JavaScript 문을 평가하는 흐름 인터페이스

**Hive 콘솔**: Hive는 Hadoop 위에 빌드되고 데이터 관리, 쿼리 및 분석을 제공하는 데이터 웨어하우스 프레임워크입니다. 이 프레임워크는 Hadoop 클러스터에 저장된 데이터를 쿼리하는 SQL 언어인 HiveQL을 사용합니다. Hive 콘솔은 다음을 제공합니다.

-   Hadoop 클러스터, 리소스 및 HDFS 명령에 대한 액세스
-   Hadoop 클러스터에서 HiveQL 문을 실행할 수 있는 Hive 프레임워크의 구현
-   분산 파일 시스템에 저장된 데이터를 마치 테이블에 저장된 것처럼 조작할 수 있도록 해 주는 HDFS의 관계형 데이터베이스 모델. JavaScript 콘솔은 데이터 흐름 언어인 Pig Latin을 사용하며, Hive 콘솔은 쿼리 언어인 HiveQL을 사용합니다.

Pig(및 JavaScript 콘솔)는 연결된(또는 흐름) 변환의 시퀀스가 데이터 처리 워크플로를 정의하는 데 사용되는 스크립팅 접근 방식에 더 익숙한 사용자가 선호하는 경향이 있습니다. 또한 Pig는 심각한 비구조적 데이터가 있는 경우에도 적합한 선택입니다.

Hive(및 해당 콘솔)는 SQL 및 관계형 데이터베이스 환경에 더 익숙한 사용자가 선호하는 경향이 있습니다. Hive의 스키마 및 테이블 추상화 계층을 사용할 경우 해당 환경은 RDBMS에서 일반적으로 보게 되는 환경과 매우 비슷합니다.

Pig 및 Hive는 MapReduce 프로그램으로 컴파일되는 고급 언어를 제공합니다. MapReduce 프로그램은 Java로 작성되어 HDFS에서 실행됩니다. 매우 정밀한 제어 기능이나 고성능이 필요한 경우 MapReduce 프로그램을 직접 작성해야 합니다.

자습서 내용
-----------

-   [JavaScript 콘솔을 사용하여 MapReduce 작업 실행](#runjob)
-   [JavaScript 콘솔을 사용하여 그래픽으로 결과 표시](#displayresults)
-   [Hive 콘솔을 사용하여 Hive 테이블로 결과 내보내기](#createhivetable)
-   [Hive 콘솔을 사용하여 Hive 테이블의 데이터 쿼리](#queryhivetable)

JavaScript 콘솔을 사용하여 MapReduce 작업 실행
----------------------------------------------

이 섹션에서는 JavaScript 콘솔을 사용하여 HDInsight Service와 함께 제공된 WordCount 샘플을 실행합니다. 여기에서 실행한 JavaScript 쿼리는 대화형 콘솔에서 제공하는 Pig에서 계층화된 흐름 API를 사용합니다. 여기에서 분석한 텍스트 파일은 *The Notebooks of Leonardo Da Vinci*의 Project Gutenberg 전자책 버전입니다. MapReduce 작업의 결과에 가장 자주 나오는 단어 10개만 포함하도록 필터를 지정했습니다.

1.  [관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2.  **HDINSIGHT**를 클릭합니다. 배포된 Hadoop 클러스터의 목록이 표시됩니다.
3.  연결할 HDInsight 클러스터의 이름을 클릭합니다.
4.  **클러스터 관리**를 클릭합니다.
5.  자격 증명을 입력한 후 **로그온**을 클릭합니다.
6.  HDInsight 포털에서 **샘플**을 클릭합니다.

    ![HDI.타일.샘플](./media/hdinsight-interactive-console/HDI.TileSamples.PNG)

7.  **Hadoop 샘플 갤러리** 페이지에서 **WordCount** 타일을 클릭합니다.
8.  오른쪽 위에서 **WordCount.js**를 클릭하고 파일을 로컬 디렉터리(예: ../downloads 폴더)에 저장합니다.

    ![HDI.JsConsole.WordCountDownloads](./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG)

9.  왼쪽 위 모서리에서 **Azure HDInsight**를 클릭하여 클러스터 대시보드 페이지로 다시 돌아갑니다.
10. **대화형 클러스터**를 클릭하여 JavaScript 콘솔을 표시합니다.

    ![HDI.타일.대화형콘솔](./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG)

11. 오른쪽 위에서 **JavaScript**를 클릭합니다.
12. 다음 명령을 실행합니다.

    fs.put()

13. 다음 매개 변수를 **파일 업로드** 창에 입력합니다.

    -   **원본:** \_..\\downloads\\Wordcount.js 
    -   **대상:** ./WordCount.js/

    ![HDI.JsConsole.UploadJs](./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG)

    WordCount.js 파일의 위치를 찾습니다. 전체 로컬 경로가 필요합니다. HDFS의 상대 주소에서는 대상 경로의 시작 부분에 점 하나가 있어야 합니다.

14. **업로드** 단추를 클릭합니다.

15. 다음 명령을 실행하여 파일을 나열하고 콘텐츠를 표시합니다.

        #ls
        #cat WordCount.js

    ![HDI.JsConsole.JsCode](./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG)

    JavaScript map 함수는 reduce 함수에서 단어가 나타나는 수를 계산하기 전에 "toLowerCase()" 메서드를 사용하여 텍스트에서 대문자를 제거합니다.

16. 다음 명령을 실행하여 WordCount MapReduce 작업에서 처리할 데이터 파일을 나열합니다.

 		#ls /example/data/gutenberg

1.  다음 명령을 실행하여 MapReduce 프로그램을 실행합니다.

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")

    admin을 현재 로그인 사용자 이름으로 바꿉니다.

    명령이 점 연산자를 사용하여 함께 "연결되며", 출력 파일은 *DaVinciTop10Words*라고 합니다. 다음 섹션에서는 출력 파일에 액세스합니다.

    명령이 완료되면 다음이 표시됩니다.

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
        2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! (View Log)

2.  작업 진행 상태를 보려면 오른쪽으로 스크롤한 후 **View Log**를 클릭합니다. 작업이 완료되지 못한 경우 이 로그는 진단도 제공합니다. 작업이 완료되면 로그의 끝 부분에서 다음 메시지를 확인할 수 있습니다.

        [main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.

3.  다음 명령을 실행하여 출력 파일을 나열합니다.

 		#ls 

    DavinciTop10Words 폴더가 만들어집니다.

JavaScript 콘솔을 사용하여 그래픽으로 결과 표시
-----------------------------------------------

마지막 섹션에서는 MapReduce 작업을 실행하여 텍스트 파일에서 상위 10개의 단어를 검색했습니다. 출력 파일은 ./DaVinciTop10Words입니다.

1.  다음 명령을 실행하여 DaVinciTop10Words 디렉터리에 결과를 표시합니다.

         file = fs.read("DaVinciTop10Words")

    결과는 다음과 유사하게 표시됩니다.

         js> file=fs.read("DaVinciTop10Words")
         the    22966
         of 11228
         and    8428
         in 5737
         to 5296
         a  4791
         is 4261
         it 3073
         that   2903
         which  2544

2.  다음 명령을 실행하여 데이터 파일로 파일 내용을 구문 분석합니다.

         data = parse(file.data, "word, count:long")

3.  다음 명령을 실행하여 데이터를 그림으로 나타냅니다.

         graph.bar(data)

    ![HDI.JsConsole.BarGraphTop10Words](./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG)

Hive 콘솔을 사용하여 Hive 테이블로 결과 내보내기
------------------------------------------------

이 섹션에서는 Hive 대화형 콘솔에 대해 소개합니다. MapReduce 작업 출력에서 Hive 테이블을 만듭니다. 다음 섹션에서는 이 테이블에서 데이터를 쿼리하는 방법을 보여 줍니다.

**Hive 테이블 만들기**

1.  오른쪽 위에서 Hive를 클릭하여 Hive 콘솔을 엽니다.

2.  다음 명령을 입력하여 "DaVinciTop10Words" 폴더에 저장된 WordCount 샘플 출력에서 이름이 *DaVinciWordCountTable*이고 열이 두 개인 테이블을 만듭니다.

         CREATE EXTERNAL TABLE DaVinciWordCountTable     
         (word STRING,
         count INT) 
         ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
         STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';   

    admin을 로그인 사용자 이름으로 바꿉니다.

    테이블이 EXTERNAL 테이블로 만들어지므로 대상 폴더가 테이블과 독립적입니다. 또한 파일 이름 그 자체가 아니라 출력 파일이 있는 폴더를 지정하면 됩니다.

3.  왼쪽 아래에서 **평가**를 클릭합니다.

4.  다음 명령을 입력하여 열이 두 개인 테이블이 만들어졌는지 확인합니다.

         SHOW TABLES;
         DESCRIBE DaVinciWordCountTable;

5.  **평가**를 클릭합니다.

    ![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

Hive 콘솔을 사용하여 Hive 테이블의 데이터 쿼리
----------------------------------------------

1.  다음 명령을 실행하여 나타나는 개수가 상위 10개인 단어를 쿼리합니다.

         SELECT word, count
         FROM DaVinciWordCountTable
         ORDER BY count DESC LIMIT 10

    이 쿼리의 결과는 다음과 같습니다.

         SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
         the 22966
         of 11228
         and 8428
         in 5737
         to 5296
         a 4791
         is 4261
         it 3073
         that 2903
         which 2544

다음 단계
---------

대화형 JavaScript 콘솔에서 Hadoop 작업을 실행하는 방법과 이 콘솔을 사용하여 작업에서 결과를 검사하는 방법을 알아보았습니다. 또한 대화형 Hive 콘솔을 사용하여 MapReduce 프로그램의 출력을 포함한 테이블을 만들고 쿼리함으로써 Hadoop 작업의 결과를 검사하고 처리하는 방법도 알아보았습니다. 콘솔에서 사용 중인 Pig Latin 및 Hive QL 문의 예제를 살펴보았습니다. 마지막으로 JavaScript 및 Hive 콘솔의 REPL 대화형 특성으로 Hadoop 클러스터의 사용을 간소화하는 방법도 알아보았습니다. 자세한 내용은 다음 문서를 참조하십시오.

-   [HDInsight와 함께 Pig 사용](/ko-kr/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [HDInsight와 함께 Hive 사용](/ko-kr/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [HDInsight와 함께 MapReduce 사용](/ko-kr/manage/services/hdinsight/using-mapreduce-with-hdinsight/)

[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Hive Table Confirmation")

