---
title: 액체 변환으로 JSON 데이터 변환
description: Logic Apps 및 Liquid 템플릿을 사용하여 고급 JSON 변환에 대한 변환 또는 맵을 만듭니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: 0ab9297e772a3b75a077da1c2ae74e5058b2731f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657292"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Azure Logic Apps에서 Liquid 템플릿을 사용하여 고급 JSON 변환 수행

**작성** 또는 **구문 분석 JSON**과 같은 네이티브 데이터 조작 작업을 사용하여 Logic Apps에서 기본 JSON 변환을 수행할 수 있습니다. 고급 JSON 변환을 수행하려면 유연한 웹앱을 위한 오픈 소스 템플릿 언어인 [Liquid](https://shopify.github.io/liquid/)를 사용하여 템플릿 또는 맵을 만들 수 있습니다. 액체 템플릿은 JSON 출력을 변환하는 방법을 정의하고 반복, 제어 흐름, 변수 등과 같은 보다 복잡한 JSON 변환을 지원합니다.

논리 앱에서 유동 변환을 수행하려면 먼저 JSON에서 JSON매핑을 Liquid 템플릿으로 정의하고 해당 맵을 통합 계정에 저장해야 합니다. 이 문서에서는 이러한 Liquid 템플릿 또는 맵을 만들고 사용하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [논리 앱을 만드는 방법에](../logic-apps/quickstart-create-first-logic-app-workflow.md) 대한 기본 지식

* 기본 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* [액체 템플릿 언어에](https://shopify.github.io/liquid/) 대한 기본 지식

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>통합 계정에 대한 Liquid 템플릿 또는 맵 만들기

1. 이 예제에서는 이 단계에 설명된 샘플 Liquid 템플릿을 만듭니다. 액체 템플릿에서 [DotLiquid](https://dotliquidmarkup.org/) 및 C# 명명 규칙을 사용하는 [액체 필터를](https://shopify.github.io/liquid/basics/introduction/#filters)사용할 수 있습니다.

   > [!NOTE]
   > 필터 이름이 템플릿에서 *문장 대/소문자를* 사용하는지 확인합니다. 그렇지 않으면 필터가 작동하지 않습니다. 또한 맵에는 [파일 크기 제한이](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)있습니다.

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. Azure [포털에서](https://portal.azure.com)Azure 검색 상자에서 `integration accounts`을 입력하고 **통합 계정을**선택합니다.

   !["통합 계정" 찾기](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 통합 계정을 찾아 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. **개요** 창에서 **구성요소**아래에서 **맵을**선택합니다.

    !["맵" 타일 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. **지도** 창에서 **추가를** 선택하고 맵에 대한 세부 정보를 제공합니다.

   | 속성 | 값 | Description | 
   |----------|-------|-------------|
   | **이름** | `JsonToJsonTemplate` | 맵의 이름이며, 이 예제에서는 "JsonToJsonTemplate"입니다. | 
   | **맵 유형** | **liquid** | 맵의 형식입니다. JSON부터 JSON 변환의 경우 **Liquid**를 선택해야 합니다. | 
   | **지도** | `SimpleJsonToJsonTemplate.liquid` | 변환에 사용할 기존 Liquid 템플릿이나 맵 파일이며 이 예제에서는 "SimpleJsonToJsonTemplate.liquid"입니다. 이 파일을 찾으려면 파일 선택기를 사용할 수 있습니다. 맵 크기 제한은 [제한 및 구성을](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)참조하십시오. |
   ||| 

   ![액체 템플릿 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>JSON 변환에 대한 Liquid 작업 추가

1. Azure Portal에서 다음 단계에 따라 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Logic Apps 디자이너에서 논리 앱에 [요청 트리거](../connectors/connectors-native-reqres.md#add-request)를 추가합니다.

1. 트리거 아래에서 **새 단계**를 선택합니다. 검색 상자에서 필터로 입력하고 `liquid` 다음 작업을 선택합니다: **JSON을 JSON으로 변환 - 액체**

   ![Liquid 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. **맵** 목록을 열고 이 예제에서 "JsonToJsonTemplate"인 액체 템플릿을 선택합니다.

   ![맵 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   맵 목록이 비어 있으면 논리 앱이 통합 계정에 연결되어 있지 않을 수 있습니다. 
   Liquid 템플릿 또는 지도를 포함한 통합 계정에 논리 앱을 연결하려면 다음 단계를 따릅니다.

   1. 논리 앱 메뉴에서 **워크플로 설정**을 선택합니다.

   1. 통합 **계정 선택** 목록에서 통합 계정을 선택하고 **저장을**선택합니다.

      ![통합 계정에 논리 앱 연결](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 이제 이 작업에 **콘텐츠** 속성을 추가합니다. 새 **매개 변수 추가** 목록을 열고 **콘텐츠**를 선택합니다.

   ![작업에 "콘텐츠" 속성 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. **콘텐츠** 속성 값을 설정하려면 동적 콘텐츠 목록이 표시되도록 **콘텐츠** 상자 내부를 클릭합니다. 트리거에서 **본문** 콘텐츠 출력을 나타내는 Body 토큰을 선택합니다.

   !["콘텐츠" 속성 값에 대 한 "본문" 토큰을 선택 합니다.](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   완료되면 작업은 다음 예제와 같습니다.

   !["JSON을 JSON으로 변환" 액션 완료](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>논리 앱 테스트

[Postman](https://www.getpostman.com/postman) 또는 유사한 도구의 논리 앱에 JSON 입력을 게시합니다. 논리 앱에서 변환된 JSON 출력은 이 예제와 같습니다.
  
![예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>더 많은 Liquid 작업 예제
Liquid는 JSON 변환으로만 제한되지 않습니다. 다음은 Liquid를 사용하는 다른 사용 가능한 변환 작업입니다.

* JSON을 텍스트로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   샘플 입력 및 출력은 다음과 같습니다.
  
   ![JSON에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML에서 JSON으로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   샘플 입력 및 출력은 다음과 같습니다.

   ![XML에서 JSON으로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML에서 텍스트로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   샘플 입력 및 출력은 다음과 같습니다.

   ![XML에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 자세히 알아보기")  
* [맵에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-maps.md "엔터프라이즈 통합 맵에 대해 알아보기")  

