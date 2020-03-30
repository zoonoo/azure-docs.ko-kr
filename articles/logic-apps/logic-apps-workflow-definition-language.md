---
title: 워크플로 정의 언어 스키마 참조
description: Azure Logic Apps의 워크플로를 설명하는 워크플로 정의 언어에 대한 JSON 스키마 및 구문에 대한 참조 가이드
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283863"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Azure 논리 앱의 워크플로 정의 언어에 대한 스키마 참조 가이드

[Azure Logic Apps에서](../logic-apps/logic-apps-overview.md)논리 앱을 만들 때 논리 앱에는 논리 앱에서 실행되는 실제 논리를 설명하는 기본 워크플로 정의가 있습니다. 해당 워크플로 [정의는 JSON을](https://www.json.org/) 사용하고 워크플로 정의 언어 스키마에 의해 유효성을 검사하는 구조를 따릅니다. 이 참조는 이 구조와 스키마가 워크플로 정의에서 특성을 정의하는 방법에 대한 개요를 제공합니다.

## <a name="workflow-definition-structure"></a>워크플로 정의 구조

워크플로 정의에는 항상 논리 앱을 인스턴스화하기 위한 트리거와 트리거가 실행된 후 실행되는 하나 이상의 작업이 포함됩니다.

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

| 특성 | 필수 | 설명 |
|-----------|----------|-------------|
| `definition` | yes | 워크플로 정의에 대한 시작 요소 |
| `$schema` | 외부에서 워크플로 정의를 참조하는 경우만 | 워크플로 정의 언어의 버전을 설명하는 JSON 스키마 파일의 위치를 여기서 찾을 수 있습니다. <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | 예 | 워크플로 런타임에서 실행할 하나 이상의 작업에 대한 정의입니다. 자세한 내용은 [트리거 및 작업을](#triggers-actions)참조하십시오. <p><p>최대 작업: 250 |
| `contentVersion` | 예 | 워크플로 정의의 버전 번호는 기본적으로 “1.0.0.0”입니다. 워크플로 배포하는 경우 올바른 정의를 식별하고 확인하려면 사용할 값을 지정합니다. |
| `outputs` | 예 | 워크플로 실행에서 반환할 출력에 대한 정의입니다. 자세한 내용은 [출력 을](#outputs)참조하십시오. <p><p>최대 출력: 10 |
| `parameters` | 예 | 논리 앱의 런타임에서 사용할 값을 전달하는 하나 이상의 매개 변수에 대한 정의입니다. 자세한 내용은 [매개 변수](#parameters)를 참조하세요. <p><p>최대 매개 변수: 50 |
| `staticResults` | 예 | 이러한 작업에서 정적 결과가 활성화될 때 모의 출력으로 작업으로 반환되는 하나 이상의 정적 결과에 대한 정의입니다. 각 작업 정의에서 `runtimeConfiguration.staticResult.name` 특성은 내부의 `staticResults`해당 정의를 참조합니다. 자세한 내용은 [정적 결과를](#static-results)참조하십시오. |
| `triggers` | 예 | 워크플로를 인스턴트화하는 하나 이상의 트리거에 대한 정의 Logic Apps Designer를 사용하여 시각적으로가 아닌, 워크플로 정의 언어를 통해서만 하나 초과 트리거를 정의할 수 있습니다. 자세한 내용은 [트리거 및 작업을](#triggers-actions)참조하십시오. <p><p>최대 트리거: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>트리거 및 작업

워크플로 정의에서 `triggers` 및 `actions` 섹션은 워크플로 실행 중에 발생하는 호출을 정의합니다. 구문 및 이러한 섹션에 대한 자세한 내용은 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조합니다.

<a name="parameters"></a>

## <a name="parameters"></a>매개 변수

배포 수명 주기에는 일반적으로 개발, 테스트, 스테이징 및 프로덕션을 위한 서로 다른 환경이 있습니다. 다양한 환경에 논리 앱을 배포할 때 배포 요구 사항에 따라 연결 문자열과 같은 다른 값을 사용하려고 할 수 있습니다. 또는 하드 코딩 없이 논리 앱 전체에서 재사용하려는 값이 있거나 자주 변경될 수 있습니다. 워크플로 정의 `parameters` 섹션에서 로직 앱이 런타임에 사용하는 값에 대한 매개 변수를 정의하거나 편집할 수 있습니다. 워크플로 정의의 다른 곳에서 이러한 매개 변수를 참조하려면 먼저 이러한 매개 변수를 정의해야 합니다.

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

| 특성 | 필수 | Type | Description |
|-----------|----------|------|-------------|
| <*매개 변수 이름*> | yes | String | 정의할 매개 변수의 이름입니다. |
| <*매개 변수 유형*> | yes | int, 플로트, 문자열, bool, 배열, 개체, 보안 문자열, 보안 개체 <p><p>**참고**: 모든 암호, 키 및 암호의 `secureobject` 경우 `GET` 작업이 이러한 형식을 반환하지 않으므로 `securestring` 또는 형식을 사용합니다. 매개 변수 보안에 대한 자세한 내용은 [작업 및 입력 매개 변수에 대한 보안 권장 사항을](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)참조하십시오. | 매개 변수의 형식 |
| <*기본 매개 변수 값*> | yes | `type`과 동일 | 워크플로가 인스턴스화될 때 값이 지정되지 않은 경우 사용할 기본 매개 변수 값입니다. 논리 `defaultValue` 앱 디자이너가 매개 변수를 올바르게 표시할 수 있지만 빈 값을 지정할 수 있도록 특성이 필요합니다. |
| <*허용된 매개 변수-값을 가진 배열*> | 예 | Array | 매개 변수를 수용할 수 있는 값으로 배열 |
| <*매개 변수 설명*> | 예 | JSON 개체 | 매개 변수에 대한 설명과 같은 기타 매개 변수 세부 정보 |
||||

그런 다음 워크플로 정의에 대한 [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md) 만들고, 배포 시 원하는 값을 받아들이는 템플릿 매개 변수를 정의하고, 하드 코딩된 값을 적절하게 템플릿 또는 워크플로 정의 매개 변수에 대한 참조로 바꾸고, 배포시 사용할 값을 별도의 [매개 변수 파일에](../azure-resource-manager/templates/parameter-files.md)저장합니다. 이렇게 하면 논리 앱을 업데이트하고 다시 배포하지 않고도 매개 변수 파일을 통해 해당 값을 보다 쉽게 변경할 수 있습니다. 사용자 이름, 암호 및 암호와 같이 중요하거나 보호해야 하는 정보의 경우 Azure Key Vault에 해당 값을 저장하고 매개 변수 파일이 키 자격 증명 모음에서 해당 값을 검색하도록 할 수 있습니다. 템플릿 및 워크플로 정의 수준에서 매개 변수 정의에 대한 자세한 내용 및 예제는 [개요: Azure 리소스 관리자 템플릿을 사용한 논리 앱에 대한 배포 자동화를](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)참조하십시오.

<a name="static-results"></a>

## <a name="static-results"></a>정적 결과

특성에서 `staticResults` 작업의 모의 `outputs` 를 정의하고 `status` 작업의 정적 결과 설정이 켜져 있을 때 작업이 반환됩니다. 작업 정의에서 특성은 `runtimeConfiguration.staticResult.name` 내부의 `staticResults`정적 결과 정의에 대한 이름을 참조합니다. 정적 결과를 [설정하여 모의 데이터로 논리 앱을 테스트하는](../logic-apps/test-logic-apps-mock-data-static-results.md)방법에 대해 알아봅니다.

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

| 특성 | 필수 | Type | Description |
|-----------|----------|------|-------------|
| <*정적 결과 정의 이름*> | yes | String | 작업 정의가 개체를 통해 참조할 수 `runtimeConfiguration.staticResult` 있는 정적 결과 정의의 이름입니다. 자세한 내용은 [런타임 구성 설정](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)을 참조하세요. <p>원하는 고유 이름을 사용할 수 있습니다. 기본적으로 이 고유 이름은 필요에 따라 증분되는 숫자와 함께 추가됩니다. |
| <*출력 속성 및 값 반환*> | yes | 다양함 | 이러한 특성에 대한 요구 사항은 조건에 따라 다릅니다. 예를 들어, `status` 는 `Succeeded`) `outputs` 이 때 특성에는 동작에 의해 모의 출력으로 반환되는 특성과 값이 포함됩니다. `status` 의 `Failed`경우 `outputs` 속성에는 오류 `errors` 정보가 있는 하나 이상의 오류 `message` 개체가 있는 배열인 특성이 포함됩니다. |
| <*헤더 값*> | 예 | JSON | 작업에서 반환되는 모든 헤더 값 |
| <*상태 코드 반환*> | yes | String | 작업에서 반환되는 상태 코드 |
| <*작업 상태*> | yes | String | 예를 들어, 액션의 상태 `Succeeded` 또는`Failed` |
|||||

예를 들어 이 HTTP 작업 `runtimeConfiguration.staticResult.name` 정의에서 `HTTP0` 특성은 작업에 대한 모의 출력이 정의된 `staticResults` 특성 내부를 참조합니다. 특성은 `runtimeConfiguration.staticResult.staticResultOptions` 정적 결과 설정이 `Enabled` HTTP 작업에 있음을 지정합니다.

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

HTTP 작업은 내부 `HTTP0` `staticResults`정의의 출력을 반환합니다. 이 예제에서는 상태 코드의 경우 모의 출력은 `OK`입니다. 헤더 값의 경우 모의 출력은 `"Content-Type": "application/JSON"`. 작업의 상태에 대해 모의 출력은 `Succeeded`입니다.

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

## <a name="expressions"></a>표현식

Json을 사용하면 예를 들어 디자인 타임 시 존재하는 리터럴 값이 있을 수 있습니다.

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

실행 시간까지 존재하지 않는 값이 있을 수 있습니다. 이러한 값을 표시하려면 실행 시간에 평가되는 *식*을 사용할 수 있습니다. 식은 하나 이상의 [함수](#functions), [연산자](#operators), 변수, 명시적인 값 또는 상수를 포함하는 시퀀스입니다. 워크플로 정의에서 식 앞에 at 기호(\@)를 붙여 JSON 문자열 값의 어디에서나 식을 사용할 수 있습니다. JSON 값을 나타내는 식을 계산하는 경우 식 본문은 다른 JSON 값에서 결과 및 \@ 문자를 제거하여 추출합니다.

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

| 특성 | 필수 | Type | Description |
|-----------|----------|------|-------------|
| <*키 이름*> | yes | String | 출력 반환 값에 대한 키 이름 |
| <*키 유형*> | yes | int, float, 문자열, securestring, bool, 배열, JSON 개체 | 출력 반환 값의 형식 |
| <*키 값*> | yes | <*키 타입과* 동일> | 출력 반환 값 |
|||||

워크플로 실행에서 출력을 얻으려면 Azure 포털에서 논리 앱의 실행 기록 및 세부 정보를 검토하거나 [워크플로 REST API를](https://docs.microsoft.com/rest/api/logic/workflows)사용합니다. 또한 대시보드를 만들 수 있도록 외부 시스템, 예를 들어 PowerBI에 출력을 전달할 수 있습니다.

<a name="operators"></a>

## <a name="operators"></a>연산자

[식](#expressions) 및 [함수](#functions)에서 연산자는 속성 참조 또는 배열의 값 등의 특정 작업을 수행합니다.

| 연산자 | Task |
|----------|------|
| ' | 리터럴 문자열을 입력으로 또는 식 및 함수에서 사용하려면 작은따옴표, 예를 들어 `'<myString>'`만 사용하여 문자열을 래핑합니다. 전체 식에서 JSON 서식과 충돌하는 큰따옴표("")를 사용하지 마세요. 예를 들어: <p>**예**: length('Hello') </br>**아니요**: length('Hello') <p>배열이나 숫자를 전달할 때 문장 부호를 래핑하지 않아도 됩니다. 예를 들어: <p>**예**: length([1, 2, 3]) </br>**아니요**: length([1, 2, 3]) |
| [] | 배열의 특정 위치(인덱스)에서 값을 참조하려면 대괄호를 사용합니다. 예를 들어 배열에서 두 번째 항목을 가져오려면: <p>`myArray[1]` |
| . | 개체의 속성을 참조하려면 점 연산자를 사용합니다. 예를 들어 `customer` JSON 개체에 대한 `name` 속성을 가져오려면: <p>`"@parameters('customer').name"` |
| ? | 런타임 오류 없이 개체의 null 속성을 참조하려면 물음표 연산자를 사용합니다. 예를 들어 트리거에서 null 출력을 처리하려면 이 식을 사용할 수 있습니다. <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>함수

일부 식은 워크플로 정의가 실행되기 시작할 때 아직 존재하지 않을 수 있는 런타임 작업에서 해당 값을 가져옵니다. 식에서 이러한 값을 사용하거나 참조하려면 워크플로 정의 언어가 제공하는 [*함수*](../logic-apps/workflow-definition-language-functions-reference.md)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [워크플로 정의 언어 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md)에 대해 알아봅니다
* [워크플로 REST API](https://docs.microsoft.com/rest/api/logic/workflows)를 사용하여 프로그래밍 방식으로 논리 앱을 만들고 관리하는 방법에 대해 알아봅니다
