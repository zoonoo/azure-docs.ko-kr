---
title: "사용자 지정 커넥터 등록 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps에서 사용할 사용자 지정 커넥터 설정"
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
ms.openlocfilehash: 2c384e27798bc4920ef73af1084600fdc4047f86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Azure Logic Apps에서 사용자 지정 커넥터 등록

REST API를 빌드하고 인증을 설정하고 OpenAPI 정의 파일 또는 Postman 컬렉션을 확보하면 커넥터를 등록할 준비가 됩니다. 

## <a name="prerequisites"></a>필수 조건

사용자 지정 커넥터를 등록하려면 다음 항목이 필요합니다.

* Azure에서 커넥터 등록을 위한 세부 정보(예: 이름, Azure 구독, Azure 리소스 그룹 및 사용할 위치)

* API를 설명하는 OpenAPI(Swagger) 파일 또는 Postman 컬렉션

  이 자습서에서는 [샘플 Azure Resources Manager OpenAPI 파일](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)을 사용할 수 있습니다.

* 커넥터를 나타내는 아이콘

* 커넥터에 대한 간단한 설명

* API의 호스트 위치

## <a name="1-create-your-connector"></a>1. 커넥터 만들기

1. Azure Portal의 기본 Azure 메뉴에서 **새로 만들기**를 선택합니다. 검색 창에 필터로 "Logic Apps 커넥터"를 입력하고 Enter 키를 누릅니다.

   ![새로 만들기, "Logic Apps 커넥터" 검색](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. 결과 목록에서 **Logic Apps 커넥터** > **만들기**를 선택합니다.

   ![Logic Apps 커넥터 만들기](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. 테이블의 설명대로 커넥터 등록을 위한 세부 정보를 제공합니다. 완료면 **대시보드에 고정** > **만들기**를 선택합니다.

   ![Logic App 사용자 지정 커넥터 세부 정보](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | 속성 | 제안 값 | 설명 | 
   | -------- | --------------- | ----------- | 
   | **Name** | *custom-connector-name* | 커넥터 이름을 제공합니다. | 
   | **구독** | *Azure-subscription-name* | Azure 구독을 선택합니다. | 
   | **리소스 그룹** | *Azure-resource-group-name* | Azure 리소스를 구성할 Azure 그룹을 만들거나 선택합니다. | 
   | **위치**: | *deployment-region* | 커넥터에 대한 배포 영역을 선택합니다. | 
   |||| 

   Azure에서 커넥터가 배치되면 사용자 지정 커넥터 메뉴가 자동으로 열립니다. 
   그렇지 않은 경우 Azure 대시보드에서 사용자 지정 커넥터를 선택합니다.

## <a name="2-define-your-connector"></a>2. 커넥터 정의

이제 커넥터를 만들기 위한 OpenAPI 파일 또는 Postman 컬렉션, 커넥터에서 사용하는 인증, 사용자 지정 커넥터에서 제공하는 작업과 트리거 및 작업과 트리거에서 사용할 수 있는 매개 변수를 지정합니다.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. 커넥터에 대한 OpenAPI 파일 또는 Postman Collection 지정

1. 커넥터 메뉴에서 **Logic Apps 커넥터**가 선택되어 있지 않으면 선택합니다. 도구 모음에서 **편집**을 선택합니다.

   ![사용자 지정 커넥터 편집](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. 사용자 지정 커넥터에 대한 작업 및 트리거를 생성, 보호 및 정의하기 위해 다음 테이블에 세부 정보를 제공할 수 있도록 **일반**을 선택합니다.

   1. **사용자 지정 커넥터**에서 API를 설명하는 OpenAPI(Swagger) 파일을 제공할 수 있는 옵션을 선택합니다.

      ![API용 OpenAPI 파일 제공](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | 옵션 | 형식 |설명 | 
      | ------ | ------ | ----------- | 
      | **OpenAPI 파일 업로드** | *OpenAPI(Swagger)-json-file* | OpenAPI 파일의 위치로 이동하여 해당 파일을 선택합니다. | 
      | **OpenAPI URL 사용** | http://*path-to-swagger-json-file* | API의 OpenAPI 파일에 대한 URL을 제공합니다. | 
      | **Postman Collection V1 업로드** | *exported-Postman-collection-V1-file* | 내보낸 Postman Collection의 위치를 V1 형식으로 찾습니다. | 
      |||| 

   2. **일반 정보**에서 커넥터 아이콘을 업로드합니다. 
   일반적으로**설명**, **호스트** 및 **기준 URL**필드는 OpenAPI 파일에서 자동으로 채워집니다. 
   그렇지 않은 경우 테이블의 설명대로 다음 정보를 추가하고 **계속**을 선택합니다. 

      ![커넥터 세부 정보](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | 옵션 또는 설정 | 형식 | 설명 | 
      | ----------------- | ------ | ----------- | 
      | **아이콘 업로드** | *png-or-jpg-file-under-1-MB* | 커넥터를 나타내는 아이콘 <p>색: 컬러 배경에 흰색 로고를 사용하는 것이 좋습니다. <p>크기: 230픽셀 사각형 내에 ~160 픽셀 로그 | 
      | **아이콘 배경색** | *icon-brand-color-hexadecimal-code* | <p>아이콘 파일의 배경색과 일치하는 아이콘 뒤의 색상입니다. <p>형식: 16진수. 예를 들어 #007ee5는 파란색을 나타냅니다. | 
      | **설명** | *connector-description* | 커넥터에 대한 간단한 설명을 제공합니다. | 
      | **호스트** | *connector-host* | Web API에 대한 호스트 도메인을 제공합니다. | 
      | **기준 URL** | *connector-base-URL* | 웹 API에 대한 기준 URL을 제공합니다. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. 커넥터에서 사용할 인증을 설명합니다.

1. 이제 커넥터에 사용되는 인증을 검토하거나 설명할 수 있도록 **보안**을 선택합니다. 인증은 서비스와 클라이언트 사이에 사용자의 ID가 적절히 흐르도록 합니다.

   ![인증 유형](./media/logic-apps-custom-connector-register/security.png)

   * OpenAPI 파일을 업로드하면 등록 마법사가 Web API에 사용되는 인증 유형을 자동으로 감지하고 해당 파일의 `securityDefinitions` 개체를 기반으로 마법사의 **보안** 섹션을 자동으로 채웁니다. 예를 들어 다음은 OAuth2.0을 사용하여 지정하는 섹션입니다.

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * OpenAPI 파일이 인증 유형 및 속성을 채우지 않은 경우 이 정보를 추가 할 수 있도록 **편집**을 선택합니다. 
   
     예를 들어, 이전에 [이 자습서에서 Azure AD 인증을 설정](../logic-apps/custom-connector-azure-active-directory-authentication.md)했다면 당시에 Web API 및 커넥터 보안을 위한 Azure AD 앱을 만들었습니다. 
     따라서 이전에 저장한 응용 프로그램 ID와 클라이언트 키를 제공할 수 있습니다.
    
     | 설정 | 제안 값 | 설명 | 
     | ------- | --------------- | ----------- | 
     | **인증 유형** | OAuth 2.0 | | 
     | **ID 공급자** | Azure Active Directory | | 
     | **클라이언트 ID** | *application-ID-for-connector-Azure-AD-app* | 커넥터의 Azure AD 앱에 대해 이전에 저장한 응용 프로그램 ID | 
     | **클라이언트 암호** | *client-key-for-connector-Azure-AD-app* | 커넥터의 Azure AD 앱용 클라이언트 키 | 
     | **로그인 URL** | `https://login.windows.net` | | 
     | **리소스 URL** | `https://management.core.windows.net/` | URL을 지정된 대로(후행 슬래시 포함) 정확히 추가했는지 확인합니다. | 
     |||| 

   * OpenAPI 파일을 자동으로 생성하는 Postman collection은 OAuth 2.0 또는 기본과 같이 지원되는 인증 유형을 사용할 때*만* 인증 유형을 자동으로 채웁니다.

2. 보안 정보를 입력한 후 커넥터를 저장하려면 페이지 위쪽에서 **커넥터 업데이트**를 선택한 다음 **계속**을 선택합니다. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. 커넥터의 작업 및 트리거 검토, 업데이트 또는 정의

1. 이제 사용자가 자신의 워크플로에 추가할 수 있는 새로운 작업 및 트리거를 검토, 편집 또는 정의할 수 있도록 **정의**를 선택합니다.

   작업 및 트리거는 **정의** 페이지를 자동으로 채우고 요청 및 응답 값을 포함하는 OpenAPI 파일 또는 Postman Collection에 정의된 작업을 기반으로 합니다. 따라서 필요한 작업이 여기에 표시되어 있으면 이 페이지에서 변경하지 않고 등록 프로세스의 다음 단계로 이동할 수 있습니다.

   ![커넥터 정의](./media/logic-apps-custom-connector-register/definition.png)

2. 선택적으로, 기존 작업 및 트리거를 편집하거나 새 작업을 추가하려면 이러한 단계를 계속 진행합니다.

#### <a name="edit-or-add-actions-for-your-connector"></a>커넥터에 대한 작업 편집 또는 추가

1. 기존 작업을 편집하려면 해당 작업의 번호를 선택합니다. OpenAPI 파일 또는 Postman Collection에 존재하지 않는 작업을 추가하려면 **작업**에서 **새 작업**을 선택합니다.

2. **일반**에서 작업에 대한 다음 정보를 제공하거나 편집합니다.
   
   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **요약** | *operation-name* | 작업의 제목 | 
   | **설명** | *operation-description* | 작업에 대한 설명. <p>**팁**: 설명이 마침표로 끝나야 합니다. |
   | **작업 ID** | *operation-identifier* | 작업을 식별하기 위한 고유 이름. 카멜 케이스(예: "GetPullRequest")를 사용합니다. | 
   |**표시 유형**| **없음**, **고급**, **내부** 또는 **중요** | 작업에 대한 사용자 측 표시 여부. 자세한 내용은 [OpenAPI 확장](../logic-apps/custom-connector-openapi-extensions.md#visibility)을 참조하세요. | 
   |||| 

3. 이제 작업에 대한 요청을 정의합니다.
  
   1. **요청** 섹션에서 **샘플에서 가져오기**를 선택합니다. 

   2. **샘플에서 가져오기** 페이지에서 샘플 요청을 붙여넣습니다. 

      일반적으로 샘플 요청은 **동사**, **URL**, **헤더** 및 **본문** 필드에 대한 정보를 얻을 수 있는 API 설명서에 제공됩니다. 예제를 보려면 [Text Analytics API 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)를 참조하세요.

      ![샘플 요청 가져오기](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Postman Collection에서 커넥터를 만드는 경우 작업 및 트리거에서 `Content-type` 헤더를 제거해야 합니다. Logic Apps가 이 헤더를 자동으로 추가합니다. 또한 작업 및 트리거에서 `Security` 섹션에 정의한 인증 헤더를 제거하십시오.

      고급 OpenAPI 기능은 [사용자 지정 커넥터용 OpenAPI 확장](../logic-apps/custom-connector-openapi-extensions.md)을 참조하세요.

   3. 요청 정의를 마치려면 **가져오기**를 선택합니다.

4. 이제 작업에 대한 응답을 정의합니다.

   1. **응답**에서 기본 응답을 지정할 수 있습니다. 
   **기본 응답 추가**를 선택합니다.

   2. **샘플에서 가져오기** 페이지에서 샘플 요청을 붙여넣은 다음 **가져오기**를 선택합니다.

5. 마지막으로 **유효성 검사**에서 작업에 대해 확인된 잠재적 문제를 검토하고 수정합니다.

#### <a name="edit-or-add-triggers-for-your-connector"></a>커넥터에 대한 트리거 편집 또는 추가

1. 기존 트리거를 편집하려면 해당 트리거의 번호를 선택합니다. OpenAPI 파일 또는 Postman Collection에 존재하지 않는 트리거를 추가하려면 **트리거**에서 **새 트리거**를 선택합니다.

2. **일반**에서 트리거에 대한 다음 정보를 제공하거나 편집합니다.

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **요약** | *operation-name* | 트리거의 제목 | 
   | **설명** | *operation-description* | 트리거에 대한 설명. <p>**팁**: 설명이 마침표로 끝나야 합니다. | 
   | **작업 ID** | *operation-identifier* | 트리거를 식별하기 위한 고유 이름. 카멜 케이스(예: "TriggerOnGitHubPushEvent")를 사용합니다. | 
   |**표시 유형**| **없음**, **고급**, **내부** 또는 **중요** | 트리거에 대한 사용자 측 표시 여부. 자세한 내용은 [OpenAPI 확장](../logic-apps/custom-connector-openapi-extensions.md#visibility)을 참조하세요. | 
   | **트리거 유형** | **웹후크** 또는 **폴링** | 트리거의 유형. 예를 들어, 웹후크 트리거는 특정 이벤트가 발생하기 전에 대기합니다. 폴링 트리거는 지정된 간격 및 빈도에 따라 서비스 끝점을 정기적으로 확인합니다. <p>웹후크 및 폴링 트리거 패턴에 대한 자세한 내용은 [사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)를 참조하세요. | 
   |||| 

3. 이제 트리거를 생성하는 요청을 정의합니다. 

   1. **요청** 섹션에서 **샘플에서 가져오기**를 선택합니다.

   2. **샘플에서 가져오기** 페이지에서 샘플 요청을 붙여넣습니다. 

      일반적으로 샘플 요청은 **동사**, **URL**, **헤더** 및 **본문** 필드에 대한 정보를 얻을 수 있는 API 설명서에 제공됩니다. 예제를 보려면 [Text Analytics API 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)를 참조하세요.

      ![샘플 요청 가져오기](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Postman Collection에서 커넥터를 만드는 경우 작업 및 트리거에서 `Content-type` 헤더를 제거해야 합니다. Logic Apps가 이 헤더를 자동으로 추가합니다. 또한 작업 및 트리거에서 `Security` 섹션에 정의한 인증 헤더를 제거하십시오.

      고급 OpenAPI 기능은 [사용자 지정 커넥터용 OpenAPI 확장](../logic-apps/custom-connector-openapi-extensions.md)을 참조하세요.

   3. 요청 정의를 마치려면 **가져오기**를 선택합니다. 

4. 이제 트리거의 응답을 정의합니다. **응답** 섹션에서 트리거의 유형에 따라 다음 단계를 수행합니다.

   **웹후크 트리거**
   1. **웹후크 응답**에서 **샘플에서 가져오기**를 선택합니다. 

   2. **샘플에서 가져오기** 페이지에서 샘플 요청을 붙여넣은 다음 **가져오기**를 선택합니다. 예제 응답을 보려면 [웹후크를 만들기 위한 GitHub API 참조](https://developer.github.com/v3/repos/hooks/#create-a-hook)를 참조하세요.

   3. **트리거 구성**에서 웹후크 만들기 요청에 사용할 매개 변수를 선택합니다. Logic Apps는 이 매개 변수 값을 사용하여 트리거와 통신하기 위해 서비스에서 사용하는 콜백 URL을 채웁니다.

   **폴링 트리거**
   1. **응답**에서 기본 응답을 지정할 수 있습니다. 
   **기본 응답 추가**를 선택합니다.

   2. **샘플에서 가져오기** 페이지에서 샘플 요청을 붙여넣은 다음 **가져오기**를 선택합니다.

   3. **트리거 구성**에서 쿼리 매개 변수, 매개 변수에 전달할 값 및 다음 요청에 대한 적절한 폴링 간격을 지정하는 *트리거 힌트*를 지정합니다.

5. 마지막으로 **유효성 검사**에서 트리거에 대해 확인된 잠재적 문제를 검토하고 수정합니다.

#### <a name="review-reference-definitions-for-your-connector"></a>커넥터에 대한 참조 정의 검토

**참조** 섹션은 API 설명을 통해 자동으로 채워지고 작업 및 트리거가 참조할 수 있는 매개 변수 필드를 설명합니다. 

1. **참조**에서 검토할 참조 정의의 번호를 선택합니다.

2. **이름**에서 참조 정의 이름을 검토하거나 업데이트합니다.

3. **유효성 검사**에서 참조 정의에 대해 확인된 잠재적 문제를 검토하고 수정합니다.

## <a name="3-finish-creating-your-connector"></a>3. 커넥터 생성 완료

준비가 되면 **만들기**를 선택하여 커넥터를 배포합니다. 기존 커넥터를 업데이트하는 경우 **커넥터 업데이트**를 선택합니다.

축하합니다. 이제 논리 앱을 만들 때 논리 앱 디자이너에서 커넥터를 찾아서 논리 앱에 해당 커넥터를 추가할 수 있습니다.

![논리 앱 디자이너에서 커넥터 찾기](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>다른 Logic Apps 사용자와 커넥터 공유

등록되었으나 인증되지 않은 사용자 지정 커넥터는 Microsoft에서 관리하는 커넥터처럼 작동하지만 커넥터의 작성자와 논리 앱이 배포된 지역에서 논리 앱에 대해 동일한 Azure Active Directory 테넌트 및 Azure 구독이 있는 사용자*만* 보고 사용할 수 있습니다. 공유는 선택 사항이지만 다른 사용자와 커넥터를 공유하려는 시나리오가 있을 수 있습니다. 

> [!IMPORTANT]
> 커넥터를 공유하는 경우 다른 사람이 해당 커넥터를 사용하기 시작할 수 있습니다. 
> ***커넥터를 삭제하면 해당 커넥터에 대한 모든 연결이 삭제됩니다.***
 
이러한 경계 외부의 사용자(예: 모든 Logic Apps, Flow 및 PowerApps 사용자)와 커넥터를 공유하려면 [Microsoft 인증을 위해 커넥터를 제출하세요](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>FAQ

**Q:** 사용자 지정 커넥터에 제한이 있나요? </br>
**A:** 예, [여기에서 사용자 지정 커넥터 제한](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)을 참조하세요.

## <a name="get-support"></a>지원 받기

* 개발 및 온보딩에 대한 지원이 필요하거나 등록 마법사에서 사용할 수 없는 기능을 요청하려면 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)에 문의하세요. Microsoft는 이 계정에서 개발자 질문 및 문제점을 모니터링한 후 적절한 팀으로 전송합니다.

* 질문하거나 질문에 답변하고, 다른 Azure Logic Apps 사용자가 무엇을 하는지 보려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

* Logic Apps 개선에 도움을 주려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요. 

## <a name="next-steps"></a>다음 단계

* [선택 사항: 커넥터 인증](../logic-apps/custom-connector-submit-certification.md)
* [사용자 지정 커넥터 FAQ](../logic-apps/custom-connector-faq.md)