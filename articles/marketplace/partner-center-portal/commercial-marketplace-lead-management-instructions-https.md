---
title: HTTPS를 사용 하는 Microsoft 상업적 marketplace 리드 관리
description: HTTPS 끝점에 대 한 Microsoft 상업적 marketplace 리드 관리를 구성 합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 1c3337e970fdbb22cb1ed88f105d5e7798a68f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133731"
---
# <a name="configure-lead-management-by-using-an-https-endpoint"></a>HTTPS 끝점을 사용 하 여 리드 관리 구성

>[!NOTE]
>이러한 지침에서 사용 되는 전원 자동화 커넥터를 사용 하려면 유료 구독이 필요 합니다. 이 문서의 지침을 따르기 전에이를 고려해 야 합니다.

CRM (고객 관계 관리) 시스템이 파트너 센터에서 명시적으로 지원 되지 않고 Microsoft AppSource 및 Azure Marketplace 잠재 고객을 받을 수 있는 경우, 전원 자동화에서 HTTPS 끝점을 사용 하 여 이러한 잠재 고객을 처리할 수 있습니다. HTTPS 끝점을 사용 하 여 이러한 잠재 고객을 전자 메일 알림으로 전송 하거나 전원 자동화를 통해 지원 되는 CRM 시스템에 쓸 수 있습니다. 이 문서의 지침에서는 Power 자동화를 사용 하 여 새 흐름을 만드는 기본 프로세스를 안내 합니다 .이를 통해 **잠재 고객 관리** > **HTTPS 끝점 url** 필드에 대 한 게시 포털에 입력 하는 HTTP 게시 URL을 생성 합니다. 온라인에서 제공 되는 [Postman](https://www.getpostman.com/downloads/)이라는 도구를 사용 하 여 흐름을 테스트 하는 방법에 대 한 지침도 포함 되어 있습니다.

## <a name="create-a-flow-by-using-power-automate"></a>전원 자동화를 사용 하 여 흐름 만들기

1. [전원 자동화](https://flow.microsoft.com/) 웹 페이지를 엽니다. **로그인**을 선택합니다. 계정이 아직 없는 경우 **무료 등록** 을 선택 하 여 무료 전원 자동화 계정을 만듭니다.

1. 로그인 하 고 메뉴에서 **내 흐름** 을 선택 합니다.

1. **빈 페이지에서 + 자동을**선택 합니다.

    ![내 흐름 + 자동-비어 있음](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. **자동화 된 흐름 빌드** 창에서 **건너뛰기**를 선택 합니다. 

    ![자동화 된 흐름 창 건너뛰기 단추 빌드](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

1. **커넥터 및 트리거 검색** 필드에서 요청 커넥터를 찾기 위한 **요청** 을 입력 합니다.
1. **트리거**에서 **HTTP 요청을 받은 경우**를 선택합니다. 

    ![트리거 메뉴](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

1. **HTTP 요청을 받은 경우** 창에서 다음 json 스키마를 복사 하 여 **요청 본문 json 스키마** 텍스트 상자에 붙여넣습니다. 이 스키마는 Microsoft에서 잠재 고객 데이터를 포함 하는 데 사용 됩니다.

    ![요청 본문 JSON 스키마 텍스트 상자](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!NOTE]
>구성의이 시점에서 CRM 시스템에 연결 하거나 전자 메일 알림을 구성 하도록 선택할 수 있습니다. 선택한 내용에 따라 나머지 지침을 따릅니다.

### <a name="connect-to-a-crm-system"></a>CRM 시스템에 연결

1. **+ 새 단계**를 선택합니다.
1. **커넥터 및 작업 검색**을 표시 하는 선택 항목을 검색 하 여 선택한 CRM 시스템을 선택 합니다. **작업 탭에서** 작업을 선택 하 여 새 레코드를 만듭니다. 다음 화면에는 **새 레코드 (Dynamics 365)를 만드는** 예가 나와 있습니다.

    ![새 레코드 만들기](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. CRM 시스템에 연결 된 **조직 이름을** 제공 합니다. **엔터티 이름** 드롭다운 목록에서 **잠재 고객** 을 선택 합니다.

    ![잠재 고객 선택](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. 전원 자동화는 잠재 고객 정보를 제공 하는 폼을 표시 합니다. 동적 콘텐츠 추가하도록 선택하여 입력 요청의 항목을 매핑할 수 있습니다. 다음 화면에서는 **OfferTitle** 을 예로 보여 줍니다.

    ![동적 콘텐츠 추가](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. 원하는 필드를 매핑한 다음 **저장** 을 선택 하 여 흐름을 저장 합니다. Http POST URL은 만들어지고 **http 요청을 받을 때** 창에서 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용 하 여이 URL을 복사 합니다. 전체 URL의 일부를 누락 하지 않도록 하려면 복사 컨트롤을 사용 하는 것이 중요 합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요 하므로이 URL을 저장 합니다.

    ![HTTP 요청을 받은 경우](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>전자 메일 알림 설정

1. JSON 스키마를 완료 했으므로 **+ 새 단계**를 선택 합니다.
1. **작업 선택**에서 **작업**을 선택합니다.
1. **작업** 탭에서 **전자 메일 보내기 (Office 365 Outlook)** 를 선택 합니다.

    >[!NOTE]
    >다른 전자 메일 공급자를 사용 하려면를 검색 하 고 대신 작업으로 메일 **알림 (메일) 보내기** 를 선택 합니다.

    ![이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. **전자 메일 보내기** 창에서 다음 필수 필드를 구성 합니다.

   - **받는**사람: 잠재 고객이 전송 될 유효한 전자 메일 주소를 하나 이상 입력 합니다.
   - **주제**: 전원 자동화는 다음 화면에 표시 된 **LeadSource** 와 같은 동적 콘텐츠를 추가 하는 옵션을 제공 합니다. 필드 이름을 입력 하 여 시작 합니다. 그런 다음 팝업 창에서 동적 콘텐츠 선택 목록을 선택 합니다. 

        >[!NOTE] 
        > 필드 이름을 추가할 때 각 이름 앞에 콜론을 사용할 수 있습니다 (:) **Enter 키** 를 선택 하 여 새 행을 만듭니다. 필드 이름을 추가한 후에는 동적 선택 목록에서 연결 된 각 매개 변수를 추가할 수 있습니다.

        ![동적 콘텐츠를 사용 하 여 전자 메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **본문**: 동적 콘텐츠 선택 목록에서 전자 메일 본문에 원하는 정보를 추가 합니다. 예를 들어 LastName, FirstName, Email 및 Company를 사용 합니다. 전자 메일 알림을 설정 하는 작업을 완료 하면 다음 화면의 예와 같습니다.


       ![전자 메일 알림 예제](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. **저장**을 선택하여 흐름을 마칩니다. Http POST URL은 만들어지고 **http 요청을 받을 때** 창에서 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용 하 여이 URL을 복사 합니다. 전체 URL의 일부를 놓치지 않으려면이 컨트롤을 사용 하는 것이 중요 합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요 하므로이 URL을 저장 합니다.

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>테스트

온라인으로 다운로드할 수 있는 [Postman](https://app.getpostman.com/app/download/win64)이라는 도구를 사용 하 여 모든 것이 예상 대로 작동 하는지 테스트할 수 있습니다. 이 도구는 Windows에서 사용할 수 있습니다. 

1. Postman을 시작 하 고 **새** > **요청** 을 선택 하 여 테스트 도구를 설정 합니다. 

   ![테스트 도구 설정 요청](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. **저장 요청** 양식을 입력 한 다음 만든 폴더에 저장 합니다.

   ![요청 양식 저장](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. 드롭다운 목록에서 **POST** 를 선택 합니다. 

   ![내 흐름 테스트](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. **요청 Url 입력**이라고 표시 된 전원 자동화에서 만든 흐름에서 HTTP POST URL을 붙여 넣습니다.

   ![HTTP POST URL 붙여넣기](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. [전원 자동화](https://flow.microsoft.com/)로 돌아갑니다. 전원 자동화 메뉴 모음에서 **내 흐름** 으로 이동 하 여 잠재 고객을 보내기 위해 만든 흐름을 찾습니다. 흐름 이름 옆에 있는 줄임표를 선택 하 여 더 많은 옵션을 표시 하 고 **편집**을 선택 합니다.


1. 오른쪽 위 모서리에서 **테스트** 를 선택 하 고 **트리거 작업 수행**을 선택한 다음 **테스트**를 선택 합니다. 화면 위쪽에 테스트가 시작 되었음을 나타내는 표시가 표시 됩니다.

   ![트리거 동작 옵션을 수행 합니다.](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Postman 앱으로 돌아가서 **보내기**를 선택 합니다.

   ![보내기 단추](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. 흐름으로 돌아가서 결과를 확인 합니다. 모든 것이 예상 대로 작동 하는 경우 흐름에 성공 했음을 나타내는 메시지가 표시 됩니다.

   ![결과 확인](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. 또한 전자 메일을 수신 해야 합니다. 전자 메일 받은 편지함을 확인 합니다. 

    >[!NOTE] 
    >테스트에서 전자 메일을 볼 수 없는 경우 스팸 및 정크 폴더를 확인 합니다. 다음 화면에서 전자 메일 알림을 구성할 때 추가한 필드 레이블만 확인할 수 있습니다. 제품에서 실제 잠재 고객이 생성 된 경우 본문 및 제목 줄에서 잠재 고객 연락처의 실제 정보를 볼 수도 있습니다.

   ![받은 전자 메일](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>잠재 고객을 HTTPS 엔드포인트로 보내도록 제안 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다.

1. 제품에 대 한 **제품 설정** 페이지로 이동 합니다.
1. **리드 관리** 섹션에서 **연결** 을 선택 합니다.
1. **연결 정보** 팝업 창에서 **리드 대상**에 대해 **HTTPS 끝점** 을 선택 합니다. 이전 단계를 수행 하 여 만든 흐름의 HTTP POST URL을 **HTTPS 끝점 url** 필드에 붙여넣습니다.
1. **연락처 전자 메일**에 새 잠재 고객이 수신 될 때 전자 메일 알림을 받을 회사의 사용자에 대 한 전자 메일 주소를 입력 합니다. 세미콜론으로 구분 하 여 여러 전자 메일을 제공할 수 있습니다.
1. **확인**을 선택합니다.

선행 대상에 성공적으로 연결 되었는지 확인 하려면 **유효성 검사** 단추를 선택 합니다. 성공 하면 잠재 고객 대상에 테스트 리드를 갖게 됩니다.

>[!NOTE] 
>제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.

잠재 고객이 생성 되 면 Microsoft에서 흐름으로 잠재 고객을 보냅니다. 잠재 고객은 구성한 CRM 시스템 또는 전자 메일 주소로 라우팅됩니다.

![리드 관리 연결 단추](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![연결 정보 리드 대상](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![연결 정보 연락처 전자 메일](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

