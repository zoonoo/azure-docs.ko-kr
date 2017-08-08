---
title: "IntelliJ용 Azure 도구 키트 - SSH를 통해 원격으로 Spark 응용 프로그램 디버그 | Microsoft Docs"
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
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>IntelliJ용 Azure 도구 키트를 사용하여 SSH를 통해 HDInsight 클러스터에서 원격으로 Spark 응용 프로그램 디버그

이 문서에서는 IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight 클러스터에서 응용 프로그램을 원격으로 디버그하는 방법에 대한 단계별 지침을 제공합니다. [비디오](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)를 수행하여 프로젝트를 디버그할 수 있습니다.

**필수 조건:**

* **IntelliJ용 Azure 도구 키트의 HDInsight 도구** IntelliJ용 Azure 도구 키트의 일부입니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트 설치](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)를 참조하세요.
* **IntelliJ용 Azure 도구 키트** 이 기능을 사용하여 HDInsight 클러스터용 Spark 응용 프로그램을 만듭니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램 만들기](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)의 지침을 따르세요.
* **사용자 이름 및 암호 관리를 포함한 HDInsight SSH 서비스** 자세한 내용은 [SSH를 사용하여 HDInsight(Hadoop)에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 및 [SSH 터널링을 사용하여 Ambari 웹 UI, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)를 참조하세요. 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Spark Scala 응용 프로그램을 만들고 원격 디버깅을 위해 구성합니다.
1. IntelliJ IDEA를 시작하고 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다. 이 문서에서는 **HDInsight의 Spark 클러스터 실행 샘플(Scala)**을 예로 듭니다.

     ![디버그 프로젝트 만들기](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - 왼쪽 창에서 **HDInsight**를 선택합니다.
   - 오른쪽 창에서 기본 설정에 따라 Java 또는 Scala 템플릿을 선택합니다. 다음 옵션 중에서 선택합니다. 
      - **HDInsight의 Spark(Scala)**
      - **HDInsight의 Spark(Java)**
      - **HDInsight의 Spark 클러스터 실행 샘플(Scala)** 
   - 빌드 도구: Scala 프로젝트 만들기 마법사는 scala 프로젝트에 대한 종속성 및 빌드를 관리하는 Maven 또는 SBT를 지원합니다. 필요에 따라 하나를 선택합니다.
2. 다음 창에서 프로젝트 세부 정보를 제공합니다.

   ![Spark SDK 선택](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - 프로젝트 이름과 프로젝트 위치를 제공합니다.
   - **프로젝트 SDK**의 경우 **Spark 2.x** 클러스터에 **Java 1.8**을 사용하고 **Spark 1.x** 클러스터에 **Java 1.7**을 사용합니다.
   - **Spark 버전**의 경우 Scala 프로젝트 생성 마법사는 Spark SDK 및 Scala SDK에 대한 올바른 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 **Spark 1.x**을 선택합니다. 그렇지 않은 경우 **Spark 2.x**을 선택합니다. 이 문서에서는 **Spark 2.0.2(Scala 2.11.8)**를 예로 듭니다.
3. **src** > **main** > **scala**를 선택하여 프로젝트에서 코드를 엽니다. 이 문서에서는 **SparkCore_wasbloTest** 스크립트를 예로 듭니다.
4. **구성 편집** 메뉴에 액세스하려면 오른쪽 위 모퉁이에서 아이콘을 선택합니다. 이 메뉴에서 원격 디버깅에 대한 구성을 만들거나 편집할 수 있습니다.

   ![구성 편집](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. **실행/디버그 구성** 창에서 더하기 기호(**+**)를 클릭합니다. 그런 다음 **Spark 작업 제출** 옵션을 선택합니다.

   ![구성 편집](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. **이름**, **Spark 클러스터** 및 **주 클래스 이름**에 정보를 입력합니다. 그런 다음 **고급 구성**을 선택합니다. 

   ![디버그 구성 실행](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. **Spark 제출 고급 구성** 대화 상자에서 **Spark 원격 디버그 설정**을 선택합니다. SSH 사용자 이름 또는 암호를 입력하거나 개인 키 파일을 사용합니다. 이를 저장하려면 **확인**을 선택합니다.

   ![Spark 원격 디버그 설정](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. 이제 사용자가 입력한 이름으로 구성이 저장됩니다. 구성 세부 정보를 보려면 구성 이름을 선택합니다. 변경하려면 **구성 편집**을 선택합니다. 
9. 구성 설정을 완료한 후에 원격 클러스터에 대해 프로젝트를 실행하거나 원격 디버깅을 수행할 수 있습니다.

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>원격 디버깅 및 원격 실행을 수행하는 방법에 대해 알아봅니다.
### <a name="scenario-1-perform-remote-run"></a>시나리오 1: 원격 실행 수행
1. 프로젝트를 원격으로 실행하려면 **실행** 아이콘을 선택합니다.

   ![실행 아이콘을 클릭합니다.](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. **HDInsight Spark 제출** 창은 응용 프로그램 실행 상태를 표시합니다. 여기에서 정보에 따라 Scala 작업의 진행률을 모니터링할 수 있습니다.

   ![실행 아이콘을 클릭합니다.](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>시나리오 2: 원격 디버깅 수행
1. 중단점을 설정한 다음 **디버그** 아이콘을 선택합니다.

    ![디버그 아이콘을 클릭합니다.](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. 프로그램 실행이 중단점에 도달하면 아래 창에 **디버거** 탭이 표시됩니다. **변수** 창에서 보기 매개 변수 및 변수 정보를 확인할 수도 있습니다. **다음 단계** 아이콘을 클릭하여 다음 코드 줄을 진행합니다. 코드를 통해 다음 단계로 진행할 수 있습니다. **프로그램 다시 시작** 아이콘을 선택하여 코드를 계속 실행합니다. **HDInsight Spark 제출** 창에서 실행 상태를 검토할 수 있습니다. 

   ![디버깅 탭](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>시나리오 3: 원격 디버깅 및 버그 수정 수행
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
1. 두 개의 중단점을 설정하고 **디버그** 아이콘을 클릭하여 원격 디버깅 프로세스를 시작합니다.

2. 첫 번째 중단점에서 코드가 중지되고 **변수** 창에 매개 변수 및 변수 정보가 표시됩니다. 

3. **프로그램 다시 시작** 아이콘을 클릭하여 계속합니다. 두 번째 지점에서 코드가 중지됩니다. 예상대로 예외가 발견됩니다.

  ![오류 throw](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. **프로그램 다시 시작** 아이콘을 다시 클릭합니다. **HDInsight Spark 제출** 창은 작업 실행 실패 오류를 표시합니다.

  ![오류 전송](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. IntelliJ 디버깅 기능을 사용하여 변수 값을 동적으로 업데이트하려면 **디버그**를 다시 선택합니다. 변수 창이 다시 나타납니다. 

6. **디버그** 탭에서 대상을 마우스 오른쪽 단추로 클릭한 다음 **값 설정**을 선택합니다. 다음으로 변수에 새 값을 입력합니다. 그런 다음 **Enter** 키를 선택하여 값을 저장합니다. 

  ![값 설정](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. **프로그램 다시 시작** 아이콘을 클릭하여 프로그램을 계속 실행합니다. 이번에는 예외가 발견되지 않았습니다. 예외 없이 프로젝트가 성공적으로 실행되는지 확인할 수 있습니다.

  ![예외 없이 디버그](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>참고 항목
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
* [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 VPN을 통해 HDInsight Spark에서 원격으로 응용 프로그램 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램 만들기](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)
 
