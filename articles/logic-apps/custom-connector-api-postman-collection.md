---
title: "Postman으로 사용자 지정 API 및 커넥터 설명 - Azure Logic Apps | Microsoft Docs"
description: "사용자 지정 API 및 커넥터를 설명하고 그룹화하고 구성할 수 있는 Postman Collection 만들기"
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
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Postman으로 사용자 지정 API 및 사용자 지정 커넥터 설명

[사용자 지정 API](../logic-apps/logic-apps-create-api-app.md)와 [사용자 지정 커넥터](../logic-apps/custom-connector-overview.md)를 더 쉽고 빠르게 개발할 수 있도록 API와 커넥터를 설명하기 위해 OpenAPI 문서가 아닌 [Postman](https://www.getpostman.com/) Collection을 만들 수 있습니다. Postman Collection을 사용하면 관련 API 요청을 구성하고 그룹화할 수 있습니다. 예를 들어 Azure Logic Apps, Microsoft Flow 또는 Microsoft PowerApps용 커넥터를 만들 때 Postman을 사용할 수 있습니다. 

이 자습서는 [Azure Cognitive Services Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)의 [언어 검색 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)를 예로 사용하여 [Postman Collection](https://www.getpostman.com/docs/postman/collections/creating_collections)을 만드는 보여줍니다. 이 API는 API에 전달하는 텍스트의 언어, 감정 및 핵심 문구를 식별합니다.

## <a name="prerequisites"></a>필수 조건

* Postman을 처음 사용하는 경우 [Postman 앱을 설치](https://www.getpostman.com/apps)하십시오.

* Azure 구독. 구독이 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/)으로 시작할 수 있습니다. 아니면 [종량제 구독](https://azure.microsoft.com/pricing/purchase-options/)에 등록하세요.

* Azure 구독이 있는 경우 [여기의 작업 1](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md)을 완료하여 Text Analytics API에 등록하세요. 

## <a name="create-a-postman-collection"></a>Postman Collection 만들기

컬렉션을 만들려면 먼저 API 끝점에 대한 HTTP 요청을 만들어야 합니다. 

### <a name="create-an-http-request-for-your-api"></a>API에 대한 HTTP 요청 만들기

1. API 끝점에 대한 [HTTP 요청](https://www.getpostman.com/docs/postman/sending_api_requests/requests)을 만들 수 있도록 Postman 앱을 엽니다. 자세한 내용은 Postman의 [요청 설명서](https://www.getpostman.com/docs/postman/sending_api_requests/requests)를 참조하세요.

   1. **작성기** 탭에서 HTTP 메서드를 선택하고 API 끝점에 대한 요청 URL을 입력한 다음 권한 부여 프로토콜이 있는 경우 해당 프로토콜을 선택합니다. 
   준비가 되면 **Params**를 선택합니다.

      이 자습서의 예제에는 다음 설정을 사용할 수 있습니다.

      ![요청 만들기: "HTTP 메서드", "요청 URL", "권한 부여"](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | 매개 변수 | 제안 값 | 
      | --------- | --------------- | 
      | **HTTP 메서드** | POST | 
      | **요청 URL** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **권한 부여** | "인증 없음" | | 
      ||| 

   2. 이제 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 키-값 쌍을 입력할 수 있습니다. Postman은 이러한 항목을 모아서 쿼리 문자열로 결합합니다.
   완료하면 **헤더**를 선택합니다.

      ![요청 계속: "매개 변수"](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | 매개 변수 | 제안 값 | 
      | --------- | --------------- | 
      | **Params** | **키**: "numberOfLanguagesToDetect" </br>**값**: "1" | 
      ||| 

   3. 요청 헤더에 대한 키-값 쌍을 입력합니다. 
   헤더 이름에 원하는 문자열을 입력합니다. 일반 HTTP 헤더는 드롭다운 목록에서 선택할 수 있습니다. 완료하면 **본문**을 선택합니다. 
   
      ![요청 계속: "헤더"](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | 매개 변수 | 값 | 
      | --------- | ----- | 
      | **헤더** | **키**: "Ocp-Apim-Subscription-Key" </br>**값**: *your-API-subscription-key*(Cognitive Services 계정에서 찾을 수 있음) <p>**키**: "Content-Type" </br> **값**: "application/json" | 
      ||| 

   4. 요청 본문에 보낼 콘텐츠를 입력합니다. 
   돌아오는 응답을 받아서 요청이 작동하는지 확인하려면 **보내기**를 선택합니다. 
   
      ![요청 계속: "본문"](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | 매개 변수 | 제안 값 | 
      | --------- | --------------- |    
      | **본문** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      응답 필드에는 API의 발생한 전체 응답(결과 또는 오류 포함)이 포함됩니다.

      ![요청 응답 받기](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. 요청이 작동하는지 확인한 후 Postman Collection에 요청을 저장합니다. 

   1. **저장**을 선택합니다. 
      
      !["저장"을 선택합니다.](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. **요청 저장** 아래에 **요청 이름**을 입력하고 선택적으로 **요청 설명**을 제공합니다. 

      > [!NOTE]
      > 이 값은 나중에 사용자 지정 커넥터에서 사용됩니다. 따라서 나중에 사용자 지정 API의 작업 요약 및 설명에 사용할 값과 동일한 값을 제공해야 합니다.

   3. **+ 컬렉션 만들기**를 선택하고 컬렉션 이름을 입력합니다. 
   확인 표시를 선택하고 컬렉션 폴더가 만들어지면 ***your-Postman-collection-name*에 저장**을 선택합니다.

      ![요청 저장](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>요청 응답 저장

요청을 저장한 후에는 응답을 저장할 수 있습니다. 그러면 나중에 요청을 로드할 때 응답이 예제로 나타납니다.

1. 응답 창 위에서 **응답 저장**을 선택합니다. 

   ![응답 저장](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   사용자 지정 커넥터는 요청당 하나의 응답만 지원합니다. 
   요청당 여러 개의 응답을 저장하면 첫 번째 응답만 사용됩니다.

2. 예제의 이름을 입력하고 **Save Example**(예제 저장)을 선택합니다.

3. 요청 및 응답이 더 있으면 Postman Collection을 계속 작성합니다.

### <a name="export-your-postman-collection"></a>Postman Collection 내보내기

1. 완료되면 컬렉션을 JSON 파일로 내보냅니다.

   ![컬렉션 내보내기](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. **Collection v1** 내보내기 형식을 선택하고 JSON 파일을 저장할 위치로 이동합니다.

   > [!NOTE]
   > 현재는 V1만 사용자 지정 커넥터에서 작동합니다.

   ![내보내기 형식 선택: "Collection v1"](./media/custom-connector-api-postman-collection/09-export-format.png)
   
이제 Postman Collection을 사용하여 사용자 지정 API 및 커넥터를 만들 수 있습니다. 컬렉션을 내보낸 후 JSON 파일을 Logic Apps, Flow 또는 PowerApps로 가져올 수 있습니다.

> [!IMPORTANT]
> Postman Collection에서 사용자 지정 커넥터를 만드는 경우 작업 및 트리거에서 `Content-type` 헤더를 제거해야 합니다. 예를 들어 대상 서비스 Flow가 이 헤더를 자동으로 추가합니다. 또한 작업 및 트리거에서 `securityDefintions` 섹션의 인증 헤더를 제거하십시오.

## <a name="next-steps"></a>다음 단계

* [Logic Apps: 사용자 지정 커넥터 등록](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: 커넥터 등록](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: 커넥터 등록](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [사용자 지정 커넥터 FAQ](../logic-apps/custom-connector-faq.md)
