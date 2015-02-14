<properties 
	pageTitle="HDInsight(Hadoop)의 Apache Storm에 대해 알아보기" 
	description="HDInsight(Hadoop)에서 Apache Storm을 사용하는 방법에 대해 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# HDInsight의 Apache Storm 개요

## Storm 정의

[Apache Storm][apachestorm]은 실시간으로 데이터를 처리할 수 있는 분산형 내결함성 오픈 소스 계산 시스템입니다. 또한 Storm 솔루션은 처음에 정상적으로 처리되지 않은 데이터를 재생하는 기능을 통해 데이터 처리를 보장할 수 있습니다.

## HDInsight의 Storm이란?

Storm은 Azure 환경에 통합된 관리 클러스터로 제공되며 여기서 대규모 Azure 솔루션의 일부분으로 사용될 수 있습니다. 예를 들어 Storm은 ServiceBus 큐 또는 이벤트 허브와 같은 서비스의 데이터를 사용할 수 있으며 웹 사이트 또는 클라우드 서비스를 사용하여 데이터 시각화 기능을 제공할 수 있습니다. Azure 가상 네트워크에서 Storm 클러스터를 구성할 수도 있습니다. 그러면 같은 가상 네트워크의 다른 리소스와 통신하는 대기 시간이 짧아지며, 개인 데이터 센터 내의 리소스와 보안 통신도 수행할 수 있습니다.

Storm 사용을 시작하려면 [HDInsight에서 Storm 시작][gettingstarted]을 참조하세요.

## HDInsight의 Storm에 있는 데이터가 처리되는 방식

HDInsight 또는 Hadoop에서는 MapReduce 작업이 아닌 Storm 클러스터 프로세스 **토폴로지**를 보다 일반적으로 사용할 수 있습니다. Storm 클러스터는 **Nimbus**를 실행하는 헤드 노드와 **감독자**를 실행하는 작업자 노드의 두 가지 노드 유형을 포함합니다.

* **Nimbus** - Hadoop의 JobTracker와 비슷하며, 클러스터 전체에서 코드를 분산하고 컴퓨터에 작업을 할당하고 오류를 모니터링합니다. HDInsight는 두 Nimbus 노드를 제공하므로 Storm 클러스터에 대한 단일 실패 지점이 발생하지 않습니다.

* **감독자** - 각 작업자 노드의 감독자는 노드에서 **작업자 프로세스**를 시작 및 중지합니다.

* **작업자 프로세스** - 작업자 프로세스는 **토폴로지**의 하위 집합을 실행합니다. 실행 중인 토폴로지는 클러스터 전체에서 여러 작업자 프로세스 간에 분산됩니다.

* **토폴로지** - 데이터 **스트림**을 처리하는 계산 그래프를 정의합니다. MapReduce 작업과는 달리 토폴로지는 중지할 때까지 실행됩니다.

* **스트림** - 바인딩되지 않은 **튜플** 컬렉션입니다. 스트림은 **Spout** 및 **Bolt**에 의해 생성되며 **Bolt**에서 사용됩니다.

* **튜플** - 동적으로 형식이 지정되는 값의 명명된 목록입니다.

* **Spout** - 데이터 원본의 데이터를 사용하며 **스트림**을 하나 이상 내보냅니다.

	> [AZURE.NOTE] 대부분의 경우에는 Kafka, Azure ServiceBus 큐 또는 이벤트 허브와 같은 큐에서 데이터를 읽습니다. 이처럼 큐가 사용되므로 가동 중지 시에도 데이터가 영구 보존됩니다.

* **Bolt** - **스트림**을 사용하고 **튜플** 처리를 수행하며 **스트림**을 내보낼 수 있습니다. 또한 Bolt는 큐, HDInsight HBase, Blob 또는 기타 데이터 저장소와 같은 외부 저장소에 데이터를 씁니다.

* **Thrift** - Apache Thrift는 확장 가능한 언어 간 서비스 개발용 소프트웨어 프레임워크입니다. Thrift를 통해 C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk 및 기타 언어 간에 작동하는 서비스를 빌드할 수 있습니다.

	* **Nimbus**는 Thrift 서비스이며 **토폴로지**는 Thrift 정의이므로 다양한 프로그래밍 언어를 사용하여 토폴로지를 개발할 수 있습니다. 

Storm 구성 요소에 대한 자세한 내용은 apache.org의 [Storm 자습서][apachetutorial]를 참조하세요.

## 시나리오: Storm의 사용 사례

아래에는 Apache Storm을 사용할 수 있는 몇 가지 일반적인 시나리오가 나와 있습니다. 실제 시나리오에 대한 자세한 내용은 기[업에서 Storm을 사용하는 방식][poweredby]을 참조하세요.

### 실시간 분석

Storm은 데이터 스트림을 실시간으로 처리하므로 데이터 스트림이 도착할 때 특정 이벤트나 패턴을 확인하고 대응하기 위한 데이터 분석용으로 적합합니다. 예를 들어 Storm 토폴로지는 시스템 상태 확인을 위해 센서 데이터를 모니터링하고 특정 패턴 발생 시 SMS 메시지를 생성하여 경고할 수 있습니다.

### ETL(Extract Transform Load)

ETL은 Storm 처리의 부작용이라고 할 수 있습니다. 예를 들어 실시간 분석을 통해 사기 행위 검색을 수행하는 경우에는 데이터를 이미 수집 및 변환하는 것입니다. 또한 이후 분석에 사용하기 위해 Bolt가 HBase, Hive 또는 기타 데이터 저장소에 데이터를 저장하도록 할 수도 있습니다.

### 분산 RPC

분산 RPC는 Storm을 사용하여 만들 수 있는 패턴입니다. 요청이 Storm으로 전달되어 계산이 여러 노드로 분산된 다음 마지막으로 결과 스트림이 대기 중인 클라이언트로 반환됩니다.

Storm에서 제공되는 분산 RPC 및 DRPCClient에 대한 자세한 내용은 [분산 RPC](https://storm.incubator.apache.org/documentation/Distributed-RPC.html)를 참조하세요.

### 온라인 기계 학습

Mahout 기반 솔루션과 같이 이전에 일괄 처리를 통해 학습된 기계 학습 솔루션과 함께 Storm을 사용할 수 있습니다. 그러나 Storm의 일반적인 분산형 계산 모델은 스트림 기반 기계 학습 솔루션과 함께 사용할 수도 있습니다. 스트림 처리를 사용하며 Storm과 함께 사용 가능한 기계 학습 라이브러리인 [SAMOA(Scalable Advanced Massive Online Analysis) 프로젝트][samoa] 등을 예로 들 수 있습니다.

## 사용 가능한 프로그래밍 언어

HDInsight의 Storm 클러스터는 .NET, Java 및 Python을 기본적으로 지원합니다. Storm은 [다른 언어도 지원](https://storm.incubator.apache.org/about/multi-language.html)하지만 이러한 대부분의 언어에서는 다른 구성을 변경해야 할 뿐 아니라 HDInsight 클러스터에 추가 프로그래밍 언어도 설치해야 합니다. 

### .NET

SCP는 .NET 개발자가 토폴로지(Spout 및 Bolt 포함)를 디자인하고 구현할 수 있도록 하는 프로젝트입니다. Storm 클러스터에서는 SCP가 기본적으로 지원됩니다.

SCP를 사용한 개발에 대한 자세한 내용은 [HDInsight에서 Storm에 대해 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발](/ko-kr/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)을 참조하세요.

### Java

흔히 확인할 수 있는 대부분의 Java 예제는 일반 Java 또는 Trident입니다. Trident는 조인, 집계, 그룹화, 필터링 등의 작업을 보다 쉽게 수행할 수 있도록 하는 고급 추상화입니다. 그러나 Trident는 튜플 배치에 대해 작동하며 이 경우 원시 Java 솔루션은 한 번에 한 튜플씩 스트림을 생성합니다.

Trident에 대한 자세한 내용은 apache.org의 [Trident 자습서](https://storm.incubator.apache.org/documentation/Trident-tutorial.html)를 참조하세요.

원시 Java 및 Trident 토폴로지에의 예제는 Storm 클러스터의 **%storm_home%\contrib\storm-starter** 디렉터리를 참조하세요.

## 일반 개발 패턴

### 메시지 처리 보장

Storm은 다양한 수준에서 메시지 처리를 보장할 수 있습니다. 예를 들어 기본적인 Storm 응용 프로그램은 최소한 한 번 처리를 보장할 수 있고 Trident는 정확히 한 번 처리를 보장할 수 있습니다.

자세한 내용은 apache.org의 [데이터 처리 보장](https://storm.apache.org/about/guarantees-data-processing.html)을 참조하세요.

### BasicBolt

입력 튜플을 읽고 튜플을 내보내지 않거나 하나 이상 내보낸 다음 실행 메서드 끝에서 입력 튜플을 즉시 승인하는 패턴은 매우 흔히 사용되기 때문에 Storm은 이 패턴을 자동화하는 [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) 인터페이스를 제공합니다.

### 조인

두 데이터 스트림을 조인하는 방법은 응용 프로그램별로 다릅니다. 예를 들어 여러 스트림의 각 튜플을 새 스트림 하나에 조인할 수도 있고 특정 창에 대한 튜플 배치만 조인할 수도 있습니다. 어떤 방식에서든 튜플을 Bolt로 라우팅하는 방법을 정의하는 방식인 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)을 사용하면 조인을 수행할 수 있습니다.

다음 Java 예제에서는 fieldsGrouping을 사용하여 구성 요소 "1", "2", "3"에서 생성된 튜플을 **MyJoiner** Bolt로 라우팅합니다.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### 일괄 처리

여러 가지 방법으로 일괄 처리를 수행할 수 있습니다. 기본 Storm Java 토폴로지에서는 간단한 카운터를 사용하여 X개의 튜플을 내보내기 전에 일괄 처리하거나 틱 튜플이라는 내부 타이밍 메커니즘을 사용하여 X초마다 배치를 내보낼 수 있습니다.

틱 튜플 사용 예제는 [Storm 및 HDInsight를 사용하여 센서 데이터 분석](/ko-kr/documentation/articles/hdinsight-storm-sensor-data-analysis.md)을 참조하세요.

Trident를 사용하는 경우에는 튜플 배치 처리를 기준으로 일괄 처리가 수행됩니다.

### 캐싱

메모리 내 캐싱은 자주 사용되는 자산을 메모리에 저장하므로 처리 속도를 높이기 위한 메커니즘으로 사용되는 경우가 많습니다. 토폴로지는 여러 노드 및 각 노드 내의 여러 프로세스로 분산되므로 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)을 사용하여 캐시 조회에 사용되는 필드가 포함된 튜플은 항상 같은 프로세스로 라우팅되도록 해야 합니다. 그러면 프로세스 간 캐시 항목 중복을 방지할 수 있습니다.

### 상위 N 값 스트리밍

토폴로지에서 Twitter의 상위 5개 추세와 같은 'top' N 값을 계산하는 경우에는 상위 N 값을 병렬로 계산한 다음 해당 계산의 출력을 전역 값으로 병합해야 합니다. 이 작업을 수행하려면 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)을 사용하여 값을 필드별로 병렬 Bolt에 라우팅한 다음(데이터가 필드 값별로 분할됨) 마지막으로 상위 N 값을 전역적으로 확인하는 Bolt로 라우팅합니다.

이 작업의 예제가 필요하면 [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) 예제를 참조하세요.

## 다음 단계

* [HDInsight에서 Storm 시작][gettingstarted]

* [Storm 및 HDInsight로 센서 데이터 분석](/ko-kr/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [HDInsight에서 Storm에 대해 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발](/ko-kr/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /ko-kr/documentation/articles/hdinsight-storm-getting-started
<!--HONumber=42-->
