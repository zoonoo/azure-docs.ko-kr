---
title: "HDInsight의 Apache Storm 소개 | Microsoft Docs"
description: "HDInsight의 Apache Storm 소개 보기 | Microsoft Docs"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>HDInsight의 Apache Storm 소개: Hadoop에 대한 실시간 분석

HDInsight의 Apache Storm을 사용하면 Azure에서 분산형 실시간 분석 솔루션을 만들 수 있습니다.

Apache Storm은 Hadoop을 통해 실시간으로 데이터를 처리할 수 있는 분산형 내결함성 오픈 소스 계산 시스템입니다. 또한 Storm 솔루션은 처음에 정상적으로 처리되지 않은 데이터를 재생하는 기능을 통해 데이터 처리를 보장할 수 있습니다.

## <a name="how-does-storm-work"></a>Storm 작동 방법

Apache Storm에서는 HDInsight 또는 Hadoop에서 친숙한 MapReduce 작업 대신 **토폴로지** 를 실행합니다. 토폴로지는 DAG(방향성 비순환 그래프)에서 정렬된 여러 구성 요소로 구성됩니다. 다음 다이어그램은 기본 단어 개수 토폴로지에 있는 구성 요소 간에 데이터가 흐르는 방식을 보여 줍니다.

![Storm 토폴로지에서 구성 요소 정렬 방법의 예제](./media/hdinsight-storm-overview/wordcount-topology.png)

* __Spout__ 구성 요소는 데이터를 토폴로지로 가져옵니다. 하나 이상의 스트림을 토폴로지에 내보냅니다.

* __Bolt__ 구성 요소는 spout 또는 다른 bolt에서 내보낸 스트림을 사용합니다. 필요에 따라 bolt는 새 스트림을 토폴로지에 내보낼 수 있습니다. 또한 bolt는 HDFS 또는 HBase와 같은 영구 저장소에 데이터를 쓰는 작업을 담당합니다.

## <a name="why-use-storm-on-hdinsight"></a>HDInsight의 Storm을 사용하는 이유

HDInsight의 Apache Storm는 다음과 같은 주요 이점을 제공합니다.

* 가동 시간 99.9%의 SLA를 사용하여 관리되는 서비스로 수행됩니다.

* 생성 도중이나 이후에 클러스터에 대해 스크립트를 실행하여 간편하게 사용자 지정합니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

* 사용자가 선택한 언어 사용: **Java**, **C#** 및 **Python** 등 다양한 언어로 Storm 구성 요소를 작성할 수 있습니다.

    * C# 토폴로지의 개발, 관리 및 모니터링을 위한 HDInsight와 Visual Studio를 통합합니다. 자세한 내용은 [Visual Studio용 HDInsight를 사용하여 C# Storm 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

    * **Trident** Java 인터페이스를 지원합니다. 이 인터페이스를 사용하면 정확히 한 번의 메시지 처리, 트랜잭션 데이터 저장소 지속성 및 일반 Stream Analytics 작업 집합을 지원하는 Storm 토폴로지를 만들 수 있습니다.

* 클러스터의 확장 및 축소 용이: 실행 중인 Storm 토폴로지에 영향을 주지 않고 작업자 노드를 추가하거나 제거합니다.

* 다음 Azure 서비스와의 통합:

    * Event Hubs

    * Virtual Network

    * SQL 데이터베이스

    * Azure 저장소

    * 저장됩니다.

    * Azure Virtual Network를 사용하여 안전하게 여러 HDInsight 클러스터의 기능 결합: HDInsight, HBase 또는 Hadoop 클러스터를 사용하는 분석 파이프라인을 만듭니다.

실시간 분석 솔루션에 Apache Storm을 사용하는 회사 목록은 [Apache Storm을 사용하는 회사](https://storm.apache.org/documentation/Powered-By.html)를 참조하세요.

Storm 사용을 시작하려면 [HDInsight에서 Storm 시작][gettingstarted]을 참조하세요.

## <a name="ease-of-creation"></a>만들기 편의성

새 HDInsight Storm 클러스터를 몇 분 내에 프로비전할 수 있습니다. Storm 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Storm 시작](hdinsight-apache-storm-tutorial-get-started-linux.md)을 참조하세요.

## <a name="ease-of-use"></a>사용 편의성

* __보안 셸 연결__: SSH를 사용하여 인터넷을 통해 HDInsight 클러스터의 헤드 노드에 액세스할 수 있습니다. SSH를 사용하면 클러스터에서 직접 명령을 실행할 수 있습니다.

  자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* __웹 연결__: HDInsight 클러스터는 Ambari 웹 UI를 제공합니다. 그러면 Ambari 웹 UI를 통해 클러스터에서 서비스를 쉽게 모니터링, 구성 및 관리할 수 있습니다. 또한 HDInsight의 Storm은 브라우저에서 실행 중인 Storm 토폴로지를 모니터링하고 관리할 수 있는 Storm UI를 제공합니다.

  자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md) 및 [Storm UI를 사용하여 모니터링 및 관리](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui)를 참조하세요.

* __Azure PowerShell 및 CLI__: Azure PowerShell 및 Azure CLI는 HDInsight 및 다른 Azure 서비스를 사용하는 클라이언트 시스템에서 사용할 수 있는 명령줄 유틸리티를 제공합니다.

* __Visual Studio 통합__: Data Lake Tools for Visual Studio에는 SCP.Net Framework를 사용하여 C# Storm 토폴로지를 만드는 프로젝트 템플릿이 포함되어 있습니다. 또한 Data Lake 도구는 HDInsight의 Storm을 사용하여 솔루션을 배포, 모니터링 및 관리하는 도구를 제공합니다.

  자세한 내용은 [Visual Studio용 HDInsight를 사용하여 C# Storm 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

## <a name="integration-with-other-azure-services"></a>다른 Azure 서비스와 통합

* __Azure Data Lake Store__: Storm에서 Data Lake Store를 사용하는 예제는 [HDInsight의 Apache Storm에서 Azure Data Lake Store 사용](hdinsight-storm-write-data-lake-store.md)을 참조하세요.

* __Azure Event Hubs__: Storm에서 Azure Event Hubs를 사용하는 예제는 다음 문서를 참조하세요.

    * [HDInsight의 Storm에 대한 Java 기반 토폴로지 개발](hdinsight-storm-develop-java-topology.md)

    * [HDInsight의 Storm(C#)에서 Azure Event Hubs의 이벤트 처리](hdinsight-storm-develop-csharp-event-hub-topology.md)

* __SQL DB__, __DocumentDB__, __EventHub__ 및 __HBase__를 사용하는 예제는 Visual Studio용 Azure Data Lake Tools에서 템플릿으로 포함되어 있습니다. 자세한 내용은 [HDInsight의 Storm에 대한 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

## <a name="reliability"></a>안정성

Apache Storm은 데이터 분석이 수백 개의 노드에 걸쳐 있는 경우에도 항상 들어오는 각 메시지의 완전한 처리를 보장합니다.

**Nimbus 노드**는 Hadoop JobTracker와 유사한 기능을 제공하며 **Zookeeper**를 통해 클러스터의 다른 노드에 태스크를 할당합니다. Zookeeper 노드는 클러스터에 대한 조정을 제공하며, Nimbus와 작업자 노드의 **감독자** 프로세스 간의 통신을 용이하게 합니다. 하나의 처리 노드가 작동이 중지되면 Nimbus 노드에 알림이 제공되고 이 노드에서 작업 및 관련 데이터를 다른 노드에 할당합니다.

Apache Storm의 기본 구성에는 하나의 Nimbus 노드만 있습니다. HDInsight의 Storm은 두 개의 Nimbus 노드를 실행합니다. 주 노드가 실패한 경우 HDInsight 클러스터는 주 노드를 복구하는 동안 보조 노드로 전환됩니다.

![nimbus, zookeeper 및 감독자 다이어그램](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>확장

만드는 동안 클러스터의 노드 수를 지정할 수 있지만 작업과 일치하도록 클러스터를 확장하거나 축소할 수도 있습니다. 모든 HDInsight 클러스터에서는 데이터를 처리하는 동안 클러스터의 노드 수를 변경할 수 있습니다.

> [!NOTE]
> 확장을 통해 추가된 새 노드를 이용하려면 클러스터 크기를 증가시키기 전에 시작된 토폴로지의 균형을 다시 조정해야 합니다.

## <a name="support"></a>지원

HDInsight의 Storm에는 완전한 엔터프라이즈 수준의 24/7 지원이 제공됩니다. HDInsight의 Storm에는 99.9%의 SLA도 있습니다. 따라서 클러스터가 최소 99.9%의 시간 동안 외부 연결을 사용할 수 있도록 보장됩니다.

자세한 내용은 [Azure 지원](https://azure.microsoft.com/support/options/)을 참조하세요.

## <a name="common-use-cases"></a>일반 사용 예

아래에는 HDInsight의 Apache Storm을 사용할 수 있는 몇 가지 일반적인 시나리오가 나와 있습니다. 

* IoT(사물 인터넷)
* 부정 행위 감지
* 소셜 분석
* ETL(추출, 변환, 로드)
* 네트워크 모니터링
* 검색
* 모바일 고객 관리

실제 시나리오에 대한 자세한 내용은 [기업에서 Storm을 사용하는 방식](https://storm.apache.org/documentation/Powered-By.html) 문서를 참조하세요.

## <a name="development"></a>개발

__Data Lake Tools for Visual Studio__를 사용하면 .NET 개발자는 __C#__으로 토폴로지를 디자인하고 구현할 수 있습니다. 또한 Java 및 C# 구성 요소를 사용하는 하이브리드 토폴로지를 만들 수 있습니다.

자세한 내용은 [Visual Studio를 사용하여 HDInsight에서 Storm에 대한 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

선택한 IDE를 사용하여 __Java__ 솔루션을 개발할 수도 있습니다. 자세한 내용은 [HDInsight의 Storm에 대한 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)을 참조하세요.

Python은 Storm 구성 요소를 개발하는 데에도 사용합니다. 자세한 내용은 [HDInsight에서 Python을 사용하여 Storm 토폴로지 개발](hdinsight-storm-develop-python-topology.md)을 참조하세요.

## <a name="common-development-patterns"></a>일반적인 개발 패턴

### <a name="guaranteed-message-processing"></a>메시지 처리 보장

Storm은 다양한 수준에서 메시지 처리를 보장할 수 있습니다. 예를 들어 기본적인 Storm 응용 프로그램은 최소한 한 번 처리를 보장할 수 있고 Trident는 정확히 한 번 처리를 보장할 수 있습니다.

자세한 내용은 apache.org에서 [데이터 처리 보장](https://storm.apache.org/about/guarantees-data-processing.html) 을 참조하세요.

### <a name="ibasicbolt"></a>IBasicBolt

입력 튜플을 읽고 튜플을 내보내지 않거나 하나 이상 내보낸 다음 실행 메서드 끝에서 입력 튜플을 즉시 승인하는 패턴은 매우 흔히 사용됩니다. Storm은 이 패턴을 자동화하는 [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) 인터페이스를 제공합니다.

### <a name="joins"></a>조인

데이터 스트림이 조인되는 방식은 응용 프로그램마다 다릅니다. 예를 들어 여러 스트림의 각 튜플을 새 스트림 하나에 조인할 수도 있고 특정 창에 대한 튜플 배치만 조인할 수도 있습니다. 어떤 방식에서든 튜플을 Bolt로 라우팅하는 방법을 정의하는 방식인 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)을 사용하면 조인을 수행할 수 있습니다.

다음 Java 예제에서는 fieldsGrouping을 사용하여 구성 요소 "1", "2", "3"에서 생성된 튜플을 **MyJoiner** Bolt로 라우팅합니다.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>일괄 처리

Storm은 X초마다 Batch를 내보내는 데 사용할 수 있는 "틱 튜플"이라는 내부 타이밍 메커니즘을 제공합니다.

C# 구성 요소에서 틱 튜플을 사용하는 예제는 [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java)를 참조하세요.

Trident를 사용하는 경우에는 튜플 배치 처리를 기준으로 일괄 처리가 수행됩니다.

### <a name="caching"></a>구성

메모리 내 캐싱은 자주 사용되는 자산을 메모리에 저장하므로 처리 속도를 높이기 위한 메커니즘으로 사용되는 경우가 많습니다. 토폴로지는 여러 노드 및 각 노드 내의 여러 프로세스로 분산되므로 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)을 사용하는 것이 좋습니다. `fieldsGrouping`을 통해 캐시 조회에 사용되는 필드가 포함된 튜플은 항상 같은 프로세스로 라우팅될 수 있습니다. 이 그룹화 기능으로 프로세스 간 캐시 항목 중복을 방지할 수 있습니다.

### <a name="streaming-top-n"></a>상위 N 값 스트리밍

토폴로지가 "상위 N" 값 계산에 따라 다른 경우 상위 N 값을 병렬로 계산해야 합니다. 그런 다음 해당 계산의 출력을 전역 값으로 병합합니다. 이 작업을 수행하려면 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)을 사용하여 병렬 처리를 위해 값을 필드별로 라우팅한 다음 상위 N 값을 전역적으로 확인하는 Bolt로 라우팅합니다.

"상위 N" 값을 계산하는 예제는 [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) 예제를 참조하세요.

## <a name="logging"></a>로깅

Storm은 Apache Log4j를 사용하여 정보를 기록합니다. 기본적으로 많은 양의 데이터를 기록하면 정보를 정렬하기가 어려울 수 있습니다. Storm 토폴로지의 일부로 로깅 구성 파일을 포함하여 로깅 동작을 제어할 수 있습니다.

로깅을 구성하는 방법을 보여 주는 예제 토폴로지는 HDInsight에서 Storm에 대한 [Java 기반 WordCount](hdinsight-storm-develop-java-topology.md) 예제를 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight의 Apache Storm을 사용한 실시간 분석 솔루션에 대해 자세히 알아봅니다.

* [HDInsight에서 Storm 시작][gettingstarted]
* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

