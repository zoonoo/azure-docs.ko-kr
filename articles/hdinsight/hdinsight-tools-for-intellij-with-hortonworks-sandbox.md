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
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d252a4a8b811f966098348866cc498e2be2924f2
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

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
    - 빌드 도구: Scala 프로젝트 만들기 마법사는 scala 프로젝트에 대한 종속성 및 빌드를 관리하는 Maven 또는 SBT를 지원합니다. 필요에 따라 하나를 선택합니다.
4. 다음 정보를 입력합니다.

    ![IntelliJ Scala 프로젝트 속성 만들기](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **프로젝트 이름**: 프로젝트 이름을 제공합니다.
    - **프로젝트 위치**: 프로젝트 위치를 제공합니다.
    - **프로젝트 SDK**: **새로 만들기**를 클릭하고 **JDK**를 클릭한 후 Java JDK 7 이상 버전의 폴더를 지정합니다. Spark 2.x 클러스터에 Java 1.8을 사용하고 Spark 1.x 클러스터에 Java 1.7을 사용합니다. 기본 위치는 C:\Program Files\Java\jdk1.8.x_xxx입니다.
    - **Spark 버전**: Scala 프로젝트 생성 마법사는 Spark SDK 및 Scala SDK에 대한 적절한 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 Spark 1.x을 선택합니다. 그렇지 않으면 Spark 2.x를 선택해야 합니다. 이 예제에서는 Spark 1.6.2(Scala 2.10.5)를 사용합니다. 또한 Scala 2.10.x로 표시된 리포지토리를 사용하고 있는지 확인합니다. Scala 2.11.x로 표시된 리포지토리는 사용하지 않도록 합니다.
5. **Finish**를 클릭합니다.
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

## <a name="next-steps"></a>다음 단계:

- IntelliJ용 HDInsight Tools를 사용하여 HDInsight용 Spark 응용 프로그램을 만드는 방법을 알아보려면 [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark Linux 클러스터용 Spark 응용 프로그램 만들기](hdinsight-apache-spark-intellij-tool-plugin.md)를 참조하세요.
- IntelliJ요 HDInsight Toolos 관련 비디오를 시청하려면 [Spark 개발을 위한 IntelliJ용 HDInsight Tools 소개](https://www.youtube.com/watch?v=YTZzYVgut6c)를 참조하세요.
- SSH를 통해 HDInsight에서 원격으로 이 도구 키트를 사용하여 Spark 응용 프로그램을 디버그하는 방법을 알아보려면 [IntelliJ용 Azure 도구 키트를 사용하여 SSH를 통해 HDInsight 클러스터에서 원격으로 Spark 응용 프로그램 디버그](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)를 참조하세요.
- VPN을 통해 HDInsight에서 원격으로 이 도구 키트를 사용하여 Spark 응용 프로그램을 디버그하는 방법을 알아보려면 [HDInsight Spark Linux 클러스터에서 IntelliJ용 Azure 도구 키트의 HDInsight Tools를 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)를 참조하세요.
- Eclipse용 HDInsight Tools를 사용하여 Spark 응용 프로그램을 만드는 방법을 알아보려면 [Eclipse용 Azure 도구 키트의 HDInsight Tools를 사용하여 Spark 응용 프로그램 만들기](hdinsight-apache-spark-eclipse-tool-plugin.md)를 참조하세요.
- Eclipse용 HDInsight Tools 관련 비디오를 시청하려면 [Eclipse용 HDInsight Tools를 사용하여 Spark 응용 프로그램 만들기](https://mix.office.com/watch/1rau2mopb6fha)를 참조하세요.
