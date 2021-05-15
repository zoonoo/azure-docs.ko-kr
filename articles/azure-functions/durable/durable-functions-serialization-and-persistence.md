---
title: Durable Functions의 데이터 지속성 및 serialization - Azure
description: Azure Functions의 Durable Functions 확장에서 데이터를 유지하는 방법을 알아보기
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 91c04b3943af5eee436bb4a18a946000b76cff3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057986"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Durable Functions의 데이터 지속성 및 serialization (Azure Functions)

Durable Functions은 안정적인 실행을 제공하기 위해 함수 매개 변수, 반환 값 및 기타 상태를 지속성 백 엔드에 자동으로 유지합니다. 그러나 지속성 스토리지에 유지되는 데이터의 양과 빈도는 애플리케이션 성능 및 스토리지 트랜잭션 비용에 영향을 줄 수 있습니다. 애플리케이션에 저장되는 데이터 형식에 따라, 데이터 보존 및 개인 정보 정책을 고려해야 할 수도 있습니다.

## <a name="azure-storage"></a>Azure Storage

기본값으로 Durable Functions는 사용자가 지정하는 [Azure Storage](https://azure.microsoft.com/services/storage/) 계정에서 큐, 테이블 및 BLOB에 데이터를 유지합니다.

### <a name="queues"></a>큐

Durable Functions는 Azure Storage 큐를 사용하여 모든 함수 실행을 안정적으로 예약합니다. 이러한 큐 메시지는 함수 입력 또는 출력을 포함합니다. 이는 메시지를 사용하여 실행을 예약하거나 호출하는 함수로 값을 반환하는지 여부에 따라 달라집니다. 이러한 큐 메시지에는 라우팅 및 엔드투엔드 상관 관계와 같이 내부용으로 Durable Functions이 사용하는 추가 메타데이터도 포함됩니다. 수신된 메시지에 대한 응답으로 함수가 실행을 완료하면, 해당 메시지가 삭제되고 실행 결과는 Azure Storage Table이나 Azure Storage Blob에도 유지될 수 있습니다.

단일 [작업 허브](durable-functions-task-hubs.md) 내에서, Durable Functions는 활동 함수 예약을 위한 *작업 항목* 큐 `<taskhub>-workitem`, 그리고 오케스트레이터 및 엔터티 함수를 예약하거나 재개하기 위한 하나 이상의 *제어 큐* `<taskhub>-control-##`에 대해 메시지를 생성하고 추가합니다. 제어 큐의 수는 애플리케이션에 구성된 파티션 수와 같습니다. 큐 및 파티션에 대한 자세한 내용은 [성능 및 확장성 설명서](durable-functions-perf-and-scale.md)를 참조하세요.

### <a name="tables"></a>테이블

오케스트레이션이 메시지를 성공적으로 처리하면 그 결과로 발생한 작업의 레코드가 *기록* 테이블 `<taskhub>History`에 유지됩니다. 또한 오케스트레이션 입력, 출력 및 사용자 지정 상태 데이터는 *인스턴스* 테이블 `<taskhub>Instances`에 유지됩니다.

### <a name="blobs"></a>Blob

대부분의 경우 Durable Functions은 Azure Storage Blob을 사용하여 데이터를 유지하지 않습니다. 그러나 큐 및 테이블의 [크기 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits)으로 인해 Durable Functions가 필요한 모든 데이터를 스토리지 행 또는 큐 메시지로 유지하지 못할 수 있습니다. 예를 들어, 큐에 유지해야 하는 데이터 조각이 serialize될 때 45KB보다 큰 경우, Durable Functions는 데이터를 압축하여 BLOB에 저장합니다. 이러한 방식으로 Blob Storage에 데이터를 유지하는 경우, Durable Function은 테이블 행 또는 큐 메시지에 해당 BLOB에 대한 참조를 저장합니다. Durable Functions에서 데이터를 검색해야 하는 경우, BLOB에서 자동으로 데이터를 가져옵니다. 이러한 BLOB은 BLOB 컨테이너 `<taskhub>-largemessages`에 저장됩니다.

> [!NOTE]
> 큰 메시지에 대한 추가 압축 및 BLOB 작업 단계는 CPU 및 I/O 대기 시간 비용 측면에서 비용이 많이 들 수 있습니다. 또한 Durable Functions는 지속형 데이터를 메모리에 로드할 필요가 있으며, 동시에 여러 다른 함수 실행에 대해서도 이 작업이 필요할 수 있습니다. 결과적으로 큰 데이터 페이로드가 지속되면 높은 메모리 사용이 발생할 수도 있습니다. 메모리 오버헤드를 최소화하려면 Blob Storage 등의 대량 데이터 페이로드를 수동으로 유지하는 것을 고려하고, 대신 이 데이터에 대한 참조를 전달합니다. 이렇게 하면 [오케스트레이터 함수가 재생](durable-functions-orchestrations.md#reliability)하는 동안 중복 로드를 방지하는 데 필요한 경우에만 코드에서 데이터를 로드할 수 있습니다. 그러나, 디스크에 페이로드를 저장하는 것은 권장하지 *않습니다*.이는 함수가 수명 동안 서로 다른 VM에서 실행될 수 있으므로 디스크 할당 상태가 사용 가능으로 보장되지 않기 때문입니다.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Serialize되고 유지되는 데이터 형식
다음은 Durable Functions 기능을 사용할 때 serialize되고 유지되는 다양한 유형의 데이터 목록입니다.

- 모든 ID 및 처리되지 않은 예외를 포함하여 오케스트레이터, 활동 및 엔터티 함수의 모든 입력 및 출력
- 오케스트레이터, 활동 및 엔터티 함수 이름
- 외부 이벤트 이름 및 페이로드
- 사용자 지정 오케스트레이션 상태 페이로드
- 오케스트레이션 종료 메시지
- 지속성 타이머 페이로드
- 지속성 HTTP 요청 및 응답 URL, 헤더 및 페이로드
- 엔터티 호출 및 신호 페이로드
- 엔터티 상태 페이로드

### <a name="working-with-sensitive-data"></a>중요한 데이터 사용 작업
Azure Storage를 사용하는 경우, 모든 데이터가 미사용 시 자동으로 암호화됩니다. 그러나 스토리지 계정에 대한 액세스 권한이 있는 사용자는 암호화되지 않은 형식으로 데이터를 읽을 수 있습니다. 중요한 데이터를 더 강력하게 보호해야 하는 경우, Durable Functions는 미리 암호화된 형식으로 데이터를 유지하도록 사용자 고유의 암호화 키를 사용하여 데이터를 암호화하는 것을 먼저 고려합니다.

또는 .NET 사용자에게 자동 암호화를 제공하는 사용자 지정 serialization 공급자를 구현할 수 있는 옵션이 있습니다. 암호화를 사용한 사용자 지정 serialization 예제는 [이 GitHub 샘플](https://github.com/charleszipp/azure-durable-entities-encryption)에서 찾을 수 있습니다.

> [!NOTE]
> 애플리케이션 수준 암호화를 구현하기로 결정한 경우에는 오케스트레이션과 엔터티가 무한한 시간 동안 존재할 수 있다는 것을 명심하십시오. 이는 암호화 키를 회전해야 하는 경우에 중요합니다. 오케스트레이션 또는 엔터티가 키 회전 정책보다 오래 실행 될 수 있기 때문입니다. 키 회전이 발생하는 경우, 데이터를 암호화하는 데 사용되는 키를 다음 번 오케스트레이션 또는 엔터티 실행 시 데이터의 암호를 해독하는 데 더 이상 사용할 수 없습니다. 따라서 고객 암호화는 오케스트레이션과 엔터티가 비교적 짧은 시간 동안 실행될 것으로 예상되는 경우에만 권장됩니다.

## <a name="customizing-serialization-and-deserialization"></a>serialization 및 deserialization의 사용자 지정

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>기본 Serialization 논리

Durable Functions는 내부적으로 [Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)을 사용하여 오케스트레이션 및 엔터티 데이터를 Json으로 serialize합니다. Json.NET에 사용되는 기본 설정 Durable Functions은 다음과 같습니다.

**입력, 출력 및 상태:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**예외:**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

자세한 내용은 `JsonSerializerSettings` [여기](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm)를 참조하세요.

## <a name="customizing-serialization-with-net-attributes"></a>.NET 특성을 사용하여 serialization 사용자 지정

데이터를 serialize할 때, Json.NET는 JSON에서 데이터를 serialize 및 deserialize하는 방법을 제어하는 클래스 및 속성에 대한 [다양한 특성](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm)을 찾습니다. Durable Functions API에 전달되는 데이터 형식에 대한 소스 코드를 소유하는 경우, 이러한 특성을 형식에 추가하여 serialization 및 deserialization을 사용자 지정하는 것이 좋습니다.

## <a name="customizing-serialization-with-dependency-injection"></a>종속성 주입을 사용하여 serialization 사용자 지정

.NET을 대상으로 하고 함수 V3 런타임에서 실행되는 함수 앱은 [DI(종속성 주입)](../functions-dotnet-dependency-injection.md)를 사용하여 데이터 및 예외를 serialize하는 방법을 사용자 지정할 수 있습니다. 아래 샘플 코드에서는 DI를 사용하여 `IMessageSerializerSettingsFactory` 및 `IErrorSerializerSettingsFactory` 서비스 인터페이스의 사용자 지정 구현을 사용하여 기본 Json.NET serialization 설정을 재정의하는 방법을 보여 줍니다.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Serialization 및 Deserialization 논리

Azure Functions Node 애플리케이션은 [serialization에는 `JSON.stringify()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify), [deserialization에는 `JSON.Parse()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)를 사용합니다. 대부분의 형식은 원활하게 serialize 및 deserialize되어야 합니다. 기본 논리가 충분하지 않은 경우에는 개체에 `toJSON()` 메서드를 정의하면 serialization 논리가 하이재킹됩니다. 그러나 개체 deserialization에 대해 아날로그는 없습니다.

Serialization/deserialization 파이프라인의 전체 사용자 지정의 경우, 사용자 고유의 코드로 serialization 및 deserialization을 처리하고 데이터를 문자열로 전달하는 것이 좋습니다.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Serialization 및 Deserialization 논리

형식 주석을 사용하여 Durable Functions가 데이터를 올바르게 serialize 및 deserialize하도록 하는 것이 최선의 방법입니다. 대부분의 기본 제공 형식이 자동으로 처리되지만, deserialization 중에 형식을 유지하려면 일부 기본 제공 데이터 형식에 형식 주석이 필요합니다.

사용자 지정 데이터 형식의 경우, 사용자 클래스에서 고정적인 `to_json` 및 `from_json` 메서드를 내보내 데이터 형식에 대해 JSON serialization 및 deserialization을 정의해야 합니다.

---
