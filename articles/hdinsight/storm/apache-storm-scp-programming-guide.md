---
title: Azure HDInsight의 Storm에 대한 SCP.NET 프로그래밍 가이드
description: Azure HDInsight에서 실행되는 Storm과 함께 사용할 수 있도록 SCP.NET을 사용하여 .NET 기반 Storm 토폴로지를 만드는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186872"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight의 아파치 스톰에 대한 SCP 프로그래밍 가이드

SCP는 실시간으로 신뢰할 수 있고 일관된 고성능 데이터 처리 애플리케이션을 구축하기 위한 플랫폼입니다. 그것은 [아파치 스톰의](https://storm.incubator.apache.org/)상단에 내장, 이는 오픈 소스 소프트웨어 커뮤니티에 의해 설계된 스트림 처리 시스템입니다. 네이선 마즈는 폭풍을 만들었습니다. 그것은 트위터에 의해 오픈 소스로 게시되었습니다. 스톰은 매우 안정적인 분산 조정 및 상태 관리를 가능하게 하는 또 다른 아파치 프로젝트인 [아파치 사육사를](https://zookeeper.apache.org/)사용합니다.

SCP 프로젝트는 Windows에서 스톰뿐만 아니라 Windows 환경에 대한 프로젝트 추가 확장 및 사용자 지정도 이식했습니다. 확장에는 .NET 개발자 환경 및 .NET 라이브러리가 포함됩니다. 사용자 지정에는 Windows 기반 배포가 포함됩니다.

확장 및 사용자 지정을 사용하면 오픈 소스 소프트웨어 프로젝트를 포크할 필요가 없습니다. 폭풍 위에 구축된 파생 환경을 사용할 수 있습니다.

## <a name="processing-model"></a>처리 모델

SCP의 데이터는 튜플의 연속 스트림으로 모델링됩니다. 일반적으로, tuples:

1. 큐로 흐칩니다.
1. Storm 토폴로지 내에서 호스팅되는 비즈니스 논리에 의해 선택되고 변환됩니다.
1. 출력을 다른 SCP 시스템에 튜플로 파이프하거나 SQL Server와 같은 분산 파일 시스템 및 데이터베이스와 같은 저장소에 커밋됩니다.

![처리에 데이터를 공급하는 큐 다이어그램으로 데이터 저장소에 피드](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Storm에서 응용 프로그램 토폴로지에서는 계산 그래프를 정의합니다. 토폴로지의 각 노드에는 처리 논리가 포함되어 있습니다. 노드 간의 링크는 데이터 흐름을 나타냅니다.

입력 데이터를 토폴로지에 삽입하는 노드를 _주어라고 합니다._ 이를 사용하여 데이터 순서를 정할 수 있습니다. 입력 데이터는 파일 로그, 트랜잭션 데이터베이스 또는 시스템 성능 카운터와 같은 소스에서 올 수 있습니다.

입력 및 출력 데이터 흐름이 모두 있는 _노드를 볼트라고 합니다._ 실제 데이터 필터링, 선택 및 집계를 수행합니다.

SCP는 최상의 노력을 지원하며, 최소 한 번, 정확히 한 번 데이터 처리를 지원합니다.

분산 스트림 처리 응용 프로그램에서 는 데이터 처리 중에 오류가 발생할 수 있습니다. 이러한 오류에는 네트워크 중단, 시스템 오류 또는 코드 오류가 포함됩니다. 적어도 한 번 처리하면 오류가 발생할 때 동일한 데이터를 자동으로 재생하여 모든 데이터가 한 번 이상 처리되도록 합니다.

최소 한 번 처리는 간단하고 신뢰할 수 있으며 많은 응용 프로그램에 적합합니다. 그러나 응용 프로그램에 정확한 계산이 필요한 경우 응용 프로그램 토폴로지에서 동일한 데이터를 재생할 수 있으므로 최소 한 번의 처리가 충분하지 않습니다. 이 경우 정확히 한 번 처리하면 데이터를 여러 번 재생하고 처리하는 경우에도 결과가 올바른지 확인할 수 있습니다.

SCP를 사용하면 .NET 개발자가 Storm과 함께 Java 가상 머신(JVM)을 사용하는 동안 실시간 데이터 처리 응용 프로그램을 만들 수 있습니다. JVM 및 .NET은 TCP 로컬 소켓을 통해 통신합니다. 각 주어우트/볼트는 .NET/Java 프로세스 쌍으로, 사용자 논리는 .NET 프로세스에서 플러그인으로 실행됩니다.

SCP 위에 데이터 처리 응용 프로그램을 빌드하려면 다음 단계를 따르십시오.

1. 큐에서 데이터를 가져오도록 주어를 디자인하고 구현합니다.
1. 입력 데이터를 처리하고 데이터베이스와 같은 외부 저장소에 저장하는 볼트를 설계하고 구현합니다.
1. 토폴로지 설계한 다음 제출하고 실행합니다.

토폴로지에서 정점과 정점 사이에 흐르는 데이터를 정의합니다. SCP는 토폴로지 사양을 가져와 각 정점이 하나의 논리 노드에서 실행되는 Storm 클러스터에 배포합니다. Storm 작업 스케줄러는 장애 조치 및 크기 조정을 처리합니다.

이 문서에서는 몇 가지 간단한 예제를 사용하여 SCP를 사용하여 데이터 처리 응용 프로그램을 빌드하는 방법을 설명합니다.

## <a name="scp-plug-in-interface"></a>SCP 플러그인 인터페이스

SCP 플러그인은 독립 실행형 응용 프로그램입니다. 개발 중에 Visual Studio 내부에서 실행되고 프로덕션 배포 후 Storm 파이프라인에 연결할 수 있습니다.

SCP 플러그인을 작성하는 것은 다른 Windows 콘솔 응용 프로그램을 작성하는 것과 동일합니다. SCP.NET 플랫폼은 주전자/볼트에 대한 몇 가지 인터페이스를 선언합니다. 플러그인 코드는 이러한 인터페이스를 구현합니다. 이 디자인의 주요 목적은 SCP.NET 플랫폼이 다른 작업을 처리하도록 하면서 비즈니스 논리에 집중할 수 있도록 하는 것입니다.

플러그인 코드는 다음 인터페이스 중 하나를 구현합니다. 토폴로지가 트랜잭션인지 비트랜잭션인지, 구성 요소가 주전자인지 볼트인지여부에 따라 어떤 인터페이스가 달라집니다.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin은** 많은 플러그인에 대한 일반적인 인터페이스입니다.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout은** 비트랜잭션 주전자의 인터페이스입니다.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

**NextTuple이** 호출되면 C# 코드가 하나 이상의 튜플을 방출할 수 있습니다. 내보낼 것이 없으면 아무 것도 내보내지 않고 반환해야 합니다.

**NextTuple**, **Ack**및 **Fail** 메서드는 모두 C# 프로세스의 단일 스레드에서 꽉 루프로 호출됩니다. 방출할 튜플이 없는 경우, 10밀리초와 같은 짧은 시간 동안 **NextTuple** 수면을 취하십시오. 이 절전 모드는 CPU 가용성을 낭비하는 것을 방지하는 데 도움이 됩니다.

**Ack** 및 **Fail** 메서드는 사양 파일에서 승인 메커니즘을 사용할 때만 호출됩니다. *seqId* 매개 변수는 승인또는 실패한 튜플을 식별합니다. 비트랜잭션 토폴로지에서 승인이 활성화된 경우 다음 **내무** 함수를 주어에서 사용해야 합니다.

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

비트랜잭션 토폴로지가 승인을 지원하지 않는 경우 **Ack** 및 **Fail은** 빈 함수로 남을 수 있습니다.

이러한 함수의 *parms* 입력 매개 변수는 빈 사전을 지정하며 나중에 사용할 수 있습니다.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt는** 비트랜잭션 볼트의 인터페이스입니다.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

새 튜플을 사용할 수 있는 경우 **Execute** 함수를 호출하여 처리합니다.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout은** 트랜잭션 주푸트의 인터페이스입니다.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

비트랜잭션 대응과 마찬가지로 **NextTx**, **Ack**및 **Fail은** 모두 C# 프로세스의 단일 스레드에서 긴밀한 루프로 호출됩니다. 방사할 투플이 없는 **경우, NextTx가** 10밀리초와 같은 짧은 시간 동안 절전 모드로 들어오도록 합니다. 이 절전 모드는 CPU 가용성을 낭비하는 것을 방지하는 데 도움이 됩니다.

새 트랜잭션을 시작 하려면 **NextTx** 호출 되 면 *seqId* 출력 매개 변수 트랜잭션을 식별 합니다. 트랜잭션은 **Ack** 및 **Fail에서도**사용됩니다. **NextTx** 메서드는 Java 측에 데이터를 내보사할 수 있습니다. 재생을 지원하기 위해 데이터는 ZooKeeper에 저장됩니다. ZooKeeper의 용량이 제한되어 있으므로 코드는 트랜잭션 주점에서 대량 데이터가 아닌 메타데이터만 내보내야 합니다.

Storm은 실패한 트랜잭션을 자동으로 재생하므로 **Fail은** 일반적으로 호출되지 않습니다. 그러나 SCP가 트랜잭션 주둥이에서 내보낸 메타데이터를 확인할 수 있는 경우 메타데이터가 유효하지 않은 경우 **Fail을** 호출할 수 있습니다.

이러한 함수의 *parms* 입력 매개 변수는 빈 사전을 지정하며 나중에 사용할 수 있습니다.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt는** 트랜잭션 볼트의 인터페이스입니다.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

새 튜플이 볼트에 도착하면 **Execute** 메서드가 호출됩니다. 이 트랜잭션이 끝날 때 **FinishBatch** 메서드가 호출 됩니다. *parms* 입력 매개 변수는 나중에 사용할 수 있습니다.

트랜잭션 토폴로지의 경우 **StormTx시도는** 중요한 클래스입니다. **TxId** 및 **시도 ID의**두 멤버가 있습니다. **TxId** 멤버는 특정 트랜잭션을 식별합니다. 트랜잭션이 실패하고 재생되는 경우 트랜잭션을 여러 번 시도할 수 있습니다.

SCP.NET Java에서 스톰이 하는 것과 마찬가지로 각 **StormTx시도** 오브젝트를 처리하는 새 **ISCPBatchBolt** 오브젝트를 만듭니다. 이 디자인의 목적은 병렬 트랜잭션 처리를 지원하는 것입니다. 트랜잭션 시도가 완료되면 해당 **ISCPBatchBolt** 개체가 소멸되고 가비지 수집됩니다.

## <a name="object-model"></a>개체 모델

SCP.NET에서는 개발자가 프로그래밍에 사용할 수 있는 주요 개체의 간단한 집합도 제공합니다. 개체는 **컨텍스트,** **상태 저장소**및 **SCPRuntime**입니다. 이 섹션에서 설명합니다.

### <a name="context"></a>Context

**Context** 개체는 응용 프로그램에 실행 중인 환경을 제공합니다. **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**또는 **ISCPBatchBolt의** 각 **ISCPPlugin** 인스턴스에는 해당 **컨텍스트** 인스턴스가 있습니다. **Context에서** 제공하는 기능은 다음 두 부분으로 나뉩니다.

* 전체 C# 프로세스에서 사용할 수 있는 정적 부품
* 특정 **컨텍스트** 인스턴스에서만 사용할 수 있는 동적 부품

### <a name="static-part"></a>정적 부품

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**로거** 개체는 로깅을 위해 제공됩니다.

**플러그인Type** 개체는 C# 프로세스의 플러그인 유형을 나타냅니다. 프로세스가 Java 없이 로컬 테스트 모드에서 실행되는 경우 플러그인 유형이 **SCP_NET_LOCAL.**

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

**Config** 속성은 Java 측에서 구성 매개 변수를 가져옵니다. **구성** 매개 변수는 두 부분으로 나뉩니다: **stormConf** 및 **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**stormConf** 부분은 Storm에 의해 정의된 매개변수이며 **플러그인Conf** 부분은 SCP에 의해 정의된 매개변수입니다. 예를 들면 다음과 같습니다.

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

**토폴로지컨텍스트** 형식은 토폴로지 컨텍스트를 가져옵니다. 여러 병렬 구성 요소에 가장 유용합니다. 예를 들면 다음과 같습니다.

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

### <a name="dynamic-part"></a>동적 부품

다음 인터페이스는 SCP.NET 플랫폼에서 만들어 코드에 전달되는 특정 **컨텍스트** 인스턴스와 관련이 있습니다.

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

승인을 지원하는 비트랜잭션 주푸의 경우 다음 메서드가 제공됩니다.

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

승인을 지원하는 비트랜잭션 볼트는 받은 튜플을 사용하여 **Ack** 또는 **Fail을** 명시적으로 호출해야 합니다. 새 튜플을 방출할 때 볼트는 튜플의 앵커도 지정해야 합니다. 다음 방법이 제공됩니다.

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore** 개체는 메타데이터 서비스, 단조로운 시퀀스 생성 및 대기 없는 조정을 제공합니다. **StateStore**에서 상위 수준의 분산 동시성 추상화를 작성할 수 있습니다. 이러한 추상화에는 분산 잠금, 분산 큐, 장벽 및 트랜잭션 서비스가 포함됩니다.

SCP 응용 프로그램은 **상태** 개체를 사용하여 [아파치 사육사의](https://zookeeper.apache.org/)정보를 직렬화할 수 있습니다. 이 기능은 트랜잭션 토폴로지에서 특히 유용합니다. 트랜잭션 주둥이가 응답을 중지하고 다시 시작되면 **State는** ZooKeeper에서 필요한 정보를 검색하고 파이프라인을 다시 시작할 수 있습니다.

**StateStore** 개체에는 다음과 같은 주요 메서드가 있습니다.

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

**State** 개체에는 다음과 같은 주요 메서드가 있습니다.

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

**simpleMode가** **true로**설정된 경우 **커밋** 메서드는 ZooKeeper에서 해당 ZNode를 삭제합니다. 그렇지 않으면 메서드는 현재 ZNode를 삭제 하 고\_커밋된 경로에 새 노드를 추가 합니다.

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime** 클래스는 다음과 같은 두 가지 방법을 제공합니다.

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**초기화** 메서드는 SCP 런타임 환경을 초기화합니다. 이 방법에서는 C# 프로세스가 Java 측에 연결되어 구성 매개 변수 및 토폴로지 컨텍스트를 가져옵니다.

**LaunchPlugin** 메서드는 메시지 처리 루프를 시작합니다. 이 루프에서 C# 플러그인은 Java 측에서 메시지를 수신합니다. 이러한 메시지에는 투플 및 제어 신호가 포함됩니다. 그런 다음 플러그인은 코드에서 제공하는 인터페이스 메서드를 호출하여 메시지를 처리합니다.

**LaunchPlugin의** 입력 매개 변수는 대리자입니다. 메서드는 **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**또는 **ISCPBatchBolt** 인터페이스를 구현하는 개체를 반환할 수 있습니다.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

**ISCPBatchBolt의**경우 *parms* 매개 변수에서 **StormTx시도** 오브젝트를 얻고 이를 사용하여 시도가 재생된 시도인지 여부를 판단할 수 있습니다. 재생 시도에 대한 검사는 커밋 볼트에서 수행되는 경우가 많습니다. 이 문서의 후반부에서 HelloWorldTx 예제에서는 이 검사를 보여 줍니다.

SCP 플러그인은 일반적으로 로컬 테스트 모드와 일반 모드의 두 가지 모드로 실행할 수 있습니다.

#### <a name="local-test-mode"></a>로컬 테스트 모드

이 모드에서는 개발 단계에서 C# 코드의 SCP 플러그인이 Visual Studio 내에서 실행됩니다. 이 **모드에서ILocalContext** 인터페이스를 사용할 수 있습니다. 인터페이스는 내보낸 tuples를 로컬 파일에 직렬화하고 다시 RAM으로 읽는 메서드를 제공합니다.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>일반 모드

이 모드에서는 Storm Java 프로세스가 SCP 플러그인을 실행합니다. 다음은 예제입니다.

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

SCP 토폴로지 사양은 SCP 토폴로지(Topologies)를 설명하고 구성하기 위한 도메인별 언어(DSL)입니다. 그것은 [폭풍의 클로주어 DSL을](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) 기반으로 하고 SCP에 의해 확장됩니다.

**runSpec** 명령을 통해 실행을 위해 스톰 클러스터에 직접 토폴로지 사양을 제출할 수 있습니다.

SCP.NET 트랜잭션 토폴로지정의를 위해 다음 함수를 추가했습니다.

| 새 함수 | 매개 변수 | 설명 |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |토폴로지 이름, 주어집 정의 맵 및 볼트 정의 맵을 사용하여 트랜잭션 토폴로지정의를 정의합니다. |
| **scp-tx-spout** |*exec-name*<br />*Args*<br />*필드* |트랜잭션 주어를 정의합니다. 이 함수는 *exec-name으로* 지정된 응용 프로그램을 실행하고 *args를*사용합니다.<br /><br />*필드* 매개 변수는 주어의 출력 필드를 지정합니다. |
| **scp-tx-batch-bolt** |*exec-name*<br />*Args*<br />*필드* |트랜잭션 일괄 처리 볼트를 정의합니다. 이 함수는 *exec-name으로* 지정된 응용 프로그램을 실행하고 *args를 사용합니다.*<br /><br />*필드* 매개 변수는 볼트의 출력 필드를 지정합니다. |
| **scp-tx-commit-bolt** |*exec-name*<br />*Args*<br />*필드* |트랜잭션 커밋 볼트를 정의합니다. 이 함수는 *exec-name으로* 지정된 응용 프로그램을 실행하고 *args를*사용합니다.<br /><br />*필드* 매개 변수는 볼트의 출력 필드를 지정합니다. |
| **논트스 토폴로지** |*topology-name*<br />*spout-map*<br />*bolt-map* |토폴로지 이름, 주어집 정의 맵 및 볼트 정의 맵을 사용하여 비트랜잭션 토폴로지정의를 정의합니다. |
| **scp-spout** |*exec-name*<br />*Args*<br />*필드*<br />*매개 변수* |비트랜잭션 주어를 정의합니다. 이 함수는 *exec-name으로* 지정된 응용 프로그램을 실행하고 *args를*사용합니다.<br /><br />*필드* 매개 변수는 주어의 출력 필드를 지정합니다.<br /><br />*매개 변수* 매개 변수는 선택 사항입니다. "nontransactional.ack.enabled"와 같은 매개 변수를 지정하는 데 사용합니다. |
| **scp-bolt** |*exec-name*<br />*Args*<br />*필드*<br />*매개 변수* |비트랜잭션 볼트를 정의합니다. 이 함수는 *exec-name으로* 지정된 응용 프로그램을 실행하고 *args를*사용합니다.<br /><br />*필드* 매개 변수는 볼트의 출력 필드를 지정합니다.<br /><br />*매개 변수* 매개 변수는 선택 사항입니다. "nontransactional.ack.enabled"와 같은 매개 변수를 지정하는 데 사용합니다. |

SCP.NET 다음 키워드를 정의합니다.

| 키워드 | 설명 |
| --- | --- |
| **:name** |토폴로지 이름 |
| **:topology** |이전 테이블의 함수와 기본 제공 함수를 사용하는 토폴로지 |
| **:p** |각 주어또는 볼트에 대한 병렬 처리 힌트 |
| **:구성** |매개 변수를 구성할지 또는 기존 매개 변수를 업데이트할지 여부 |
| **:스키마** |스트림의 스키마 |

SCP.NET 자주 사용하는 매개 변수도 정의합니다.

| 매개 변수 | 설명 |
| --- | --- |
| "plugin.name" |C# 플러그인의 .exe 파일 이름 |
| "plugin.args" |플러그인 인수 |
| "output.schema" |출력 스키마 |
| "nontransactional.ack.enabled" |비트랜잭션 토폴로지에 대해 승인이 활성화되었는지 여부 |

**runSpec** 명령은 비트와 함께 배포됩니다. 명령 사용법은 다음과 같습니다.

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*리소스 dir* 매개 변수는 선택 사항입니다. C# 응용 프로그램을 연결하려는 경우 지정합니다. 지정된 디렉터리에는 응용 프로그램, 종속성 및 구성이 포함됩니다.

*classpath* 매개 변수도 선택 사항으로, 사양 파일에 Java 주어 또는 볼트가 포함되어 있는 경우 Java 클래스 경로를 지정합니다.

## <a name="miscellaneous-features"></a>기타 기능

### <a name="input-and-output-schema-declarations"></a>입력 및 출력 스키마 선언

C# 프로세스는 투플을 방출할 수 있습니다. 이렇게 하려면 플랫폼은 tuples를 **바이트[]** 개체로 직렬화하고 개체를 Java 측으로 전송합니다. 폭풍은 이 울을 대상에게 전달합니다.

다운스트림 구성 요소에서 C# 프로세스는 Java 측에서 다시 tuples를 수신하고 플랫폼의 원래 유형으로 변환합니다. 이러한 모든 작업은 플랫폼에 의해 숨김이 있습니다.

직렬화 및 역직렬화를 지원하려면 코드에서 입력 및 출력의 스키마를 선언해야 합니다. 스키마는 사전으로 정의됩니다. 스트림 ID는 사전 키입니다. 키 값은 열의 유형입니다. 구성 요소는 여러 스트림을 선언할 수 있습니다.

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

다음 함수가 **컨텍스트** 개체에 추가됩니다.

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

개발자는 내보낸 tuples가 스트림에 대해 정의된 스키마를 준수하는지 확인해야 합니다. 그렇지 않으면 시스템에서 런타임 예외를 throw합니다.

### <a name="multistream-support"></a>멀티스트림 지원

SCP를 사용하면 코드가 동시에 여러 개의 별개의 스트림을 내보내고 수신할 수 있습니다. **Context** 개체는 **내분** 기 메서드의 선택적 스트림 ID 매개 변수로 이 지원을 반영합니다.

SCP.NET **컨텍스트** 개체에 두 가지 메서드가 추가되었습니다. 특정 스트림에 하나 이상의 튜플을 방출합니다. *streamId* 매개 변수는 문자열입니다. 해당 값은 C# 코드와 토폴로지 정의 사양 모두에서 동일해야 합니다.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

존재하지 않는 스트림으로 내보내면 런타임 예외가 발생합니다.

### <a name="fields-grouping"></a>필드 그룹화

SCP.NET 스톰에 내장된 필드가 제대로 작동하지 않습니다. Java 프록시 측면에서 모든 필드의 데이터 형식은 실제로 **바이트[]입니다.** 필드 그룹화는 **byte[]** 개체의 해시 코드를 사용하여 그룹화를 수행합니다. 해시 코드는 RAM에서 이 개체의 주소입니다. 따라서 동일한 콘텐츠를 공유하지만 주소가 같지 않은 다중 바이트 개체의 경우 그룹화가 잘못됩니다.

SCP.NET 사용자 지정된 그룹화 메서드를 추가하고 **byte[]** 개체의 내용을 사용하여 그룹화를 수행합니다. 사양 파일에서 구문은 다음과 같습니다.

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

앞의 사양 파일에서:

* `scp-field-group`그룹화는 SCP에서 구현한 사용자 지정 필드 그룹이라고 지정합니다.
* `:tx`또는 `:non-tx` 토폴로지가 트랜잭션인지 여부를 지정합니다. 시작 인덱스는 트랜잭션 및 비트랜잭션 토폴로지 간에 다르기 때문에 이 정보가 필요합니다.
* `[0,1]`0으로 시작하는 필드 아이디의 해시 집합을 지정합니다.

### <a name="hybrid-topology"></a>하이브리드 토폴로지

네이티브 스톰 코드는 Java로 작성됩니다. SCP.NET 비즈니스 논리를 처리하기 위해 C# 코드를 작성할 수 있도록 Storm을 향상시켰습니다. 그러나 SCP.NET C # 주어 / 볼트뿐만 아니라 자바 주유 / 볼트를 포함하는 하이브리드 토폴로지도 지원합니다.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>사양 파일에 Java 주어장/볼트 지정

사양 파일에서 **scp-spout** 및 **scp-bolt를** 사용하여 Java 및 볼트를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

다음은 `microsoft.scp.example.HybridTopology.Generator` Java 주어칭 클래스의 이름입니다.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>runSpec 명령에서 Java 클래스경로 지정

Java 주전자 나 볼트가 포함 된 토폴로지 (topology)를 제출하려면 먼저 JAR 파일을 생성하기 위해 컴파일하십시오. 그런 다음 토폴로지제출 시 JAR 파일이 포함된 java 클래스경로를 지정합니다. 예를 들면 다음과 같습니다.

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

여기, `examples\HybridTopology\java\target\` 자바 주어 / 볼트 JAR 파일을 포함하는 폴더입니다.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java와 C 간의 직렬화 및 직렬화 #

SCP 구성 요소에는 Java 측과 C# 측이 포함됩니다. 네이티브 Java 주푸트/볼트와 상호 작용하려면 다음 그래프와 같이 Java 측과 C# 측 간에 직렬화 및 직렬화가 이루어져야 합니다.

![SCP 구성 요소로 보내는 Java 구성 요소다이어그램은 다른 Java 구성 요소로 전송됩니다.](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 측의 직렬화 및 C# 측의 직렬화

먼저 Java 측에서 직렬화및 C# 측의 직렬화에 대한 기본 구현을 제공합니다.

사양 파일에 Java 측의 직렬화 방법을 지정합니다.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

C# 코드에서 C# 측의 직렬화 메서드를 지정합니다.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

데이터 형식이 너무 복잡하지 않은 경우 이 기본 구현은 대부분의 경우를 처리해야 합니다. 다음은 사용자 고유의 구현을 연결할 수 있는 경우입니다.

* 데이터 형식이 기본 구현에 너무 복잡합니다.
* 기본 구현의 성능이 요구 사항을 충족하지 않습니다.

Java 측의 직렬화 인터페이스는 다음과 같이 정의됩니다.

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 측의 직렬화 인터페이스는 다음과 같이 정의됩니다.

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>C# 측의 직렬화 및 Java 측의 직렬화

C# 코드에서 C# 측의 직렬화 방법을 지정합니다.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

사양 파일에 Java 측의 직렬화 방법을 지정합니다.

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

`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` 여기서는 역직렬화기의 이름이며 `"microsoft.scp.example.HybridTopology.Person"` 데이터가 역직렬화되는 대상 클래스입니다.

C# 직렬화기와 Java 역직렬화기의 구현을 직접 연결할 수도 있습니다.

이 코드는 C# 직렬화기의 인터페이스입니다.

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

이 코드는 Java 디직직렬화기의 인터페이스입니다.

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 호스트 모드

이 모드에서는 코드를 DLL로 컴파일하고 SCP에서 제공한 대로 SCPHost.exe를 사용하여 토폴로지제출을 할 수 있습니다. 사양 파일은 다음과 같습니다.

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

여기서는 `"plugin.name"` SCP `"SCPHost.exe"`SDK에서 제공하는 로 지정됩니다. SCPHost.exe는 다음 순서로 세 가지 매개 변수를 허용합니다.

1. 이 예제에 있는 `"HelloWorld.dll"` DLL 이름입니다.
1. 이 예제에 있는 `"Scp.App.HelloWorld.Generator"` 클래스 이름입니다.
1. **ISCPPlugin의**인스턴스를 얻기 위해 호출 할 수있는 공용 정적 메서드의 이름입니다.

호스트 모드에서는 SCP 플랫폼에서 호출할 수 있는 DLL로 코드를 컴파일합니다. 플랫폼은 전체 처리 논리를 완전히 제어할 수 있으므로 SCP 호스트 모드에서 토폴로지(topology)를 제출하는 것이 좋습니다. 이렇게 하면 개발 환경이 간소화됩니다. 또한 이후 릴리스에서 더 많은 유연성과 이전 버전과의 호환성을 제공합니다.

## <a name="scp-programming-examples"></a>SCP 프로그래밍 예제

### <a name="helloworld"></a>HelloWorld

다음 간단한 HelloWorld 예제에서는 SCP.NET 맛을 보여줍니다. **그것은 생성기라는** 주어와 **스플리터** 및 **카운터라는**두 개의 볼트와 비 트랜잭션 토폴로지를 사용합니다. **생성기** 주푸는 무작위로 문장을 생성하고 **스플리터에**이러한 문장을 방출한다. **스플리터** 볼트는 문장을 단어로 분할하고 카운터 **볼트에** 이러한 단어를 내보전합니다. **카운터** 볼트는 사전을 사용하여 각 단어의 발생을 기록합니다.

이 예제에는 HelloWorld.spec과 HelloWorld\_EnableAck.spec의 두 가지 사양 파일이 있습니다. C# 코드는 Java 측에서 개체를 가져오는 `pluginConf` 것으로 승인이 활성화되어 있는지 여부를 확인할 수 있습니다.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

주어에서 승인을 사용하도록 설정하면 사전이 승인되지 않은 투플을 캐시합니다. 호출된 경우 `Fail` 실패한 튜플이 재생됩니다.

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

다음 HelloWorldTx 예제에서는 트랜잭션 토폴로지를 구현하는 방법을 보여 줍니다. 예제에는 **생성기라는**하나의 주어, **부분 카운트라고**하는 일괄 처리 볼트 및 **카운트 합계라는**커밋 볼트가 있습니다. 이 예제에는 데이터소스0.txt, DataSource1.txt 및 DataSource2.txt의 세 가지 기존 텍스트 파일도 있습니다.

각 트랜잭션에서 **생성기** 주전자는 기존 세 파일에서 두 개의 파일을 임의로 선택하고 두 파일 이름을 **부분 개수** 볼트로 내보합니다. **부분 개수** 볼트:

1. 수신된 튜플에서 파일 이름을 가져옵니다.
1. 해당 파일을 엽니다.
1. 파일의 단어 수를 계산합니다.
1. 카운트합계 볼트에 단어 **count-sum** 수를 내보전합니다.

**count-sum** Bolt는 총 단어 개수의 요약을 표시합니다.

정확히 한 번 의미 체계를 달성하려면 **카운트 합계** 커밋 볼트가 재생 된 트랜잭션인지 여부를 판단해야합니다. 이 예제에서는 다음과 같은 정적 멤버 변수가 있습니다.

```csharp
public static long lastCommittedTxId = -1; 
```

**ISCPBatchBolt** 인스턴스가 만들어지면 입력 매개 변수에서 `txAttempt` 개체의 값을 가져옵니다.

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

호출될 `FinishBatch` 때 `lastCommittedTxId` 재생된 트랜잭션이 아닌 경우 업데이트됩니다.

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

이 토폴로지에는 Java 주어와 C# 볼트가 포함되어 있습니다. SCP 플랫폼에서 제공하는 기본 직렬화 및 직렬화 구현을 사용합니다. 사양 파일 세부 정보는 예제\\하이브리드토폴로지 폴더의 HybridTopology.spec 파일을 참조하십시오. 또한 Java 클래스 경로를 지정하는 방법은 SubmitTopology.bat를 참조하십시오.

### <a name="scphostdemo"></a>SCPHostDemo

이 예제는 본질적으로 HelloWorld와 동일합니다. 유일한 차이점은 코드가 DLL로 컴파일되고 SCPHost.exe를 사용하여 토폴로지가 제출된다는 것입니다. 자세한 설명은 SCP 호스트 모드 섹션을 참조하십시오.

## <a name="next-steps"></a>다음 단계

SCP를 사용하여 만든 아파치 스톰 토폴로지의 예는 다음 문서를 참조하십시오.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight의 Apache Storm을 사용하여 Event Hubs의 차량 센서 데이터 처리](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure 이벤트 허브에서 아파치 HBase로 추출, 변환 및 로드(ETL)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
