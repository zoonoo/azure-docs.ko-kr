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
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리
Azure 이벤트 허브를 사용하면 웹 사이트, 앱 및 장치에서 대량의 데이터를 처리할 수 있습니다. 이벤트 허브 Spout를 사용하면 HDInsight에서 Apache Storm을 사용하여 이 데이터를 실시간으로 분석하기가 쉽습니다. 또한 이벤트 허브 Bolt를 사용하여 Storm에서 이벤트 허브에 데이터를 기록할 수도 있습니다.

이 자습서에서는 Visual Studio용 HDInsight 도구와 함께 설치된 Visual Studio 템플릿을 사용하여 Azure 이벤트 허브에서 작동하는 두 토폴로지를 만드는 방법을 알아봅니다.

* **EventHubWriter**: 무작위로 데이터를 생성하여 이벤트 허브에 기록합니다.
* **EventHubReader**: Event Hubs에서 데이터를 읽어 Storm 로그에 데이터를 로깅합니다.

> [!NOTE]
> 이 문서의 단계는 Visual Studio가 설치된 Windows 개발 환경을 사용하지만 컴파일된 프로젝트는 Linux 또는 Windows 기반 HDInsight 클러스터로 전송될 수 있습니다. 2016년 10월 28일 이후 생성된 Linux 기반 클러스터만 SCP.NET 토폴로지를 지원합니다.
> 
> Linux 기반 클러스터에 C# 토폴로지를 사용하려면 프로젝트에 사용되는 Microsoft.SCP.Net.SDK NuGet 패키지를 0.10.0.6 버전 이상으로 업데이트해야 합니다. 패키지 버전은 HDInsight에 설치된 Storm의 주 버전과도 일치해야 합니다. 예를 들어 HDInsight에서 Storm 버전 3.3 및 3.4는 Storm 버전 0.10.x를 사용하는 반면, HDInsight 3.5는 Storm 1.0.x를 사용합니다.
> 
> Linux 기반 클러스터의 C# 토폴로지는 .NET 4.5를 사용해야 하며 Mono를 사용하여 HDInsight 클러스터에서 실행해야 합니다. 대부분의 항목이 작동하지만 호환성 문제는 [Mono 호환성](http://www.mono-project.com/docs/about-mono/compatibility/) 문서를 확인해야 합니다.
> 
> Linux 기반 또는 Windows 기반 클러스터로 작동하는 이 프로젝트의 Java 버전의 경우 [HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리(Java)](hdinsight-storm-develop-java-event-hub-topology.md)를 참조하세요.
> 
> 

## <a name="prerequisites"></a>필수 조건
* [HDInsight의 Apache Storm 클러스터](hdinsight-apache-storm-tutorial-get-started.md)
* [Azure 이벤트 허브](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* [Visual Studio용 HDInsight 도구](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>완성된 프로젝트
이 자습서에서 만든 프로젝트의 전체 버전은 GitHub [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)에서 다운로드할 수 있습니다. 그러나 이 자습서의 단계에 따라 구성 설정을 제공해야 합니다.

## <a name="event-hubs-spout-and-bolt"></a>이벤트 허브 Spout 및 Bolt
이벤트 허브 Spout 및 Bolt는 Apache Storm에서 이벤트 허브를 쉽게 사용할 수 있도록 하는 Java 구성 요소입니다. 이러한 구성 요소는 Java로 작성되지만 Visual Studio용 HDInsight 도구를 사용하면 C# 및 Java 구성 요소를 혼합하는 하이브리드 토폴로지를 만들 수 있습니다.

Spout 및 Bolt는 **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**이라는 단일 Java 보관(.jar) 파일로 분산됩니다. 여기서 #.#은 파일의 버전입니다.

### <a name="download-the-jar-file"></a>.jar 파일 다운로드
최신 버전의 jar 파일은 **lib/eventhubs** 폴더 아래의 [HDInsight Storm 예제](https://github.com/hdinsight/hdinsight-storm-examples) 프로젝트에 포함되어 있습니다. 이 파일을 다운로드하려면 다음 방법 중 하나를 사용합니다.

> [!NOTE]
> spout 및 bolt는 Apache Storm 프로젝트에 포함되도록 제출되었습니다. 자세한 내용은 GitHub에서 [STORM-583: storm-event hubs 초기 체크 인](https://github.com/apache/storm/pull/336/files) 을 참조하세요.
> 
> 

* **ZIP 파일 다운로드**: [HDInsight Storm 예제](https://github.com/hdinsight/hdinsight-storm-examples) 사이트의 오른쪽 창에서 **ZIP 다운로드**를 선택하여 프로젝트가 포함된 .zip 파일을 다운로드합니다.
  
    ![download zip 단추](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    파일을 다운로드한 후 보관 파일을 추출하면 파일이 **lib** 디렉터리에 저장됩니다.
* **프로젝트 복제**: [Git](http://git-scm.com/)가 설치된 경우 다음 명령을 사용하여 로컬에서 리포지토리를 복제한 다음 **lib** 디렉터리에서 파일을 찾습니다.
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

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
2. **토폴로지 제출** 화면에서 **Storm 클러스터**를 선택합니다. **추가 구성**을 확장하고 **Java 파일 경로**, **...**를 차례로 선택한 다음 앞에서 다운로드한 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 파일이 포함된 디렉터리를 선택합니다. 마지막으로 **제출**을 클릭합니다.
   
    ![제출 대화 상자 이미지](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. 토폴로지가 제출되었으면 **Storm Topologies Viewer** 가 나타납니다. 왼쪽 창에서 **EventHubReader** 토폴로지를 선택하여 토폴로지에 대한 통계를 확인합니다. 현재 이벤트 허브에 기록된 이벤트가 없으므로 아무 것도 발생하지 않아야 합니다.
   
    ![예제 저장소 보기](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. **솔루션 탐색기**에서 **EventHubWriter** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.
5. **토폴로지 제출** 화면에서 **Storm 클러스터**를 선택합니다. **추가 구성**을 확장하고 **Java 파일 경로**, **...**를 차례로 선택한 다음 앞에서 다운로드한 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 파일이 포함된 디렉터리를 선택합니다. 마지막으로 **제출**을 클릭합니다.
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

## <a name="notes"></a>참고 사항
### <a name="checkpointing"></a>검사점 설정
EventHubSpout는 해당 상태의 검사점을 큐에서 읽은 메시지의 현재 오프셋을 저장하는 Zookeeper 노드로 주기적으로 설정합니다. 이를 통해 구성 요소는 다음 시나리오에서 저장된 오프셋으로 메시지 수신을 시작할 수 있습니다.

* 구성 요소 인스턴스가 실패하고 다시 시작된 경우
* 노드를 추가하거나 제거하여 클러스터를 확장 또는 축소한 경우
* 토폴로지자 종료되고 **동일한 이름**으로 다시 시작된 경우

영구 검사점을 WASB(HDInsight 클러스터에서 사용하는 Azure 저장소)로 내보내고 가져올 수도 있습니다. 이 작업을 수행하는 스크립트는 HDInsight의 Storm 클러스터(**c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**)에 있습니다.

> [!NOTE]
> 클러스터에 설치된 Storm 버전이 나중에 변경될 수 있으므로 경로의 버전 번호는 다를 수 있습니다.
> 
> 

이 디렉터리의 스크립트는 다음과 같습니다.

* **stormmeta_import.cmd**: 클러스터 기본 저장소 컨테이너의 모든 Storm 메타데이터를 Zookeeper로 가져옵니다.
* **stormmeta_export.cmd**: Zookeeper의 모든 Storm 메타데이터를 클러스터 기본 저장소 컨테이너로 내보냅니다.
* **stormmeta_delete.cmd**: Zookeeper에서 모든 Storm 메타데이터를 삭제합니다.

내보내기 및 가져오기를 통해 클러스터를 삭제해야 하는 경우 검사점 데이터를 유지할 수 있지만 새 클러스터를 다시 온라인으로 전환할 때 허브의 현재 오프셋에서 처리를 다시 시작할 수 있습니다.

> [!NOTE]
> 데이터가 기본 저장소 컨테이너에 유지되므로 새 클러스터는 **반드시** 이전 클러스터와 동일한 저장소 계정 및 컨테이너를 사용해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
이 문서에서는 C# 토폴로지에서 Java 이벤트 허브 Spout 및 Bolt를 사용하여 Azure 이벤트 허브의 데이터로 작업하는 방법을 배웠습니다. C# 토폴로지 만들기에 대해 자세히 알아보려면 다음을 참조하세요.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [SCP 프로그래밍 가이드](hdinsight-storm-scp-programming-guide.md)
* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


