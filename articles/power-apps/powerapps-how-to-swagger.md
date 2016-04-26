<properties
	pageTitle="PowerApps 및 논리 흐름에 대한 Swagger 정의 사용자 지정 방법 | Microsoft Azure"
	description="Swagger에서 PowerApps 및 논리 흐름으로 작업하는 데 필요한 스키마 확장 보기"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>


# PowerApps 및 논리 흐름에 대한 Swagger 정의 사용자 지정

## API에 대한 Swagger 2.0 API 정의 작성

WebAPI에 Swagger를 추가하는 방법에 대해 알아보려면 [Swashbuckle][1]을 참조하세요.

## 스키마 확장
표준 Swagger 사양 외에, PowerApps 및 논리 흐름에 대한 사용자 지정 API를 만들 때 사용할 수 있는 Swagger 확장이 더 있습니다. 이 섹션은 이러한 확장을 나열하고 설명합니다.

##### x-ms-summary
Swagger 사양에 정의된 `summary` 필드가 없는 엔터티에 대한 표시 이름을 설명하는 문자열입니다. 여기에 해당하는 예로는 **Parameter names**가 있습니다.

##### x-ms-visibility
이 값은 논리 흐름 디자이너에 엔터티의 표시 여부를 설명합니다. 사용할 수 있는 값은 다음과 같습니다.

- “none”(기본값)
- “advanced”
- “internal” - 논리 흐름 디자이너에 이 연산이 표시되지 않습니다.

연산에 "important" 표시를 하면, 논리 흐름 클라이언트는 이 연산을 강조 표시합니다.

##### x-ms-trigger
연산이 논리 흐름에서 트리거로 사용될 수 있을지를 정의합니다. 옵션은 다음과 같습니다.
	
- none(기본값): 연산이 트리거로 사용될 수 없습니다.
- single: 연산이 트리거로도 사용될 수 있습니다.
- batched: 연산이 트리거로 사용될 수 있습니다. 또한, 이 연산은 JSON 개체 ‘배열’로 응답하며 논리 흐름은 해당 배열에 포함된 각 항목에 대해 트리거를 발생시킵니다.


##### x-ms-dynamic-values
API가 이 매개 변수에 대해 동적으로 허용되는 값의 목록을 제공하는 논리 흐름 디자이너에 대한 힌트입니다. 논리 흐름 디자이너는 이 필드의 값에 의해 정의된 대로 연산을 호출할 수 있고, 결과로부터 가능한 값을 추출할 수 있습니다. 그런 다음 논리 흐름 디자이너는 이 값을 최종 사용자에게 옵션으로 표시할 수 있습니다.

값은 다음 속성을 포함하는 개체입니다.
	
- `operationId`: 호출되는 연산에 대한 operationId와 일치하는 문자열입니다.
- `parameters`: 개체의 속성이 연산에 필요한 매개 변수를 정의하는 개체입니다.
- `value-collection`: 응답 페이로드에 포함된 개체 배열의 값을 구하는 경로 문자열입니다.
- `value-path`: 매개 변수에 대한 값을 참조하는 "value-collection" 내부 개체에 포함된 경로 문자열입니다.
- `value-title`: 값에 대한 설명을 참조하는 "value-collection" 내부 개체에 포함된 경로 문자열입니다.


예제:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

이 예제에서 swagger는 Salesforce에 새 개체를 만드는 `TableData_CreateItem` 작업을 정의합니다.

Salesforce에는 기본 제공되는 개체가 많이 있습니다. `x-ms-dynamic-values`는 디자이너가 기본 제공되는 Salesforce 개체 목록을 알아내는 데 도움을 주기 위해 사용됩니다. `TableMetadata_ListTables`를 호출하여 목록을 가져옵니다.

##### x-ms-dynamic-schema
이 매개 변수(또는 응답)에 대한 스키마가 사실상 동적이라는 것을 알려주는, 논리 흐름 디자이너에 대한 힌트입니다. 이 필드의 값에 의해 정의된 대로 연산을 호출할 수 있고 스키마를 동적으로 검색할 수 있습니다. 그런 다음 사용자로부터 입력을 받거나 사용 가능한 필드를 표시하기 위해 적절한 UI를 표시할 수 있습니다.

예제:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

이것은 연산에 대한 입력이 동적인 시나리오에 유용합니다. 예를 들어 SQL의 경우를 생각해 보겠습니다. 각 테이블의 스키마가 다릅니다. 따라서 사용자가 특정 테이블을 선택하면 논리 흐름 디자이너는 열 이름을 표시할 수 있도록 테이블 구조를 이해해야 합니다. 이런 경우, Swagger 정의에 `x-ms-dynamic-schema`가 있으면 스키마를 가져오도록 해당 연산을 호출합니다.


<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0413_2016-->