---
title: Azure Monitor 로그 쿼리의 함수
description: 이 문서에서는 함수를 사용하여 Azure Monitor의 한 쿼리에서 다른 로그 쿼리를 호출하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752856"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리의 함수
함수는 명령인 것처럼 다른 로그 쿼리에서 사용할 수 있는 Azure Monitor의 로그 쿼리입니다. 함수를 통해 개발자는 다양한 고객에게 솔루션을 제공할 수 있으며 사용자는 자체 환경에서 쿼리 논리를 다시 사용할 수 있습니다. 이 문서에서는 함수를 사용하는 방법과 자체 함수를 만드는 방법에 대해 자세히 설명합니다.

## <a name="types-of-functions"></a>함수 유형
Azure Monitor에는 두 가지 유형의 함수가 있습니다.

- **솔루션 함수:** Azure Monitor에 포함된 미리 작성된 함수입니다. 모든 Log Analytics 작업 영역에서 사용할 수 있으며 수정할 수 없습니다.
- **작업 영역 함수:** 특정 Log Analytics 작업 영역에 설치된 함수이며 사용자가 수정 및 제어할 수 있습니다.

## <a name="viewing-functions"></a>함수 보기
Log Analytics 작업 영역의 왼쪽 창에 있는 **함수** 탭에서 현재 작업 영역에 있는 솔루션 함수 및 작업 영역 함수를 볼 수 있습니다. **필터** 단추를 사용하여 목록에 포함된 함수를 필터링하고 **그룹화 기준** 을 사용하여 그룹화를 변경할 수 있습니다. 특정 함수를 찾으려면 **검색** 상자에 문자열을 입력합니다. 함수를 마우스로 가리키면 설명 및 매개 변수를 포함한 세부 정보를 볼 수 있습니다.

:::image type="content" source="media/functions/view-functions.png" alt-text="함수 보기" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>함수 사용
명령에 입력하는 것처럼 쿼리에 함수의 이름을 매개 변수 값과 함께 입력하여 함수를 사용할 수 있습니다. 함수의 출력은 결과로 반환되거나 다른 명령으로 파이프될 수 있습니다.

함수 이름을 두 번 클릭하거나 마우스로 가리키고 **편집기에서 사용** 을 선택하면 현재 쿼리에 함수를 추가할 수 있습니다. 쿼리를 입력할 때 작업 영역의 함수도 Intellisense에 포함됩니다. 

쿼리에 매개 변수가 필요하면 다음 구문을 사용하여 제공합니다. `function_name(param1,param2,...)`.

:::image type="content" source="media/functions/function-use.png" alt-text="함수 사용" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>함수 만들기
편집기의 현재 쿼리에서 함수를 만들려면 **저장**, **함수로 저장** 을 차례로 선택합니다. 

:::image type="content" source="media/functions/function-save.png" alt-text="함수 만들기" lightbox="media/functions/function-save.png":::

**저장** 을 클릭한 후, 다음 표의 정보를 지정하여 Azure Portal에서 Log Analytics로 함수를 만듭니다.

| 설정 | 설명 |
|:---|:---|
| 함수 이름  | 함수의 이름입니다. 공백이나 특수 문자가 포함될 수 없습니다. 또한, 밑줄(_)로 시작할 수 없습니다. 이 문자는 솔루션 함수용으로 예약되어 있기 때문입니다. |
| 레거시 범주 | 함수를 필터링하고 그룹화하는 데 도움이 되는 사용자 정의 범주입니다.   |
| 컴퓨터 그룹으로 저장 | 쿼리를 [컴퓨터 그룹](computer-groups.md)으로 저장합니다.  |
| 매개 변수 | 함수에서 사용할 때 값이 필요한 각 변수에 대한 매개 변수를 추가합니다. 자세한 내용은 [함수 매개 변수](#function-parameters)를 참조하세요. |

:::image type="content" source="media/functions/function-details.png" alt-text="함수 세부 정보" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>함수 매개 변수 
함수를 호출할 때 특정 변수에 대한 값을 제공할 수 있도록 함수에 매개 변수를 추가할 수 있습니다. 이렇게 하면 서로 다른 쿼리에서 동일한 함수를 사용하면서 각 매개 변수에 다른 값을 제공할 수 있습니다. 매개 변수는 다음 속성으로 정의됩니다.

| 설정 | 설명 |
|:---|:---|
| Type  | 값의 데이터 유형입니다. |
| 속성  | 매개 변수의 이름입니다. 매개 변수 값으로 대체하기 위해 쿼리에 사용해야 하는 이름입니다.  |
| 기본값 | 값이 제공되지 않으면 매개 변수에 사용할 값입니다. |

매개 변수는 기본값이 있는 매개 변수 앞에 기본값이 없는 매개 변수를 사용하여 생성될 때 순서가 지정됩니다.

## <a name="working-with-function-code"></a>함수 코드 작업
함수의 코드를 살펴보고 작동 방식에 대한 인사이트를 얻거나 작업 영역 함수의 코드를 수정할 수 있습니다. 편집기에서 현재 쿼리에 함수 코드를 추가하려면 **함수 코드 로드** 를 선택합니다. 빈 쿼리나 기존 쿼리의 첫 번째 줄에 추가하면 탭에 함수 이름이 추가됩니다. 작업 영역 함수인 경우 함수 정보를 편집할 수 있는 옵션이 활성화됩니다.

:::image type="content" source="media/functions/function-code.png" alt-text="함수 코드 로드" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>함수 편집
새 쿼리를 만든 다음, 함수 이름을 마우스로 가리키고 **함수 코드 로드** 를 선택하여 함수의 속성 또는 코드를 편집할 수 있습니다. 코드를 원하는 대로 수정하고 **저장** 을 선택한 다음, **함수 세부 정보 편집** 을 선택합니다. 함수의 속성과 매개 변수를 원하는 대로 변경한 다음, **저장** 을 클릭합니다.

:::image type="content" source="media/functions/function-edit.png" alt-text="함수 편집" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>예제
다음 샘플 함수는 특정 범주와 일치하는 특정 날짜 이후의 Azure 활동 로그의 모든 이벤트를 반환합니다. 

하드코딩된 값을 사용하여 다음 쿼리로 시작합니다. 쿼리가 예상대로 작동하는지 확인합니다.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="예제 함수 - 초기 쿼리" lightbox="media/functions/example-query.png":::

다음으로, 하드코딩된 값을 매개 변수 이름으로 바꾼 다음, **저장** 및 **함수로 저장** 을 차례로 선택하여 함수를 저장합니다.

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="예제 함수 - 함수 저장" lightbox="media/functions/example-save-function.png":::

 함수 속성에 대해 다음 값을 제공합니다.

| 속성 | 값 |
|:---|:---|
| 함수 이름 | AzureActivityByCategory |
| 레거시 범주 | 데모 함수 |

함수를 저장하기 전에 다음 매개 변수를 정의합니다.

| Type | 속성 | 기본값 |
|:---|:---|:---|
| 문자열   | CategoryParam | "Administrative" |
| Datetime | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="예제 함수 - 함수 속성" lightbox="media/functions/example-function-properties.png":::

새 쿼리를 만들고 마우스로 가리켜서 새 함수를 살펴봅니다. 매개 변수의 순서에 유의합니다. 함수를 사용할 때 지정해야 하는 순서이기 때문입니다.

:::image type="content" source="media/functions/example-view-details.png" alt-text="예제 함수 - 세부 정보 보기" lightbox="media/functions/example-view-details.png":::

**편집기에서 사용** 을 선택하여 쿼리에 새 함수를 추가한 다음, 매개변수에 대한 값을 추가합니다. CategoryParam에는 기본값이 있으므로 값을 지정할 필요가 없습니다.

:::image type="content" source="media/functions/example-use-function.png" alt-text="예제 함수 - 함수 사용" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>다음 단계
Azure Monitor 로그 쿼리 작성에 대한 다른 단원을 참조하세요.

- [문자열 작업](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

