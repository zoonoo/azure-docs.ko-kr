---
title: HTTPS 엔드포인트 | Azure 마켓플레이스
description: HTTPS 엔드포인트에 대한 잠재 고객 관리 구성
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 33359883df86091120295b93618a13476e428d2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262616"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS 엔드포인트를 사용하여 잠개 고객 관리 구성

>[!Note]
>이 지침에 사용된 전원 자동 자동화 커넥터에는 Power Automate에 대한 유료 구독이 필요합니다. 이 문서의 지침을 따르기 전에 이 사항을 설명하십시오.

CrM(고객 관계 관리) 시스템이 Azure Marketplace 및 AppSource 잠재 고객을 수신하기 위해 파트너 센터에서 명시적으로 지원되지 않는 경우 Power 자동화의 HTTPS 끝점을 사용하여 이러한 잠재 고객을 처리할 수 있습니다. HTTPS 끝점을 사용하면 이러한 잠재 고객을 전자 메일 알림으로 보내거나 Power Automate에서 지원하는 CRM(고객 관계 관리) 시스템에 기록할 수 있습니다. 이 문서의 지침은 > 기본 프로세스를 통해 Power Automate를 사용하여 새 흐름을 생성하며, 이 URL은 HTTPS **엔드포인트 URL** 필드의 게시 포털에 입력할 HTTP POST URL을 생성합니다. 또한 온라인에서 찾을 수 있는 [Postman이라는](https://www.getpostman.com/downloads/) 도구의 도움으로 흐름을 테스트하는 방법에 대한 지침이 포함되어 있습니다.

## <a name="create-a-flow-using-power-automate"></a>전원 자동화를 사용하여 흐름 만들기

1. [Flow](https://flow.microsoft.com/) 웹 페이지를 엽니다. **로그인을**선택하거나 계정이 아직 없는 경우 무료 **등록을** 선택하여 무료 Flow 계정을 만듭니다.

2. 로그인하고 메뉴 모음에서 **내 흐름**을 선택합니다.

3. **+자동화 - 빈 것에서**선택합니다.

    ![내 흐름 + 자동화 - 공백에서](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. *자동화된 흐름* 창 빌드에서 **건너뛰기**를 선택합니다. 

    ![자동 흐름 빌드 - 건너뛰기](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. **커넥터 및 트리거 검색** 필드에서 “요청”을 입력하여 요청 커넥터를 찾습니다.
6. *트리거*에서 **HTTP 요청을 받은 경우**를 선택합니다. 

    ![커넥터 요청 - 트리거](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. HTTP *요청이 수신되면* 창 복사본을 아래 JSON 스키마를 **요청 본문 JSON 스키마** 텍스트 상자에 붙여 넣습니다. 이 스키마는 Microsoft에서 잠재 고객 데이터를 포함하는 데 사용됩니다.

    ![커넥터 요청 - 트리거](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **JSON 스키마**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>구성의 이 시점에서 CRM 시스템에 연결하거나 전자 메일 알림을 구성하도록 선택할 수 있습니다. 선택한 지침에 따라 나머지 지침을 따르십시오.

### <a name="to-connect-to-a-crm-system"></a>CRM 시스템에 연결하려면

1. **+ 새 단계**를 선택합니다.
2. *검색 커넥터 및 작업이라는*위치를 검색하여 선택한 CRM 시스템을 선택하고 *작업* 섹션에서 선택하여 새 레코드를 만듭니다. 다음 화면 캡처에는 **Dynamics 365 -** 예제로 새 레코드 만들기가 표시됩니다.

    ![새 레코드 만들기](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. CRM 시스템과 연결된 **조직 이름을** 제공합니다. **엔터티 이름** 드롭다운 목록에서 **잠재 고객**을 선택합니다.

    ![잠재 고객 선택](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow에는 잠재 고객 정보를 입력하기 위한 양식이 나와 있습니다. 동적 콘텐츠를 추가하도록 선택하여 입력 요청에서 항목을 매핑할 수 있습니다. 다음 화면 캡처에는 **OfferTitle**이 예제로 나와 있습니다.

    ![동적 콘텐츠 추가](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. 원하는 필드를 매핑한 후 **저장**을 선택하여 흐름을 저장합니다. HTTP POST URL이 만들어지고 HTTP *요청이 수신된 창에서* 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용하여 이 URL을 복사하는 것은 전체 URL의 일부를 실수로 놓치지 않도록 하는 것이 중요합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요에 따라 이 URL을 저장합니다.

    ![HTTP 요청이 수신된 경우](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>이메일 알림을 설정하려면

1. 이제 JSON 스키마를 완료했습니다. **+ New step**
2. **작업 선택**에서 **작업**을 선택합니다.
3. **작업**에서 **전자 메일 보내기(Office 365 Outlook)를**선택합니다.

    >[!Note]
    >다른 이메일 공급자 검색을 사용 하 고 대신 작업으로 *전자 메일 알림 (메일) 보내기를* 선택 합니다.

    ![이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. **전자 메일 보내기** 창에서 다음과 같은 필수 필드를 구성합니다.

   - **To** - 잠재 고객을 보낼 유효한 이메일 주소를 하나 이상 입력합니다.
   - **제목** - Flow에서는 다음 화면 캡처의 **LeadSource**와 같이 동적 콘텐츠를 추가하는 옵션이 제공됩니다. 먼저 필드 이름을 입력한 다음 팝업 창에서 동적 콘텐츠 선택 목록을 클릭합니다. 

        >[!Note] 
        > 필드 이름을 추가할 때 각 을 사용하여 ":"를 따라 가다가 Enter하여 새 행을 만들 수 있습니다. 필드 이름을 추가한 후 동적 선택 목록에서 연결된 각 매개 변수를 추가할 수 있습니다.

        ![동적 콘텐츠를 사용하여 이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **본문** - 동적 콘텐츠 선택 목록에서 이메일 본문에 원하는 정보를 추가합니다. 예를 들어, LastName, FirstName, Email 및 Company입니다. <br> <br> 이메일 알림 설정을 완료하면 다음 화면 캡처에 나오는 예제와 같이 표시됩니다.


       ![이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. **저장**을 선택하여 흐름을 마칩니다. HTTP POST URL이 만들어지고 *HTTP 요청이 수신된 창에서* 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용하여 이 URL을 복사하는 것은 전체 URL의 일부를 실수로 놓치지 않도록 하는 것이 중요합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요에 따라 이 URL을 저장합니다.

   ![HTTP POST URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>테스트

온라인으로 다운로드할 수 있는 [Postman이라는](https://app.getpostman.com/app/download/win64)도구를 사용하여 다음 단계를 사용하여 모든 것이 예상대로 작동하는지 테스트할 수 있습니다. Windows에서 사용할 수 있습니다. 

1. 우체부 를 실행 하 고 테스트 도구를 설정 하려면 **새** > **요청을** 선택 합니다. 

   ![테스트 도구 설정 요청](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. *요청 저장* 양식을 작성한 다음 만든 폴더에 **저장합니다.**

   ![요청 저장](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. 드롭다운 목록에서 **POST를** 선택합니다. 

   ![내 흐름 테스트](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. 전원 자동화에서 만든 흐름에서 HTTP POST URL을 붙여 넣기 요청 *URL을 입력하라는*위치.

   ![HTTP 게시물 URL 붙여넣기](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. [흐름으로](https://flow.microsoft.com/) 돌아가서 흐름 메뉴 모음에서 **내 흐름으로** 이동하여 잠재 고객을 보낼 수 있도록 만든 흐름을 찾습니다.  흐름 이름 옆에 있는 3개의 점을 선택하고 **편집을**선택합니다.

   ![내 흐름 - 편집](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. 오른쪽 상단 모서리에서 **테스트를** 선택한 다음 "트리거 작업을 수행하겠습니다"를 선택한 다음 **테스트를**선택합니다. 화면 상단에 테스트가 시작되었음을 나타내는 표시가 표시됩니다.

   ![테스트 흐름 - 트리거](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Postman 앱으로 돌아가 HTTPS URL을 붙여넣은 위치 옆의 **보내기를** 선택합니다.

   ![내 흐름 테스트 - 보내기](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. 흐름으로 돌아가 결과를 확인합니다. 모든 것이 예상대로 작동하면 성공했다는 메시지가 표시됩니다.

   ![흐름 - 결과 확인](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. 또한 이메일을 받았어야 합니다. 이메일 받은 편지함을 확인합니다. 

    >[!Note] 
    >테스트에서 이메일이 표시되지 않으면 스팸 및 정크 폴더를 확인합니다. 아래에서 전자 메일 알림을 구성할 때 추가한 필드 레이블만 확인할 수 있습니다. 오퍼에서 생성된 실제 잠재 고객인 경우 본문 및 제목 줄의 잠재 고객 연락처의 실제 정보도 표시됩니다.

   ![받은 이메일](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>잠재 고객을 HTTPS 엔드포인트로 보내도록 제안 구성

게시 포털에서 오퍼의 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 따르십시오.

1. **오퍼의 제안 설정** 페이지로 이동합니다.
2. 잠재 고객 관리 섹션에서 **연결을** 선택합니다.
3. 연결 세부 정보 팝업 창에서 잠재 고객 **대상에** 대한 **HTTPS 끝점을** 선택하고 **HTTPS 끝점 URL** 필드에 이전 단계를 수행하여 만든 흐름에서 HTTP POST URL에 붙여넣습니다.
4. **연락처 이메일** - 새 잠재 고객으로 연결될 때 이메일 알림을 받아야 하는 회사의 사람들에게 이메일을 제공합니다. 세미콜론으로 구분하여 여러 이메일을 제공할 수 있습니다.
5. **확인**을 선택합니다.

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 유효성 검사 단추를 클릭합니다. 성공하면 잠재 고객 대상에 테스트 잠재 고객도 있습니다.

>[!Note] 
>오퍼의 나머지 부분을 구성을 완료하고 게시해야 쿠폰에 대한 잠재 고객을 받을 수 있습니다.

잠재 고객이 생성되면 Microsoft는 Flow로 잠재 고객을 보냅니다. 그러면 이 잠재 고객은 사용자가 구성한 CRM 시스템 또는 이메일 주소로 라우팅됩니다.

![잠재 고객 관리 - 연결](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![연결 정보](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![연결 정보](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

