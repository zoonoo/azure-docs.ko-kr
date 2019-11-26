---
title: Durable Functions에 대 한 가동 중지 시간이 0 인 배포
description: 가동 중지 시간이 0 인 배포에 대해 Durable Functions 오케스트레이션을 사용 하도록 설정 하는 방법을 알아봅니다.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231266"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Durable Functions에 대 한 가동 중지 시간이 0 인 배포

Durable Functions의 [안정적인 실행 모델](durable-functions-checkpointing-and-replay.md) 을 사용 하려면 오케스트레이션이 결정적 이어야 합니다. 그러면 업데이트를 배포할 때 고려해 야 할 추가 과제가 생성 됩니다. 배포에 작업 함수 서명 또는 orchestrator 논리에 대 한 변경 내용이 포함 된 경우 진행 중인 오케스트레이션 인스턴스가 실패 합니다. 이 상황은 시간이 나 작업일을 나타낼 수 있는 장기 실행 오케스트레이션의 인스턴스에 대 한 문제입니다.

이러한 오류가 발생 하지 않도록 하려면 다음 두 가지 옵션을 사용할 수 있습니다. 
- 실행 중인 모든 오케스트레이션 인스턴스가 완료 될 때까지 배포를 지연 합니다.
- 실행 중인 오케스트레이션 인스턴스에서 기존 버전의 함수를 사용 하는지 확인 합니다. 

> [!NOTE]
> 이 문서에서는 Durable Functions 1.x를 대상으로 하는 함수 앱에 대 한 지침을 제공 합니다. Durable Functions 2.x에 도입 된 변경 내용을 고려 하 여 업데이트 되지 않았습니다. 확장 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md)을 참조 하세요.

다음 차트에서는 Durable Functions에 대 한 가동 중지 시간이 0 인 배포를 구현 하는 세 가지 주요 전략을 비교 합니다. 

| 전략 |  사용 시기 | 전문가 | 단점 |
| -------- | ------------ | ---- | ---- |
| [버전 관리](#versioning) |  자주 발생 하지 않는 응용 프로그램은 크게 변경 되지 않습니다 [.](durable-functions-versioning.md) | 간단히 구현할 수 있습니다. |  메모리의 함수 앱 크기와 함수 수를 늘립니다.<br/>코드 중복. |
| [슬롯으로 상태 검사](#status-check-with-slot) | 장기 실행 오케스트레이션이 24 시간 이상 지속 되지 않거나 자주 겹치는 오케스트레이션이 없는 시스템입니다. | 간단한 코드 베이스입니다.<br/>추가 함수 앱 관리가 필요 하지 않습니다. | 추가 저장소 계정 또는 작업 허브 관리가 필요 합니다.<br/>오케스트레이션이 실행 되지 않는 기간이 필요 합니다. |
| [응용 프로그램 라우팅](#application-routing) | 오케스트레이션이 실행 되지 않는 시간 (예: 오케스트레이션이 24 시간 이상 이거나 자주 겹치는 오케스트레이션이 있는 기간)이 없는 시스템 | 지속적으로 변경 되는 오케스트레이션을 지속적으로 실행 하는 새 버전의 시스템을 처리 합니다. | 지능형 응용 프로그램 라우터가 필요 합니다.<br/>구독에서 허용 하는 함수 앱의 수를 최대로 설정할 수 있습니다. 기본값은 100입니다. |

## <a name="versioning"></a>버전 관리

함수의 새 버전을 정의 하 고 함수 앱에 이전 버전을 그대로 둡니다. 다이어그램에서 볼 수 있듯이 함수의 버전은 이름에 포함 됩니다. 이전 버전의 함수는 유지 되므로 진행 중인 오케스트레이션 인스턴스는 계속 해 서 참조할 수 있습니다. 한편, 새 오케스트레이션 인스턴스에 대 한 요청은 오케스트레이션 클라이언트 함수가 앱 설정에서 참조할 수 있는 최신 버전을 호출 합니다.

![버전 관리 전략](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

이 전략에서는 모든 함수를 복사 하 고 다른 함수에 대 한 참조를 업데이트 해야 합니다. 스크립트를 작성 하 여 더 쉽게 만들 수 있습니다. 마이그레이션 스크립트를 사용 하는 [샘플 프로젝트](https://github.com/TsuyoshiUshio/DurableVersioning) 는 다음과 같습니다.

>[!NOTE]
>이 전략은 배포 중 가동 중지 시간을 방지 하기 위해 배포 슬롯을 사용 합니다. 새 배포 슬롯을 만들고 사용 하는 방법에 대 한 자세한 내용은 [Azure Functions 배포 슬롯](../functions-deployment-slots.md)을 참조 하세요.

## <a name="status-check-with-slot"></a>슬롯으로 상태 검사

프로덕션 슬롯에서 현재 버전의 함수 앱이 실행 되는 동안 새 버전의 함수 앱을 스테이징 슬롯에 배포 합니다. 프로덕션 및 스테이징 슬롯을 교환 하기 전에 실행 중인 오케스트레이션 인스턴스가 있는지 확인 하십시오. 모든 오케스트레이션 인스턴스를 완료 한 후에는 교환을 수행할 수 있습니다. 이 전략은 비행 중인 오케스트레이션 인스턴스가 없을 때 예측 가능한 기간이 있는 경우에 작동 합니다. 오케스트레이션이 장기 실행 되지 않고 오케스트레이션 실행이 자주 겹치지 않는 경우에 가장 적합 한 방법입니다.

### <a name="function-app-configuration"></a>함수 앱 구성

이 시나리오를 설정 하려면 다음 절차를 따르십시오.

1. 스테이징 및 프로덕션을 위해 함수 앱에 [배포 슬롯을 추가](../functions-deployment-slots.md#add-a-slot) 합니다.

1. 각 슬롯에 대해 [Azurewebjobsstorage 응용 프로그램 설정을](../functions-app-settings.md#azurewebjobsstorage) 공유 저장소 계정의 연결 문자열로 설정 합니다. 이 저장소 계정 연결 문자열은 Azure Functions 런타임에 사용 됩니다. 이 계정은 Azure Functions 런타임에 사용 되며 함수의 키를 관리 합니다.

1. 각 슬롯에 대해 `DurableManagementStorage`와 같은 새 앱 설정을 만듭니다. 해당 값을 다른 저장소 계정의 연결 문자열로 설정 합니다. 이러한 저장소 계정은 [안정적으로 실행](durable-functions-checkpointing-and-replay.md)하기 위해 Durable Functions 확장에서 사용 됩니다. 각 슬롯에 별도의 저장소 계정을 사용 합니다. 이 설정을 배포 슬롯 설정으로 표시 하지 마세요.

1. 함수 앱의 [microsoft.azure.webjobs.extensions.durabletask 섹션](durable-functions-bindings.md#hostjson-settings)에서 3 단계에서 만든 앱 설정의 이름으로 `azureStorageConnectionStringName`를 지정 합니다.

다음 다이어그램에서는 배포 슬롯과 저장소 계정의 설명 된 구성을 보여 줍니다. 이 잠재적 배포 전 시나리오에서 함수 앱의 버전 2는 프로덕션 슬롯에서 실행 되는 반면 버전 1은 스테이징 슬롯에 남아 있습니다.

![배포 슬롯 및 저장소 계정](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host. json 예

다음 JSON 조각은 *호스트 json* 파일에 있는 연결 문자열 설정의 예입니다.

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

함수 앱에 보류 중이거나 실행 중인 오케스트레이션 인스턴스가 없는 경우에만 배포 하도록 CI/CD 파이프라인을 구성 합니다. Azure Pipelines를 사용 하는 경우 다음 예제와 같이 이러한 조건을 확인 하는 함수를 만들 수 있습니다.

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

다음으로, 오케스트레이션이 실행 되 고 있지 않을 때까지 대기 하도록 준비 게이트를 구성 합니다. 자세한 내용은 [게이트를 사용 하 여 배포 제어 릴리스](/azure/devops/pipelines/release/approvals/gates?view=azure-devops) 를 참조 하세요.

![배포 게이트](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines는 배포를 시작 하기 전에 함수 앱에서 오케스트레이션 인스턴스를 실행 하는지 확인 합니다.

![배포 게이트 (실행 중)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

이제 새 버전의 함수 앱을 스테이징 슬롯에 배포 해야 합니다.

![스테이징 슬롯](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

마지막으로 슬롯을 교환 합니다. 

배포 슬롯 설정으로 표시 되지 않은 응용 프로그램 설정도 교환 되므로 버전 2 앱은 저장소 계정 A에 대 한 참조를 유지 합니다. 오케스트레이션 상태는 저장소 계정에서 추적 되므로 버전 2 앱에서 실행 되는 오케스트레이션은 중단 없이 새 슬롯에서 계속 실행 됩니다.

![배포 슬롯](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

두 슬롯에 동일한 저장소 계정을 사용 하려면 작업 허브의 이름을 변경할 수 있습니다. 이 경우 슬롯의 상태와 앱의 HubName 설정을 관리 해야 합니다. 자세히 알아보려면 [Durable Functions의 작업 허브](durable-functions-task-hubs.md)를 참조 하세요.

## <a name="application-routing"></a>응용 프로그램 라우팅

이 전략은 가장 복잡 합니다. 그러나 오케스트레이션 실행 사이에 시간이 없는 함수 앱에도 사용할 수 있습니다.

이 전략의 경우 Durable Functions 앞에 *응용 프로그램 라우터* 를 만들어야 합니다. Durable Functions를 사용 하 여이 라우터를 구현할 수 있습니다. 라우터는 다음과 같은 역할을 합니다.

* 함수 앱을 배포 합니다.
* Durable Functions 버전을 관리 합니다. 
* 오케스트레이션 요청을 함수 앱에 라우팅합니다.

오케스트레이션 요청이 처음 수신 될 때 라우터는 다음 작업을 수행 합니다.

1. Azure에서 새 함수 앱을 만듭니다.
2. 함수 앱의 코드를 Azure의 새 함수 앱에 배포 합니다.
3. 오케스트레이션 요청을 새 앱으로 전달 합니다.

라우터는 Azure의 함수 앱에 배포 되는 앱 코드 버전의 상태를 관리 합니다.

![응용 프로그램 라우팅 (첫 번째 시간)](media/durable-functions-zero-downtime-deployment/application-routing.png)

라우터는 요청과 함께 전송 된 버전에 따라 적절 한 함수 앱에 배포 및 오케스트레이션 요청을 보냅니다. 패치 버전을 무시 합니다.

주요 변경 내용 없이 새 버전의 앱을 배포 하는 경우 패치 버전을 증가 시킬 수 있습니다. 라우터는 기존 함수 앱에 배포 되 고 동일한 함수 앱에 라우팅되는 코드의 이전 버전 및 새 버전에 대 한 요청을 보냅니다.

![응용 프로그램 라우팅 (주요 변경 내용 없음)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

주요 변경 내용으로 새 버전의 앱을 배포할 때 주 버전 또는 부 버전을 증가 시킬 수 있습니다. 그런 다음 응용 프로그램 라우터는 Azure에서 새로운 함수 앱을 만들어 배포 하 고 앱의 새 버전에 대 한 요청을 라우팅합니다. 다음 다이어그램에서는 1.0.1 버전의 앱에서 오케스트레이션을 실행 하는 것은 계속 실행 되지만 1.1.0 버전에 대 한 요청은 새 함수 앱으로 라우팅됩니다.

![응용 프로그램 라우팅 (주요 변경)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

라우터는 1.0.1 버전에서 오케스트레이션의 상태를 모니터링 하 고 모든 오케스트레이션이 완료 된 후에 앱을 제거 합니다. 

### <a name="tracking-store-settings"></a>추적 저장소 설정

각 함수 앱은 별도의 저장소 계정으로 별도의 일정 큐를 사용 해야 합니다. 모든 버전의 응용 프로그램에서 모든 오케스트레이션 인스턴스를 쿼리하려면 함수 앱에서 인스턴스 및 기록 테이블을 공유할 수 있습니다. 동일한 값을 사용 하도록 `trackingStoreConnectionStringName` 및 `trackingStoreNamePrefix` 설정을 구성 하 여 [호스트 json 설정](durable-functions-bindings.md#host-json) 파일에서 테이블을 공유할 수 있습니다.

자세한 내용은 [Azure에서 Durable Functions 인스턴스 관리](durable-functions-instance-management.md)를 참조 하세요.

![추적 저장소 설정](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 Durable Functions](durable-functions-versioning.md)

