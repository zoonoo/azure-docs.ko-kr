---
title: "워크플로 정의 언어 스키마 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps에 대한 워크플로 정의 스키마에 따라 워크플로 정의"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 09ccdaa18d383569747612c33b63595c06a0131e
ms.lasthandoff: 03/28/2017

---

# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Azure Logic Apps에 대한 워크플로 정의 언어 스키마

워크플로 정의는 논리 앱의 일부로 실행할 실제 논리를 포함합니다. 이 정의는 논리 앱을 실행하는 트리거와 논리 앱에 대해 수행할 작업을 각각 하나 이상 포함합니다.  
  
## <a name="basic-workflow-definition-structure"></a>기본 워크플로 정의 구조

워크플로 정의의 기본 구조는 다음과 같습니다.  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> [워크플로 관리 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 설명서에는 논리 앱 워크플로를 만들고 관리하는 방법에 대한 정의가 들어 있습니다.
  
|요소 이름|필수|설명|  
|------------------|--------------|-----------------|  
|$schema|아니요|정의 언어의 버전을 설명하는 JSON 스키마 파일의 위치를 지정합니다. 정의를 외부에서 참조할 때 이 위치가 필요합니다. 이 문서에서 위치는 다음과 같습니다. <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|아니요|정의 버전을 지정합니다. 정의를 사용하여 워크플로를 배포할 때 이 값을 사용하여 올바른 정의가 사용되는지 확인할 수 있습니다.|  
|매개 변수|아니요|정의에 데이터를 입력하는 데 사용되는 매개 변수를 지정합니다. 최대 50개의 매개 변수를 정의할 수 있습니다.|  
|트리거|아니요|워크플로를 시작하는 트리거에 대한 정보를 지정합니다. 최대 250개의 트리거를 정의할 수 있습니다.|  
|actions|아니요|흐름이 실행될 때 수행할 작업을 지정합니다. 최대 250개의 작업을 정의할 수 있습니다.|  
|outputs|아니요|배포된 리소스에 대한 정보를 지정합니다. 최대 10개의 출력을 정의할 수 있습니다.|  
  
## <a name="parameters"></a>매개 변수

이 섹션에서는 배포 시 워크플로 정의에 사용되는 모든 매개 변수를 지정합니다. 이 섹션에 모든 매개 변수를 먼저 선언해야 정의의 다른 섹션에 사용할 수 있습니다.  
  
다음 예제에서는 매개 변수 정의의 구조를 보여 줍니다.  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|요소 이름|필수|설명|  
|------------------|--------------|-----------------|  
|type|예|**형식**: string <p> **선언**: `"parameters": {"parameter1": {"type": "string"}` <p> **사양**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **형식**: securestring <p> **선언**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **사양**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **형식**: int <p> **선언**: `"parameters": {"parameter1": {"type": "int"}}` <p> **사양**: `"parameters": {"parameter1": {"value" : 5}}` <p> **형식**: bool <p> **선언**: `"parameters": {"parameter1": {"type": "array"}}` <p> **사양**: `"parameters": {"parameter1": { "value": true }}` <p> **형식**: array <p> **선언**: `"parameters": {"parameter1": {"type": "array"}}` <p> **사양**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **형식**: object <p> **선언**: `"parameters": {"parameter1": {"type": "object"}}` <p> **사양**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **형식**: secureobject <p> **선언**: `"parameters": {"parameter1": {"type": "object"}}` <p> **사양**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **참고:** `securestring` 및 `secureobject` 형식은 `GET` 작업에서 반환되지 않습니다. 모든 암호, 키 및 비밀은 이 형식을 사용해야 합니다.|  
|defaultValue|아니요|리소스를 만들 때 값이 지정되지 않으면 매개 변수에 대한 기본값을 지정합니다.|  
|allowedValues|아니요|매개 변수에 대해 허용된 값 배열을 지정합니다.|  
|metadata|아니요|Visual Studio 또는 다른 도구에서 사용하는 읽을 수 있는 설명이나 디자인 시간 데이터와 같은 매개 변수에 대한 추가 정보를 지정합니다.|  
  
이 예제에서는 작업의 본문 섹션에 매개 변수를 사용하는 방법을 보여 줍니다.  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 매개 변수는 출력에도 사용할 수 있습니다.  
  
## <a name="triggers-and-actions"></a>트리거 및 작업  

트리거 및 작업은 워크플로 실행에 참여할 수 있는 호출을 지정합니다. 이 섹션에 대한 자세한 내용은 [워크플로 작업 및 트리거](logic-apps-workflow-actions-triggers.md)를 참조하세요.
  
## <a name="outputs"></a>outputs  

출력은 워크플로 실행에서 반환할 수 있는 정보를 지정합니다. 예를 들어 각 실행에 대해 추적할 특정 상태 또는 값이 있는 경우 실행 출력에 데이터를 포함할 수 있습니다. 데이터는 해당 실행에 대한 관리 REST API 및 Azure Portal에서 실행에 대한 관리 UI에 나타납니다. 대시보드를 만들기 위해 이러한 출력을 PowerBI와 같은 다른 외부 시스템으로 전달할 수도 있습니다. 출력은 서비스 REST API에 대해 들어오는 요청에 응답하는 데 사용되지 *않습니다.* `response` 작업 유형을 사용하여 들어오는 요청에 응답하는 예는 다음과 같습니다.
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|요소 이름|필수|설명|  
|------------------|--------------|-----------------|  
|key1|예|출력에 대한 키 식별자를 지정합니다. **key1**을 출력을 식별하는 데 사용할 이름으로 바꿉니다.|  
|값|예|출력의 값을 지정합니다.|  
|type|예|지정된 값 형식을 지정합니다. 가능한 값 형식은 다음과 같습니다. <p>- `string`<br />- `securestring`<br />- `int`<br />- `bool`<br />- `array`<br />- `object`|
  
## <a name="expressions"></a>식  

정의의 JSON 값은 리터럴일 수도 있고 정의가 사용될 때 평가되는 식일 수도 있습니다. 예:  
  
```json
"name": "value"
```

 또는  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> 일부 식은 실행 시작 시 존재하지 않을 수도 있는 런타임 작업에서 해당 값을 가져옵니다. **함수**를 사용하여 이러한 값 일부를 검색할 수 있습니다.  
  
식은 JSON 문자열 값에서 어느 위치에나 나타날 수 있으며 그 결과 항상 다른 JSON 값이 발생합니다. JSON 값이 식으로 판별되면 at 기호(@)를 제거하여 식의 본문을 추출합니다. @로 시작하는 리터럴 문자열이 필요한 경우 해당 문자열은 @@를 사용하여 이스케이프해야 합니다. 다음 예제는 식의 작동 방식을 보여 줍니다.  
  
|JSON 값|결과|  
|----------------|------------|  
|"parameters"|'parameters' 문자가 반환됩니다.|  
|"parameters[1]"|'parameters[1]' 문자가 반환됩니다.|  
|"@@"|'@'를 포함하는 1개 문자열이 반환됩니다.|  
|" @"|' @'를 포함하는 2개 문자열이 반환됩니다.|  
  
*문자열 보간*을 사용하면 식이 `@{ ... }`로 묶인 문자열 내부에 나타날 수도 있습니다. 예: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName'}"`

결과는 항상 문자열이며 이 기능은 `concat` 함수와 유사합니다. `myNumber`를 `42`로, `myString`을 `sampleString`으로 정의했다고 가정해 보겠습니다.  
  
|JSON 값|결과|  
|----------------|------------|  
|"@parameters('myString')"|`sampleString`을 문자열로 반환합니다.|  
|"@{parameters('myString')}"|`sampleString`을 문자열로 반환합니다.|  
|"@parameters('myNumber')"|`42`를 *숫자*로 반환합니다.|  
|"@{parameters('myNumber')}"|`42`를 *문자열*로 반환합니다.|  
|"Answer is: @{parameters('myNumber')}"|`Answer is: 42` 문자열을 반환합니다.|  
|"@concat('Answer is: ', string(parameters('myNumber')))"|`Answer is: 42` 문자열을 반환합니다.|  
|"Answer is: @@{parameters('myNumber')}"|`Answer is: @{parameters('myNumber')}` 문자열을 반환합니다.|  
  
## <a name="operators"></a>연산자  

연산자는 식 또는 함수 내에 사용할 수 있는 문자입니다. 
  
|연산자|설명|  
|--------------|-----------------|  
|을 참조하세요.|점 연산자를 통해 개체의 속성을 참조할 수 있습니다.|  
|?|물음표 연산자를 통해 런타임 오류 없이 개체의 null 속성을 참조할 수 있습니다. 예를 들어 이 식을 사용하여 null 트리거 출력을 처리할 수 있습니다. <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|작은따옴표는 문자열 리터럴을 감싸는 유일한 방법입니다. 이 문장 부호는 전체 식을 감싸는 JSON 따옴표와 충돌하기 때문에 식 안에 큰따옴표를 사용할 수 없습니다.|  
|[]|특정 인덱스의 배열에서 값을 가져오는 데 대괄호를 사용할 수 있습니다. 예를 들어 `range(0,10)`을 `forEach` 함수로 전달하는 작업이 있는 경우 이 함수를 사용하여 배열에서 항목을 가져올 수 있습니다.  <p>`myArray[item()]`|  
  
## <a name="functions"></a>함수  

또한 식 내에서 함수를 호출할 수도 있습니다. 다음 표에서는 식에 사용할 수 있는 함수를 보여 줍니다.  
  
|식|평가|  
|----------------|----------------|  
|"@function('Hello')"|첫 번째 매개 변수로 Hello 리터럴 문자열이 있는 정의를 포함하는 함수 멤버를 호출합니다.|  
|"@function('It's Cool!')"|첫 번째 매개 변수로 'It's Cool!' 리터럴 문자열이 있는 정의를 포함하는 함수 멤버를 호출합니다.|  
|"@function().prop1"|정의의 `myfunction` 멤버에서 prop1 속성 값을 반환합니다.|  
|"@function('Hello').prop1"|첫 번째 매개 변수로 'Hello' 리터럴 문자열이 있는 정의를 포함하는 함수 멤버를 호출하고 개체의 prop1 속성을 반환합니다.|  
|"@function(parameters('Hello'))"|Hello 매개 변수를 평가하고 값을 함수에 전달합니다.|  
  
### <a name="referencing-functions"></a>참조 함수  

논리 앱의 다른 작업에서 출력을 참조하거나 논리 앱이 생성될 때 전달된 값을 참조할 때 이 함수를 사용할 수 있습니다. 예를 들어 한 단계에서 데이터를 참조하여 다른 단계에서 사용할 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|매개 변수|정의에 정의된 매개 변수 값을 반환합니다. <p>`parameters('password')` <p> **매개 변수 번호**: 1 <p> **이름**: Parameter <p> **설명**: 필수. 값이 필요한 매개 변수의 이름입니다.|  
|action|다른 JSON 이름 및 값 쌍 또는 현재 런타임 작업의 출력을 파생시키는 식을 작성할 수 있습니다. 다음 예에서 propertyPath로 표시되는 속성은 선택 사항입니다. propertyPath를 지정하지 않을 경우, 전체 작업 개체에 대한 참조입니다. 이 함수는 작업의 do-until 조건 내부에만 사용할 수 있습니다. <p>`action().outputs.body.propertyPath`|  
|actions|다른 JSON 이름 및 값 쌍 또는 런타임 작업의 출력을 파생시키는 식을 작성할 수 있습니다. 이러한 식은 한 작업이 다른 작업에 종속되어 있음을 명시적으로 선언합니다. 다음 예에서 propertyPath로 표시되는 속성은 선택 사항입니다. propertyPath를 지정하지 않을 경우, 전체 작업 개체에 대한 참조입니다. 이 요소 또는 conditions 요소를 사용하여 종속성을 지정할 수 있지만 같은 종속 리소스에 대해 두 가지를 모두 사용할 필요는 없습니다. <p>`actions('myAction').outputs.body.propertyPath` <p> **매개 변수 번호**: 1 <p> **이름**: Action name <p> **설명**: 필수. 값이 필요한 작업의 이름입니다.|  
|trigger|다른 JSON 이름 및 값 쌍 또는 런타임 트리거의 출력을 파생시키는 식을 작성할 수 있습니다. 다음 예에서 propertyPath로 표시되는 속성은 선택 사항입니다. propertyPath를 지정하지 않을 경우, 전체 트리거 개체에 대한 참조입니다. <p>`trigger().outputs.body.propertyPath` <p>트리거의 입력 내부에 사용하면 함수에서 이전 실행의 출력을 반환합니다. 하지만 트리거의 조건 내부에 사용하면 `trigger` 함수에서 현재 실행의 출력을 반환합니다.|  
|actionOutputs|이 함수는 `actions('actionName').outputs`의 약식입니다. <p> **매개 변수 번호**: 1 <p> **이름**: Action name <p> **설명**: 필수. 값이 필요한 작업의 이름입니다.|  
|actionBody 및 body|이 함수는 `actions('actionName').outputs.body`의 약식입니다. <p> **매개 변수 번호**: 1 <p> **이름**: Action name <p> **설명**: 필수. 값이 필요한 작업의 이름입니다.|  
|triggerOutputs|이 함수는 `trigger().outputs`의 약식입니다.|  
|triggerBody|이 함수는 `trigger().outputs.body`의 약식입니다.|  
|항목|반복 작업 내부에 사용하면, 이 함수는 작업 반복을 위해 배열 안에 있는 항목을 반환합니다. 예를 들어 메시지 배열의 각 항목에 대해 실행하는 작업이 있는 경우 다음 구문을 사용할 수 있습니다. <p>`"input1" : "@item().subject"`|  
  
### <a name="collection-functions"></a>컬렉션 함수  

이 함수는 컬렉션에 대해 작동하고 배열, 문자열 및 경우에 따라 사전에 대해 일반적으로 적용됩니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|contains|사전에 키가 포함되거나, 목록에 값이 포함되거나, 문자열에 하위 문자열이 포함된 경우 true를 반환합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`contains('abacaba','aca')` <p> **매개 변수 번호**: 1 <p> **이름**: Within collection <p> **설명**: 필수. 내부를 검색할 컬렉션입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Find object <p> **설명**: 필수. **Within collection** 내부에서 찾을 개체입니다.|  
|length|배열 또는 문자열 내의 요소 수를 반환합니다. 예를 들어 이 함수는 `3`를 반환합니다.  <p>`length('abc')` <p> **매개 변수 번호**: 1 <p> **이름**: Collection <p> **설명**: 필수. 길이를 가져올 컬렉션입니다.|  
|empty|개체, 배열 또는 문자열이 비어 있으면 true를 반환합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`empty('')` <p> **매개 변수 번호**: 1 <p> **이름**: Collection <p> **설명**: 필수. 비어 있는지 확인할 컬렉션입니다.|  
|교집합|전달된 배열 또는 개체 간에 공통의 요소가 있는 단일 배열 또는 개체를 반환합니다. 예를 들어 이 함수는 `[1, 2]`를 반환합니다. <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>이 함수의 매개 변수는 개체 또는 배열 집합일 수 있습니다(둘 다의 혼합은 아님). 같은 이름의 개체가 두 개 있는 경우 해당 이름을 가진 마지막 개체가 최종 개체에 나타납니다. <p> **매개 변수 번호**: 1 ... *n* <p> **이름**: Collection *n* <p> **설명**: 필수. 평가할 컬렉션입니다. 개체는 결과에 표시하기 위해 전달된 모든 컬렉션에 있어야 합니다.|  
|union|이 함수에 전달된 배열 또는 개체에 있는 모든 요소를 포함하는 단일 배열 또는 개체를 반환합니다. 예를 들어 이 함수는 `[1, 2, 3, 10, 101]`을 반환합니다. <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>이 함수의 매개 변수는 개체 또는 배열 집합일 수 있습니다(이들의 혼합은 아님). 최종 출력에 같은 이름의 개체가 두 개 있는 경우 해당 이름을 가진 마지막 개체가 최종 개체에 나타납니다. <p> **매개 변수 번호**: 1 ... *n* <p> **이름**: Collection *n* <p> **설명**: 필수. 평가할 컬렉션입니다. 컬렉션에 나타나는 개체가 결과에도 나타납니다.|  
|first|전달된 배열 또는 문자열의 첫 번째 요소를 반환합니다. 예를 들어 이 함수는 `0`을 반환합니다. <p>`first([0,2,3])` <p> **매개 변수 번호**: 1 <p> **이름**: Collection <p> **설명**: 필수. 첫 번째 개체를 가져올 컬렉션입니다.|  
|last|전달된 배열 또는 문자열의 마지막 요소를 반환합니다. 예를 들어 이 함수는 `3`를 반환합니다. <p>`last('0123')` <p> **매개 변수 번호**: 1 <p> **이름**: Collection <p> **설명**: 필수. 마지막 개체를 가져올 컬렉션입니다.|  
|take|전달된 배열 또는 문자열의 첫 번째 **Count** 요소를 반환합니다. 예를 들어 이 함수는 `[1, 2]`를 반환합니다.  <p>`take([1, 2, 3, 4], 2)` <p> **매개 변수 번호**: 1 <p> **이름**: Collection <p> **설명**: 필수. 첫 번째 **Count** 개체를 가져올 컬렉션입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Count <p> **설명**: 필수. **Collection**에서 가져올 개체 수입니다. 양의 정수여야 합니다.|  
|skip|인덱스 **Count**에서 시작하는 배열의 요소를 반환합니다. 예를 들어 이 함수는 `[3, 4]`를 반환합니다. <p>`skip([1, 2 ,3 ,4], 2)` <p> **매개 변수 번호**: 1 <p> **이름**: Collection <p> **설명**: 필수. 첫 번째 **Count** 개체를 건너뛸 컬렉션입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Count <p> **설명**: 필수. **Collection**의 앞에서 제거할 개체 수입니다. 양의 정수여야 합니다.|  
|join|구분 기호로 조인된 배열의 각 항목이 있는 문자열을 반환합니다. 예를 들어 `"1,2,3,4"`를 반환합니다.<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Collection<br /><br /> **설명**: 필수. 항목을 조인할 컬렉션입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Delimiter<br /><br /> **설명**: 필수. 항목을 구분할 문자열입니다.|  
  
### <a name="string-functions"></a>문자열 함수

다음 함수는 문자열에만 적용됩니다. 문자열에서 몇 가지 컬렉션 함수도 사용할 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|concat|임의 개수 문자열을 함께 결합합니다. 예를 들어 매개 변수 1이 `p1`이면 이 함수는 `somevalue-p1-somevalue`를 반환합니다. <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **매개 변수 번호**: 1 ... *n* <p> **이름**: String *n* <p> **설명**: 필수. 단일 문자열로 결합할 문자열입니다.|  
|substring|문자열의 하위 집합을 반환합니다. 예를 들어 이 함수는 `abc`를 반환합니다. <p>`substring('somevalue-abc-somevalue',10,3)` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 부분 문자열을 가져올 원래 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Start index <p> **설명**: 필수. 매개 변수 1에서 하위 문자열이 시작되는 위치의 인덱스입니다. <p> **매개 변수 번호**: 3 <p> **이름**: Length <p> **설명**: 필수. 부분 문자열의 길이입니다.|  
|replace|문자열을 지정된 문자열로 바꿉니다. 예를 들어 이 함수는 `the new string`을 반환합니다. <p>`replace('the old string', 'old', 'new')` <p> **매개 변수 번호**: 1 <p> **이름**: string <p> **설명**: 필수. 매개 변수 1에서 매개 변수 2를 검색하여 매개 변수 2가 있으면 매개 변수 3으로 업데이트한 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Old string <p> **설명**: 필수. 매개 변수 1에 일치 항목이 있으면 매개 변수 3으로 바꿀 문자열입니다. <p> **매개 변수 번호**: 3 <p> **이름**: New string <p> **설명**: 필수. 매개 변수 1에 일치 항목이 있으면 매개 변수 2의 문자열을 바꾸는 데 사용할 문자열입니다.|  
|GUID|이 함수는 전역적으로 고유한 문자열(GUID)을 생성합니다. 예를 들어 이 함수는 다음 GUID를 생성할 수 있습니다. `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **매개 변수 번호**: 1 <p> **이름**: Format <p> **설명**: 선택 사항. [이 Guid 값의 형식을 지정하는 방법](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)을 나타내는 단일 형식 지정자입니다. 형식 매개 변수는 "N", "D", "B", "P" 또는 "X"일 수 있습니다. 형식이 제공되지 않으면 "D"가 사용됩니다.|  
|toLower|문자열을 소문자로 변환합니다. 예를 들어 이 함수는 `two by two is four`를 반환합니다. <p>`toLower('Two by Two is Four')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 소문자로 변환할 문자열입니다. 문자열에 있는 문자에 소문자로 변환할 항목이 없으면 반환된 문자열에서 해당 문자가 변경되지 않고 포함됩니다.|  
|toUpper|문자열을 대문자로 변환합니다. 예를 들어 이 함수는 `TWO BY TWO IS FOUR`를 반환합니다. <p>`toUpper('Two by Two is Four')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 대문자로 변환할 문자열입니다. 문자열의 문자에 대문자로 변환할 항목이 없으면 반환된 문자열에서 해당 문자가 변경되지 않고 포함됩니다.|  
|indexof|문자열 내에서 대소문자를 구분하지 않고 값의 인덱스를 찾습니다. 예를 들어 이 함수는 `7`을 반환합니다. <p>`indexof('hello, world.', 'world')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 문자열은 이 값을 포함할 수 있습니다. <p> **매개 변수 번호**: 2 <p> **이름**: String <p> **설명**: 필수. 인덱스를 검색할 값입니다.|  
|lastindexof|문자열 내에서 대소문자를 구분하지 않고 값의 마지막 인덱스를 찾습니다. 예를 들어 이 함수는 `3`를 반환합니다. <p>`lastindexof('foofoo', 'foo')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 문자열은 이 값을 포함할 수 있습니다. <p> **매개 변수 번호**: 2 <p> **이름**: String <p> **설명**: 필수. 인덱스를 검색할 값입니다.|  
|startswith|문자열이 대소문자를 구분하지 않고 값으로 시작하는지 확인합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`lastindexof('hello, world', 'hello')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 문자열은 이 값을 포함할 수 있습니다. <p> **매개 변수 번호**: 2 <p> **이름**: String <p> **설명**: 필수. 문자열은 이 값으로 시작할 수 있습니다.|  
|endswith|문자열이 대소문자를 구분하지 않고 값으로 끝나는지 확인합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`lastindexof('hello, world', 'world')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 문자열은 이 값을 포함할 수 있습니다. <p> **매개 변수 번호**: 2 <p> **이름**: String <p> **설명**: 필수. 문자열은 이 값으로 끝날 수 있습니다.|  
|분할|구분 기호를 사용하여 문자열을 분할합니다. 예를 들어 이 함수는 `["a", "b", "c"]`를 반환합니다. <p>`split('a;b;c',';')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 분할된 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: String <p> **설명**: 필수. 구분 기호입니다.|  
  
### <a name="logical-functions"></a>논리 함수  

이 함수는 조건 내에서 유용하며 논리의 형식을 평가하는 데 사용할 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|equals|두 값이 같으면 true를 반환합니다. 예를 들어 매개 변수 1이 어떤 값이면 이 함수는 `true`를 반환합니다. <p>`equals(parameters('parameter1'), 'someValue')` <p> **매개 변수 번호**: 1 <p> **이름**: Object 1 <p> **설명**: 필수. **Object 2**와 비교할 개체입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Object 2 <p> **설명**: 필수. **Object 1**과 비교할 개체입니다.|  
|less|첫 번째 인수가 두 번째 인수보다 작으면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`less(10,100)` <p> **매개 변수 번호**: 1 <p> **이름**: Object 1 <p> **설명**: 필수. **Object 2**보다 작은지 확인할 개체입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Object 2 <p> **설명**: 필수. **Object 1**보다 큰지 확인할 개체입니다.|  
|lessOrEquals|첫 번째 인수가 두 번째 인수보다 작거나 같으면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`lessOrEquals(10,10)` <p> **매개 변수 번호**: 1 <p> **이름**: Object 1 <p> **설명**: 필수. **Object 2**보다 작거나 같은지 확인할 개체입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Object 2 <p> **설명**: 필수. **Object 1**보다 크거나 같은지 확인할 개체입니다.|  
|greater|첫 번째 인수가 두 번째 인수보다 크면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 이 함수는 `false`를 반환합니다.  <p>`greater(10,10)` <p> **매개 변수 번호**: 1 <p> **이름**: Object 1 <p> **설명**: 필수. **Object 2**보다 큰지 확인할 개체입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Object 2 <p> **설명**: 필수. **Object 1**보다 작은지 확인할 개체입니다.|  
|greaterOrEquals|첫 번째 인수가 두 번째 인수보다 크거나 같으면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 이 함수는 `false`를 반환합니다. <p>`greaterOrEquals(10,100)` <p> **매개 변수 번호**: 1 <p> **이름**: Object 1 <p> **설명**: 필수. **Object 2**보다 크거나 같은지 확인할 개체입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Object 2 <p> **설명**: 필수. **Object 1**보다 작거나 같은지 확인할 개체입니다.|  
|and|두 매개 변수가 true이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 예를 들어 이 함수는 `false`를 반환합니다. <p>`and(greater(1,10),equals(0,0))` <p> **매개 변수 번호**: 1 <p> **이름**: Boolean 1 <p> **설명**: 필수. 첫 번째 인수는 `true`여야 합니다. <p> **매개 변수 번호**: 2 <p> **이름**: Boolean 2 <p> **설명**: 필수. 두 번째 인수는 `true`여야 합니다.|  
|또는|매개 변수 중 하나라도 true이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`or(greater(1,10),equals(0,0))` <p> **매개 변수 번호**: 1 <p> **이름**: Boolean 1 <p> **설명**: 필수. 첫 번째 인수는 `true`일 수 있습니다. <p> **매개 변수 번호**: 2 <p> **이름**: Boolean 2 <p> **설명**: 필수. 두 번째 인수는 `true`일 수 있습니다.|  
|not|매개 변수가 `false`이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 예를 들어 이 함수는 `true`를 반환합니다. <p>`not(contains('200 Success','Fail'))` <p> **매개 변수 번호**: 1 <p> **이름**: Boolean <p> **설명**: 매개 변수가 `false`이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 다음 함수는 `true`를 반환합니다. `not(contains('200 Success','Fail'))`|  
|if|식 결과가 `true` 또는 `false`인지에 따라 지정된 값을 반환합니다.  예를 들어 이 함수는 `"yes"`를 반환합니다. <p>`if(equals(1, 1), 'yes', 'no')` <p> **매개 변수 번호**: 1 <p> **이름**: Expression <p> **설명**: 필수. 식에서 어떤 값을 반환할지 결정하는 부울 값입니다. <p> **매개 변수 번호**: 2 <p> **이름**: True <p> **설명**: 필수. 식이 `true`인 경우 반환할 값입니다. <p> **매개 변수 번호**: 3 <p> **이름**: False <p> **설명**: 필수. 식이 `false`인 경우 반환할 값입니다.|  
  
### <a name="conversion-functions"></a>변환 함수  

이 함수는 언어의 각 기본 형식 간에 변환하는 데 사용됩니다.  
  
- string  
  
- 정수  
  
- float  
  
- 부울  
  
- arrays  
  
- dictionaries  

-   forms  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|int|매개 변수를 정수로 변환합니다. 예를 들어 이 함수는 문자열이 아닌 숫자로 100을 반환합니다. <p>`int('100')` <p> **매개 변수 번호**: 1 <p> **이름**: Value <p> **설명**: 필수. 정수로 변환할 값입니다.|  
|string|매개 변수를 문자열로 변환합니다. 예를 들어 이 함수는 `'10'`을 반환합니다. <p>`string(10)` <p>개체를 문자열로 변환할 수도 있습니다. 예를 들어 `myPar` 매개 변수가 한 개의 `abc : xyz` 속성을 포함하는 개체인 경우 이 함수는 `{"abc" : "xyz"}`를 반환합니다. <p>`string(parameters('myPar'))` <p> **매개 변수 번호**: 1 <p> **이름**: Value <p> **설명**: 필수. 문자열로 변환할 값입니다.|  
|json :|매개 변수를 JSON 형식 값으로 변환하고 `string()`의 반대입니다. 예를 들어 이 함수는 문자열이 아닌 배열로 `[1,2,3]`을 반환합니다. <p>`parse('[1,2,3]')` <p>마찬가지로, 문자열을 개체로 변환할 수 있습니다. 예를 들어 이 함수는 `{ "abc" : "xyz" }`를 반환합니다. <p>`json('{"abc" : "xyz"}')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 기본 형식 값으로 변환할 문자열입니다. <p>`json()` 함수는 XML 입력도 지원합니다. 예를 들어 다음 매개 변수 값은 <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>다음 JSON으로 변환됩니다. <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|매개 변수 인수를 부동 소수점 숫자로 변환합니다. 예를 들어 이 함수는 `10.333`을 반환합니다. <p>`float('10.333')` <p> **매개 변수 번호**: 1 <p> **이름**: Value <p> **설명**: 필수. 부동 소수점 숫자로 변환할 값입니다.|  
|bool|매개 변수를 부울로 변환합니다. 예를 들어 이 함수는 `false`를 반환합니다. <p>`bool(0)` <p> **매개 변수 번호**: 1 <p> **이름**: Value <p> **설명**: 필수. 부울로 변환할 값입니다.|  
|coalesce|전달된 인수에서 첫 번째 null이 아닌 개체를 반환합니다. **참고**: 빈 문자열은 null이 아닙니다. 예를 들어 매개 변수 1 및 2가 정의되지 않은 경우 이 함수는 `fallback`을 반환합니다.  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **매개 변수 번호**: 1 ... *n* <p> **이름**: Object*n* <p> **설명**: 필수. null을 검사할 개체입니다.|  
|base64|입력 문자열의 base64 표현을 반환합니다. 예를 들어 이 함수는 `c29tZSBzdHJpbmc=`를 반환합니다. <p>`base64('some string')` <p> **매개 변수 번호**: 1 <p> **이름**: String 1 <p> **설명**: 필수. base64 표현으로 인코딩할 문자열입니다.|  
|base64ToBinary|base64 인코딩 문자열의 이진 표현을 반환합니다. 예를 들어 이 함수는 `some string`의 이진 표현을 반환합니다. <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. Base64 인코딩된 문자열입니다.|  
|base64ToString|based64 인코딩 문자열의 문자열 표현을 반환합니다. 예를 들어 이 함수는 `some string`을 반환합니다. <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. Base64 인코딩된 문자열입니다.|  
|이진|값의 이진 표현을 반환합니다.  예를 들어 이 함수는 `some string`의 이진 표현을 반환합니다. <p>`binary('some string')` <p> **매개 변수 번호**: 1 <p> **이름**: Value <p> **설명**: 필수. 이진으로 변환할 값입니다.|  
|dataUriToBinary|데이터 URI의 이진 표현을 반환합니다. 예를 들어 이 함수는 `some string`의 이진 표현을 반환합니다. <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 이진 표현으로 변환할 데이터 URI입니다.|  
|dataUriToString|데이터 URI의 문자열 표현을 반환합니다. 예를 들어 이 함수는 `some string`을 반환합니다. <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **매개 변수 번호**: 1 <p> **이름**: String<p> **설명**: 필수. 문자열 표현으로 변환할 데이터 URI입니다.|  
|dataUri|값의 데이터 URI를 반환합니다. 예를 들어 이 함수는 데이터 URI `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`를 반환합니다. <p>`dataUri('some string')` <p> **매개 변수 번호**: 1<p> **이름**: Value<p> **설명**: 필수. 데이터 URI로 변환할 값입니다.|  
|decodeBase64|입력 based64 문자열의 문자열 표현을 반환합니다. 예를 들어 이 함수는 `some string`을 반환합니다. <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 입력 based64 문자열의 문자열 표현을 반환합니다.|  
|encodeUriComponent|전달된 문자열을 URL-이스케이프합니다. 예를 들어 이 함수는 `You+Are%3ACool%2FAwesome`을 반환합니다. <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. URL 안전하지 않은 문자 양식을 이스케이프할 문자열입니다.|  
|decodeUriComponent|전달된 문자열을 URL-이스케이프하지 않습니다. 예를 들어 이 함수는 `You Are:Cool/Awesome`을 반환합니다. <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. URL 안전하지 않은 문자를 디코딩할 문자열입니다.|  
|decodeDataUri|입력 데이터 URI 문자열의 이진 표현을 반환합니다. 예를 들어 이 함수는 `some string`의 이진 표현을 반환합니다. <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **매개 변수 번호**: 1 <p> **이름**: String <p> **설명**: 필수. 이진 표현으로 디코딩할 데이터 URI입니다.|  
|uriComponent|값의 URI 인코딩 표현을 반환합니다. 예를 들어 이 함수는 `You+Are%3ACool%2FAwesome`을 반환합니다. <p>`uriComponent('You Are:Cool/Awesome')` <p> **매개 변수 번호**: 1<p> **이름**: String <p> **설명**: 필수. URI 인코딩할 문자열입니다.|  
|uriComponentToBinary|URI 인코딩 문자열의 이진 표현을 반환합니다. 예를 들어 이 함수는 `You Are:Cool/Awesome`의 이진 표현을 반환합니다. <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **매개 변수 번호**: 1 <p> **이름**: String<p> **설명**: 필수. URI 인코딩된 문자열입니다.|  
|uriComponentToString|URI 인코딩 문자열의 문자열 표현을 반환합니다. 예를 들어 이 함수는 `You Are:Cool/Awesome`을 반환합니다. <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **매개 변수 번호**: 1<p> **이름**: String<p> **설명**: 필수. URI 인코딩된 문자열입니다.|  
|xml|값의 XML 표현을 반환합니다. 예를 들어 이 함수는 `'\<name>Alan\</name>'`으로 표시된 XML 콘텐츠를 반환합니다. <p>`xml('\<name>Alan\</name>')` <p>`xml()` 함수는 JSON 개체 입력도 지원합니다. 예를 들어 `{ "abc": "xyz" }` 매개 변수는 XML 콘텐츠로 변환됩니다. `\<abc>xyz\</abc>` <p> **매개 변수 번호**: 1<p> **이름**: Value<p> **설명**: 필수. XML로 변환할 값입니다.|  
|xpath|xpath 식을 평가할 값의 xpath 식과 일치하는 XML 노드 배열을 반환합니다. <p> **예 1** <p>매개 변수 `p1` 값이 이 XML의 문자열 표현이라고 가정합니다. <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>다음 코드는 `xpath(xml(parameters('p1'), '/lab/robot/name')` <p>다음을 반환합니다. <p>`[ <name>R1</name>, <name>R2</name> ]` <p>반면에 다음 코드는 <p>`xpath(xml(parameters('p1'), ' sum(/lab/robot/parts)')` <p>다음을 반환합니다. <p>`13` <p> <p> **예 2** <p>다음 XML 콘텐츠를 가정한다면: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>다음 코드는 `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>또는 다음 코드는 <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>다음을 반환합니다. <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>그리고 다음 코드는 `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>다음을 반환합니다. <p>``xyz`` <p> **매개 변수 번호**: 1 <p> **이름**: Xml <p> **설명**: 필수. XPath 식을 평가할 XML입니다. <p> **매개 변수 번호**: 2 <p> **이름**: XPath <p> **설명**: 필수. 평가할 XPath 식입니다.|  
|array|매개 변수를 배열로 변환합니다. 예를 들어 이 함수는 `["abc"]`를 반환합니다. <p>`array('abc')` <p> **매개 변수 번호**: 1 <p> **이름**: Value <p> **설명**: 필수. 배열로 변환할 값입니다.|
|createArray|매개 변수에서 배열을 만듭니다. 예를 들어 이 함수는 `["a", "c"]`를 반환합니다. <p>`createArray('a', 'c')` <p> **매개 변수 번호**: 1 ... *n* <p> **이름**: Any *n* <p> **설명**: 필수. 배열로 결합할 값입니다.|
|triggerFormDataValue|form-data 또는 form-encoded 트리거 출력에서 키 이름과 일치하는 단일 값을 반환합니다.  일치 항목이 여러 개이면 오류입니다.  예를 들어 다음 코드는 `bar`를 반환합니다. `triggerFormDataValue('foo')`<br /><br />**매개 변수 번호**: 1<br /><br />**이름**: Key Name<br /><br />**설명**: 필수. 반환할 양식 데이터 값의 키 이름입니다.|
|triggerFormDataMultiValues|form-data 또는 form-encoded 트리거 출력에서 키 이름과 일치하는 값 배열을 반환합니다.  예를 들어 다음 코드는 `["bar"]`를 반환합니다. `triggerFormDataValue('foo')`<br /><br />**매개 변수 번호**: 1<br /><br />**이름**: Key Name<br /><br />**설명**: 필수. 반환할 양식 데이터 값의 키 이름입니다.|
|triggerMultipartBody|트리거의 다중 부분 출력에서 일부 본문을 반환합니다.<br /><br />**매개 변수 번호**: 1<br /><br />**이름**: Index<br /><br />**설명**: 필수. 검색할 부분의 인덱스입니다.|
|formDataValue|form-data 또는 form-encoded 작업 출력에서 키 이름과 일치하는 단일 값을 반환합니다.  일치 항목이 여러 개이면 오류입니다.  예를 들어 다음 코드는 `bar`를 반환합니다. `formDataValue('someAction', 'foo')`<br /><br />**매개 변수 번호**: 1<br /><br />**이름**: Action Name<br /><br />**설명**: 필수. form-data 또는 form-encoded 응답이 포함된 작업 이름입니다.<br /><br />**매개 변수 번호**: 2<br /><br />**이름**: Key Name<br /><br />**설명**: 필수. 반환할 양식 데이터 값의 키 이름입니다.|
|formDataMultiValues|form-data 또는 form-encoded 작업 출력에서 키 이름과 일치하는 값 배열을 반환합니다.  예를 들어 다음 코드는 `["bar"]`를 반환합니다. `formDataMultiValues('someAction', 'foo')`<br /><br />**매개 변수 번호**: 1<br /><br />**이름**: Action Name<br /><br />**설명**: 필수. form-data 또는 form-encoded 응답이 포함된 작업 이름입니다.<br /><br />**매개 변수 번호**: 2<br /><br />**이름**: Key Name<br /><br />**설명**: 필수. 반환할 양식 데이터 값의 키 이름입니다.|
|multipartBody|작업의 다중 부분 출력에서 일부 본문을 반환합니다.<br /><br />**매개 변수 번호**: 1<br /><br />**이름**: Action Name<br /><br />**설명**: 필수. multipart 응답이 포함된 작업 이름입니다.<br /><br />**매개 변수 번호**: 2<br /><br />**이름**: Index<br /><br />**설명**: 필수. 검색할 부분의 인덱스입니다.|

### <a name="math-functions"></a>수학 함수  

이 함수는 **integers** 및 **floats**의 숫자 형식에 사용할 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|추가|두 숫자를 더한 결과를 반환합니다. 예를 들어 이 함수는 `20.333`을 반환합니다. <p>`add(10,10.333)` <p> **매개 변수 번호**: 1 <p> **이름**: Summand 1 <p> **설명**: 필수. **Summand 2**에 더할 숫자입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Summand 2 <p> **설명**: 필수. **Summand 1**에 더할 숫자입니다.|  
|sub|두 숫자를 뺀 결과를 반환합니다. 예를 들어 이 함수는 `-0.333`을 반환합니다. <p>`sub(10,10.333)` <p> **매개 변수 번호**: 1 <p> **이름**: Minuend <p> **설명**: 필수. **Subtrahend**를 뺄 숫자입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Subtrahend <p> **설명**: 필수. **Minuend**에서 뺄 숫자입니다.|  
|mul|두 숫자를 곱한 결과를 반환합니다. 예를 들어 이 함수는 `103.33`을 반환합니다. <p>`mul(10,10.333)` <p> **매개 변수 번호**: 1 <p> **이름**: Multiplicand 1 <p> **설명**: 필수. **Multiplicand 2**를 곱할 숫자입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Multiplicand 2 <p> **설명**: 필수. **Multiplicand 1**을 곱할 숫자입니다.|  
|div|두 숫자를 나눈 결과를 반환합니다. 예를 들어 이 함수는 `1.0333`을 반환합니다. <p>`div(10.333,10)` <p> **매개 변수 번호**: 1 <p> **이름**: Dividend <p> **설명**: 필수. **Divisor**로 나눌 숫자입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Divisor <p> **설명**: 필수. **Dividend**로 나누어지는 숫자입니다.|  
|mod|두 숫자를 나눈 후 나머지를 반환합니다(모듈로). 예를 들어 이 함수는 `2`를 반환합니다. <p>`mod(10,4)` <p> **매개 변수 번호**: 1 <p> **이름**: Dividend <p> **설명**: 필수. **Divisor**로 나눌 숫자입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Divisor <p> **설명**: 필수. **Dividend**로 나누어지는 숫자입니다. 나눈 후 나머지를 가져옵니다.|  
|Min|이 함수를 호출하는 데는 두 가지 다른 패턴이 있습니다. <p>여기서 `min`은 배열을 사용하고 함수는 `0`를 반환합니다. <p>`min([0,1,2])` <p>또는 이 함수는 쉼표로 구분된 값 목록을 사용하고 `0`를 반환할 수도 있습니다. <p>`min(0,1,2)` <p> **참고**: 모든 값은 숫자여야 하므로 매개 변수가 배열이면 배열에 숫자만 포함되어야 합니다. <p> **매개 변수 번호**: 1 <p> **이름**: Collection 또는 Value <p> **설명**: 필수. 최소값을 찾을 값 배열이거나 집합의 첫 번째 값입니다. <p> **매개 변수 번호**: 2 ... *n* <p> **이름**: Value *n* <p> **설명**: 선택 사항. 첫 번째 매개 변수가 Value인 경우 추가 값을 전달할 수 있으며 전달된 모든 값의 최소값이 반환됩니다.|  
|max|이 함수를 호출하는 데는 두 가지 다른 패턴이 있습니다. <p>여기서 `max`은 배열을 사용하고 함수는 `2`를 반환합니다. <p>`max([0,1,2])` <p>또는 이 함수는 쉼표로 구분된 값 목록을 사용하고 `2`를 반환할 수도 있습니다. <p>`max(0,1,2)` <p> **참고**: 모든 값은 숫자여야 하므로 매개 변수가 배열이면 배열에 숫자만 포함되어야 합니다. <p> **매개 변수 번호**: 1 <p> **이름**: Collection 또는 Value <p> **설명**: 필수. 최대값을 찾을 값 배열이거나 집합의 첫 번째 값입니다. <p> **매개 변수 번호**: 2 ... *n* <p> **이름**: Value *n* <p> **설명**: 선택 사항. 첫 번째 매개 변수가 Value인 경우 추가 값을 전달할 수 있으며 전달된 모든 값의 최대값이 반환됩니다.|  
|range|특정 숫자부터 시작되는 정수 배열을 생성합니다. 반환된 배열의 길이를 정의합니다. <p>예를 들어 이 함수는 `[3,4,5,6]`을 반환합니다. <p>`range(3,4)` <p> **매개 변수 번호**: 1 <p> **이름**: Start index <p> **설명**: 필수. 배열에서 첫 번째 정수입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Count <p> **설명**: 필수. 이 값은 배열에 있는 정수 개수입니다.|  
|rand|지정된 범위 내에서 정수를 임의로 생성합니다(양쪽 끝 포함). 예를 들어 이 함수는 `42`를 반환할 수 있습니다. <p>`rand(-1000,1000)` <p> **매개 변수 번호**: 1 <p> **이름**: Minimum <p> **설명**: 필수. 반환 가능한 가장 작은 정수입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Maximum <p> **설명**: 필수. 반환 가능한 가장 큰 정수입니다.|  
  
### <a name="date-functions"></a>날짜 함수  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|utcnow|현재 타임스탬프를 문자열로 반환합니다. 예: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **매개 변수 번호**: 1 <p> **이름**: Format <p> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|addseconds|전달된 문자열 타임스탬프에 시간(초)에 대한 정수를 더합니다. 시간(초) 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **매개 변수 번호**: 1 <p> **이름**: Timestamp <p> **설명**: 필수. 시간을 포함하는 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Seconds <p> **설명**: 필수. 추가할 시간(초) 수입니다. 시간(초)을 빼기 위한 음수일 수 있습니다. <p> **매개 변수 번호**: 3 <p> **이름**: Format <p> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|addminutes|전달된 문자열 타임스탬프에 시간(분)에 대한 정수를 더합니다. 시간(분) 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **매개 변수 번호**: 1 <p> **이름**: Timestamp <p> **설명**: 필수. 시간을 포함하는 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Minutes <p> **설명**: 필수. 더할 시간(분) 수입니다. 시간(분)을 빼기 위한 음수일 수 있습니다. <p> **매개 변수 번호**: 3 <p> **이름**: Format <p> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|addhours|전달된 문자열 타임스탬프에 시간(시)에 대한 정수를 더합니다. 시간(시) 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **매개 변수 번호**: 1 <p> **이름**: Timestamp <p> **설명**: 필수. 시간을 포함하는 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Hours <p> **설명**: 필수. 더할 시간 수입니다. 시간(시)을 빼기 위한 음수일 수 있습니다. <p> **매개 변수 번호**: 3 <p> **이름**: Format <p> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|adddays|전달된 문자열 타임스탬프에 날 수에 대한 정수를 더합니다. 날 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예: `2015-02-23T13:27:36Z`: <p>`addseconds('2015-03-15T13:27:36Z', -20)` <p> **매개 변수 번호**: 1 <p> **이름**: Timestamp <p> **설명**: 필수. 시간을 포함하는 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Days <p> **설명**: 필수. 더할 날 수입니다. 날 수를 빼기 위한 음수일 수 있습니다. <p> **매개 변수 번호**: 3 <p> **이름**: Format <p> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|formatDateTime|날짜 형식으로 문자열을 반환합니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예: `2015-02-23T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **매개 변수 번호**: 1 <p> **이름**: Date <p> **설명**: 필수. 날짜를 포함하는 문자열입니다. <p> **매개 변수 번호**: 2 <p> **이름**: Format <p> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|startOfHour|전달된 문자열 타임스탬프에 그 시간의 시작 시간을 반환합니다. 예 `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Timestamp<br /><br /> **설명**: 필수. 시간을 포함하는 문자열입니다.<br /><br />**매개 변수 번호**: 2<br /><br /> **이름**: Format<br /><br /> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|startOfDay|전달된 문자열 타임스탬프에 그 날의 시작 시간을 반환합니다. 예 `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Timestamp<br /><br /> **설명**: 필수. 시간을 포함하는 문자열입니다.<br /><br />**매개 변수 번호**: 2<br /><br /> **이름**: Format<br /><br /> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.| 
|startOfMonth|전달된 문자열 타임스탬프에 그 달의 시작 시간을 반환합니다. 예 `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Timestamp<br /><br /> **설명**: 필수. 시간을 포함하는 문자열입니다.<br /><br />**매개 변수 번호**: 2<br /><br /> **이름**: Format<br /><br /> **설명**: 선택 사항. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.| 
|dayOfWeek|문자열 타임스탬프의 요일 구성 요소를 반환합니다.  일요일은 0, 월요일은 1 등입니다. 예 `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Timestamp<br /><br /> **설명**: 필수. 시간을 포함하는 문자열입니다.| 
|dayOfMonth|문자열 타임스탬프에서 그 달의 날짜 구성 요소를 반환합니다. 예 `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Timestamp<br /><br /> **설명**: 필수. 시간을 포함하는 문자열입니다.| 
|dayOfYear|문자열 타임스탬프에서 해당 연도의 날짜 구성 요소를 반환합니다. 예 `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Timestamp<br /><br /> **설명**: 필수. 시간을 포함하는 문자열입니다.| 
|ticks|문자열 타임스탬프에서 틱 속성을 반환합니다. 예 `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Timestamp<br /><br /> **설명**: 필수. 시간을 포함하는 문자열입니다.| 
  
### <a name="workflow-functions"></a>워크플로 함수  

이 함수를 통해 런타임에 워크플로 자체에 대한 정보를 얻을 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|listCallbackUrl|트리거 또는 작업을 호출하기 위한 문자열을 반환합니다. <p> **참고**: 이 함수는 **httpWebhook** 및 **apiConnectionWebhook**에서만 사용할 수 있으며 **manual**, **recurrence**, **http** 또는 **apiConnection**에서는 사용할 수 없습니다. <p>예를 들어 `listCallbackUrl()` 함수는 다음을 반환합니다. <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|워크플로|이 함수는 런타임에 워크플로 자체에 대한 모든 정보를 제공합니다. 이름, 위치 및 리소스 ID 등 관리 API에서 사용 가능한 모든 항목을 가져올 수 있습니다. 이러한 속성에 대한 자세한 내용은 [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617)를 참조하세요. 예를 들어 이 함수는 `westus`와 같은 위치를 반환합니다. <p>`workflow().location` <p> **참고**: `@workflow`는 현재 트리거 내에서 지원됩니다.|

## <a name="next-steps"></a>다음 단계

[워크플로 작업 및 트리거](logic-apps-workflow-actions-triggers.md)
