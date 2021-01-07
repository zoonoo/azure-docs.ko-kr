---
title: 워크플로 정의 언어 스키마 참조
description: Azure Logic Apps에서 워크플로를 설명 하는 워크플로 정의 언어에 대 한 JSON 스키마 및 구문에 대 한 참조 가이드
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 71929cd449f4a00b91cc6c8620b33b0e0c6d506c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078141"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로 정의 언어에 대 한 스키마 참조 가이드

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 논리 앱을 만들 때 논리 앱에는 논리 앱에서 실행 되는 실제 논리를 설명 하는 기본 워크플로 정의가 있습니다. 해당 워크플로 정의는 [JSON](https://www.json.org/) 을 사용 하 고 워크플로 정의 언어 스키마에 의해 유효성을 검사 하는 구조를 따릅니다. 이 참조는이 구조와 스키마가 워크플로 정의에서 특성을 정의 하는 방법에 대 한 개요를 제공 합니다.

## <a name="workflow-definition-structure"></a>워크플로 정의 구조

워크플로 정의에는 항상 논리 앱을 인스턴스화하기 위한 트리거와 트리거가 발생 한 후 실행 되는 하나 이상의 작업이 포함 됩니다.

워크플로 정의에 대한 고급 구조는 다음과 같습니다.

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| attribute | 필수 | 설명 |
|-----------|----------|-------------|
| `definition` | 예 | 워크플로 정의에 대한 시작 요소 |
| `$schema` | 외부에서 워크플로 정의를 참조하는 경우만 | 워크플로 정의 언어의 버전을 설명하는 JSON 스키마 파일의 위치를 여기서 찾을 수 있습니다. <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | 아니요 | 워크플로 런타임에서 실행할 하나 이상의 작업에 대 한 정의입니다. 자세한 내용은 [트리거 및 작업](#triggers-actions)을 참조 하세요. <p><p>최대 작업: 250 |
| `contentVersion` | 아니요 | 워크플로 정의의 버전 번호는 기본적으로 “1.0.0.0”입니다. 워크플로 배포하는 경우 올바른 정의를 식별하고 확인하려면 사용할 값을 지정합니다. |
| `outputs` | 아니요 | 워크플로 실행에서 반환 되는 출력에 대 한 정의입니다. 자세한 내용은 [출력](#outputs)을 참조 하세요. <p><p>최대 출력: 10 |
| `parameters` | 아니요 | 논리 앱의 런타임에 사용할 값을 전달 하는 하나 이상의 매개 변수에 대 한 정의입니다. 자세한 내용은 [매개 변수](#parameters)를 참조하세요. <p><p>최대 매개 변수: 50 |
| `staticResults` | 아니요 | 이러한 작업에 정적 결과가 사용 되는 경우 작업에서 모의 출력으로 반환 하는 하나 이상의 정적 결과에 대 한 정의입니다. 각 작업 정의에서 특성은 `runtimeConfiguration.staticResult.name` 내부에서 해당 하는 정의를 참조 합니다 `staticResults` . 자세한 내용은 [정적 결과](#static-results)를 참조 하세요. |
| `triggers` | 아니요 | 워크플로를 인스턴트화하는 하나 이상의 트리거에 대한 정의 Logic Apps Designer를 사용하여 시각적으로가 아닌, 워크플로 정의 언어를 통해서만 하나 초과 트리거를 정의할 수 있습니다. 자세한 내용은 [트리거 및 작업](#triggers-actions)을 참조 하세요. <p><p>최대 트리거: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>트리거 및 작업

워크플로 정의에서 `triggers` 및 `actions` 섹션은 워크플로 실행 중에 발생하는 호출을 정의합니다. 구문 및 이러한 섹션에 대한 자세한 내용은 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조합니다.

<a name="parameters"></a>

## <a name="parameters"></a>매개 변수

배포 수명 주기는 일반적으로 개발, 테스트, 스테이징 및 프로덕션에 대해 서로 다른 환경을 포함 합니다. 다양 한 환경에 논리 앱을 배포할 때 배포 요구 사항에 따라 연결 문자열과 같은 다른 값을 사용 하는 것이 좋습니다. 또는 자주 하드 코딩 하지 않고 논리 앱 전체에서 다시 사용 하려는 값이 있거나 자주 변경 될 수 있습니다. 워크플로 정의의 섹션에서 `parameters` 런타임에 논리 앱이 사용 하는 값에 대 한 매개 변수를 정의 하거나 편집할 수 있습니다. 이러한 매개 변수를 먼저 정의 해야 워크플로 정의의 다른 위치에서 이러한 매개 변수를 참조할 수 있습니다.

매개 변수 정의에 대한 일반적인 구조는 다음과 같습니다.

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| attribute | 필수 | Type | 설명 |
|-----------|----------|------|-------------|
| <*매개 변수-이름*> | 예 | String | 정의 하려는 매개 변수의 이름입니다. |
| <*매개 변수 형식*> | 예 | int, float, string, bool, array, object, securestring, secureobject <p><p>**참고**: 모든 암호, 키 및 비밀의 경우 `securestring` `secureobject` `GET` 작업이 이러한 형식을 반환 하지 않으므로 또는 형식을 사용 합니다. 매개 변수 보안에 대 한 자세한 내용은 [작업 및 입력 매개 변수에 대 한 보안 권장 사항](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)을 참조 하세요. | 매개 변수의 형식 |
| <*기본 매개 변수 값*> | 예 | `type`과 동일 | 워크플로에서 인스턴스화할 때 값이 지정 되지 않은 경우 사용할 기본 매개 변수 값입니다. `defaultValue`이 특성은 논리 앱 디자이너에서 매개 변수를 올바르게 표시 하는 데 필요 하지만 빈 값을 지정할 수 있습니다. |
| <*허용 되는 매개 변수-값 배열*> | 아니요 | 배열 | 매개 변수를 수용할 수 있는 값으로 배열 |
| <*매개 변수-설명*> | 아니요 | JSON 개체 | 매개 변수에 대 한 설명과 같은 다른 모든 매개 변수 세부 정보 |
||||

그런 다음 워크플로 정의에 대 한 [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md) 만들고, 배포할 때 원하는 값을 허용 하는 템플릿 매개 변수를 정의 하 고, 하드 코드 된 값을 템플릿 또는 워크플로 정의 매개 변수에 대 한 참조로 바꾸고, 배포 시 사용할 값을 별도의 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)에 저장 합니다. 이렇게 하면 논리 앱을 업데이트 하 고 다시 배포 하지 않고도 매개 변수 파일을 통해 이러한 값을 보다 쉽게 변경할 수 있습니다. 사용자 이름, 암호 및 암호와 같은 중요 하거나 보안을 유지 해야 하는 정보는 Azure Key Vault에 해당 값을 저장 하 고 매개 변수 파일에서 키 자격 증명 모음에서 해당 값을 검색 하도록 할 수 있습니다. 템플릿 및 워크플로 정의 수준에서 매개 변수를 정의 하는 방법에 대 한 자세한 내용 및 예제는 [개요: Azure Resource Manager 템플릿을 사용 하 여 논리 앱에 대 한 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)를 참조 하세요.

<a name="static-results"></a>

## <a name="static-results"></a>정적 결과

특성에서 동작의 mock를 정의 하 고 작업의 `staticResults` `outputs` `status` 정적 결과 설정이 설정 된 경우 작업이 반환 되도록 합니다. 작업 정의에서 `runtimeConfiguration.staticResult.name` 특성은 내의 정적 결과 정의에 대 한 이름을 참조 합니다 `staticResults` . [정적 결과를 설정 하 여 모의 데이터를 사용 하 여 논리 앱을 테스트](../logic-apps/test-logic-apps-mock-data-static-results.md)하는 방법을 알아봅니다.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| attribute | 필수 | Type | 설명 |
|-----------|----------|------|-------------|
| <*정적-결과-정의-이름*> | 예 | String | 작업 정의가 개체를 통해 참조할 수 있는 정적 결과 정의의 이름입니다 `runtimeConfiguration.staticResult` . 자세한 내용은 [런타임 구성 설정](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)을 참조하세요. <p>원하는 고유한 이름을 사용할 수 있습니다. 기본적으로이 고유 이름에는 필요에 따라 증가 하는 숫자가 추가 됩니다. |
| <*출력-특성-값-반환 됨*> | 예 | 상황에 따라 다름 | 이러한 특성에 대 한 요구 사항은 다양 한 조건에 따라 달라 집니다. 예를 들어 `status` 가 인 경우 `Succeeded` 특성은 `outputs` 작업에서 모의 출력으로 반환 되는 특성 및 값을 포함 합니다. `status`가 인 경우 `Failed` 특성은 `outputs` `errors` 특성을 포함 하며,이 특성은 오류 정보가 있는 하나 이상의 오류 `message` 개체를 포함 하는 배열입니다. |
| <*헤더-값*> | 아니요 | JSON | 작업에서 반환 된 모든 헤더 값 |
| <*상태-코드 반환 됨*> | 예 | String | 작업에서 반환 된 상태 코드입니다. |
| <*작업-상태*> | 예 | String | 작업의 상태 (예: 또는)입니다. `Succeeded``Failed` |
|||||

예를 들어이 HTTP 작업 정의에서 특성은 `runtimeConfiguration.staticResult.name` `HTTP0` `staticResults` 작업에 대 한 모의 출력이 정의 된 특성 내에서 참조 됩니다. `runtimeConfiguration.staticResult.staticResultOptions`특성은 정적 결과 설정이 `Enabled` HTTP 동작에 있음을 지정 합니다.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

HTTP 작업은 내의 정의에 있는 출력을 반환 합니다 `HTTP0` `staticResults` . 이 예제에서 상태 코드의 경우 모의 출력은 `OK` 입니다. 헤더 값의 경우 모의 출력은 `"Content-Type": "application/JSON"` 입니다. 작업의 상태에 대 한 모의 출력은 `Succeeded` 입니다.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>식

Json을 사용하면 예를 들어 디자인 타임 시 존재하는 리터럴 값이 있을 수 있습니다.

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

실행 시간까지 존재하지 않는 값이 있을 수 있습니다. 이러한 값을 표시하려면 실행 시간에 평가되는 *식*을 사용할 수 있습니다. 식은 하나 이상의 [함수](#functions), [연산자](#operators), [변수](./logic-apps-create-variables-store-values.md), 명시적 값 또는 상수를 포함할 수 있는 시퀀스입니다. 워크플로 정의에서 식 앞에 at 기호(\@)를 붙여 JSON 문자열 값의 어디에서나 식을 사용할 수 있습니다. JSON 값을 나타내는 식을 계산하는 경우 식 본문은 다른 JSON 값에서 결과 및 \@ 문자를 제거하여 추출합니다.

예를 들어 이전에 정의된 `customerName` 속성의 경우 식에서 [매개 변수()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 함수를 사용하여 속성 값을 얻고 해당 값을 `accountName` 속성에 할당할 수 있습니다.

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*문자열 보간*은 \@ 문자와 중괄호({})로 래핑된 문자열 내에 여러 개의 식을 사용할 수 있게 합니다. 구문은 다음과 같습니다.

```json
@{ "<expression1>", "<expression2>" }
```

결과는 항상 문자열로서 예를 들어 이 기능을 `concat()` 함수와 유사하게 만듭니다. 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

\@ 문자로 시작하는 리터럴 문자열이 있는 경우 \@ 문자 앞에 이스케이프 문자(\@\@)로서 다른 \@ 문자를 붙입니다.

이러한 예제는 식의 평가 방식을 보여 줍니다.

| JSON 값 | 결과 |
|------------|--------|
| "Sophia Owen" | 이러한 문자 'Sophia Owen'을 반환 |
| "배열[1]" | 이러한 문자 '배열[1]'을 반환 |
| "\@\@" | 이러한 문자를 한 문자열 '\@'로 반환 |
| " \@" | 이러한 문자를 두 문자열 '\@'로 반환 |
|||

이 예제에서는 "myBirthMonth"를 "1월", "myAge"를 숫자 42와 동일하게 정의하는 것으로 가정합니다.

```json
"myBirthMonth": "January",
"myAge": 42
```

이러한 예제는 다음 식의 평가 방식을 보여 줍니다.

| JSON 식 | 결과 |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | 이 문자열 "1월"을 반환 |
| "\@{parameters('myBirthMonth')}" | 이 문자열 "1월"을 반환 |
| "\@parameters('myAge')" | 이 숫자 42를 반환 |
| "\@{parameters('myAge')}" | 이 숫자 "42"를 문자열로 반환 |
| "내 나이는 \@{parameters('myAge')}살" | 이 문자열 "내 나이는 42살"을 반환 |
| "\@('내 나이는 ', string(parameters('myAge')))" | 이 문자열 "내 나이는 42살"을 반환 |
| "내 나이는 \@\@{parameters('myAge')}살" | 식이 포함된 이 문자열 "내 나이는 \@{parameters('myAge')}살'을 반환 |
|||

Logic Apps Designer에서 시각적으로 작업하는 경우 예를 통해 식 작성기를 통해 식을 만들 수 있습니다.

![Logic Apps Designer > 식 작성기](./media/logic-apps-workflow-definition-language/expression-builder.png)

완료되면 워크플로 정의에서 해당 속성, 예를 들어 여기서는 `searchQuery` 속성에 대해 식이 표시됩니다.

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>outputs

`outputs` 섹션에서 워크플로가 실행을 종료할 때 반환할 수 있는 데이터를 정의합니다. 예를 들어, 각 실행에서 특정 상태 또는 값을 추적하려면 워크플로 출력이 해당 데이터를 반환하도록 지정합니다.

> [!NOTE]
> 서비스의 REST API에서 들어오는 요청에 응답하는 경우 `outputs`을 사용하지 마세요. 대신 `Response` 작업 형식을 사용합니다. 자세한 내용은 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조하세요.

출력 정의에 대한 일반적인 구조는 다음과 같습니다.

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| attribute | 필수 | Type | 설명 |
|-----------|----------|------|-------------|
| <*키 이름*> | 예 | String | 출력 반환 값에 대한 키 이름 |
| <*키 유형*> | 예 | int, float, 문자열, securestring, bool, 배열, JSON 개체 | 출력 반환 값의 형식 |
| <*key-value*> | 예 | <*키 형식과* 동일> | 출력 반환 값 |
|||||

워크플로 실행의 출력을 가져오려면 Azure Portal에서 논리 앱의 실행 기록 및 세부 정보를 검토 하거나 [워크플로 REST API](/rest/api/logic/workflows)를 사용 합니다. 또한 대시보드를 만들 수 있도록 외부 시스템, 예를 들어 PowerBI에 출력을 전달할 수 있습니다.

<a name="operators"></a>

## <a name="operators"></a>연산자

[식](#expressions) 및 [함수](#functions)에서 연산자는 속성 참조 또는 배열의 값 등의 특정 작업을 수행합니다.

| 연산자 | Task |
|----------|------|
| ' | 리터럴 문자열을 입력으로 또는 식 및 함수에서 사용하려면 작은따옴표, 예를 들어 `'<myString>'`만 사용하여 문자열을 래핑합니다. 전체 식에서 JSON 서식과 충돌하는 큰따옴표("")를 사용하지 마세요. 예를 들면 다음과 같습니다. <p>**예**: length('Hello') </br>**아니요**: length('Hello') <p>배열이나 숫자를 전달할 때 문장 부호를 래핑하지 않아도 됩니다. 예를 들면 다음과 같습니다. <p>**예**: length([1, 2, 3]) </br>**아니요**: length([1, 2, 3]) |
| [] | 배열의 특정 위치(인덱스)에서 값을 참조하려면 대괄호를 사용합니다. 예를 들어 배열에서 두 번째 항목을 가져오려면: <p>`myArray[1]` |
| . | 개체의 속성을 참조하려면 점 연산자를 사용합니다. 예를 들어 `customer` JSON 개체에 대한 `name` 속성을 가져오려면: <p>`"@parameters('customer').name"` |
| ? | 런타임 오류 없이 개체의 null 속성을 참조하려면 물음표 연산자를 사용합니다. 예를 들어 트리거에서 null 출력을 처리하려면 이 식을 사용할 수 있습니다. <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

일부 식에서는 워크플로 정의가 실행 되기 시작할 때 아직 존재 하지 않을 수 있는 런타임 작업에서 해당 값을 가져옵니다. 식에서 이러한 값을 사용하거나 참조하려면 워크플로 정의 언어가 제공하는 [*함수*](../logic-apps/workflow-definition-language-functions-reference.md)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [워크플로 정의 언어 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md)에 대해 알아봅니다
* [워크플로 REST API](/rest/api/logic/workflows)를 사용하여 프로그래밍 방식으로 논리 앱을 만들고 관리하는 방법에 대해 알아봅니다
