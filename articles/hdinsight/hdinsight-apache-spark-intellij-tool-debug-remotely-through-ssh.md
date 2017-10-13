---
title: "IntelliJ용 Azure 도구 키트: SSH를 통해 원격으로 Spark 응용 프로그램 디버그 | Microsoft Docs"
description: "IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 SSH를 통해 HDInsight 클러스터에서 응용 프로그램을 원격으로 디버그하는 방법에 대한 단계별 지침"
keywords: "IntelliJ 원격으로 디버그, IntelliJ 원격 디버깅, SSH, IntelliJ, HDInsight, IntelliJ 디버그, 디버깅"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: Jenny Jiang
ms.openlocfilehash: 19053e31d6eb097bc91a04ef9c6af5772aaa16da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>IntelliJ용 Azure 도구 키트를 사용하여 SSH를 통해 HDInsight 클러스터에서 Spark 응용 프로그램 디버그

이 문서에서는 IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight 클러스터에서 응용 프로그램을 원격으로 디버그하는 방법에 대한 단계별 지침을 제공합니다. 프로젝트를 디버그하려는 경우 [IntelliJ용 Azure 도구 키트로 HDInsight Spark 응용 프로그램 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) 비디오를 시청할 수도 있습니다.

**필수 구성 요소**

* **IntelliJ용 Azure 도구 키트의 HDInsight 도구** 이 도구는 IntelliJ용 Azure 도구 키트의 일부입니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트 설치](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)를 참조하세요.
* **IntelliJ용 Azure 도구 키트** 이 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램을 만듭니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램 만들기](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)의 지침을 따르세요.
* **사용자 이름 및 암호 관리를 포함한 HDInsight SSH 서비스**. 자세한 내용은 [SSH를 사용하여 HDInsight(Hadoop)에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 및 [SSH 터널링을 사용하여 Ambari 웹 UI, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)를 참조하세요. 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Spark Scala 응용 프로그램을 만들고 원격 디버깅을 위해 구성합니다.

1. IntelliJ IDEA를 시작하고 프로젝트를 만듭니다. **새 프로젝트** 대화 상자에서 다음을 수행합니다.

   a. **HDInsight**를 선택합니다. 

   b. 기본 설정에 따라 Java 또는 Scala 템플릿을 선택합니다. 다음 옵션 중에서 선택합니다.

      - **HDInsight의 Spark(Scala)**

      - **HDInsight의 Spark(Java)**

      - **HDInsight의 Spark 클러스터 실행 샘플(Scala)** 

      이 예제에서는 **HDInsight의 Spark 클러스터 실행 샘플(Scala)** 템플릿을 사용합니다.

   c. **빌드 도구** 목록에서 요구 사항에 따라 다음 중 하나를 선택합니다.

      - Scala 프로젝트 만들기 마법사 지원에 대해 **Maven**

      -  종속성 관리 및 Scala 프로젝트에 대한 빌드에 대해 **SBT** 

      ![디버그 프로젝트 만들기](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)

   d. **다음**을 선택합니다.     
 
3. 다음 **새 프로젝트** 창에서 다음을 수행합니다.

   ![Spark SDK 선택](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)

   a. 프로젝트 이름과 프로젝트 위치를 입력합니다.

   b. **프로젝트 SDK** 드롭다운 목록에서 **Spark 2.x** 클러스터에 대해 **Java 1.8**을 선택하거나 **Spark 1.x** 클러스터에 대해 **Java 1.7**을 선택합니다.

   c. **Spark 버전** 드롭다운 목록에서 Scala 프로젝트 생성 마법사는 Spark SDK 및 Scala SDK에 대한 올바른 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 **Spark 1.x**를 선택합니다. 그렇지 않은 경우 **Spark 2.x**을 선택합니다. 이 예제에서는 **Spark 2.0.2(Scala 2.11.8)**를 사용합니다.

   d. **마침**을 선택합니다.

4. **src** > **main** > **scala**를 선택하여 프로젝트에서 코드를 엽니다. 이 예제에서는 **SparkCore_wasbloTest** 스크립트를 사용합니다.

5. **구성 편집** 메뉴에 액세스하려면 오른쪽 위 구석에 있는 아이콘을 선택합니다. 이 메뉴에서 원격 디버깅에 대한 구성을 만들거나 편집할 수 있습니다.

   ![구성 편집](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 

6. **실행/디버깅 구성** 대화 상자에서 더하기 기호(**+**)를 선택합니다. 그런 다음 **Spark 작업 제출** 옵션을 선택합니다.

   ![새 구성 추가](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
7. **이름**, **Spark 클러스터** 및 **주 클래스 이름**에 정보를 입력합니다. 그런 다음 **고급 구성**을 선택합니다. 

   ![디버그 구성 실행](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)

8. **Spark 제출 고급 구성** 대화 상자에서 **Spark 원격 디버그 설정**을 선택합니다. SSH 사용자 이름을 입력하고 암호를 입력하거나 개인 키 파일을 사용합니다. 구성을 저장하려면 **확인**을 선택합니다.

   ![Spark 원격 디버그 설정](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)

9. 이제 사용자가 입력한 이름으로 구성이 저장됩니다. 구성 세부 정보를 보려면 구성 이름을 선택합니다. 변경하려면 **구성 편집**을 선택합니다. 

10. 구성 설정을 완료한 후에 원격 클러스터에 대해 프로젝트를 실행하거나 원격 디버깅을 수행할 수 있습니다.

## <a name="learn-how-to-perform-remote-debugging"></a>원격 디버깅을 수행하는 방법 알아보기
### <a name="scenario-1-perform-remote-run"></a>시나리오 1: 원격 실행 수행

이 섹션에서는 드라이버 및 실행기를 디버그하는 방법을 보여 줍니다.

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. 중단점을 설정한 다음 **디버그** 아이콘을 선택합니다.

   ![디버그 아이콘 선택](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)

2. 프로그램 실행이 중단점에 도달하면 **디버거** 창에 1개의 **드라이버** 탭과 2개의 **실행기** 탭이 표시됩니다. **프로그램 다시 시작** 아이콘을 선택하여 코드를 계속 실행합니다. 그런 다음 중단점에 도달하면 해당 **실행기** 탭에 초점이 맞춰집니다. 해당 **콘솔** 탭에서 실행 로그를 검토할 수 있습니다.

   ![디버깅 탭](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>시나리오 2: 원격 디버깅 및 버그 수정 수행
이 섹션에서는 간단한 수정을 위해 IntelliJ 디버깅 기능을 사용하여 변수 값을 동적으로 업데이트하는 방법을 보여줍니다. 다음 코드 예제에서는 대상 파일이 이미 있기 때문에 예외가 throw됩니다.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>원격 디버깅 및 버그 수정을 수행하려면
1. 두 개의 중단점을 설정하고 **디버그** 아이콘을 선택하여 원격 디버깅 프로세스를 시작합니다.

2. 첫 번째 중단점에서 코드가 중지되고 **변수** 창에 매개 변수 및 변수 정보가 표시됩니다. 

3. **프로그램 다시 시작** 아이콘을 선택하여 계속합니다. 두 번째 지점에서 코드가 중지됩니다. 예상대로 예외가 catch됩니다.

  ![오류 throw](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. **프로그램 다시 시작** 아이콘을 다시 선택합니다. **HDInsight Spark 제출** 창에 “작업 실행 실패” 오류가 표시됩니다.

  ![오류 전송](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. IntelliJ 디버깅 기능을 사용하여 변수 값을 동적으로 업데이트하려면 **디버그**를 다시 선택합니다. **변수** 창이 다시 나타납니다. 

6. **디버그** 탭에서 대상을 마우스 오른쪽 단추로 클릭한 다음 **값 설정**을 선택합니다. 다음으로 변수의 새 값을 입력합니다. 그런 다음 **Enter** 키를 선택하여 값을 저장합니다. 

  ![값 설정](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. **프로그램 다시 시작** 아이콘을 선택하여 프로그램을 계속 실행합니다. 이번에는 예외가 catch되지 않습니다. 예외 없이 프로젝트가 성공적으로 실행되는지 확인할 수 있습니다.

  ![예외 없이 디버그](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>다음 단계
* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>데모
* Scala 프로젝트 만들기(비디오): [Spark Scala 응용 프로그램 만들기](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* 원격 디버그(비디오): [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터에서 원격으로 Spark 응용 프로그램 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램 만들기](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 VPN을 통해 원격으로 Spark 응용 프로그램 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램 만들기](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)
