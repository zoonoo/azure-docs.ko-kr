---
title: .NET에 대한 Azure Cosmos DB 성능 팁
description: Azure Cosmos DB 성능을 개선하기 위한 클라이언트 구성 옵션에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: 68a9a7d5f90831bb2e0c9284cb71ae4b92edffad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131415"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB 및 .NET에 대한 성능 팁

> [!div class="op_single_selector"]
> * [비동기 Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB는 보장된 대기 시간 및 처리량으로 매끄럽게 크기가 조정되는 빠르고 유연한 분산 데이터베이스입니다. Azure Cosmos DB를 사용하여 데이터베이스를 확장하기 위해 주요 아키텍처를 변경하거나 복잡한 코드를 작성할 필요가 없습니다. 규모를 확장 및 축소하는 방법이 API 호출을 하나 만드는 것만큼 쉽습니다. 자세한 내용은 [컨테이너 처리량을 프로비전하는 방법](how-to-provision-container-throughput.md) 또는 [데이터베이스 처리량을 프로비전하는 방법](how-to-provision-database-throughput.md)을 참조하세요. 그러나 Azure Cosmos DB는 네트워크 호출을 통해 액세스되므로 [SQL .NET SDK를](sql-api-sdk-dotnet-standard.md)사용할 때 최대 성능을 달성하기 위해 클라이언트 측 최적화를 수행할 수 있습니다.

따라서 데이터베이스 성능을 향상시키려는 경우 다음 옵션을 고려하십시오.

## <a name="hosting-recommendations"></a>호스팅 권장 사항

**쿼리 집약적인 워크로드의 경우 Linux 또는 Windows 32비트 호스트 처리 대신 Windows 64비트를 사용합니다.**

성능 향상을 위해 Windows 64비트 호스트 처리를 권장합니다. SQL SDK에는 로컬에서 쿼리를 구문 분석하고 최적화하는 네이티브 ServiceInterop.dll이 포함되어 있습니다. ServiceInterop.dll은 Windows x64 플랫폼에서만 지원됩니다. ServiceInterop.dll을 사용할 수 없는 Linux 및 기타 지원되지 않는 플랫폼의 경우 게이트웨이에 추가 네트워크 호출을 통해 최적화된 쿼리를 가져옵니다. 다음 유형의 응용 프로그램은 기본적으로 32비트 호스트 처리를 사용합니다. 호스트 처리를 64비트 처리로 변경하려면 응용 프로그램 유형에 따라 다음 단계를 따르십시오.

- 실행 가능한 응용 프로그램의 경우 **빌드** 탭에서 **프로젝트 속성** 창에서 [플랫폼 대상을](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) **x64로** 설정하여 호스트 처리를 변경할 수 있습니다.

- VSTest 기반 테스트 프로젝트의 경우 Visual Studio **테스트** 메뉴에서 **테스트** > **테스트 설정** > **기본 프로세서 아키텍처를 X64로** 선택하여 호스트 처리를 변경할 수 있습니다.

- 로컬로 배포된 ASP.NET 웹 응용 프로그램의 경우 **도구** > **옵션** > **프로젝트 및 솔루션** > 웹 프로젝트에서 **웹 사이트 및 프로젝트에 64비트 버전의 IIS Express 사용을** 선택하여 호스트 처리를 변경할 수**있습니다.**

- Azure에 배포된 ASP.NET 웹 응용 프로그램의 경우 Azure 포털의 응용 **프로그램 설정에서** **64비트** 플랫폼을 선택하여 호스트 처리를 변경할 수 있습니다.

> [!NOTE] 
> 기본적으로 새 Visual Studio 프로젝트는 **모든 CPU로**설정됩니다. 프로젝트를 **x64로** 설정하여 **x86으로**전환하지 않는 것이 좋습니다. **모든 CPU로** 설정된 프로젝트는 **x86** 전용 종속성을 추가하면 x86으로 쉽게 전환할 수 있습니다.<br/>
> ServiceInterop.dll은 SDK DLL이 실행중인 폴더에 있어야 합니다. 이는 DLL을 수동으로 복사하거나 사용자 지정 빌드/배포 시스템이 있는 경우에만 문제가 됩니다.
    
**서버 측 가비지 수집(GC)을 켭니다.**

가비지 수집 빈도를 줄이면 경우에 따라 도움이 될 수 있습니다. .NET에서 [gcServer를](https://msdn.microsoft.com/library/ms229357.aspx) `true`로 설정합니다.

**클라이언트 워크로드 확장**

처리량이 높은 수준(50,000RU/s 이상)으로 테스트하는 경우 CPU 또는 네트워크 사용률이 제한되는 컴퓨터로 인해 클라이언트 응용 프로그램이 병목 현상이 발생할 수 있습니다. 이 시점에 도달하면 여러 서버에 걸쳐 클라이언트 애플리케이션을 확장하여 Azure Cosmos DB 계정을 계속 추가할 수 있습니다.

> [!NOTE] 
> CPU 사용량이 높으면 대기 시간이 증가하고 시간 시간 시간 예외가 발생할 수 있습니다.

## <a name="networking"></a>네트워킹
<a id="direct-connection"></a>

**연결 정책: 직접 연결 모드 사용**

클라이언트가 Azure Cosmos DB에 연결하는 방식은 특히 관찰된 클라이언트 측 대기 시간에 중요한 성능 영향을 미칩니다. 클라이언트 연결 정책을 구성하는 데 사용할 수 있는 두 가지 주요 구성 설정은 연결 *모드와* 연결 *프로토콜입니다.*  두 가지 사용 가능한 모드는 같습니다.

   * 게이트웨이 모드
      
     게이트웨이 모드는 모든 SDK 플랫폼에서 지원되며 [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md)에 대해 구성된 기본값입니다. 응용 프로그램이 엄격한 방화벽 제한으로 회사 네트워크 내에서 실행되는 경우 게이트웨이 모드는 표준 HTTPS 포트와 단일 끝점을 사용하기 때문에 최선의 선택입니다. 그러나 게이트웨이 모드에는 Azure Cosmos DB에서 데이터를 읽거나 쓸 때마다 추가 네트워크 홉이 포함됩니다. 따라서 직접 모드는 네트워크 홉수가 적기 때문에 더 나은 성능을 제공합니다. 또한 소켓 연결 수가 제한된 환경에서 응용 프로그램을 실행할 때 게이트웨이 연결 모드를 권장합니다.

     Azure 함수에서 SDK를 사용할 때, 특히 [소비 계획에서](../azure-functions/functions-scale.md#consumption-plan) [연결에 대한](../azure-functions/manage-connections.md)현재 제한을 알고 있어야 합니다. 이 경우 Azure Functions 응용 프로그램 내에서 다른 HTTP 기반 클라이언트로 작업하는 경우 게이트웨이 모드가 더 좋을 수 있습니다.

   * 직접 모드

     직접 모드는 TCP 프로토콜을 통한 연결을 지원하며 [Microsoft.Azure.Cosmos/.NET V3 SDK를](sql-api-sdk-dotnet-standard.md)사용하는 경우 기본 연결 모드입니다.

게이트웨이 모드에서 Azure Cosmos DB는 MongoDB에 Azure Cosmos DB API를 사용할 때 포트 443 및 포트 10250, 10255 및 10256을 사용합니다. 포트 10250은 지역 복제없이 기본 MongoDB 인스턴스에 매핑됩니다. 포트 10255 및 10256 지역 복제가 있는 MongoDB 인스턴스에 매핑됩니다.
     
직접 모드에서 TCP를 사용하는 경우 게이트웨이 포트 외에도 Azure Cosmos DB가 동적 TCP 포트를 사용하기 때문에 10000에서 20000 사이의 포트 범위가 열려 있는지 확인해야 합니다. 이러한 포트가 열려 있지 않고 TCP를 사용하려고 하면 503 서비스 사용할 수 없는 오류가 나타납니다. 이 표에서는 다양한 API에 사용할 수 있는 연결 모드와 각 API에 사용되는 서비스 포트를 보여 주며 있습니다.

|연결 모드  |지원되는 프로토콜  |지원되는 SDK  |API/서비스 포트  |
|---------|---------|---------|---------|
|게이트웨이  |   HTTPS    |  모든 SDK    |   SQL (443), 몽고DB (10250, 10255, 10256), 표 (443), 카산드라 (10350), 그래프 (443)    |
|직접    |     TCP    |  .NET SDK    | 10000 ~20000 범위의 포트 |

Azure Cosmos DB는 HTTPS를 통해 간단하고 개방적인 RESTful 프로그래밍 모델을 제공합니다. 또한 통신 모델이 RESTful이며 .NET 클라이언트 SDK를 통해 사용할 수 있는 효율적인 TCP 프로토콜도 제공합니다. TCP 프로토콜은 초기 인증 및 트래픽 암호화에 SSL을 사용합니다. 최상의 성능을 위해 가능한 경우 TCP 프로토콜을 사용 합니다.

SDK V3의 경우 에서 `CosmosClient` `CosmosClientOptions`인스턴스를 만들 때 연결 모드를 구성합니다. 직접 모드가 기본값임을 기억하십시오.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Microsoft.Azure.DocumentDB SDK의 경우 매개 변수를 사용하여 `DocumentClient` 인스턴스를 구성하는 `ConnectionPolicy` 동안 연결 모드를 구성합니다. 직접 모드를 사용하는 경우 매개 변수를 `Protocol` `ConnectionPolicy` 사용하여 설정할 수도 있습니다.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

TCP는 직접 모드에서만 지원되므로 게이트웨이 모드를 사용하는 경우 HTTPS 프로토콜은 항상 게이트웨이와 `Protocol` 통신하는 데 사용되며 의 `ConnectionPolicy` 값은 무시됩니다.

![Azure 코스모스 DB 연결 정책](./media/performance-tips/connection-policy.png)

**첫 번째 요청 시 시작 대기 시간을 피하기 위해 OpenAsync 호출**

기본적으로 첫 번째 요청은 주소 라우팅 테이블을 가져와야 하기 때문에 대기 시간이 더 높습니다. [SDK V2를](sql-api-sdk-dotnet.md)사용하는 `OpenAsync()` 경우 초기화 중에 한 번 호출하여 첫 번째 요청에서 이 시작 대기 시간을 방지합니다.

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`계정의 모든 컨테이너에 대한 주소 라우팅 테이블을 가져오는 요청을 생성합니다. 컨테이너가 많지만 응용 프로그램이 하위 `OpenAsync` 집합에 액세스하는 계정의 경우 불필요한 양의 트래픽이 생성되어 초기화가 느려집니다. 따라서 `OpenAsync` 응용 프로그램 시작을 느리게 하기 때문에 이 시나리오에서는 사용 이 유용하지 않을 수 있습니다.

   <a id="same-region"></a>
**성능을 위해 동일한 Azure 리전에서 클라이언트를 대조배치합니다.**

가능하면 Azure Cosmos DB 데이터베이스와 동일한 지역에 Azure Cosmos DB를 호출하는 모든 응용 프로그램을 배치합니다. 대략적인 비교는 다음과 같습니다: 동일한 지역 내의 Azure Cosmos DB에 대한 호출은 1ms에서 2ms 이내로 완료되지만 미국 서부와 동부 해안 사이의 대기 시간은 50ms 이상입니다. 이 대기 시간은 클라이언트에서 Azure 데이터 센터 경계로 전달될 때 요청이 가져온 경로에 따라 요청마다 다를 수 있습니다. 호출 응용 프로그램이 프로비저닝된 Azure Cosmos DB 끝점과 동일한 Azure 지역 내에 위치하도록 하여 가능한 가장 낮은 대기 시간을 얻을 수 있습니다. 사용 가능한 지역 목록은 [Azure 지역을](https://azure.microsoft.com/regions/#services)참조하십시오.

![Azure 코스모스 DB](./media/performance-tips/same-region.png) 연결 정책<a id="increase-threads"></a>

**스레드/작업 수 늘리기**

Azure Cosmos DB에 대한 호출은 네트워크를 통해 이루어지므로 클라이언트 응용 프로그램이 요청 간에 대기하는 데 최소한의 시간을 소비하도록 요청의 병렬 처리 정도를 변경해야 할 수 있습니다. 예를 들어 .NET 작업 병렬 [라이브러리를](https://msdn.microsoft.com//library/dd460717.aspx)사용하는 경우 Azure Cosmos DB에서 읽거나 쓰는 수백 개의 작업의 순서로 만듭니다.

**가속화된 네트워킹 지원**
 
 대기 시간 및 CPU 지터를 줄이려면 클라이언트 가상 컴퓨터에서 빠른 네트워킹을 사용하도록 설정하는 것이 좋습니다. 참조 [빠른 네트워킹을 사용 하 고 Windows 가상 컴퓨터 만들기](../virtual-network/create-vm-accelerated-networking-powershell.md) 또는 [가속된 네트워킹으로 리눅스 가상 컴퓨터 만들기.](../virtual-network/create-vm-accelerated-networking-cli.md)

## <a name="sdk-usage"></a>SDK 사용
**최신 SDK 설치**

Azure Cosmos DB SDK는 최상의 성능을 제공하기 위해 지속적으로 향상됩니다. [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 페이지를 참조하여 최신 SDK를 확인하고 향상된 기능을 검토하세요.

**스트림 API 사용**

[.NET SDK V3에는](sql-api-sdk-dotnet-standard.md) 직렬화 없이 데이터를 수신하고 반환할 수 있는 스트림 API가 포함되어 있습니다. 

SDK에서 직접 응답을 사용하지 않고 다른 응용 프로그램 계층에 릴레이하는 중간 계층 응용 프로그램은 스트림 API의 이점을 활용할 수 있습니다. 스트림 처리의 예는 [항목 관리](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) 샘플을 참조하십시오.

**응용 프로그램의 수명 동안 단일 Azure Cosmos DB 클라이언트 사용**

각 `DocumentClient` `CosmosClient` 인스턴스는 스레드에서 안전하며 직접 모드에서 작동할 때 효율적인 연결 관리 및 주소 캐싱을 수행합니다. 효율적인 연결 관리와 더 나은 SDK 클라이언트 성능을 허용하려면 `AppDomain` 응용 프로그램의 수명 동안 단일 인스턴스를 사용하는 것이 좋습니다.

   <a id="max-connection"></a>

**게이트웨이 모드를 사용하는 경우 호스트당 System.Net MaxConnections 증가**

Azure Cosmos DB 요청은 게이트웨이 모드를 사용할 때 HTTPS/REST를 통해 수행됩니다. 호스트 이름 또는 IP 주소당 기본 연결 제한이 적용됩니다. 클라이언트 라이브러리가 `MaxConnections` Azure Cosmos DB에 대한 여러 동시 연결을 사용할 수 있도록 더 높은 값(100~1,000)으로 설정해야 할 수 있습니다. .NET SDK 1.8.0 이상에서 [ServicePointManager.DefaultConnectionLimit의](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 기본값은 50입니다. 값을 변경하려면 [Documents.Client.ConnectionPolicy.MaxConnectionLimit를](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 더 높은 값으로 설정할 수 있습니다.

**분할된 컬렉션에 대한 병렬 쿼리 조정**

SQL .NET SDK 1.9.0 이상에서는 병렬 쿼리를 지원하므로 분할된 컬렉션을 병렬로 쿼리할 수 있습니다. 자세한 내용은 SDK 사용과 관련된 [코드 샘플](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)을 참조하세요. 병렬 쿼리는 직렬 쿼리보다 더 나은 쿼리 대기 시간 및 처리량을 제공하도록 설계되었습니다. 병렬 쿼리는 요구 사항에 맞게 조정할 수 있는 두 가지 매개 변수를 제공합니다. 
- `MaxDegreeOfParallelism`을 사용하면 병렬로 쿼리할 수 있는 최대 파티션 수를 제어합니다. 
- `MaxBufferedItemCount`은 미리 가져온 결과 수를 제어합니다.

***병렬 처리 정도***

병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동합니다. 그러나 개별 파티션의 데이터는 쿼리와 관련하여 직렬로 가져옵니다. 다른 `MaxDegreeOfParallelism` 모든 시스템 조건이 `MaxConcurrency` 동일하게 유지되는 경우 [SDK V2](sql-api-sdk-dotnet.md) 또는 [SDK V3에서](sql-api-sdk-dotnet-standard.md) 파티션 수를 설정하면 가장 성능이 뛰어난 쿼리를 얻을 가능성이 가장 높습니다. 파티션 수를 모르는 경우 병렬 처리 정도를 높은 숫자로 설정할 수 있습니다. 시스템은 최소(파티션 수, 사용자가 제공한 입력)를 병렬 처리 의 정도로 선택합니다.

병렬 쿼리는 쿼리와 관련하여 데이터가 모든 파티션에 고르게 분포되어 있는 경우 가장 큰 이점을 얻을 수 있습니다. 쿼리에서 반환되는 데이터의 전부 또는 대부분이 몇 개의 파티션에 집중되도록 분할된 컬렉션이 분할된 경우(하나의 파티션은 최악의 경우) 해당 파티션은 쿼리 성능을 병목 현상처리합니다.

***최대 버퍼드항목 카운트 튜닝***
    
병렬 쿼리는 현재 결과 일괄 처리가 클라이언트에서 처리되는 동안 결과를 미리 가져오도록 설계되었습니다. 이 사전 페칭은 쿼리의 전체 대기 시간을 개선하는 데 도움이 됩니다. 매개 `MaxBufferedItemCount` 변수는 미리 가져온 결과의 수를 제한합니다. 쿼리가 사전 페칭을 통해 최대 이점을 받을 수 있도록 반환된 결과(또는 더 높은 수)로 설정합니다. `MaxBufferedItemCount`

사전 페칭은 병렬 처리 정도에 관계없이 동일한 방식으로 작동하며 모든 파티션의 데이터에 대한 단일 버퍼가 있습니다.  

**RetryAfter 간격으로 백오프 구현**

성능 테스트 중에 작은 요청 비율이 제한될 때까지 부하를 늘려야 합니다. 요청이 제한되면 클라이언트 응용 프로그램은 서버에서 지정한 재시도 간격에 대해 스로틀에서 다시 꺼야 합니다. 백오프를 고려하면 재시도 사이에 최소한의 시간을 할애할 수 있습니다. 

다시 시도 정책 지원은 다음 SDK에 포함됩니다.
- 버전 1.8.0 이상 [SQL용 .NET SDK](sql-api-sdk-dotnet.md) 및 [SQL용 Java SDK](sql-api-sdk-java.md)
- 버전 1.9.0 이상 [SQL용 Node.js SDK](sql-api-sdk-node.md) 및 [SQL용 파이썬 SDK](sql-api-sdk-python.md)
- [.NET 코어](sql-api-sdk-dotnet-core.md) SDK의 모든 지원되는 버전 

자세한 내용은 [다시 시도 후](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)를 참조하십시오.
    
.NET SDK 버전 1.19 이상에서는 다음 샘플과 같이 추가 진단 정보를 로깅하고 대기 시간 문제를 해결하는 메커니즘이 있습니다. 읽기 대기 시간이 더 긴 요청에 대한 진단 문자열을 기록할 수 있습니다. 캡처된 진단 문자열은 지정된 요청에 대해 429개의 오류를 받은 횟수를 이해하는 데 도움이 됩니다.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**짧은 읽기 대기 시간 동안 문서 URI 캐시**

최상의 문서 읽기 성능이 필요할 때마다 문서 URI를 캐시합니다. 리소스를 만들 때 리소스 ID를 캐시하는 논리를 정의해야 합니다. 리소스 ID를 기반으로 하는 조회는 이름 기반 조회보다 빠르므로 이러한 값을 캐싱하면 성능이 향상됩니다.

   <a id="tune-page-size"></a>
**더 나은 성능을 위해 쿼리/읽기 피드에 대한 페이지 크기 조정**

읽기 피드 기능(예: `ReadDocumentFeedAsync`SQL 쿼리를 발급하는 경우)을 사용하여 대량으로 문서를 읽는 경우 결과 집합이 너무 큰 경우 결과가 분할된 방식으로 반환됩니다. 기본적으로, 100개의 항목 또는 1MB 단위(둘 중 먼저 도달하는 단위)로 결과가 반환됩니다.

적용 가능한 모든 결과를 검색하는 데 필요한 네트워크 왕복 횟수를 줄이려면 [x-ms-max-item-count를](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 사용하여 최대 1,000개의 헤더를 요청하여 페이지 크기를 늘릴 수 있습니다. 예를 들어 사용자 인터페이스 또는 응용 프로그램 API가 한 번에 10개의 결과만 반환하는 경우와 같이 몇 가지 결과만 표시해야 하는 경우 페이지 크기를 10으로 줄여 읽기 및 쿼리에 사용되는 처리량을 줄일 수도 있습니다.

> [!NOTE] 
> 이 `maxItemCount` 속성은 페이지 만 을 사용해서는 안됩니다. 주요 용도는 단일 페이지에서 반환되는 최대 항목 수를 줄여 쿼리 성능을 향상시키는 것입니다.  

사용 가능한 Azure Cosmos DB SDK를 사용하여 페이지 크기를 설정할 수도 있습니다. [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) 속성을 `FeedOptions` 사용하면 열거 작업에서 반환할 최대 항목 수를 설정할 수 있습니다. -1로 `maxItemCount` 설정하면 SDK는 문서 크기에 따라 자동으로 최적의 값을 찾습니다. 예를 들어:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
쿼리가 실행되면 결과 데이터가 TCP 패킷 내에서 전송됩니다. 값을 `maxItemCount`너무 낮게 지정하면 TCP 패킷 내에서 데이터를 전송하는 데 필요한 트립 수가 많으면 성능에 영향을 줍니다. 따라서 `maxItemCount` 속성에 대해 설정할 값을 잘 모르는 경우 속성을 -1로 설정하고 SDK에서 기본값을 선택하도록 하는 것이 가장 좋습니다.

**스레드/작업 수 늘리기**

이 문서의 네트워킹 섹션에서 [스레드/작업 수 증가를](#increase-threads) 참조하십시오.

## <a name="indexing-policy"></a>인덱싱 정책
 
**더 빠른 쓰기를 위해 인덱싱에서 사용되지 않는 경로를 제외**

또한 Azure Cosmos DB 인덱싱 정책을 사용하면 인덱싱 경로(인덱싱 정책.포함 경로 및 인덱싱정책.ExcludedPaths)를 사용하여 인덱싱에 포함하거나 제외할 문서 경로를 지정할 수 있습니다. 인덱싱 경로는 쓰기 성능을 향상시키고 쿼리 패턴이 미리 알려진 시나리오에 대한 인덱스 저장소를 줄일 수 있습니다. 이는 인덱싱 비용이 인덱싱된 고유 경로 수와 직접 관련이 있기 때문입니다. 예를 들어 이 코드는 "*" 와일드카드를 사용하여 문서(하위 트리)의 전체 섹션을 인덱싱에서 제외하는 방법을 보여 주며 있습니다.

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

자세한 내용은 [Azure Cosmos DB 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="throughput"></a>처리량
<a id="measure-rus"></a>

**낮은 요청 단위/초 사용량에 대한 측정 및 조정**

Azure Cosmos DB는 풍부한 데이터베이스 작업 집합을 제공합니다. 이러한 작업에는 데이터베이스 컬렉션 내의 문서에서 작동하는 모든 UdF, 저장 프로시저 및 트리거가 포함된 관계형 및 계층 적 쿼리가 포함됩니다. 이러한 각 작업과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스에 대해 생각하고 관리하는 대신 RU(요청 단위)를 다양한 데이터베이스 작업을 수행하고 응용 프로그램 요청을 서비스하는 데 필요한 리소스에 대한 단일 측정값으로 생각할 수 있습니다.

처리량은 각 컨테이너에 대해 설정된 [요청 단위](request-units.md) 수에 따라 프로비전됩니다. 요청 단위 소비는 초당 비율로 평가됩니다. 컨테이너에 대한 프로비저닝된 요청 단위 비율을 초과하는 응용 프로그램은 컨테이너에 대한 프로비저닝된 수준 이하로 요금이 떨어질 때까지 제한됩니다. 응용 프로그램에 더 높은 수준의 처리량이 필요한 경우 추가 요청 단위를 프로비전하여 처리량을 늘릴 수 있습니다.

쿼리의 복잡성은 작업에 사용되는 요청 단위 수에 영향을 줍니다. 조건자의 수, 술어의 특성, UdF 수 및 원본 데이터 집합의 크기는 모두 쿼리 작업 비용에 영향을 미칩니다.

모든 작업(생성, 업데이트 또는 삭제)의 오버헤드를 측정하려면 [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 헤더(또는 .NET SDK `RequestCharge` `ResourceResponse\<T>` 또는 `FeedResponse\<T>` .NET SDK의 해당 속성)를 검사하여 작업에서 사용되는 요청 단위 수를 측정합니다.

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

이 헤더에 반환되는 요청 요금은 프로비저닝된 처리량(즉, 2,000R/초)의 일부입니다. 예를 들어 앞의 쿼리에서 1,000개의 1KB 문서를 반환하는 경우 작업 비용은 1,000입니다. 따라서 서버는 1초 이내에 이후 요청을 제한하기 전에 이러한 요청을 두 번만 준수합니다. 자세한 내용은 [요청 단위](request-units.md) 및 [요청 단위 계산기를](https://www.documentdb.com/capacityplanner)참조하십시오.
<a id="429"></a>

**너무 큰 속도 제한/요청 속도 처리**

클라이언트가 계정의 예약된 처리량을 초과하려고 하면 서버의 성능 저하가 없고 예약된 수준을 초과하는 처리량 용량을 사용할 수 없습니다. 서버는 RequestRateTooLarge(HTTP 상태 코드 429)로 요청을 선제적으로 종료합니다. 사용자가 요청을 다시 시도하기 전에 기다려야 하는 시간(밀리초)을 나타내는 [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 헤더를 반환합니다.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

SDK는 이 응답을 암시적으로 모두 catch하고, server-specified retry-after 헤더를 준수하고 요청을 다시 시도합니다. 동시에 여러 클라이언트가 계정에 액세스하지만 않으면 다음 재시도가 성공할 것입니다.

두 개 이상의 클라이언트가 요청 속도보다 일관되게 일관되게 작동하는 경우 클라이언트가 내부적으로 설정한 기본 재시도 횟수는 충분하지 않을 수 있습니다. 이 경우 클라이언트는 상태 코드 429를 사용하여 DocumentClientException을 응용 프로그램에 throw합니다. 

`ConnectionPolicy` 인스턴스를 설정하여 `RetryOptions` 기본 재시도 수를 변경할 수 있습니다. 기본적으로, 요청이 계속 요청 속도 이상으로 작동하는 경우 상태 코드가 429인 DocumentClientException은 누적 대기 시간 30초 후에 반환됩니다. 이 오류는 현재 재시도 수가 최대 재시도 개수보다 작은 경우에도 현재 값이 기본값인 9또는 사용자 정의 값인지 여부에 관계없이 반환됩니다.

자동화된 재시도 동작은 대부분의 응용 프로그램의 복원력과 유용성을 개선하는 데 도움이 됩니다. 그러나 성능 벤치마크를 수행할 때 특히 대기 시간을 측정할 때 최상의 동작이 아닐 수 있습니다. 실험이 서버 제한에 도달하고 클라이언트 SDK를 자동으로 재시도하는 경우 클라이언트 관찰 대기 시간이 급증합니다. 성능 실험 중 대기 시간 급증을 방지하려면, 각 작업에 의해 반환된 비용을 측정하고 요청이 예약된 요청 속도 이하로 작동하고 있는지 확인합니다. 자세한 내용은 [단위 요청](request-units.md)을 참조하십시오.

**더 높은 처리량을 위해 더 작은 문서를 위한 설계**

지정된 작업의 요청 요금(즉, 요청 처리 비용)은 문서 크기와 직접 관련이 있습니다. 대규모 문서에 대한 작업은 작은 문서에 대한 작업보다 비용이 더 많이 듭니다.

## <a name="next-steps"></a>다음 단계
일부 클라이언트 컴퓨터에서 고성능 시나리오에 대해 Azure Cosmos DB를 평가하는 데 사용되는 샘플 응용 프로그램의 경우 [Azure Cosmos DB를 사용한 성능 및 확장 테스트를](performance-testing.md)참조하십시오.

확장성 및 고성능을 위한 애플리케이션 설계 방법에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](partition-data.md)을 참조하세요.
