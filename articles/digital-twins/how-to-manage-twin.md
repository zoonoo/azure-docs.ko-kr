---
title: Digital Twins 관리
titleSuffix: Azure Digital Twins
description: 개별 트윈 및 관계를 검색, 업데이트 및 삭제하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68127a7603db2d408ed1bbdb244f49de4eb21031
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108208494"
---
# <a name="manage-digital-twins"></a>Digital Twins 관리

사용자 환경의 엔터티는 [디지털 트윈](concepts-twins-graph.md)으로 표현됩니다. 디지털 트윈 관리에는 만들기, 수정 및 제거가 포함될 수 있습니다. 이러한 작업을 수행하기 위해 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins), [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 또는 [Azure Digital Twins CLI](how-to-use-cli.md)를 사용할 수 있습니다.

이 문서에서는 디지털 트윈을 관리하는 방법을 중점적으로 설명합니다. 관계 및 [트윈 그래프](concepts-twins-graph.md) 전체를 사용하려면 [방법: 관계를 사용하여 트윈 그래프 관리](how-to-manage-graph.md)를 참조하세요.

> [!TIP]
> 모든 SDK 함수는 동기 및 비동기 버전으로 제공됩니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>트윈을 관리하는 방법

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>디지털 트윈 만들기

트윈을 만들려면 서비스 클라이언트에서 다음과 같이 `CreateOrReplaceDigitalTwinAsync()` 메서드를 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

디지털 트윈을 만들려면 다음을 제공해야 합니다.
* 원하는 디지털 트윈 ID
* 사용하려는 [모델](concepts-models.md)

필요에 따라 디지털 트윈의 모든 속성에 대해 초기 값을 제공할 수 있습니다. 속성은 선택 사항으로 처리되고 나중에 설정할 수 있지만 **설정될 때까지 트윈의 일부로 표시되지 않습니다.**

>[!NOTE]
>트윈 속성을 초기화할 필요가 없지만 트윈을 만들 때 트윈의 모든 [구성 요소](concepts-models.md#elements-of-a-model)를 **설정해야 합니다**. 비어 있는 개체일 수 있지만 구성 요소 자체는 존재해야 합니다.

모델과 모든 초기 속성 값은 관련 데이터를 포함하는 JSON 문자열인 `initData` 매개 변수를 통해 제공됩니다. 이 개체를 구조화하는 방법에 대한 자세한 내용을 보려면 다음 섹션을 계속 진행하세요.

> [!TIP]
> 트윈을 만들거나 업데이트한 후에는 변경 내용이 [쿼리](how-to-query-graph.md)에 반영될 때까지 최대 10초의 대기 시간이 있을 수 있습니다. `GetDigitalTwin` API([이 문서의 뒷부분](#get-data-for-a-digital-twin)에서 설명)는 이러한 지연 시간을 발생하지 않으므로 즉각적인 응답이 필요한 경우 쿼리 대신 API 호출을 사용하여 새로 만든 트윈을 확인합니다. 

### <a name="initialize-model-and-properties"></a>모델 및 속성 초기화

트윈이 생성될 때 트윈의 속성을 초기화할 수 있습니다. 

트윈 생성 API는 트윈 속성의 유효한 JSON 설명으로 직렬화된 개체를 허용합니다. 트윈의 JSON 형식에 대한 설명은 [개념: 디지털 트윈 및 트윈 그래프](concepts-twins-graph.md)를 참조하세요. 

먼저 트윈 및 해당 속성 데이터를 나타내는 데이터 개체를 만들 수 있습니다. 매개 변수 개체는 수동으로 만들거나 제공된 도우미 클래스를 사용하여 만들 수 있습니다. 각각의 예는 다음과 같습니다.

#### <a name="create-twins-using-manually-created-data"></a>수동으로 만든 데이터를 사용하여 트윈 만들기

사용자 지정 도우미 클래스를 사용하지 않고 `Dictionary<string, object>`에서 트윈의 속성을 나타낼 수 있습니다. 여기서 `string`은 속성의 이름이고 `object`는 속성 및 해당 값을 나타내는 개체입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>도우미 클래스를 사용하여 트윈 만들기

`BasicDigitalTwin`의 도우미 클래스를 사용하여 속성 필드를 "트윈" 개체에 직접 저장할 수 있습니다. `Dictionary<string, object>`를 사용하여 속성 목록을 빌드할 수 있습니다. 그런 다음 `CustomProperties`로 트윈 개체에 직접 쌍으로 추가할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` 개체는 `Id` 필드와 함께 제공됩니다. 이 필드는 비워 둘 수 있지만 ID 값을 추가하는 경우 `CreateOrReplaceDigitalTwinAsync()` 호출에 전달된 ID 매개 변수와 일치해야 합니다. 예를 들면 다음과 같습니다.
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>디지털 트윈에 대한 데이터 가져오기

다음과 같이 `GetDigitalTwin()` 메서드를 호출하여 디지털 트윈의 세부 정보에 액세스할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

이 호출은 `BasicDigitalTwin`과 같은 강력한 형식의 개체 형식으로 트윈 데이터를 반환합니다. `BasicDigitalTwin`은 SDK에 포함된 serialization 도우미 클래스로, 이 클래스는 미리 구문 분석된 형식으로 핵심 트윈 메타데이터 및 속성을 반환합니다. 다음은 이를 사용하여 트윈 세부 정보를 보는 방법에 대한 예입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

`GetDigitalTwin()` 메서드를 사용하여 트윈을 검색할 때 한 번 이상 설정된 속성만 반환됩니다.

>[!TIP]
>트윈의 `displayName`은 해당 모델 메타데이터의 일부이므로 트윈 인스턴스에 대한 데이터를 가져올 때 표시되지 않습니다. 이 값은 [모델에서 검색](how-to-manage-model.md#retrieve-models)하여 확인할 수 있습니다.

단일 API 호출을 사용하여 여러 트윈을 검색하려면 [방법: 트윈 그래프 쿼리](how-to-query-graph.md)의 쿼리 API 예를 참조하세요.

*문식 선문자* 를 정의하는 다음 모델([DTDL(디지털 트윈 정의 언어](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL))로 작성됨)을 고려합니다.

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

*문식 선문자* 형식 트윈에 대한 `object result = await client.GetDigitalTwinAsync("my-moon");` 호출 결과는 다음과 같을 수 있습니다.

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

디지털 트윈의 정의된 속성은 디지털 트윈의 최상위 속성으로 반환됩니다. DTDL 정의의 일부가 아닌 메타데이터 또는 시스템 정보는 `$` 접두사와 함께 반환됩니다. 메타데이터 속성에는 다음 값이 포함됩니다.
* `$dtId`: 이 Azure Digital Twins 인스턴스의 디지털 트윈 ID입니다.
* `$etag`: 웹 서버에서 할당한 표준 HTTP 필드입니다. 이 값은 트윈이 업데이트될 때마다 새 값으로 업데이트되므로 이전 검사 이후 서버에서 트윈의 데이터가 업데이트되었는지 여부를 확인하는 데 유용할 수 있습니다. `If-Match`를 사용하여 엔터티의 etag가 제공된 etag와 일치하는 경우에만 완료되는 업데이트 및 삭제를 수행할 수 있습니다. 이러한 작업에 대한 자세한 내용은 [DigitalTwins 업데이트](/rest/api/digital-twins/dataplane/twins/digitaltwins_update) 및 [DigitalTwins 삭제](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete) 설명서를 참조하세요.
* `$metadata`: 다음을 비롯한 다른 속성 집합
  - 디지털 트윈 모델의 DTMI입니다.
  - 쓰기 가능한 각 속성의 동기화 상태입니다. 디바이스에 가장 유용합니다. 이 경우 서비스와 디바이스에 분기된 상태가 있을 수 있습니다(예: 디바이스가 오프라인 상태인 경우). 현재 이 속성은 IoT Hub에 연결된 물리적 디바이스에만 적용됩니다. 메타데이터 섹션의 데이터를 사용하여 마지막으로 수정한 타임스탬프뿐만 아니라 속성의 전체 상태를 이해할 수 있습니다. 동기화 상태에 대한 자세한 내용은 디바이스 상태 동기화에 대한 [이 IoT Hub 자습서](../iot-hub/tutorial-device-twins.md)를 참조하세요.
  - IoT Hub 또는 Azure Digital Twins와 같은 서비스별 메타데이터. 

[방법: Azure Digital Twins API 및 SDK 사용](how-to-use-apis-sdks.md)에서 `BasicDigitalTwin`과 같은 serialization 도우미 클래스에 대해 자세히 알아볼 수 있습니다.

## <a name="view-all-digital-twins"></a>모든 디지털 트윈 보기

인스턴스의 모든 디지털 트윈을 보려면 [쿼리](how-to-query-graph.md)를 사용합니다. [쿼리 API](/rest/api/digital-twins/dataplane/query) 또는 [CLI 명령](how-to-use-cli.md)을 사용하여 쿼리를 실행할 수 있습니다.

다음은 인스턴스의 모든 디지털 트윈 목록을 반환하는 기본 쿼리의 본문입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>디지털 쌍 업데이트

디지털 트윈의 속성을 업데이트하려면 [JSON 패치](http://jsonpatch.com/) 형식으로 바꿀 정보를 작성합니다. 이러한 방식으로 여러 속성을 한 번에 바꿀 수 있습니다. 그런 다음 JSON 패치 문서를 `UpdateDigitalTwin()` 메서드에 전달합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

패치 호출은 모든 속성을 원하는 대로 단일 트윈으로 업데이트할 수 있습니다. 여러 트윈에서 속성을 업데이트해야 하는 경우에는 각 트윈에 대해 별도의 업데이트 호출이 필요합니다.

> [!TIP]
> 트윈을 만들거나 업데이트한 후에는 변경 내용이 [쿼리](how-to-query-graph.md)에 반영될 때까지 최대 10초의 대기 시간이 있을 수 있습니다. `GetDigitalTwin` API([이 문서의 앞부분](#get-data-for-a-digital-twin)에서 설명)는 이러한 지연 시간을 발생하지 않으므로 즉각적인 응답이 필요한 경우 쿼리 대신 API 호출을 사용하여 새로 업데이트한 트윈을 확인합니다. 

다음은 JSON 패치 코드의 예입니다. 이 문서는 적용되는 디지털 트윈의 *mass* 및 *radius* 속성 값을 대체합니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Azure .NET SDK의 [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument)를 사용하여 패치를 만들 수 있습니다. 다음은 예제입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>디지털 트윈 구성 요소의 속성 업데이트

모델에 구성 요소가 포함되어 있을 수 있으므로 다른 모델로 구성될 수 있습니다. 

디지털 트윈의 구성 요소에서 속성을 패치하려면 JSON 패치에서 경로 구문을 사용할 수 있습니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>디지털 트윈의 모델 업데이트

`UpdateDigitalTwin()` 함수를 사용하여 디지털 트윈을 다른 모델로 마이그레이션할 수도 있습니다. 

예를 들어 디지털 트윈의 메타데이터 `$model` 필드를 대체하는 다음과 같은 JSON 패치 문서를 고려합니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

이 작업은 패치로 수정되는 디지털 트윈이 새 모델을 준수하는 경우에만 성공합니다. 

다음 예제를 참조하세요.
1. *foo_old* 모델을 사용하는 디지털 트윈을 생각해 보세요. *foo_old* 는 필요한 속성 *mass* 를 정의합니다.
2. 새 모델 *foo_new* 는 속성 mass를 정의하고 새 필수 속성 *temperature* 를 추가합니다.
3. 패치 후에 디지털 트윈에는 mass 속성과 temperature 속성이 모두 있어야 합니다. 

이 상황에 대한 패치는 모델과 트윈의 temperature 속성을 모두 업데이트해야 합니다. 예를 들면 다음과 같습니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>충돌하는 업데이트 호출 처리

Azure Digital Twins는 들어오는 모든 요청이 차례로 처리되도록 합니다. 즉, 여러 함수가 트윈의 동일한 속성을 동시에 업데이트하려고 하는 경우에도 충돌을 처리하기 위해 명시적인 잠금 코드를 작성할 **필요가 없습니다**.

이 동작은 트윈 단위로 발생합니다. 

예를 들어 이러한 세 호출이 동시에 도착하는 시나리오를 가정해 보겠습니다. 
*   *Twin1* 에 속성 A 쓰기
*   *Twin1* 에 속성 B 쓰기
*   *Twin2* 에 속성 A 쓰기

*Twin1* 을 수정하는 두 호출이 차례로 실행되며 각 변경 내용에 대한 변경 메시지가 생성됩니다. *Twin2* 를 수정하는 호출은 충돌 없이 동시에 실행될 수 있습니다(도착하는 즉시).

## <a name="delete-a-digital-twin"></a>디지털 트윈 삭제

`DeleteDigitalTwin()` 메서드를 사용하여 트윈을 삭제할 수 있습니다. 그러나 더 이상 관계가 없는 경우에만 트윈을 삭제할 수 있습니다. 따라서 트윈의 들어오고 나가는 관계를 먼저 삭제합니다.

다음은 트윈 및 해당 관계를 삭제하는 코드의 예입니다. `DeleteDigitalTwin` SDK 호출이 광범위한 예제 컨텍스트에서 어디에 속하는지 명확하게 강조 표시됩니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>모든 디지털 트윈 삭제

한 번에 모든 트윈을 삭제하는 방법에 대한 예를 보려면 [자습서: 샘플 클라이언트 앱으로 기본 사항 살펴보기](tutorial-command-line-app.md)에서 사용된 샘플 앱을 다운로드합니다. *CommandLoop.cs* 파일은 `CommandDeleteAllTwins()` 함수에서 이를 수행합니다.

## <a name="runnable-digital-twin-code-sample"></a>실행 가능한 디지털 트윈 코드 샘플

아래 실행 가능한 코드 샘플을 사용하여 트윈을 만들고, 해당 세부 정보를 업데이트하고, 트윈을 삭제할 수 있습니다. 

### <a name="set-up-the-runnable-sample"></a>실행 가능한 샘플 설정

이 코드 조각은 [자습서: 샘플 클라이언트 앱으로 Azure Digital Twins 살펴보기](tutorial-command-line-app.md)에서 [Room.json](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) 모델 정의를 사용합니다. 이 링크를 사용하여 파일로 직접 이동하거나 전체 [엔드투엔드 샘플 프로젝트](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)의 일부로 다운로드할 수 있습니다.

샘플을 실행하려면 먼저 다음을 수행합니다.
1. 모델 파일을 다운로드하고 프로젝트에 배치한 다음 아래 코드의 `<path-to>` 자리 표시자를 바꿔서 찾는 위치를 프로그램에 알립니다.
2. 자리 표시자 `<your-instance-hostname>`을 Azure Digital Twins 인스턴스의 호스트 이름으로 바꿉니다.
3. Azure Digital Twins와 함께 작동하는 데 필요한 두 개의 종속성을 프로젝트에 추가합니다. 첫 번째는 [.NET용 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client)의 패키지이고, 두 번째는 Azure에 대한 인증을 지원하는 도구를 제공합니다.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

또한 샘플을 직접 실행하려면 로컬 자격 증명을 설정해야 합니다. 다음 섹션에서는 이 과정을 안내합니다.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>샘플 실행

위의 단계를 완료한 후에는 다음 샘플 코드를 직접 실행할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

다음은 위의 프로그램에 대한 콘솔 출력입니다. 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="트윈을 만들고, 업데이트하고, 삭제하는 것을 보여 주는 콘솔 출력" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>다음 단계

디지털 트윈 간의 관계를 만들고 관리하는 방법을 참조하세요.
* [방법: 관계를 사용하여 쌍 그래프 관리](how-to-manage-graph.md)