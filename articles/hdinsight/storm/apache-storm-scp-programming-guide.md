---
title: Azure HDInsight의 Storm에 대한 SCP.NET 프로그래밍 가이드
description: Azure HDInsight에서 실행되는 Storm과 함께 사용할 수 있도록 SCP.NET을 사용하여 .NET 기반 Storm 토폴로지를 만드는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: c993b3f70f609fb79c51ba9be08fa3d5dc7e8317
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864111"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight의 Apache Storm에 대한 SCP 프로그래밍 가이드

SCP는 안정적이며 일관성 있는 실시간 고성능 데이터 처리 애플리케이션을 빌드하기 위한 플랫폼입니다. 이 플랫폼은 오픈 소스 소프트웨어 커뮤니티에서 디자인한 스트림 처리 시스템인 [Apache Storm](https://storm.incubator.apache.org/)을 기반으로 구축되었습니다. Storm은 Nathan Marz가 만들었으며 Twitter에서 오픈 소스로 게시되었습니다. Storm은 매우 안정적인 분산 방식 조정과 상태 관리를 수행하는 데 사용할 수 있는 또 다른 Apache 프로젝트인 [Apache ZooKeeper](https://zookeeper.apache.org/)를 활용합니다.

SCP 프로젝트는 Windows에서 Storm을 포팅했을 뿐 아니라 Windows 환경용 확장 및 사용자 지정도 추가했습니다. 확장에는 .NET 개발자 환경 및 .NET 라이브러리가 포함됩니다. 사용자 지정에는 Windows 기반 배포가 포함됩니다.

확장 및 사용자 지정을 사용하면 오픈 소스 소프트웨어 프로젝트를 포크하지 않아도 됩니다. Storm에 구축된 파생 환경을 사용할 수 있습니다.

## <a name="processing-model"></a>처리 모델

SCP의 데이터는 튜플의 연속 스트림으로 모델링됩니다. 일반적으로 튜플은 다음을 수행합니다.

1. 큐로 흐릅니다.
1. Storm 토폴로지 내에서 호스트되는 비즈니스 논리에 의해 선택되고 변환됩니다.
1. 출력을 다른 SCP 시스템에 튜플로 파이프하거나 분산 파일 시스템 및 SQL Server와 같은 데이터베이스와 같은 저장소에 커밋됩니다.

:::image type="content" source="./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png" alt-text="큐에서 데이터를 처리 단계로 피드하고, 처리 단계에서는 데이터 저장소를 피드하는 다이어그램" border="false":::

Storm에서는 애플리케이션 토폴로지에 따라 계산 그래프가 정의됩니다. 토폴로지 내 각 노드에는 처리 논리가 포함됩니다. 노드 간의 링크는 데이터 흐름을 나타냅니다.

토폴로지에 입력 데이터를 삽입하는 노드를 _Spout_ 라고 합니다. 데이터를 시퀀싱하는 데 사용할 수 있습니다. 입력 데이터는 파일 로그, 트랜잭션 데이터베이스 또는 시스템 성능 카운터와 같은 소스에서 가져올 수 있습니다.

입력과 출력 데이터 흐름이 모두 있는 노드를 _Bolt_ 라고 합니다. Bolt는 실제 데이터 필터링, 선택, 집계를 수행합니다.

SCP는 최상의 노력, 최소한 한 번 및 정확히 한 번 방식의 데이터 처리를 지원합니다.

분산 스트림 처리 애플리케이션에서는 데이터 처리 중 오류가 발생할 수 있습니다. 이런 오류에는 네트워크 중단, 머신 오류 또는 코드 오류 등이 포함됩니다. 최소한 한 번 방식의 처리는 오류 발생 시 동일한 데이터를 자동으로 재생하여 모든 데이터를 한 번 이상 처리되도록 합니다.

최소한 한 번 방식의 처리는 간단하고 안정적이며 대부분의 애플리케이션에 적합합니다. 그러나 애플리케이션에서 정확한 계산을 수행해야 하는 경우에는 애플리케이션 토폴로지에서 같은 데이터가 재생될 수 있으므로 최소한 한 번 방식의 처리로는 부족합니다. 이와 같은 경우에는 정확히 한 번 방식의 처리 방식을 사용해야 데이터가 여러 번 재생 및 처리되어도 올바른 결과가 도출됩니다.

.NET 개발자는 SCP를 통해 JVM(Java Virtual Machine)을 사용하면서 실시간 데이터 처리 애플리케이션을 만들 수 있습니다. JVM과 .NET은 TCP 로컬 소켓을 통해 통신합니다. 각 Spout/Bolt는 .NET/Java 프로세스 쌍이며 사용자 논리는 .NET 프로세스에서 플러그 인으로 실행됩니다.

SCP를 기반으로 데이터 처리 애플리케이션을 빌드하려면 다음 단계를 따라야 합니다.

1. 큐에서 데이터를 끌어오도록 Spout를 설계 및 구현합니다.
1. 입력 데이터를 처리한 다음 데이터베이스 등의 외부 저장소에 저장하도록 Bolt를 설계 및 구현합니다.
1. 토폴로지를 설계한 다음 제출하고 실행합니다.

토폴로지는 꼭짓점 및 꼭짓점 간의 데이터 흐름을 정의합니다. SCP는 토폴로지 사양을 가져온 다음 Storm 클러스터에 배포합니다. 이 클러스터의 각 논리 노드에서 개별 꼭짓점이 실행됩니다. Storm 작업 스케줄러는 장애 조치(failover) 및 크기 조정을 담당합니다.

이 문서에서는 몇 가지 간단한 예를 통해 SCP를 사용하여 데이터 처리 애플리케이션을 빌드하는 방법을 안내합니다.

## <a name="scp-plug-in-interface"></a>SCP 플러그 인 인터페이스

SCP 플러그 인은 독립 실행형 애플리케이션입니다. 개발 중에 Visual Studio 내에서 실행되고 프로덕션 배포 후에는 Storm 파이프라인에 연결될 수 있습니다.

SCP 플러그 인을 작성하는 것은 다른 Windows 콘솔 애플리케이션을 작성하는 것과 같습니다. SCP.NET 플랫폼은 Spout/Bolt에 대한 일부 인터페이스를 선언합니다. 플러그 인 코드는 해당 인터페이스를 구현합니다. 이 설계의 주요 목적은 SCP.NET 플랫폼에서 다른 작업을 처리할 수 있도록 하는 동시에 비즈니스 논리에 집중할 수 있도록 하는 것입니다.

플러그 인 코드는 다음 인터페이스 중 하나를 구현합니다. 인터페이스는 토폴로지가 트랜잭션인지와 구성 요소가 Spout인지, Bolt인지에 따라 달라집니다.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** 은 대부분의 플러그 인에 대한 공통 인터페이스입니다. 현재는 더미 인터페이스입니다.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** 는 비트랜잭션 Spout용 인터페이스입니다.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

**NextTuple** 이 호출되면 C# 코드에서 하나 이상의 튜플을 내보낼 수 있습니다. 내보낼 튜플이 없으면 아무 항목도 내보내지 않고 이 메서드가 반환되어야 합니다.

**NextTuple**, **Ack**, **Fail** 메서드는 모두 C# 프로세스의 단일 스레드에 있는 긴밀한 루프에서 호출됩니다. 내보낼 튜플이 없는 경우 10밀리초 정도의 짧은 시간 동안 **NextTuple** 을 일시 중지합니다. 해당 일시 중지 모드는 CPU 가용성 낭비를 방지하는 데 도움이 됩니다.

**Ack** 및 **Fail** 메서드는 사양 파일이 승인 메커니즘을 사용 설정하는 경우에만 호출됩니다. *seqId* 매개 변수는 승인되거나 실패한 튜플을 식별합니다. 비트랜잭션 토폴로지에서 승인이 사용 설정된 경우 Spout에서 다음 **내보내기** 함수를 사용해야 합니다.

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

비트랜잭션 토폴로지가 승인을 지원하지 않는 경우 **Ack** 및 **Fail** 은 빈 함수로 남겨둘 수 있습니다.

해당 함수의 *parms* 입력 매개 변수는 빈 사전이며 나중에 사용하도록 예약됩니다.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** 는 비트랜잭션 Bolt의 인터페이스입니다.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

새 튜플이 있으면 **Execute** 함수가 호출되어 해당 튜플을 처리합니다.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** 는 트랜잭션 Spout용 인터페이스입니다.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

비트랜잭션와 마찬가지로 **NextTx**, **Ack**, **Fail** 은 모두 C# 프로세스의 단일 스레드에 있는 긴밀한 루프에서 호출됩니다. 내보낼 튜플이 없는 경우 10밀리초 정도의 짧은 시간 동안 **NextTx** 를 일시 중지합니다. 해당 일시 중지 모드는 CPU 가용성 낭비를 방지하는 데 도움이 됩니다.

새 트랜잭션을 시작하기 위해 **NextTx** 를 호출하면 *seqId* 출력 매개 변수가 트랜잭션을 식별합니다. 트랜잭션은 **Ack** 및 **Fail** 에도 사용됩니다. **NextTx** 메서드는 Java 측으로 데이터를 내보낼 수 있습니다. 재생을 지원하기 위해 데이터는 ZooKeeper에 저장됩니다. ZooKeeper의 용량은 제한적이므로, 코드는 트랜잭션 Spout에서 대량 데이터가 아닌 메타데이터만 내보내야 합니다.

Storm은 실패한 트랜잭션을 자동으로 재생하기 때문에 **Fail** 은 일반적으로 호출되지 않습니다. 그러나 SCP는 트랜잭션 Spout에서 내보낸 메타데이터를 확인할 수 있으면 메타데이터가 잘못된 경우 **Fail** 을 호출할 수 있습니다.

해당 함수의 *parms* 입력 매개 변수는 빈 사전이며 나중에 사용하도록 예약됩니다.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** 는 트랜잭션 Bolt용 인터페이스입니다.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

새 튜플이 Bolt에 도착하면 **Execute** 메서드가 호출됩니다. 이 트랜잭션이 종료되면 **FinishBatch** 메서드가 호출됩니다. *parms* 입력 매개 변수는 나중에 사용하도록 예약됩니다.

트랜잭션 토폴로지의 경우 **StormTxAttempt** 는 중요한 클래스입니다. 이 클래스에는 두 개의 멤버(**TxId** 및 **AttemptId**)가 있습니다. **TxId** 멤버는 특정 트랜잭션을 식별합니다. 트랜잭션이 실패하여 재생되는 경우 트랜잭션이 여러 번 시도될 수 있습니다.

SCP.NET은 Java에서 Storm이 수행하는 것처럼 새 **ISCPBatchBolt** 개체를 만들어 각 **StormTxAttempt** 개체를 처리합니다. 이 설계의 목적은 병렬 트랜잭션 처리를 지원하는 것입니다. 트랜잭션 시도가 완료되면 해당 **ISCPBatchBolt** 개체가 삭제되고 가비지 수집됩니다.

## <a name="object-model"></a>개체 모델

SCP.NET에서는 개발자가 프로그래밍에 사용할 수 있는 주요 개체의 간단한 집합도 제공합니다. 개체는 **Context**, **StateStore**, **SCPRuntime** 입니다. 해당 개체에 대해서는 이 섹션에서 설명합니다.

### <a name="context"></a>Context

**컨텍스트** 개체는 애플리케이션에 실행 환경을 제공합니다. **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** 또는 **ISCPBatchBolt** 의 각 **ISCPPlugin** 인스턴스에는 해당하는 **컨텍스트** 인스턴스가 있습니다. **컨텍스트** 에서 제공하는 기능은 다음 두 파트로 나누어집니다.

* 전체 C# 프로세스에서 사용할 수 있는 정적 파트
* 특정 **컨텍스트** 인스턴스에만 사용할 수 있는 동적 파트

### <a name="static-part"></a>정적 파트

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**로거** 개체는 로깅을 위해 제공됩니다.

**pluginType** 개체는 C# 프로세스의 플러그 인 유형을 나타냅니다. 프로세스를 Java 없이 로컬 테스트 모드에서 실행하는 경우 플러그 인 유형은 **SCP_NET_LOCAL** 입니다.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Config** 속성이 Java 측에서 구성 매개 변수를 가져오고, Java 측은 C# 플러그 인이 초기화될 때 이 매개 변수를 전달합니다. **Config** 매개 변수는 **stormConf** 와 **pluginConf** 의 두 파트로 나누어집니다.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**stormConf** 파트는 Storm에서 정의한 매개 변수이며, **pluginConf** 파트는 SCP에서 정의한 매개 변수입니다. 예를 들면 다음과 같습니다.

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**TopologyContext** 형식은 토폴로지 컨텍스트를 가져옵니다. 병렬 구성 요소가 여러 개인 경우에 가장 유용합니다. 예를 들면 다음과 같습니다.

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>동적 파트

다음 인터페이스는 SCP.NET 플랫폼에서 만들고 코드에 전달되는 특정 **컨텍스트** 인스턴스와 관련이 있습니다.

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

승인을 지원하는 비트랜잭션 Spout의 경우 다음 메서드가 제공됩니다.

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

승인을 지원하는 비트랜잭션 Bolt는 수신된 튜플로 **Ack** 또는 **Fail** 을 명시적으로 호출합니다. 새 튜플을 내보낼 때 Bolt가 튜플의 앵커도 지정해야 합니다. 다음과 같은 메서드가 제공됩니다.

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore** 개체는 메타데이터 서비스, 단조 시퀀스 생성, 대기 없는 조정을 제공합니다. **StateStore** 에서 더 높은 수준의 분산 동시성 추상화를 빌드할 수 있습니다. 이런 추상화에는 분산 잠금, 분산 큐, 장벽, 트랜잭션 서비스가 포함됩니다.

SCP 애플리케이션은 **상태** 개체를 사용하여 [Apache ZooKeeper](https://zookeeper.apache.org/)의 정보를 직렬화할 수 있습니다. 이 기능은 트랜잭션 토폴로지에 특히 유용합니다. 트랜잭션 Spout가 응답을 중지하고 다시 시작하는 경우 **상태** 는 ZooKeeper에서 필요한 정보를 검색하고 파이프라인을 다시 시작할 수 있습니다.

**StateStore** 개체에는 3가지 주요 메서드가 있습니다.

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**State** 개체에는 3가지 주요 메서드가 있습니다.

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

**simpleMode** 를 **true** 로 설정하면 **Commit** 메서드가 ZooKeeper에서 해당 ZNode를 삭제합니다. 그렇지 않으면 메서드가 현재 ZNode를 삭제하고 COMMITTED\_PATH에서 새 노드를 추가합니다.

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime** 클래스는 다음의 두 메서드를 제공합니다.

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**Initialize** 메서드는 SCP 런타임 환경을 초기화합니다. 이 메서드에서 C# 프로세스는 Java 측에 연결되어 구성 매개 변수와 토폴로지 컨텍스트를 가져옵니다.

**LaunchPlugin** 메서드는 메시지 처리 루프를 시작합니다. 이 루프에서 C# 플러그 인은 Java 측으로부터 메시지를 수신합니다. 이런 메시지에는 튜플 및 컨트롤 신호가 포함됩니다. 그러면 플러그 인이 코드에서 제공하는 인터페이스 메서드를 호출하여 메시지를 처리합니다.

**LaunchPlugin** 의 입력 매개 변수는 대리자입니다. 메서드는 **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** 또는 **ISCPBatchBolt** 인터페이스를 구현하는 개체를 반환할 수 있습니다.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

**ISCPBatchBolt** 의 경우 *parms* 매개 변수에서 **StormTxAttempt** 개체를 가져와 이 개체를 사용하여 시도가 재생된 시도인지를 판단할 수 있습니다. 재생 시도에 대한 확인은 커밋 Bolt에서 수행되는 경우가 많습니다. 이 문서의 뒷부분에 나오는 HelloWorldTx 예제에서는 이와 같은 확인을 보여 줍니다.

SCP 플러그 인은 일반적으로 로컬 테스트 모드와 일반 모드의 두 가지 모드로 실행될 수 있습니다.

#### <a name="local-test-mode"></a>로컬 테스트 모드

이 모드에서 C# 코드로 작성된 SCP 플러그 인은 개발 단계 중에 Visual Studio 내에서 실행됩니다. 이 모드에서는 **ILocalContext** 인터페이스를 사용할 수 있습니다. 인터페이스는 내보낸 튜플을 로컬 파일에 직렬화하고 다시 RAM으로 읽어오는 메서드를 제공합니다.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>일반 모드

이 모드에서는 Storm Java 프로세스가 SCP 플러그 인을 실행합니다. 예를 들면 다음과 같습니다.

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>토폴로지 사양 언어

SCP 토폴로지 사양은 SCP 토폴로지를 설명하고 구성하기 위한 DSL(Domain-Specific Language)로, [Storm의 Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html)을 기반으로 하며 SCP에 의해 확장됩니다.

**runSpec** 명령을 통해 실행하기 위해 Storm 클러스터에 직접 토폴로지 사양을 제출할 수 있습니다.

SCP.NET에는 트랜잭션 토폴로지를 정의하는 다음 함수가 추가되었습니다.

| 새 함수 | 매개 변수 | Description |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |토폴로지 이름, Spout 정의 맵, Bolt 정의 맵으로 트랜잭션 토폴로지를 정의합니다. |
| **scp-tx-spout** |*exec-name*<br />*args*<br />*필드* |트랜잭션 Spout를 정의합니다. 함수는 *exec-name* 으로 지정된 애플리케이션을 실행하고 *args* 를 사용합니다.<br /><br />*fields* 매개 변수는 Spout의 출력 필드를 지정합니다. |
| **scp-tx-batch-bolt** |*exec-name*<br />*args*<br />*필드* |트랜잭션 일괄 처리 Bolt를 정의합니다. 함수는 *exec-name* 으로 지정된 애플리케이션을 실행하고 *args* 를 사용합니다.<br /><br />*fields* 매개 변수는 Bolt의 출력 필드를 지정합니다. |
| **scp-tx-commit-bolt** |*exec-name*<br />*args*<br />*필드* |트랜잭션 커밋 Bolt를 정의합니다. 함수는 *exec-name* 으로 지정된 애플리케이션을 실행하고 *args* 를 사용합니다.<br /><br />*fields* 매개 변수는 Bolt의 출력 필드를 지정합니다. |
| **nontx-topology** |*topology-name*<br />*spout-map*<br />*bolt-map* |토폴로지 이름, Spout 정의 맵, Bolt 정의 맵으로 비트랜잭션 토폴로지를 정의합니다. |
| **scp-spout** |*exec-name*<br />*args*<br />*필드*<br />*parameters* |비트랜잭션 Spout를 정의합니다. 함수는 *exec-name* 으로 지정된 애플리케이션을 실행하고 *args* 를 사용합니다.<br /><br />*fields* 매개 변수는 Spout의 출력 필드를 지정합니다.<br /><br />*parameters* 매개 변수는 선택 사항입니다. 이를 사용하여 “nontransactional.ack.enabled”와 같은 매개 변수를 지정합니다. |
| **scp-bolt** |*exec-name*<br />*args*<br />*필드*<br />*parameters* |비트랜잭션 Bolt를 정의합니다. 함수는 *exec-name* 으로 지정된 애플리케이션을 실행하고 *args* 를 사용합니다.<br /><br />*fields* 매개 변수는 Bolt의 출력 필드를 지정합니다.<br /><br />*parameters* 매개 변수는 선택 사항입니다. 이를 사용하여 “nontransactional.ack.enabled”와 같은 매개 변수를 지정합니다. |

SCP.NET은 다음 키워드를 정의합니다.

| 키워드 | Description |
| --- | --- |
| **:name** |토폴로지 이름 |
| **:topology** |이전 테이블의 함수 및 기본 제공 함수를 사용하는 토폴로지 |
| **:p** |각 Spout 또는 Bolt에 대한 병렬 처리 힌트 |
| **:config** |매개 변수를 구성할지 기존 매개 변수를 업데이트할지 여부 |
| **:schema** |스트림의 스키마 |

SCP.NET은 다음과 같은 자주 사용되는 매개 변수도 정의합니다.

| 매개 변수 | Description |
| --- | --- |
| "plugin.name" |C# 플러그 인의 .exe 파일 이름 |
| "plugin.args" |플러그 인 인수 |
| "output.schema" |출력 스키마 |
| "nontransactional.ack.enabled" |비트랜잭션 토폴로지에 대해 승인이 사용되는지 여부 |

**runSpec** 명령은 비트와 함께 배포됩니다. 명령은 다음과 같이 사용됩니다.

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*resource-dir* 매개 변수는 선택 사항입니다. C# 애플리케이션을 연결하려는 경우 지정합니다. 지정된 디렉터리에는 애플리케이션, 종속성, 구성이 포함됩니다.

*classpath* 매개 변수도 선택 사항으로, 사양 파일에 Java Spout 또는 Bolt가 포함된 경우 Java 클래스 경로를 지정합니다.

## <a name="miscellaneous-features"></a>기타 기능

### <a name="input-and-output-schema-declarations"></a>입력 및 출력 스키마 선언

C# 프로세스에서 튜플을 내보낼 수 있습니다. 이렇게 하기 위해 플랫폼은 튜플을 **byte[]** 개체로 직렬화하고 개체를 Java 측으로 전송합니다. 그런 다음 Storm은 이 튜플을 대상으로 전송합니다.

다운스트림 구성 요소에서 C# 프로세스는 Java 측에서 튜플을 다시 수신하여 플랫폼의 원래 형식으로 변환합니다. 이 모든 작업은 플랫폼에 의해 숨겨집니다.

직렬화와 역직렬화를 지원하려면 코드가 입력과 출력의 스키마를 선언해야 합니다. 스키마는 사전으로 정의됩니다. 스트림 ID는 사전 키입니다. 키 값은 열의 형식입니다. 구성 요소는 여러 스트림을 선언할 수 있습니다.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

**컨텍스트** 개체에 다음 함수가 추가됩니다.

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

개발자는 내보낸 튜플이 스트림에 대해 정의된 스키마를 준수하는지 확인해야 합니다. 그렇지 않으면 시스템이 런타임 예외를 throw합니다.

### <a name="multistream-support"></a>멀티스트림 지원

코드는 SCP를 통해 동시에 여러 고유 스트림으로 내보내거나 수신할 수 있습니다. **Context** 개체는 이 지원을 **Emit** 메서드의 선택 사항 스트림 ID 매개 변수로 반영합니다.

SCP.NET **Context** 개체에는 두 개의 메서드가 추가되었습니다. 해당 메서드는 하나 이상의 튜플을 특정 스트림으로 내보냅니다. *streamId* 매개 변수는 문자열입니다. 해당 값은 C# 코드와 토폴로지 정의 사양에서 동일해야 합니다.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

존재하지 않는 스트림으로 내보내면 런타임 예외가 발생합니다.

### <a name="fields-grouping"></a>필드 그룹화

SCP.NET에서는 Storm의 기본 제공 필드 그룹화가 제대로 작동하지 않습니다. Java 프록시 측에서는 모든 필드의 데이터 형식이 실제로 **byte[]** 입니다. 필드 그룹화는 **byte[]** 개체의 해시 코드를 사용하여 그룹화를 수행합니다. 해시 코드는 RAM에서 이 개체의 주소입니다. 따라서 콘텐츠를 동일하지만 주소는 다른 두 멀티바이트 개체의 경우 그룹화가 올바르게 적용되지 않습니다.

SCP.NET에는 사용자 지정된 그룹화 메서드가 추가되었습니다. 이 메서드는 **byte[]** 개체의 콘텐츠를 사용하여 그룹화를 수행합니다. 사양 파일에서 구문은 다음 예제와 같습니다.

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

위의 사양 파일에서는 다음을 수행합니다.

* `scp-field-group`은 그룹화가 SCP에서 구현하는 사용자 지정 필드 그룹화임을 지정합니다.
* `:tx` 또는 `:non-tx`는 토폴로지가 트랜잭션인지를 지정합니다. 트랜잭션 토폴로지의 시작 인덱스와 비트랜잭션 토폴로지의 시작 인덱스는 서로 다르기 때문에 이 정보가 필요합니다.
* `[0,1]`은 0으로 시작하는 필드 ID의 해시 집합을 지정합니다.

### <a name="hybrid-topology"></a>하이브리드 토폴로지

네이티브 Storm 코드는 Java로 작성됩니다. SCP.NET에는 비즈니스 논리를 처리하는 C# 코드를 작성할 수 있도록 개선된 Storm이 있습니다. 그러나 SCP.NET은 C# Spout/Bolt뿐 아니라 Java Spout/Bolt도 포함하는 하이브리드 토폴로지도 지원합니다.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>사양 파일에서 Java Spout/Bolt 지정

사양 파일에서 **scp-spout** 및 **scp-bolt** 를 사용하여 Java Spout 및 Bolt를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

여기에서 `microsoft.scp.example.HybridTopology.Generator`는 Java Spout 클래스의 이름입니다.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>runSpec 명령에 Java 클래스 경로 지정

Java Spout 또는 Bolt가 포함된 토폴로지를 제출하려면 먼저 이를 컴파일하여 JAR 파일을 생성합니다. 그런 다음 토폴로지를 제출할 때 JAR 파일을 포함하는 Java 클래스 경로를 지정합니다. 예를 들면 다음과 같습니다.

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

여기에서 `examples\HybridTopology\java\target\`은 Java Spout/Bolt JAR 파일을 포함하는 폴더입니다.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java와 C# 간 직렬화 및 역직렬화

SCP 구성 요소는 Java 측과 C# 측을 포함합니다. 네이티브 Java Spout/Bolt와 상호 작용하려면 다음 그래프와 같이 Java 측과 C# 측 간에 직렬화 및 역직렬화가 발생해야 합니다.

:::image type="content" source="./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png" alt-text="다른 Java 구성 요소가 SCP 구성 요소로 전송하고, SCP 구성 요소가 다른 Java 구성 요소로 전송하는 다이어그램" border="false":::

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 측 직렬화 및 C# 측 역직렬화

먼저 Java 측 직렬화와 C# 측 역직렬화를 위한 기본 구현을 제공합니다.

사양 파일에서 Java 측의 직렬화 메서드를 지정합니다.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

C# 코드에서 C# 측의 역직렬화 메서드를 지정합니다.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

데이터 형식이 너무 복잡하지 않으면 이 기본 구현에서 대부분의 경우를 처리할 수 있습니다. 자체적인 구현을 연결할 수 있는 경우는 다음과 같습니다.

* 데이터 형식이 너무 복잡하여 기본 구현을 구현이 불가능한 경우
* 기본 구현의 성능이 요구 사항을 충족하지 않는 경우

Java 측의 직렬화 인터페이스는 다음과 같이 정의됩니다.

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 측의 역직렬화 인터페이스는 다음과 같이 정의됩니다.

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>C# 측 직렬화 및 Java 측 역직렬화

C# 코드에서 C# 측의 직렬화 메서드를 지정합니다.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

사양 파일에서 Java 측의 역직렬화 메서드를 지정합니다.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

여기에서 `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"`는 역직렬 변환기의 이름이고 `"microsoft.scp.example.HybridTopology.Person"`은 데이터가 역직렬화되는 대상 클래스입니다.

C# 직렬 변환기 및 Java 역직렬 변환기의 자체적인 구현을 연결할 수도 있습니다.

다음 코드는 C# 직렬 변환기에 대한 인터페이스입니다.

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

다음 코드는 Java 역직렬 변환기에 대한 인터페이스입니다.

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 호스트 모드

이 모드에서는 코드를 DLL로 컴파일하고 SCP가 제공하는 SCPHost.exe를 사용하여 토폴로지를 제출할 수 있습니다. 사양 파일은 다음 코드와 같습니다.

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

여기에서 `"plugin.name"`은 SCP SDK에서 제공하는 `"SCPHost.exe"`로 지정됩니다. SCPHost.exe는 다음과 같은 순서로 세 개의 매개 변수를 허용합니다.

1. DLL 이름으로, 본 예제에서는 `"HelloWorld.dll"`입니다.
1. 클래스 이름으로, 본 예제에서는 `"Scp.App.HelloWorld.Generator"`입니다.
1. 퍼블릭 정적 메서드의 이름으로, **ISCPPlugin** 의 인스턴스를 가져오기 위해 호출할 수 있습니다.

호스트 모드에서 코드를 SCP 플랫폼의 호출을 위한 DLL로 컴파일합니다. 그러면 플랫폼이 전체 처리 논리를 완전히 제어할 수 있으므로 SCP 호스트 모드에서 토폴로지를 제출하는 것이 좋습니다. 이렇게 하면 개발 환경이 간소화됩니다. 또한 이후 릴리스의 유연성과 이전 버전과의 호환성이 향상됩니다.

## <a name="scp-programming-examples"></a>SCP 프로그래밍 예제

### <a name="helloworld"></a>HelloWorld

다음의 간단한 HelloWorld 예제는 SCP.NET를 간략히 보여 줍니다. 여기에는 **generator** 라는 Spout와 **splitter** 및 **counter** 라는 Bolt 2개가 포함된 비트랜잭션 토폴로지가 사용됩니다. **generator** Spout는 문장을 임의로 생성하여 **splitter** 로 내보냅니다. **splitter** Bolt는 문장을 단어로 분할한 다음 **counter** Bolt로 내보냅니다. **counter** Bolt는 사전을 사용하여 각 단어가 나오는 횟수를 기록합니다.

이 예제에는 두 개의 사양 파일인 HelloWorld. spec 및 HelloWorld\_EnableAck.spec이 있습니다. C# 코드는 Java 측에서 `pluginConf` 개체를 가져와 승인이 사용되는지를 확인할 수 있습니다.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Spout에서 승인을 사용하는 경우 사전이 승인되지 않은 튜플을 캐시합니다. `Fail`을 호출하면 오류가 발생한 튜플이 재생됩니다.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

다음 HelloWorldTx 예제에서는 트랜잭션 토폴로지 구현 방법을 보여 줍니다. 이 예제에는 **generator** Spout, **partial-count** Batch Bolt 및 **count-sum** Commit Bolt가 있습니다. 또한 이 예제에는 DataSource0.txt, DataSource1.txt, DataSource2.txt의 세 가지 기존 텍스트 파일이 있습니다.

각 트랜잭션에서 **generator** Spout는 기존의 3개 파일 중 2개를 임의로 선택하여 이 두 파일 이름을 **partial-count** Bolt로 내보냅니다. **partial-count** Bolt:

1. 수신한 튜플에서 파일 이름을 가져옵니다.
1. 해당 파일을 엽니다.
1. 파일의 단어 수를 계산합니다.
1. 단어 수를 **count-sum** Bolt로 내보냅니다.

**count-sum** Bolt는 총 단어 개수의 요약을 표시합니다.

정확히 한 번 의미 체계를 적용하려면 **count-sum** Commit Bolt가 재생된 트랜잭션인지를 판단해야 합니다. 이 예제에서는 다음 정적 멤버 변수가 있습니다.

```csharp
public static long lastCommittedTxId = -1; 
```

**ISCPBatchBolt** 인스턴스를 만들면 입력 매개 변수에서 `txAttempt` 개체의 값을 가져옵니다.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

`FinishBatch`가 호출되면 재생된 트랜잭션이 아닌 경우 `lastCommittedTxId`가 업데이트됩니다.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

이 토폴로지는 Java Spout와 C# Bolt를 포함하며, SCP 플랫폼에서 제공하는 기본 직렬화 및 역직렬화 구현을 사용합니다. 사양 파일 세부 정보는 예제\\HybridTopology 폴더의 HybridTopology.spec 파일을 참조하세요. 또한 Java 클래스 경로를 지정하는 방법은 SubmitTopology.bat를 참조하세요.

### <a name="scphostdemo"></a>SCPHostDemo

이 예제는 기본적으로 HelloWorld와 같습니다. 차이점은 코드가 DLL로 컴파일되며 토폴로지가 SCPHost.exe를 사용하여 제출된다는 것뿐입니다. 자세한 설명은 SCP 호스트 모드 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

SCP를 사용하여 만든 Apache Storm 토폴로지 예제는 다음 문서를 참조하세요.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight의 Apache Storm을 사용하여 Event Hubs의 차량 센서 데이터 처리](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure Event Hubs에서 Apache HBase로 ETL(추출, 변환, 로드)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
