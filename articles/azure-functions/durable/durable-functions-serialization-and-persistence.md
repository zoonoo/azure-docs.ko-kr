---
title: Durable Functions의 데이터 지 속성 및 직렬화-Azure
description: Azure Functions에 대 한 Durable Functions 확장이 데이터를 유지 하는 방법 알아보기
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: ea4aaa1cdbe10e2db9cf619452558d104a2293ab
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449376"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Durable Functions의 데이터 지 속성 및 serialization (Azure Functions)

Durable Functions은 안정적인 실행을 제공 하기 위해 함수 매개 변수, 반환 값 및 기타 상태를 영구 백 엔드에 자동으로 유지 합니다. 그러나 지 속성 저장소에 유지 되는 데이터의 양과 빈도는 응용 프로그램 성능 및 저장소 트랜잭션 비용에 영향을 줄 수 있습니다. 응용 프로그램에 저장 되는 데이터 형식에 따라 데이터 보존 및 개인 정보 취급 방침을 고려해 야 할 수도 있습니다.

## <a name="azure-storage"></a>Azure Storage

기본적으로 Durable Functions는 사용자가 지정 하는 [Azure Storage](https://azure.microsoft.com/services/storage/) 계정에서 큐, 테이블 및 blob에 데이터를 유지 합니다.

### <a name="queues"></a>큐

Durable Functions는 Azure Storage 큐를 사용 하 여 모든 함수 실행을 안정적으로 예약 합니다. 이러한 큐 메시지는 메시지를 사용 하 여 실행을 예약 하거나 호출 하는 함수로 값을 반환 하는지 여부에 따라 함수 입력 또는 출력을 포함 합니다. 이러한 큐 메시지에는 라우팅 및 종단 간 상관 관계와 같이 내부용으로 사용 Durable Functions 하는 추가 메타 데이터도 포함 됩니다. 수신 된 메시지에 대 한 응답으로 함수가 실행을 완료 하면 해당 메시지가 삭제 되 고 실행 결과는 Azure Storage 테이블이 나 Azure Storage Blob에도 유지 될 수 있습니다.

단일 [작업 허브](durable-functions-task-hubs.md)내에서 작업을 예약 하거나 다시 시작 하기 위해 이름이 지정 된 *작업 항목* 큐 `<taskhub>-workitem` 및 하나 이상의 *제어 큐* 에 대 한 메시지를 만들고 추가 Durable Functions `<taskhub>-control-##` 합니다. 제어 큐의 수는 응용 프로그램에 대해 구성 된 파티션 수와 같습니다. 큐 및 파티션에 대 한 자세한 내용은 [성능 및 확장성 설명서](durable-functions-perf-and-scale.md)를 참조 하세요.

### <a name="tables"></a>테이블

오케스트레이션이 메시지를 성공적으로 처리 하면 해당 결과 작업의 레코드가 이라는 *기록* 테이블에 유지 됩니다 `<taskhub>History` . 오케스트레이션 입력, 출력 및 사용자 지정 상태 데이터는 라는 *인스턴스* 테이블에도 유지 됩니다 `<taskhub>Instances` .

### <a name="blobs"></a>Blob

대부분의 경우 Durable Functions Azure Storage Blob을 사용 하 여 데이터를 유지 하지 않습니다. 그러나 큐 및 테이블의 [크기 제한은](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) Durable Functions 필요한 모든 데이터를 저장소 행 또는 큐 메시지로 유지 하지 못할 수 있습니다. 예를 들어 큐에 저장 해야 하는 데이터 조각이 serialize 될 때 45 KB 보다 큰 경우 Durable Functions는 데이터를 압축 하 여 blob에 저장 합니다. 이러한 방식으로 blob storage에 데이터를 유지 하는 경우 지 속성 함수는 테이블 행 또는 큐 메시지에 해당 blob에 대 한 참조를 저장 합니다. Durable Functions에서 데이터를 검색 해야 하는 경우 blob에서 자동으로 인출 합니다. 이러한 blob은 blob 컨테이너에 저장 됩니다 `<taskhub>-largemessages` .

> [!NOTE]
> 큰 메시지에 대 한 추가 압축 및 blob 작업 단계는 CPU 및 i/o 대기 시간 비용 측면에서 비용이 많이 들 수 있습니다. 또한 Durable Functions는 지속형 데이터를 메모리에 로드 해야 하며, 동시에 여러 다른 함수 실행에 대해이 작업을 수행할 수 있습니다. 결과적으로 큰 데이터 페이로드가 지속 될 경우에도 높은 메모리 사용이 발생할 수 있습니다. 메모리 오버 헤드를 최소화 하려면 blob storage 등의 대량 데이터 페이로드를 수동으로 유지 하 고 대신이 데이터에 대 한 참조를 전달 하는 것이 좋습니다. 이렇게 하면 [orchestrator 함수를 재생](durable-functions-orchestrations.md#reliability)하는 동안 중복 로드를 방지 하는 데 필요한 경우에만 코드에서 데이터를 로드할 수 있습니다. 그러나 디스크에 페이로드를 저장 하 *는 것은* 사용 하지 않는 것이 좋습니다 .이는 기능이 수명 동안 서로 다른 vm에서 실행 될 수 있기 때문입니다.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Serialize 되 고 유지 되는 데이터 형식
다음은 Durable Functions 기능을 사용할 때 serialize 되 고 유지 되는 다양 한 유형의 데이터 목록입니다.

- 모든 Id 및 처리 되지 않은 예외를 포함 하 여 orchestrator, activity 및 entity 함수의 모든 입력 및 출력
- Orchestrator, 활동 및 엔터티 함수 이름
- 외부 이벤트 이름 및 페이로드
- 사용자 지정 오케스트레이션 상태 페이로드
- 오케스트레이션 종료 메시지
- 지 속성 타이머 페이로드
- 지 속성 HTTP 요청 및 응답 Url, 헤더 및 페이로드
- 엔터티 호출 및 신호 페이로드
- 엔터티 상태 페이로드

### <a name="working-with-sensitive-data"></a>중요 한 데이터 작업
Azure Storage 사용 하는 경우 모든 데이터가 미사용 시 자동으로 암호화 됩니다. 그러나 저장소 계정에 대 한 액세스 권한이 있는 사용자는 암호화 되지 않은 형식으로 데이터를 읽을 수 있습니다. 중요 한 데이터를 더 강력 하 게 보호 해야 하는 경우 Durable Functions는 미리 암호화 된 형식으로 데이터를 유지 하도록 사용자 고유의 암호화 키를 사용 하 여 먼저 데이터를 암호화 하는 것이 좋습니다.

또는 .NET 사용자에 게 자동 암호화를 제공 하는 사용자 지정 serialization 공급자를 구현할 수 있는 옵션이 있습니다. 암호화를 사용한 사용자 지정 serialization 예제는 [이 GitHub 샘플](https://github.com/charleszipp/azure-durable-entities-encryption)에서 찾을 수 있습니다.

> [!NOTE]
> 응용 프로그램 수준 암호화를 구현 하기로 결정 한 경우에는 오케스트레이션과 엔터티가 무한 한 시간 동안 존재할 수 있습니다. 오케스트레이션 또는 엔터티가 키 회전 정책 보다 오래 실행 될 수 있기 때문에 암호화 키를 회전 해야 하는 경우에 중요 합니다. 키 회전이 발생 하는 경우 데이터를 암호화 하는 데 사용 되는 키를 다음에 오케스트레이션 또는 엔터티가 실행 될 때 암호를 해독 하는 데 더 이상 사용할 수 없습니다. 따라서 고객 암호화는 오케스트레이션과 엔터티가 비교적 짧은 시간 동안 실행 될 것으로 예상 되는 경우에만 권장 됩니다.

## <a name="customizing-serialization-and-deserialization"></a>Serialization 및 deserialization 사용자 지정

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>기본 직렬화 논리

Durable Functions 내부적으로 [Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) 을 사용 하 여 오케스트레이션 및 엔터티 데이터를 Json으로 직렬화 합니다. Json.NET에 사용 되는 기본 설정 Durable Functions은 다음과 같습니다.

**입력, 출력 및 상태:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**단**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

자세한 내용은 `JsonSerializerSettings` [여기](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm)를 참조 하세요.

## <a name="customizing-serialization-with-net-attributes"></a>.NET 특성을 사용 하 여 serialization 사용자 지정

데이터를 serialize 할 때 Json.NET는 JSON에서 데이터를 serialize 및 deserialize 하는 방법을 제어 하는 클래스 및 속성에 대 한 [다양 한 특성](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) 을 찾습니다. Durable Functions Api에 전달 되는 데이터 형식에 대 한 소스 코드를 소유 하는 경우 이러한 특성을 형식에 추가 하 여 serialization 및 deserialization을 사용자 지정 하는 것이 좋습니다.

## <a name="customizing-serialization-with-dependency-injection"></a>종속성 주입을 사용 하 여 serialization 사용자 지정

.NET을 대상으로 하 고 함수 V3 런타임에서 실행 되는 함수 앱은 [DI (종속성 주입)](../functions-dotnet-dependency-injection.md) 를 사용 하 여 데이터 및 예외를 serialize 하는 방법을 사용자 지정할 수 있습니다. 아래 샘플 코드에서는 DI를 사용 하 여 `IMessageSerializerSettingsFactory` 및 서비스 인터페이스의 사용자 지정 구현을 사용 하 여 기본 Json.NET serialization 설정을 재정의 하는 방법을 보여 줍니다 `IErrorSerializerSettingsFactory` .

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

### <a name="serialization-and-deserialization-logic"></a>직렬화 및 deserialization 논리

Azure Functions 노드 응용 프로그램은 serialization 및 [ `JSON.Parse()` deserialization](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)을 [ `JSON.stringify()` 위해](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 를 사용 합니다. 대부분의 형식은 원활 하 게 직렬화 및 deserialize 되어야 합니다. 기본 논리가 충분 하지 않은 경우에는 `toJSON()` 개체에 대 한 메서드를 정의 하면 serialization 논리가 하이재킹 됩니다. 그러나 개체 deserialization에 대해 아날로그가 없습니다.

Serialization/deserialization 파이프라인의 전체 사용자 지정을 위해 사용자 고유의 코드로 serialization 및 deserialization을 처리 하 고 데이터를 문자열로 전달 하는 것이 좋습니다.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>직렬화 및 deserialization 논리

형식 주석을 사용 하 여 데이터를 올바르게 serialize 및 deserialize Durable Functions 확인 하는 것이 좋습니다. 대부분의 기본 제공 형식이 자동으로 처리 되지만, deserialization 중에 형식을 유지 하려면 일부 기본 제공 데이터 형식에 형식 주석이 필요 합니다.

사용자 지정 데이터 형식의 경우 `to_json` 클래스에서 정적 및 메서드를 내보내 데이터 형식의 JSON serialization 및 deserialization을 정의 해야 합니다 `from_json` .

---
