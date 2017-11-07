---
title: "사용자 지정 커넥터용 OpenAPI 확장 - Azure Logic Apps | Microsoft Docs"
description: "고급 기능으로 사용자 지정 커넥터의 OpenAPI 확장"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>고급 기능으로 사용자 지정 커넥터의 OpenAPI 확장

Azure Logic Apps, Microsoft Flow 또는 Microsoft PowerApps에 대한 사용자 지정 커넥터를 만들려면 OpenAPI 정의 파일을 제공해야 합니다. 이 파일은 API의 작업과 매개 변수를 설명하는 문서이며 언어에 구애받지 않고 컴퓨터가 읽을 수 있는 문서입니다. OpenAPI의 기본 기능 외에도 Logic Apps 및 Flow용 사용자 지정 커넥터를 만들 때 다음과 같은 OpenAPI 확장을 포함할 수 있습니다.

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

이러한 확장에 대한 자세한 내용은 다음과 같습니다.

<a name="summary"></a>

## <a name="summary"></a>요약

작업의 제목을 지정합니다. </br>
적용 대상: 작업 </br>
권장: `summary`에서 *문장의 첫 글자를 대문자로* 사용합니다. </br>
예: "When an event is added to calendar" 또는 "Send an email"

![각 작업에 대한 "요약"](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

엔터티의 제목을 지정합니다. </br>
적용 대상: 매개 변수, 응답 스키마 </br>
권장: `x-ms-summary`에서 *단어의 첫 글자를 대문자로* 사용합니다. </br>
예: "Calendar ID", "Subject", "Event Description" 등

![각 엔터티에 대한 "x-ms-summary"](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>설명

작업의 기능이나 엔터티의 형식 및 함수에 대한 자세한 설명을 지정합니다. </br>
적용 대상: 작업, 매개 변수, 응답 스키마 </br>
권장: `description`에서 *문장의 첫 글자를 대문자로* 사용합니다. </br>
예: "This operation triggers when a new event is added to the calendar", "Specify the subject of the mail" 등

![각 작업 또는 엔터티에 대한 "description"](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

엔터티에 대한 사용자 측 표시 여부를 지정합니다. </br>
가능한 값: `important`, `advanced` 및 `internal` </br>
적용 대상: 작업, 매개 변수, 스키마

* `important` 작업 및 매개 변수가 항상 사용자에게 먼저 표시됩니다.
* `advanced` 작업 및 매개 변수가 추가 메뉴 아래에 숨겨집니다.
* `internal` 작업 및 매개 변수가 사용자에게 표시되지 않습니다.

> [!NOTE] 
> `internal` 및 `required` 매개 변수의 경우 이러한 매개 변수의 기본값을 **반드시** 제공해야 합니다.

예: **자세히 보기** 메뉴 및 **고급 옵션 표시** 메뉴는 `advanced` 작업 및 매개 변수를 숨깁니다.

![작업 및 매개 변수를 표시하거나 숨기기 위한 "x-ms-visibility"](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

사용자가 작업에 대한 입력 매개 변수를 선택할 수 있도록 사용자에게 채워진 목록을 표시합니다. </br>
적용 대상: 매개 변수 </br>
사용 방법: `x-ms-dynamic-values` 개체를 매개 변수 정의에 추가합니다. 예를 들어 이 [OpenAPI 샘플](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json)을 참조하십시오.

![목록 표시를 위한 "x-ms-dynamic-values"](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>x-ms-dynamic-values의 속성

| 이름 | 필수 또는 선택 | 설명 | 
| ---- | -------------------- | ----------- | 
| **operationID** | 필수 | 목록 채우기를 요청하는 작업 | 
| **value-path** | 필수 | 매개 변수 값을 참조하는 `value-collection` 안의 개체에 있는 경로 문자열. </br>`value-collection`이 지정되지 않으면 응답은 배열로 평가됩니다. | 
| **value-title** | 옵션 | 값의 설명을 참조하는 `value-collection` 안의 개체에 있는 경로 문자열. </br>`value-collection`이 지정되지 않으면 응답은 배열로 평가됩니다. | 
| **value-collection** | 옵션 | 응답 페이로드에 포함된 개체 배열로 평가되는 경로 문자열 | 
| **매개 변수** | 옵션 | 개체의 매개 변수가 dynamic-values 작업을 호출하는 데 필요한 입력 매개 변수를 지정하는 개체 | 
|||| 

다음은 `x-ms-dynamic-values`의 속성을 보여주는 예입니다.

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>예: 이 시점까지의 모든 OpenAPI 확장

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

현재 매개 변수 또는 응답의 스키마가 동적임을 나타냅니다. 이 개체는 이 필드의 값으로 정의된 작업을 호출하고 스키마를 동적으로 검색하고 사용자 입력을 수집하거나 사용 가능한 필드를 표시하는 데 적절한 UI를 표시할 수 있습니다. 

적용 대상: 매개 변수, 응답

사용 방법: `x-ms-dynamic-schema` 개체를 요청 매개 변수 또는 응답 본문 정의에 추가합니다. 예제는 [OpenAPI 샘플](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json)을 참조하세요.

이 예제는 사용자가 드롭다운 목록에서 선택한 항목에 따라 입력 양식이 어떻게 변하는지 보여줍니다.

![동적 매개 변수에 대한 "x-ms-dynamic-schema"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

이 예제는 사용자가 드롭다운 목록에서 선택한 항목에 따라 출력이 어떻게 변하는지 보여줍니다. 이 버전에서는 사용자가 "자동차"를 선택합니다.

![선택한 항목 "자동차"에 대한 "x-ms-dynamic-schema-response"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

이 버전에서는 사용자가 "음식"을 선택합니다.

![선택한 항목 "음식"에 대한 "x-ms-dynamic-schema-response"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>x-ms-dynamic-schema의 속성

| 이름 | 필수 또는 선택 | 설명 | 
| ---- | -------------------- | ----------- | 
| **operationID** | 필수 | 스키마 가져오기를 요청하는 연산 | 
| **매개 변수** | 필수 | 개체의 매개 변수가 dynamic-schema 작업을 호출하는 데 필요한 입력 매개 변수를 지정하는 개체 | 
| **value-path** |옵션 | 스키마가 있는 속성을 참조하는 경로 문자열. </br>지정하지 않으면 응답이 루트 개체의 특성에 스키마를 포함한다고 가정합니다. | 
|||| 

다음은 동적 매개 변수의 예입니다.

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

다음은 동적 응답의 예입니다.

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>다음 단계

* [사용자 지정 API 및 커넥터 설명](../logic-apps/custom-connector-api-postman-collection.md)
* [Logic Apps: 커넥터 등록](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: 커넥터 등록](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)