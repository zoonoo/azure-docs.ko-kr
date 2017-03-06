---
title: "HDInsight의 Storm으로 Event Hub에서 이벤트 처리 | Microsoft Docs"
description: "Visual Studio용 HDInsight 도구를 사용하여 Visual Studio에서 만든 C# Storm 토폴로지로 이벤트 허브 데이터를 처리하는 방법에 대해 알아봅니다."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: c9a5091973395dd888939432292fbd06dcbf0680
ms.openlocfilehash: c0349b5890a75c6ffaa6b7eca93baa3101912cf6
ms.lasthandoff: 01/18/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>HDInsight의 Storm(C#)으로 Azure 이벤트 허브에서 이벤트 처리

Azure 이벤트 허브를 사용하면 웹 사이트, 앱 및 장치에서 대량의 데이터를 처리할 수 있습니다. 이벤트 허브 Spout를 사용하면 HDInsight에서 Apache Storm을 사용하여 이 데이터를 실시간으로 분석하기가 쉽습니다. 또한 이벤트 허브 Bolt를 사용하여 Storm에서 이벤트 허브에 데이터를 기록할 수도 있습니다.

이 자습서에서는 Visual Studio용 HDInsight 도구와 함께 설치된 Visual Studio 템플릿을 사용하여 Azure 이벤트 허브에서 작동하는 두 토폴로지를 만드는 방법을 알아봅니다.

* **EventHubWriter**: 무작위로 데이터를 생성하여 이벤트 허브에 기록합니다.
* **EventHubReader**: Event Hubs에서 데이터를 읽어 Storm 로그에 데이터를 로깅합니다.

> [!NOTE] 
> 이 프로젝트의 Java 버전은 [HDInsight의 Storm(Java)으로 Azure Event Hubs에서 이벤트 처리](hdinsight-storm-develop-java-event-hub-topology.md)를 참조하세요.

## <a name="scpnet"></a>SCP.NET

이러한 프로젝트는 NuGet 패키지인 SCP.NET을 사용하여 HDInsight에서 Storm과 함께 사용할 C# 토폴로지 및 구성 요소를 쉽게 만들 수 있습니다.

> [!IMPORTANT]
> 이 문서의 단계에서는 Visual Studio를 사용하는 Windows 개발 환경을 사용하지만 컴파일된 프로젝트는 Linux를 사용하는 HDInsight 클러스터의 Storm에 제출할 수 있습니다. __2016년 10월 28일 이후에 만든 Linux 기반 클러스터만 SCP.NET 토폴로지를 지원합니다.__

### <a name="cluster-versioning"></a>클러스터 버전 관리

프로젝트에서 사용하는 Microsoft.SCP.Net.SDK NuGet 패키지는 HDInsight에 설치된 Storm의 주요 버전과 일치해야 합니다. HDInsight 버전 3.3 및 3.4의 Storm은 Storm 버전 0.10.x를 사용하므로 SCP.NET 버전 0.10.x.x를 사용해야 합니다. HDInsight 3.5는 Storm 1.0.x를 사용하므로 이 클러스터 버전에서 SCP.NET 버전 1.0.x.x를 사용해야 합니다.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

HDInsight 3.4 이상은 Mono를 사용하여 C# 토폴로지를 실행합니다. 대부분의 작업은 Mono와 작동하지만 [Mono 호환성](http://www.mono-project.com/docs/about-mono/compatibility/) 문서에서 잠재적인 비호환성을 확인해야 합니다.

C# 토폴로지도 .NET 4.5를 대상으로 해야 합니다.

## <a name="how-to-work-with-event-hubs"></a>Event Hubs 작동 방법

Microsoft는 Storm 토폴로지의 Azure Event Hubs와 통신하는 데 사용할 수 있는 일단의 Java 구성 요소를 제공합니다. [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/)에서 이러한 구성 요소의 최신 버전이 들어있는 jar 파일을 찾을 수 있습니다.

> [!IMPORTANT]
> 구성 요소는 Java로 작성되지만 C# 토폴로지에서 쉽게 사용할 수 있습니다.

기본적으로 사용할 구성 요소는 다음과 같습니다.

* __EventHubSpout__: Event Hubs에서 데이터를 읽습니다.
* __EventHubBolt__: Event Hubs에 데이터를 씁니다.
* __EventHubSpoutConfig__: EventHubSpout를 구성하는 데 사용됩니다.
* __EventHubBoltConfig__: EventHubBolt를 구성하는 데 사용됩니다.
* __UnicodeEventDataScheme__: Event Hubs에서 읽을 때 UTF-8 인코딩을 사용하도록 Spout를 구성하는 데 사용됩니다. 이 구성 요소를 사용하지 않으면 Spout 기본값은 문자열 인코딩입니다.

### <a name="example-spout-usage"></a>예제 Spout 사용

SCP.NET은 토폴로지에 EventHubSpout를 추가하기 위한 메서드를 구체적으로 제공합니다. 이러한 메서드를 사용하면 Java 구성 요소를 추가하기 위한 제네릭 메서드를 사용하는 것보다 더 쉽게 Spout를 추가할 수 있습니다. 다음 예제에서는 SCP.NET에서 제공하는 __SetEventHubSpout__ 및 EventHubSpoutConfig 메서드를 사용하여 새 Spout를 만드는 방법을 보여 줍니다.

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

앞의 예제에서는 __EventHubSpout__라는 새 Spout 구성 요소를 만들고 Event Hub와 통신하도록 구성합니다. 구성 요소에 대한 병렬 처리 힌트는 Event Hub의 파티션 수로 설정됩니다. 이를 통해 Storm은 각 파티션에 대한 구성 요소의 인스턴스를 만들 수 있습니다.

> [!WARNING]
> 2017년 1월 1일부터 SetEventHubSpout 및 EventHubSpoutConfig 메서드를 사용하면 Event Hubs에서 데이터를 읽을 때 문자열 인코딩을 사용하는 Spout를 만들 수 있습니다. UTF-8 인코딩을 사용해야 하는 경우 다음 예제를 참조하세요.

또한 Spout를 만들 때 JavaCompoentConstructor 제네릭 메서드를 사용할 수도 있습니다. 다음 예제에서는 JavaComponentConstructor 메서드를 사용하여 새 Spout를 만드는 방법을 보여 줍니다. 또한 문자열 대신 UTF-8 인코딩을 사용하여 데이터를 읽도록 Spout를 구성하는 방법도 보여 줍니다.

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> UnicodeEventDataScheme은 [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/)에서 사용할 수 있는 Event Hub 구성 요소의 9.5 버전에서만 사용할 수 있습니다.

### <a name="example-bolt-usage"></a>예제 Bolt 사용

JavaComponmentConstructor 메서드를 사용하여 Bolt의 인스턴스를 만들어야 합니다. 다음 예제에서는 EventHubBolt의 새 인스턴스를 만들고 구성하는 방법을 보여 줍니다.

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> 이 예제에서는 Spout 예제와 같이 JavaComponentConstructor를 사용하여 별도의 EventHubBoltConfig를 만드는 대신 문자열로 전달되는 Clojure 식을 사용합니다. 어떤 방법이든 작동합니다. 자신에게 가장 좋은 것을 사용합니다.

## <a name="download-the-completed-project"></a>완성된 프로젝트 다운로드

이 자습서에서 만든 프로젝트의 전체 버전은 GitHub [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)에서 다운로드할 수 있습니다. 그러나 이 자습서의 단계에 따라 구성 설정을 제공해야 합니다.

### <a name="prerequisites"></a>필수 조건

* [HDInsight 클러스터 버전 3.5의 Apache Storm](hdinsight-apache-storm-tutorial-get-started.md)

    > [!WARNING]
    > 이 문서에서 사용되는 예제에는 HDInsight 버전 3.5에서 Storm이 필요합니다. 이전 버전의 클러스터의 버전과 HDInsight 3.5에 포함된 Storm 버전 간의 코어 Storm 구성 요소에 사용되는 클래스 이름이 변경되었습니다. 이전 클러스터에서 작동하는 이 예제의 버전은 [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases)를 참조하세요.

* [Azure 이벤트 허브](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Azure .NET SDK](http://azure.microsoft.com/downloads/)

* [Visual Studio용 HDInsight 도구](hdinsight-hadoop-visual-studio-tools-get-started.md)

* 개발 환경에서 Java JDK 1.7 이상 - JDK 다운로드는 [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)에서 사용할 수 있습니다.

  * **JAVA_HOME** 환경 변수가 Java를 포함하고 있는 디렉터리를 가리켜야 합니다.
  * **%JAVA_HOME%/bin** 디렉터리가 경로에 있어야 합니다.

## <a name="download-the-event-hub-components"></a>Event Hub 구성 요소 다운로드

Spout 및 Bolt는 **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**이라는 단일 Java 보관(.jar) 파일로 분산됩니다. 여기서 #.#은 파일의 버전입니다.

HDInsight 버전 3.5에서 Storm과 작동하는 jar 파일 버전은 [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/)에서 다운로드할 수 있습니다.

`eventhubspout`라는 새 디렉터리를 만들고 이 디렉터리에 파일을 저장합니다.

## <a name="configure-event-hubs"></a>이벤트 허브 구성

이벤트 허브는 이 예제의 데이터 원본입니다. [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) 문서의 **Event Hub 만들기** 섹션에 있는 정보를 참조하세요.

1. 이벤트 허브가 생성된 후에는 Azure Portal에서 EventHub 블레이드를 보고 **공유 액세스 정책**을 선택합니다. **+ 추가** 항목을 사용하여 다음 정책에 추가합니다.
   
   | 이름 | 권한 |
   | --- | --- |
   | 기록기 |보내기 |
   | 판독기 |수신 대기 |
   
    ![정책](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. **판독기** 및 **기록기** 정책을 선택합니다. 나중에 사용되므로 두 정책에 대한 **PRIMARY KEY** 값을 복사 및 저장합니다.

## <a name="configure-the-eventhubwriter"></a>EventHubWriter 구성

1. 최신 버전의 Visual Studio용 HDInsight 도구를 아직 설치하지 않은 경우 [Visual Studio용 HDInsight 도구 사용 시작](hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

2. [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)에서 솔루션을 다운로드합니다. 솔루션을 열고 잠시 시간을 내어 **EventHubWriter** 프로젝트 코드를 살펴봅니다.

3. **EventHubWriter** 프로젝트에서 **App.config** 파일을 엽니다. 앞에서 구성한 Event Hub에 대한 정보를 다음 키에 대한 값에 입력합니다.
   
   | 키 | 값 |
   | --- | --- |
   | EventHubPolicyName |기록기(*보내기* 권한이 있는 정책에 다른 이름을 사용한 경우 대신 사용) |
   | EventHubPolicyKey |기록기 정책에 대한 키 |
   | EventHubNamespace |Event Hub가 포함된 네임스페이스 |
   | EventHubName |Event Hub 이름 |
   | EventHubPartitionCount |Event Hub에서 파티션 수 |

4. **App.config** 파일을 저장하고 닫습니다.

## <a name="configure-the-eventhubreader"></a>EventHubReader 구성

1. **EventHubReader** 프로젝트를 열고 잠시 시간을 내어 코드를 살펴봅니다.

2. **EventHubWriter**에 대한 **App.config**를 엽니다. 앞에서 구성한 Event Hub에 대한 정보를 다음 키에 대한 값에 입력합니다.
   
   | 키 | 값 |
   | --- | --- |
   | EventHubPolicyName |판독기(*수신 대기* 권한이 있는 정책에 다른 이름을 사용한 경우 대신 사용) |
   | EventHubPolicyKey |판독기 정책에 대한 키 |
   | EventHubNamespace |Event Hub가 포함된 네임스페이스 |
   | EventHubName |Event Hub 이름 |
   | EventHubPartitionCount |Event Hub에서 파티션 수 |

3. **App.config** 파일을 저장하고 닫습니다.

## <a name="deploy-the-topologies"></a>토폴로지 배포

1. **솔루션 탐색기**에서 **EventHubReader** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.
   
    ![storm에 제출](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. **토폴로지 제출** 화면에서 **Storm 클러스터**를 선택합니다. **추가 구성**을 확장하고 **Java 파일 경로**, **...**을 차례로 선택한 다음 이전에 다운로드한 jar 파일을 포함하고 있는 디렉터리를 선택합니다. 마지막으로 **제출**을 클릭합니다.
   
    ![제출 대화 상자 이미지](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. 토폴로지가 제출되었으면 **Storm Topologies Viewer** 가 나타납니다. 왼쪽 창에서 **EventHubReader** 토폴로지를 선택하여 토폴로지에 대한 통계를 확인합니다. 현재 이벤트 허브에 기록된 이벤트가 없으므로 아무 것도 발생하지 않아야 합니다.
   
    ![예제 저장소 보기](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. **솔루션 탐색기**에서 **EventHubWriter** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.

5. **토폴로지 제출** 화면에서 **Storm 클러스터**를 선택합니다. **추가 구성**을 확장하고 **Java 파일 경로**, **...**을 차례로 선택한 다음 이전에 다운로드한 jar 파일을 포함하고 있는 디렉터리를 선택합니다. 마지막으로 **제출**을 클릭합니다.

6. 토폴로지가 제출되었으면 **Storm Topologies Viewer** 에서 토폴로지 목록을 새로 고쳐 두 토폴로지 모두 클러스터에서 실행되는지 확인합니다.

7. **Storm Topologies Viewer**에서 **EventHubReader** 토폴로지를 선택합니다.

8. 그래프 보기에서 **LogBolt** 구성 요소를 두 번 클릭합니다. 그러면 bolt에 대한 **구성 요소 요약** 페이지가 열립니다.

9. **Executors** 섹션에서 **Port** 열의 링크 중 하나를 선택합니다. 그러면 구성 요소가 로깅한 정보가 표시됩니다. 로깅된 정보는 다음과 유사합니다.
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>토폴로지 중지

토폴로지를 중지하려면 **Storm Topology Viewer**에서 각 토폴로지를 선택한 다음 **중단**을 클릭합니다.

![토폴로지를 중단하는 이미지](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 C# 토폴로지에서 Java 이벤트 허브 Spout 및 Bolt를 사용하여 Azure 이벤트 허브의 데이터로 작업하는 방법을 배웠습니다. C# 토폴로지 만들기에 대해 자세히 알아보려면 다음을 참조하세요.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [SCP 프로그래밍 가이드](hdinsight-storm-scp-programming-guide.md)
* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)


