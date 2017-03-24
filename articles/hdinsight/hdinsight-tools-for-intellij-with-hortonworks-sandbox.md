---
title: "Hortonworks 샌드박스에서 IntelliJ용 Azure 도구 키트 사용 | Microsoft Docs"
description: "Hortonworks 샌드박스에서 IntelliJ용 Azure 도구 키트의 HDInsight Tools 사용 방법을 알아봅니다."
keywords: "hadoop 도구,hive 쿼리,intellij,hortonworks 샌드박스,intellij용 azure 도구 키트"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: b52e5bcf443afd05bf8cc37ce436cb80ab41412f
ms.lasthandoff: 01/24/2017


---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용

IntelliJ용 HDInsight Tools를 사용하여 워크스테이션에서 실행되는 [Hortonworks 샌드박스](http://hortonworks.com/products/sandbox/)에서 Apache Scala 응용 프로그램을 개발하고 응용 프로그램을 테스트하는 방법을 알아봅니다. [IntelliJ IDEA](https://www.jetbrains.com/idea/)는 컴퓨터 소프트웨어를 개발하기 위한 Java IDE(통합 개발 환경)입니다. Hortonworks 샌드박스에서 응용 프로그램을 개발하고 테스트한 후에 [Azure HDInsight](hdinsight-hadoop-introduction.md)로 이동할 수 있습니다.

##<a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- 로컬 환경에서 실행되는 Hortonworks 샌드박스의 HDP 2.4. 구성하려면 [가상 컴퓨터에서 Hadoop Sandbox를 사용하여 Hadoop 에코 시스템 시작](hdinsight-hadoop-emulator-get-started.md)을 참조하세요. IntelliJ용 HDInsight Tools는 HDP 2.4에서만 테스트되었습니다. [Hortonworks 샌드박스 다운로드 사이트](http://hortonworks.com/downloads/#sandbox)에서 **Hortonworks 샌드박스 보관**을 확장하여 가져옵니다.
- [JDK(Java Developer Kit) 버전 1.8 이상](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) JDK는 IntelliJ용 Azure 도구 키트에 필요합니다.
- [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download)과 [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) 플러그 인 및 [IntelliJ용 Azure 도구 키트](../azure-toolkit-for-intellij.md) 플러그 인. IntelliJ용 HDInsight Tools는 IntelliJ용 Azure 도구 키트에 포함되어 제공됩니다. 

  **플러그 인을 설치하려면**

  1. IntelliJ IDEA를 엽니다.
  2. 시작 화면에서 **구성**을 클릭한 다음 **플러그 인**을 클릭합니다.
  3. 왼쪽 아래 모서리에서 **JetBrains 플러그 인 설치**를 클릭합니다.
  4. 검색 기능을 사용하여 **Scala**를 검색한 후 **설치**를 클릭합니다.
  5. 설치가 완료되면 **IntelliJ IDEA 다시 시작**을 클릭합니다.
  6. 4~5단계를 반복하여 **IntelliJ용 Azure 도구 키트**를 설치합니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트 설치](../azure-toolkit-for-intellij-installation.md)를 참조하세요.

## <a name="create-a-spark-scala-application"></a>Spark Scala 응용 프로그램 만들기

이 섹션에서는 IntelliJ IDEA를 사용하여 샘플 scala 프로젝트를 만듭니다. 다음 섹션에서는 프로젝트를 제출하기 전에 Hortonworks 샌드박스(에뮬레이터)에 IntelliJ IDEA를 연결합니다.

1. 워크스테이션에서 IntelliJ IDEA를 엽니다.
2. **새 프로젝트 만들기**를 클릭합니다.
3. 왼쪽 창의 **HDInsight**를 클릭하고 오른쪽 창에서 **HDInsight(Scala)의 Spark**를 클릭한 후 **다음**을 클릭합니다.

    ![IntelliJ Scala 프로젝트 만들기](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
4. 다음 정보를 입력합니다.

    ![IntelliJ Scala 프로젝트 속성 만들기](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **프로젝트 이름**: 프로젝트 이름을 제공합니다.
    - **프로젝트 위치**: 프로젝트 위치를 제공합니다.
    - **프로젝트 SDK**: **새로 만들기**를 클릭하고 **JDK**를 클릭한 후 Java JDK 7 이상 버전의 폴더를 지정합니다.  기본 위치는 C:\Program Files\Java\jdk1.8.x_xxx입니다.
    - **Scala SDK**: **선택**을 클릭하고 버전 **2.10.6**을 선택한 후 **확인**을 클릭합니다. 버전이 나열되지 않으면 **다운로드**를 클릭하고 **Scala 버전**을 선택한 후 **확인**을 클릭합니다. 버전 2.11.x는 사용하지 않아야 합니다. 이 문서에서는 버전 2.10.6을 사용합니다.
    - **Spark SDK**: [SDK](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)를 다운로드합니다. 이 메시지를 무시하고 Spark Maven 리포지토리를 대신 사용할 수도 있지만 Spark 응용 프로그램을 개발하려면 올바른 Maven 리포지토리가 설치되어 있어야 합니다. (예를 들어 Spark 스트리밍을 사용하는 경우 Spark 스트리밍 부분이 설치되어 있는지 확인해야 합니다. 또한 Scala 2.10으로 표시된 리포지토리를 사용하고 있는지 확인해야 합니다. Scala 2.11로 표시된 리포지토리는 사용하지 마세요.)
5. **마침**을 클릭합니다.
6. 프로젝트 보기가 아직 열려 있지 않으면 **[ALT]+1**을 눌러 프로젝트 보기를 엽니다.
7. **프로젝트 탐색기**에서 프로젝트를 확장한 후 **src**를 클릭합니다.
8. **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **Scala 클래스**를 클릭합니다.
9. 이름을 입력하고 **종류**에서 **개체**를 클릭한 후 **확인**을 클릭합니다.

    ![새 IntelliJ Scala 클래스 만들기](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. .scala 파일에서 다음 코드를 붙여 넣습니다.

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

11. **빌드** 메뉴에서 **솔루션 빌드**를 클릭합니다. 컴파일이 성공적으로 완료되었는지 확인합니다.


## <a name="link-to-the-hortonworks-sandbox"></a>HortonWorks 샌드박스에 연결

Hortonworks 샌드박스(에뮬레이터)에 연결하려면 먼저 기존 IntelliJ 응용 프로그램이 있어야 합니다.

**에뮬레이터에 연결하려면**

1. 프로젝트가 아직 열려 있지 않으면 IntelliJ에서 프로젝트를 엽니다.
2. **보기** 메뉴에서 **도구 창**, **Azure 탐색기**를 차례로 클릭합니다.
3. **Azure**를 확장하고 **HDInsight**를 마우스 오른쪽 단추로 클릭한 다음 **에뮬레이터에 연결**을 클릭합니다.
4. Hortonworks 샌드박스의 루트 계정에 대해 구성한 암호와 다음 스크린샷에 표시된 값과 비슷한 나머지 값을 입력한 후 **확인**을 클릭합니다. 

  ![IntelliJ 에뮬레이터 연결](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. **예**를 클릭하여 에뮬레이터를 구성합니다.

  성공적으로 연결되면 HDInsight 노드 아래에 에뮬레이터(Hortonworks 샌드박스)가 표시되는 것을 볼 수 있습니다.

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>샌드박스에 Spark Scala 응용 프로그램 제출

IntelliJ IDEA를 에뮬레이터에 연결한 후에는 프로젝트를 제출할 수 있습니다.

**에뮬레이터에 프로젝트를 제출하려면**

1. **프로젝트 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **HDInsight에 Spark 응용 프로그램 제출**을 클릭합니다.
2. 다음 필드를 지정합니다.

    - **Spark 클러스터(Linux만 해당)**: 로컬 Hortonworks 샌드박스를 선택합니다.
    - **기본 클래스 이름**: 기본 클래스 이름을 선택하거나 입력합니다.  이 자습서에서는 **GroupByTest**입니다.
3. **Submit**를 클릭합니다. 작업 제출 로그가 Spark 제출 도구 창에 표시됩니다.

##<a name="next-steps"></a>다음 단계:

- IntelliJ용 HDInsight Tools를 사용하여 HDInsight용 Spark 응용 프로그램을 만드는 방법을 알아보려면 [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark Linux 클러스터용 Spark 응용 프로그램 만들기](hdinsight-apache-spark-intellij-tool-plugin.md)를 참조하세요.
- IntelliJ요 HDInsight Toolos 관련 비디오를 시청하려면 [Spark 개발을 위한 IntelliJ용 HDInsight Tools 소개](https://mix.office.com/watch/1nqkqjt5xonza)를 참조하세요.
- HDInsight에서 원격으로 이 도구 키트를 사용하여 Spark 응용 프로그램을 디버그하는 방법을 알아보려면 [HDInsight Spark Linux 클러스터에서 IntelliJ용 Azure 도구 키트의 HDInsight Tools를 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)를 참조하세요.
- Eclipse용 HDInsight Tools를 사용하여 Spark 응용 프로그램을 만드는 방법을 알아보려면 [Eclipse용 Azure 도구 키트의 HDInsight Tools를 사용하여 Spark 응용 프로그램 만들기](hdinsight-apache-spark-eclipse-tool-plugin.md)를 참조하세요.
- Eclipse용 HDInsight Tools 관련 비디오를 시청하려면 [Eclipse용 HDInsight Tools를 사용하여 Spark 응용 프로그램 만들기](https://mix.office.com/watch/1rau2mopb6fha)를 참조하세요.
