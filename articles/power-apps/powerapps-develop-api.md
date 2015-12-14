<properties
	pageTitle="PowerApps 엔터프라이즈용 API 개발 | Microsoft Azure"
	description="PowerApps에 대한 사용자 지정 API 빌드 또는 만들기"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="rajram"/>

# PowerApps용 API 개발

PowerApps에 사용할 수 있는 자신만의 API를 만들거나 개발할 수 있습니다. 단계는 다음과 같습니다.

- API 빌드 및 배포
- API에 대한 [Swagger 2.0](http://swagger.io/) API 정의 작성

이 항목에서는 이러한 단계를 나열합니다.

## 1단계: API 빌드 및 배포

PowerApps에 대한 API를 빌드하고 배포하는 것은 여느 API를 만드는 것과 같습니다. 원하는 프로그래밍 언어와 프레임워크를 선택할 수 있습니다. 어느 곳에서든 API를 호스트하도록 선택할 수 있습니다. 개발한 앱은 [API 앱](https://azure.microsoft.com/services/app-service/api/)과 동일한 PowerApps의 앱 서비스 환경에서 호스트하는 것이 좋습니다.

다음 문서는 앱 서비스 환경에서 .Net, Java 또는 Node.js API를 빌드하고 배포하는 방법을 보여줍니다.

- [Azure 앱 서비스에서 .NET 빌드 및 배포](../app-service-api-dotnet-get-started.md)
- [Azure 앱 서비스에서 Java API 앱 빌드 및 배포](../app-service-api-java-api-app.md)
- [Azure 앱 서비스에서 Node.js API 앱 빌드 및 배포](../app-service-api-nodejs-api-app.md)


## 2단계: API에 대한 Swagger 2.0 API 정의 작성

*1단계*에 언급된 문서 중 하나를 따라서 적용하면, API에 대한 표준 Swagger 2.0 API 정의가 자동으로 생성됩니다. 이것을 PowerApps에 맞게 최적화하려면, 생성된 Swagger 2.0 API 정의를 다음 스키마 확장을 사용하여 선택적으로 사용자 지정할 수 있습니다.

Swagger 2.0 API 정의를 사용자 지정하는 일반적인 방법을 알아보려면 [Swashbuckle 생성 API 정의 사용자 지정](../app-service-api-dotnet-swashbuckle-customize.md)을 참조하세요.

### 스키마 확장
Swashbuckle에 의해 자동으로 생성되는 Swagger 외에, PowerApps용 API를 만들 때 사용할 수 있는 Swagger 확장이 더 있습니다. 이 섹션은 이러한 확장을 나열하고 설명합니다.

##### x-ms-summary
Swagger 스펙에 정의된 summary 필드가 없는 엔터티에 대한 표시 이름을 설명하는 문자열입니다. 여기에 해당하는 예로는 **Parameter names**가 있습니다.

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
	
- 호출되는 연산에 대한 operationId와 일치하는 문자열입니다.
- parameters: 개체의 속성이 연산에 필요한 매개 변수를 정의하는 개체입니다.
- value-collection: 응답 페이로드에 포함된 개체 배열의 값을 구하는 경로 문자열입니다.
- value-path: 매개 변수에 대한 값을 참조하는 "value-collection" 내부 개체에 포함된 경로 문자열입니다.
- value-title: 값에 대한 설명을 참조하는 "value-collection" 내부 개체에 포함된 경로 문자열입니다.


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

위의 예제에서 Swagger는 Salesforce에 새로운 개체를 생성하는 _TableData\_CreateItem_이라는 연산을 정의합니다.

Salesforce에는 기본 제공되는 개체가 많이 있습니다. _x-ms-dynamic-values_는 디자이너가 기본 제공되는 Salesforce 개체 목록을 알아내는 데 도움을 주기 위해 사용됩니다. _TableMetadata\_ListTables_을 호출하여 해당 데이터를 확보합니다.

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

이것은 연산에 대한 입력이 동적인 시나리오에 유용합니다. 예를 들어 SQL의 경우를 생각해 보겠습니다. 각 테이블의 스키마가 다릅니다. 따라서 사용자가 특정 테이블을 선택하면 논리 흐름 디자이너는 열 이름을 표시할 수 있도록 테이블 구조를 이해해야 합니다. 이런 경우, Swagger 정의에 _x-ms-dynamic-schema_가 있으면 스키마를 가져오도록 해당 연산을 호출합니다.

<!---HONumber=AcomDC_1203_2015-->