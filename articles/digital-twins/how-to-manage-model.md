---
title: DTDL 모델 관리
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 내에서 모델을 만들고, 편집하고, 삭제하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 4/07/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f306fd1a2cccb3733640f5da100718908c444c3a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110478147"
---
# <a name="manage-azure-digital-twins-models"></a>Azure Digital Twins 모델 관리

이 문서에서는 Azure Digital Twins 인스턴스에서 [모델](concepts-models.md)을 관리하는 방법을 설명합니다. 관리 작업에는 모델 업로드, 유효성 검사, 검색 및 삭제가 포함됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="샘플 모델 및 그래프를 보여주는 Azure Digital Twins Explorer의 스크린샷." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

## <a name="create-models"></a>모델 만들기

Azure Digital Twins에 대한 모델은 DTDL로 작성되고 .json 파일로 저장됩니다. DTDL 문서 작성을 용이하게 하는 구문 유효성 검사 및 기타 기능을 제공하는 [Visual Studio Code](https://code.visualstudio.com/)에 사용할 수 있는 [DTDL 확장명](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)도 있습니다.

병원에서 병실을 디지털로 표현하는 예를 생각해 보세요. 각 병실에는 손 씻기를 모니터링하는 스마트 비누 디스펜서와 병실 트래픽을 모니터링하는 센서가 포함되어 있습니다.

솔루션에 대한 첫 번째 단계는 병원의 양상을 나타내는 모델을 만드는 것입니다. 이 시나리오에서 병실은 다음과 같이 설명할 수 있습니다.

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> 이는 모델을 정의하고 저장하는 .json 파일에 대한 샘플 본문으로, 클라이언트 프로젝트의 일부로 업로드됩니다. 반면에 REST API 호출은 위에 있는 것과 같은 모델 정의의 배열을 사용하며 이 배열은 .NET SDK의 `IEnumerable<string>`에 매핑됩니다. 따라서 REST API에서 이 모델을 직접 사용하려면 대괄호로 묶습니다.

이 모델은 병실의 이름과 고유 ID를 정의하고 방문자 수 및 손 씻기 상태를 나타내는 속성을 정의합니다. 이러한 카운터는 동작 센서 및 스마트 비누 디스펜서에서 업데이트되며 *handwash percentage* 속성을 계산하는 데 함께 사용됩니다. 또한 모델은 *hasDevices* 관계를 정의하며 이는 Room 모델을 기반으로 모든 [디지털 트윈](concepts-twins-graph.md)을 실제 디바이스에 연결하는 데 사용됩니다.

이 메서드에 따라 병원의 병동, 구역 또는 병원 자체에 대한 모델을 정의할 수 있습니다.

### <a name="validate-syntax"></a>구문 유효성 검사

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>모델 업로드

모델을 만든 후에는 Azure Digital Twins 인스턴스에 업로드할 수 있습니다.

모델을 업로드할 준비가 되면 다음 코드 조각을 사용할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

`CreateModels` 메서드가 하나의 단일 트랜잭션에서 여러 파일을 허용하는지 확인합니다. 다음은 이를 보여 주는 샘플입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

모델 파일에는 두 개 이상의 모델이 포함될 수 있습니다. 이 경우에는 모델을 JSON 배열에 배치해야 합니다. 예를 들면 다음과 같습니다.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

업로드할 때 서비스에서 모델 파일의 유효성을 검사합니다.

## <a name="retrieve-models"></a>모델 검색

Azure Digital Twins 인스턴스에 저장된 모델을 나열하고 검색할 수 있습니다. 

이 경우 옵션은 다음과 같습니다.
* 단일 모델 검색
* 모든 모델 검색
* 모델에 대한 메타데이터 및 종속성 검색

다음은 일부 예제 호출입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

모델을 검색하는 API 호출은 모두 `DigitalTwinsModelData` 개체를 반환합니다. `DigitalTwinsModelData`는 이름, DTMI 및 모델 생성 날짜 등 Azure Digital Twins 인스턴스에 저장된 모델에 대한 메타데이터를 포함합니다. 경우에 따라 `DigitalTwinsModelData` 개체는 모델 자체를 포함하기도 합니다. 매개 변수에 따라 검색 호출을 사용하여 메타데이터만 검색하거나(예를 들어 사용 가능한 도구의 UI 목록을 표시하려는 경우에 유용) 전체 모델을 검색할 수 있습니다.

`RetrieveModelWithDependencies` 호출은 요청된 모델뿐만 아니라 요청된 모델이 종속된 모든 모델을 반환합니다.

모델이 업로드된 문서 양식으로 반드시 반환되는 것은 아닙니다. Azure Digital Twins는 반환 양식이 의미상 동일함을 보장합니다. 

## <a name="update-models"></a>모델 업데이트

모델이 Azure Digital Twins 인스턴스에 업로드되면 모델 인터페이스 전체를 변경할 수 없습니다. 이는 기존의 모델 "편집"이 없음을 의미합니다. 또한 Azure Digital Twins는 동일한 모델을 다시 업로드할 수 없습니다.

대신 모델을 변경하려는 경우(예: `displayName` 또는 `description` 업데이트) 모델의 **새 버전** 을 업로드하는 방법이 있습니다. 

### <a name="model-versioning"></a>모델 버전 관리

기존 모델의 새 버전을 만들려면 원래 모델의 DTDL로 시작합니다. 변경할 필드를 업데이트, 추가 또는 제거합니다.

그런 다음 모델의 `id` 필드를 업데이트하여 이를 최신 버전의 모델로 표시합니다. `;` 뒤에 있는 모델 ID의 마지막 섹션은 모델 번호를 나타냅니다. 이제 이 모델의 더 업데이트된 버전임을 나타내려면 `id` 값의 끝에 있는 숫자를 현재 버전 번호보다 큰 숫자로 증분합니다.

예를 들어 이전 모델 ID가 다음과 같았다면

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

이 모델의 버전 2는 다음과 같습니다.

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

그런 다음 새 버전의 모델을 인스턴스에 업로드합니다. 

그러면 이 버전의 모델이 인스턴스에 제공되어 디지털 트윈에 사용할 수 있습니다. 이전 버전의 모델을 덮어쓰지 **않으므로** [제거](#remove-models)하기 전에는 여러 버전의 모델이 인스턴스에 공존하게 됩니다.

### <a name="impact-on-twins"></a>트윈에 미치는 영향

새 트윈을 만들 때 새 모델 버전과 이전 모델 버전이 공존하므로 새 트윈은 새 버전의 모델 또는 이전 버전을 사용할 수 있습니다.

즉, 새 버전의 모델을 업로드해도 기존 트윈에 자동으로 영향을 주지 않습니다. 기존 트윈은 단순히 이전 모델 버전의 인스턴스를 유지합니다.

방법: 디지털 트윈 관리의 *[디지털 트윈의 모델 업데이트](how-to-manage-twin.md#update-a-digital-twins-model)* 섹션에 설명된 대로 패치를 적용하여 기존 트윈을 새 모델 버전으로 업데이트할 수 있습니다. 동일한 패치 내에서 **모델 ID**(새 버전) 및 **새 모델에 맞게 변경해야 하는 트윈의 모든 필드** 를 둘 다 업데이트해야 합니다.

## <a name="remove-models"></a>모델 제거

모델은 다음 두 가지 방법 중 하나로 서비스에서 제거될 수도 있습니다.
* **서비스 해제**: 모델을 서비스 해제한 후에는 더 이상 새 디지털 트윈을 만드는 데 사용할 수 없습니다. 이미 이 모델을 사용하는 기존 디지털 트윈은 영향을 받지 않으므로 속성 변경, 관계 추가 또는 삭제 등으로 업데이트할 수 있습니다.
* **삭제**: 솔루션에서 모델을 완전히 제거합니다. 이 모델을 사용 중이었던 모든 트윈은 더 이상 유효한 모델과 연결되지 않으므로 모델이 전혀 없는 것처럼 처리됩니다. 여전히 이러한 트윈을 읽을 수는 있지만 다른 모델에 다시 할당될 때까지 업데이트를 수행할 수 없습니다.

이러한 기능은 개별 기능이며 서로에게 영향을 주지 않지만 모델을 점진적으로 제거하는 데 함께 사용할 수 있습니다. 

### <a name="decommissioning"></a>서비스 해제

다음은 모델을 서비스 해제하는 코드입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

모델의 서비스 해제 상태는 모델 검색 API에서 반환하는 `ModelData` 레코드에 포함됩니다.

### <a name="deletion"></a>삭제

인스턴스의 모든 모델을 한 번에 삭제하거나 개별적으로 수행할 수 있습니다.

모든 모델을 삭제하는 방법에 대한 예제를 보려면 [자습서: 샘플 클라이언트 앱으로 기본 사항 살펴보기](tutorial-command-line-app.md)에서 사용된 샘플 앱을 다운로드합니다. *CommandLoop.cs* 파일은 `CommandDeleteAllModels` 함수에서 이를 수행합니다.

이 섹션의 나머지 부분에서는 모델 삭제를 자세히 설명하고 개별 모델에 대해 이 작업을 수행하는 방법을 보여 줍니다.

#### <a name="before-deletion-deletion-requirements"></a>삭제 전: 삭제 요구 사항

일반적으로 모델은 언제든지 삭제할 수 있습니다.

다른 모델이 `extends` 관계 또는 구성 요소로 종속된 모델은 예외입니다. 예를 들어 ConferenceRoom 모델이 Room 모델을 확장하고 ACUnit 모델을 구성 요소로 포함하는 경우 ConferenceRoom이 해당 참조를 제거할 때까지 Room 또는 ACUnit을 삭제할 수 없습니다. 

종속 모델을 업데이트하여 종속성을 제거하거나 종속 모델을 완전히 삭제하여 이 작업을 수행할 수 있습니다.

#### <a name="during-deletion-deletion-process"></a>삭제 중: 삭제 프로세스

모델이 즉시 삭제하는 요구 사항을 충족하는 경우에도 남아 있는 트윈에 의도하지 않은 결과가 발생하지 않도록 몇 가지 단계를 수행하는 것이 좋습니다. 다음은 프로세스 관리에 도움이 되는 몇 가지 단계입니다.
1. 먼저 모델을 서비스 해제합니다.
2. 서비스에서 서비스를 해제하기 전에 전송된 마지막 순간 트윈 생성 요청을 처리했는지 확인할 수 있도록 몇 분 정도 기다립니다.
3. 모델별로 트윈을 쿼리하여 현재 서비스 해제된 모델을 사용하는 모든 트윈을 확인합니다.
4. 더 이상 필요하지 않은 경우 트윈을 삭제하거나 필요한 경우 새 모델에 패치합니다. 그대로 둘 수도 있으며 이 경우 모델이 삭제되고 나면 모델 없는 트윈이 됩니다. 이 상태의 의미에 대해서는 다음 섹션을 참조하세요.
5. 몇 분 후에 변경 내용이 적용되었는지 확인합니다.
6. 모델 삭제 

모델을 삭제하려면 다음 호출을 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>삭제 후: 모델 없는 트윈

모델을 삭제한 후에는 모델을 사용하던 모든 디지털 트윈이 이제 모델이 없는 것으로 간주됩니다. 이 상태의 모든 트윈 목록을 제공할 수 있는 쿼리가 없습니다. 그러나 영향을 받는 트윈을 확인하기 위해 삭제된 모델을 기준으로 트윈을 *쿼리할 수 있습니다*.

다음은 모델이 없는 트윈으로 수행할 수 있는 작업과 수행할 수 없는 작업에 대한 개요입니다.

수행 **할 수 있는** 작업:
* 트윈 쿼리
* 속성 읽기
* 나가는 관계 읽기
* 들어오는 관계 추가 및 삭제(다른 트윈은 여전히 이 트윈 *으로의* 관계를 형성할 수 있음)
  - 관계 정의의 `target`은 삭제된 모델의 DTMI를 계속 반영할 수 있습니다. 정의된 대상이 없는 관계도 여기서 작동할 수 있습니다.      
* 관계 삭제
* 트윈 삭제

수행 **할 수 없는** 작업:
* 나가는 관계 편집(이 트윈 *에서* 다른 트윈으로의 관계)
* 속성 편집

#### <a name="after-deletion-re-uploading-a-model"></a>삭제 후: 모델 다시 업로드

모델을 삭제한 후 나중에 삭제한 모델과 동일한 ID를 사용하여 새 모델을 업로드하도록 결정할 수 있습니다. 이 경우 다음과 같은 상황이 발생합니다.
* 솔루션 스토어의 관점에서 이는 완전히 새로운 모델을 업로드하는 것과 같습니다. 서비스는 이전에 업로드된 적이 있는지 기억하지 않습니다.   
* 삭제된 모델을 참조하는 그래프에 남아 있는 트윈이 있으면 더 이상 분리되지 않습니다. 새 정의에서 이 모델 ID를 다시 사용할 수 있습니다. 그러나 모델에 대한 새 정의가 삭제된 모델 정의와 다를 경우 이러한 트윈은 삭제된 정의와 일치하고 새 정의에는 맞지 않는 속성 및 관계를 가질 수 있습니다.

Azure Digital Twins는 이 상태를 방지하지 않으므로 모델 정의 스위치를 통해 유효하게 유지할 수 있도록 적절하게 트윈을 패치해야 합니다.

## <a name="next-steps"></a>다음 단계

모델을 기반으로 디지털 트윈을 만들고 관리하는 방법을 참조하세요.
* [방법: 디지털 트윈 관리](how-to-manage-twin.md)