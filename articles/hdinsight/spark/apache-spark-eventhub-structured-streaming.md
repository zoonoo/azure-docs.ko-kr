---
title: "Azure HDInsight에서 Event Hubs와 함께 Apache Spark 구조적 스트리밍 사용 | Microsoft Docs"
description: "Azure 이벤트 허브에 데이터 스트림을 보내는 방법에 대한 Apache Spark 스트리밍 샘플을 빌드한 다음 scala 응용 프로그램을 사용하여 HDInsight Spark 클러스터에서 해당 이벤트를 수신합니다."
keywords: "apache spark 스트리밍, spark 스트리밍, spark 샘플, apache spark 스트리밍 예제, 이벤트 허브 azure 샘플, spark 샘플"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: a542295e91a641289fa4261920a08eddbad6a217
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Event Hubs에서 이벤트를 처리하는 HDInsight의 Apache Spark 구조적 스트리밍

이 문서에서는 Spark 구조적 스트리밍을 사용하여 실시간 원격 분석을 처리하는 방법을 알아봅니다. 이렇게 하려면 다음과 같은 개략적인 단계를 수행해야 합니다.

1. 로컬 워크스테이션에서 Event Hubs에 보낼 이벤트를 생성하는 샘플 이벤트 생산자 응용 프로그램을 컴파일하고 실행합니다.
2. [Spark 셸](apache-spark-shell.md)을 사용하여 간단한 Spark 구조적 스트리밍 응용 프로그램을 정의하고 실행합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

* Azure Event Hubs 네임스페이스. 자세한 정보는 [Azure Event Hubs 네임스페이스 만들기](apache-spark-eventhub-streaming.md#create-an-azure-event-hub)를 참조하세요.

* Oracle Java Development 키트. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.

* Apache Maven [여기](https://maven.apache.org/download.cgi)에서 다운로드할 수 있습니다. Maven 설치 지침은 [여기](https://maven.apache.org/install.html)에서 제공됩니다.

## <a name="build-configure-and-run-the-event-producer"></a>이벤트 생산자 빌드, 구성 및 실행
이 작업에서는 임의의 이벤트를 만들고 구성된 Event Hub에 전송하는 응용 프로그램 예제를 복제합니다. 이 응용 프로그램 예제는 [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)의 GitHub에서 사용할 수 있습니다.

1. Git 도구가 설치되어 있는지 확인합니다. [GIT 다운로드](http://www.git-scm.com/downloads)에서 다운로드할 수 있습니다. 
2. 명령 프롬프트 또는 터미널 셸을 열고 프로젝트를 복제할 디렉터리에서 다음 명령을 실행합니다.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. 그러면 eventhubs-client-sample이라는 새 폴더가 만들어집니다. 셸 또는 명령 프롬프트 내에서 이 폴더로 이동합니다.
4. 다음 명령을 사용하여 응용 프로그램을 빌드하기 위해 Maven을 실행합니다.

          mvn package

5. 셸 또는 명령 프롬프트 내에서 ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar`` 파일을 만들고 포함하는 대상 디렉터리로 이동합니다.
6. 다음으로 Event Hub에 대한 이벤트 생산자를 실행하는 명령줄을 빌드해야 합니다. 다음과 같이 명령의 값을 바꿔서 이 작업을 수행합니다.

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. 예를 들어 전체 명령은 다음과 유사합니다.

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. 명령이 시작되고 구성이 올바를 경우 몇 분 안에 다음과 비슷하게 Event Hub에 보내는 이벤트와 관련된 출력이 표시됩니다.

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. 단계를 진행하는 동안 이벤트 생성자가 계속 실행됩니다.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>HDInsight 클러스터에서 Spark 셸 실행
이 작업에서는 HDInsight 클러스터의 헤드 노드로 SSH하고, Spark 셸을 시작하고, Event Hubs에서 이벤트를 검색하고 처리하는 Spark 스트리밍 응용 프로그램을 실행합니다. 

이 지점에서 HDInsight 클러스터를 준비해야 합니다. 그렇지 않으면 프로비전을 마칠 때까지 기다려야 합니다. 준비되면 다음 단계를 진행합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 지침은 [SSH를 사용하여 HDInsight에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

5. 빌드할 응용 프로그램에는 Spark 스트리밍 Event Hubs 패키지가 필요합니다. Spark 셸이 실행되어 [Maven Central](https://search.maven.org)에서 이 종속성을 자동으로 검색하려면 다음과 같이 Maven 좌표를 사용하여 패키지 스위치를 제공해야 합니다.

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.0"

6. Spark 셸 로드가 완료되면 다음이 표시됩니다.

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.0.2.6.0.10-29
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_131)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. 다음 코드 조각을 텍스트 편집기에 복사하고 Event Hub에 적절하게 설정되도록 정책 키 및 네임스페이스를 수정합니다.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )

8. 대기 중인 scala> 프롬프트에 수정된 코드 조각을 붙여넣고 반환 키를 누릅니다. 다음과 유사한 결과가 표시됩니다.

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

9. 원본을 지정하도록 Spark 구조적 스트리밍 쿼리를 작성하기 시작합니다. 다음을 Spark 셸에 붙여넣고 반환 키를 누릅니다.

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

10. 다음과 유사한 결과가 표시됩니다.

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

11. 다음으로 해당 출력을 콘솔에 기록하도록 쿼리를 작성합니다. Spark 셸에 다음을 붙여넣고 반환 키를 눌러 이 작업을 수행합니다.

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

12. 다음과 유사한 출력을 사용하여 몇 가지 일괄 처리가 시작됩니다.

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

13. 이벤트의 각 microbatch를 처리하는 출력 결과가 나옵니다. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

14. 새 이벤트가 이벤트 재생기에서 전송되면 이 구조적 스트리밍 쿼리에 의해 처리됩니다.
15. 이 샘플의 실행을 완료할 때 HDInsight 클러스터를 삭제해야 합니다.



## <a name="see-also"></a>참고 항목

* [Spark 스트리밍 개요](apache-spark-streaming-overview.md)













