---
title: 내구성있는 기능을 위한 제로 가동 중지 시간 배포
description: 가동 중지 시간 제로 배포를 위해 내구성 있는 함수 오케스트레이션을 활성화하는 방법에 대해 알아봅니다.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231266"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>내구성있는 기능을 위한 제로 가동 중지 시간 배포

내구성 함수의 [신뢰할 수 있는 실행 모델은](durable-functions-checkpointing-and-replay.md) 오케스트레이션을 결정적으로 수행해야 하므로 업데이트를 배포할 때 고려해야 할 추가 적인 문제가 발생합니다. 배포에 활동 함수 서명 또는 오케스트레이터 논리에 대한 변경 내용이 포함된 경우 기내 오케스트레이션 인스턴스가 실패합니다. 이 상황은 특히 작업 시간 또는 일수를 나타낼 수 있는 장기 실행 오케스트레이션의 인스턴스에서 문제가 됩니다.

이러한 오류가 발생하지 않도록 하려면 다음 두 가지 옵션이 있습니다. 
- 실행 중인 모든 오케스트레이션 인스턴스가 완료될 때까지 배포를 지연합니다.
- 실행 중인 모든 오케스트레이션 인스턴스가 함수의 기존 버전을 사용해야 합니다. 

> [!NOTE]
> 이 문서에서는 내구성 함수 1.x를 대상으로 하는 함수 앱에 대한 지침을 제공합니다. 지속 기능 2.x에 도입된 변경 사항을 고려하여 업데이트되지 않았습니다. 확장 버전 간의 차이점에 대한 자세한 내용은 [지속형 함수 버전을](durable-functions-versions.md)참조하십시오.

다음 차트는 내구성 함수에 대한 가동 중지 시간 제로 배포를 달성하기 위한 세 가지 주요 전략을 비교합니다. 

| 전략 |  사용 시기 | 장점 | 단점 |
| -------- | ------------ | ---- | ---- |
| [버전 관리](#versioning) |  [주요 변경 내용이](durable-functions-versioning.md) 자주 발생하지 않는 응용 프로그램입니다. | 구현하기 쉽습니다. |  메모리 및 기능 수의 기능 앱 크기가 증가했습니다.<br/>코드 중복. |
| [슬롯으로 상태 확인](#status-check-with-slot) | 24시간 이상 지속되거나 자주 겹치는 오케스트레이션이 없는 시스템입니다. | 간단한 코드 베이스.<br/>추가 기능 앱 관리가 필요하지 않습니다. | 추가 저장소 계정 또는 작업 허브 관리가 필요합니다.<br/>오케스트레이션이 실행되지 않는 기간은 필요합니다. |
| [응용 프로그램 라우팅](#application-routing) | 오케스트레이션이 실행되지 않는 기간이 없는 시스템(예: 24시간 이상 지속되는 오케스트레이션이 있는 기간 또는 자주 겹치는 오케스트레이션이 있는 기간). | 변경 사항이 있는 오케스트레이션을 계속 실행하여 새 버전의 시스템을 처리합니다. | 지능형 응용 프로그램 라우터가 필요합니다.<br/>구독에서 허용하는 기능 앱 수를 최대로 초과할 수 있습니다. 기본값은 100입니다. |

## <a name="versioning"></a>버전 관리

함수의 새 버전을 정의하고 함수 앱에 이전 버전을 그대로 둡니다. 다이어그램에서 볼 수 있듯이 함수의 버전은 해당 이름의 일부가 됩니다. 이전 버전의 함수가 보존되므로 기내 오케스트레이션 인스턴스는 계속 참조할 수 있습니다. 한편 새 오케스트레이션 인스턴스에 대한 요청은 앱 설정에서 오케스트레이션 클라이언트 함수가 참조할 수 있는 최신 버전을 호출합니다.

![버전 전환 전략](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

이 전략에서는 모든 함수를 복사해야 하며 다른 함수에 대한 참조를 업데이트해야 합니다. 스크립트를 작성하여 더 쉽게 만들 수 있습니다. 다음은 마이그레이션 스크립트가 있는 [샘플 프로젝트입니다.](https://github.com/TsuyoshiUshio/DurableVersioning)

>[!NOTE]
>이 전략은 배포 슬롯을 사용하여 배포 중 가동 중지 시간을 방지합니다. 새 배포 슬롯을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Functions 배포 슬롯을](../functions-deployment-slots.md)참조하십시오.

## <a name="status-check-with-slot"></a>슬롯으로 상태 확인

현재 버전의 함수 앱이 프로덕션 슬롯에서 실행되는 동안 스테이징 슬롯에 함수 앱의 새 버전을 배포합니다. 프로덕션 및 스테이징 슬롯을 교체하기 전에 실행 중인 오케스트레이션 인스턴스가 있는지 확인합니다. 모든 오케스트레이션 인스턴스가 완료되면 스왑을 수행할 수 있습니다. 이 전략은 오케스트레이션 인스턴스가 비행 중일 때 예측 가능한 기간이 있는 경우에 작동합니다. 오케스트레이션이 오래 실행되지 않고 오케스트레이션 실행이 자주 겹치지 않는 경우에 가장 좋은 방법입니다.

### <a name="function-app-configuration"></a>함수 앱 구성

다음 절차를 사용하여 이 시나리오를 설정합니다.

1. 스테이징 및 프로덕션을 위해 함수 앱에 [배포 슬롯을 추가합니다.](../functions-deployment-slots.md#add-a-slot)

1. 각 슬롯에 대해 [AzureWebJobsStorage 응용 프로그램 설정을](../functions-app-settings.md#azurewebjobsstorage) 공유 저장소 계정의 연결 문자열로 설정합니다. 이 저장소 계정 연결 문자열은 Azure Functions 런타임에서 사용됩니다. 이 계정은 Azure Functions 런타임에서 사용되며 함수의 키를 관리합니다.

1. 각 슬롯에 대해 새 앱 설정을 만듭니다(예: `DurableManagementStorage`. 값을 다른 저장소 계정의 연결 문자열로 설정합니다. 이러한 저장소 계정은 [안정적인 실행을](durable-functions-checkpointing-and-replay.md)위해 내구성 함수 확장에서 사용됩니다. 각 슬롯에 대해 별도의 저장소 계정을 사용합니다. 이 설정을 배포 슬롯 설정으로 표시하지 마십시오.

1. 함수 앱의 [host.json 파일의 지속적 작업](durable-functions-bindings.md#hostjson-settings) `azureStorageConnectionStringName` 섹션에서 3단계에서 만든 앱 설정의 이름으로 지정합니다.

다음 다이어그램에서는 설명된 배포 슬롯 및 저장소 계정 구성을 보여 주며, 이 잠재적인 배포 전 시나리오에서는 함수 앱의 버전 2가 프로덕션 슬롯에서 실행되고 버전 1은 스테이징 슬롯에 남아 있습니다.

![배포 슬롯 및 저장소 계정](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.json 예제

다음 JSON 조각은 *host.json* 파일의 연결 문자열 설정의 예입니다.

#### <a name="functions-20"></a>함수 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD 파이프라인 구성

CI/CD 파이프라인을 구성하여 함수 앱에 보류 중이거나 실행 중인 오케스트레이션 인스턴스가 없는 경우에만 배포하도록 구성합니다. Azure 파이프라인을 사용하는 경우 다음 예제와 같이 이러한 조건을 확인하는 함수를 만들 수 있습니다.

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

그런 다음 오케스트레이션이 실행되지 않고 대기하도록 스테이징 게이트를 구성합니다. 자세한 내용은 [게이트를 사용한 릴리스 배포 제어를](/azure/devops/pipelines/release/approvals/gates?view=azure-devops) 참조하십시오.

![배포 게이트](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure 파이프라인은 배포가 시작되기 전에 함수 앱에서 오케스트레이션 인스턴스를 실행하는지 확인합니다.

![배포 게이트(실행 중)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

이제 함수 앱의 새 버전을 스테이징 슬롯에 배포해야 합니다.

![스테이징 슬롯](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

마지막으로 슬롯을 교체합니다. 

배포 슬롯 설정으로 표시되지 않는 응용 프로그램 설정도 교환되므로 버전 2 앱은 저장소 계정 A에 대한 참조를 유지합니다. 오케스트레이션 상태는 저장소 계정에서 추적되므로 버전 2 앱에서 실행되는 모든 오케스트레이션은 중단 없이 새 슬롯에서 계속 실행됩니다.

![배포 슬롯](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

두 슬롯에 동일한 저장소 계정을 사용하려면 작업 허브의 이름을 변경할 수 있습니다. 이 경우 슬롯 상태와 앱의 HubName 설정을 관리해야 합니다. 자세한 내용은 [지속 형 기능의 작업 허브를](durable-functions-task-hubs.md)참조하십시오.

## <a name="application-routing"></a>응용 프로그램 라우팅

이 전략은 가장 복잡합니다. 그러나 오케스트레이션 실행 사이에 시간이 없는 함수 앱에 사용할 수 있습니다.

이 전략의 경우 지속형 함수 앞에 *응용 프로그램 라우터를* 만들어야 합니다. 이 라우터는 내구성 함수로 구현할 수 있습니다. 라우터는 다음을 수행할 책임이 있습니다.

* 함수 앱을 배포합니다.
* 지속 형 함수의 버전을 관리 합니다. 
* 기능 앱에 대한 오케스트레이션 요청을 라우팅합니다.

오케스트레이션 요청을 처음 받을 때 라우터는 다음 작업을 수행합니다.

1. Azure에서 새 함수 앱을 만듭니다.
2. 함수 앱의 코드를 Azure의 새 함수 앱에 배포합니다.
3. 오케스트레이션 요청을 새 앱으로 전달합니다.

라우터는 Azure에서 어떤 함수 앱에 배포되는 앱 코드 버전의 상태를 관리합니다.

![응용 프로그램 라우팅(처음)](media/durable-functions-zero-downtime-deployment/application-routing.png)

라우터는 요청과 함께 전송된 버전에 따라 배포 및 오케스트레이션 요청을 적절한 함수 앱으로 보냅니다. 패치 버전을 무시합니다.

주요 변경 없이 새 버전의 앱을 배포하는 경우 패치 버전을 증분할 수 있습니다. 라우터는 기존 함수 앱에 배포하고 동일한 함수 앱으로 라우팅되는 코드의 이전 버전과 새 버전에 대한 요청을 보냅니다.

![응용 프로그램 라우팅(주요 변경 없음)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

주요 버전 또는 부 버전을 증분하여 새 버전의 앱을 배포할 때 주요 버전 또는 부 버전을 증분할 수 있습니다. 그런 다음 응용 프로그램 라우터는 Azure에서 새 함수 앱을 만들고 배포하며 새 버전의 앱에 대한 요청을 라우팅합니다. 다음 다이어그램에서는 앱의 1.0.1 버전에서 오케스트레이션을 실행하면서 계속 실행되지만 1.1.0 버전에 대한 요청은 새 함수 앱으로 라우팅됩니다.

![응용 프로그램 라우팅(주요 변경)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

라우터는 1.0.1 버전에서 오케스트레이션 상태를 모니터링하고 모든 오케스트레이션이 완료된 후 앱을 제거합니다. 

### <a name="tracking-store-settings"></a>매장 설정 추적

각 함수 앱은 별도의 저장소 계정에서 별도의 예약 큐를 사용해야 합니다. 응용 프로그램의 모든 버전에서 모든 오케스트레이션 인스턴스를 쿼리하려는 경우 함수 앱에서 인스턴스 및 기록 테이블을 공유할 수 있습니다. [host.json 설정](durable-functions-bindings.md#host-json) 파일의 `trackingStoreConnectionStringName` `trackingStoreNamePrefix` 및 설정을 구성하여 테이블을 공유하여 모두 동일한 값을 사용할 수 있습니다.

자세한 내용은 [Azure의 지속가능한 함수에서 인스턴스 관리를](durable-functions-instance-management.md)참조하십시오.

![매장 설정 추적](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [내구성이 뛰어난 기능 버전](durable-functions-versioning.md)

