---
title: HTTPS 엔드포인트를 사용하여 잠재 고객 관리 - Microsoft 상업용 Marketplace
description: Power Automate 및 HTTPS 엔드포인트를사용하여 Microsoft AppSource 및 Azure Marketplace에서 잠재 고객을 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: b4dabee6a725fd02b7be6dd12c8a032e3d816835
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489372"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>HTTPS 엔드포인트를 사용하여 상업용 Marketplace 잠재 고객 관리

CRM(고객 관계 관리) 시스템이 Microsoft AppSource 및 Azure Marketplace 잠재 고객을 받을 수 있도록 파트너 센터에서 명시적으로 지원되지 않는 경우 [Power Automate](https://powerapps.microsoft.com/automate-processes/)에서 HTTPS 엔드포인트를 사용하여 이러한 잠재 고객을 처리할 수 있습니다. HTTPS 엔드포인트를 사용하면 상업용 Marketplace 잠재 고객을 이메일 알림으로 전송하거나, Power Automate를 통해 지원되는 CRM 시스템에 기록할 수 있습니다.

이 문서에서는 파트너 센터에서 잠재 고객을 구성하는 데 사용할 HTTP POST URL을 생성하기 위해 Power Automate에서 새 흐름을 만드는 방법을 설명합니다. 또한 [Postman](https://www.getpostman.com/downloads/)을 사용하여 흐름을 테스트하는 단계가 포함됩니다.

>[!NOTE]
>이러한 지침에서 사용되는 Power Automate 커넥터를 이용하려면 Power Automate에 대한 유료 구독이 필요합니다. 이 흐름을 구성하기 전에 이 사항을 해결해야 합니다.

## <a name="create-a-flow-by-using-power-automate"></a>Power Automate를 사용하여 흐름 만들기

1. [Power Automate](https://flow.microsoft.com/) 웹 페이지를 엽니다. **로그인** 을 선택합니다. 계정이 아직 없는 경우 **체험판 가입** 을 선택하여 체험판 Power Automate 계정을 만듭니다.

1. 로그인하고 메뉴에서 **내 흐름** 을 선택합니다.

    ![내 흐름에 로그인](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. **+ 새로 만들기** 에서 **+ 즉시 - 새로 시작** 을 선택합니다.

    ![내 흐름 + 자동 - 새로 시작](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. 흐름의 이름을 지정한 다음, **이 흐름을 트리거하는 방법** 을 선택하고 **HTTP 요청을 받을 때** 를 선택합니다.

    ![자동화된 흐름 창 건너뛰기 단추 빌드](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. 흐름 단계를 클릭하여 확장합니다.

    ![흐름 단계 확장](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. 다음 방법 중 하나를 사용하여 **요청 본문 JSON 스키마** 를 구성합니다.

    - **요청 본문 JSON 스키마** 텍스트 상자에 JSON 스키마를 복사합니다.
    - **샘플 페이로드를 사용하여 스키마 생성** 을 선택합니다. **샘플 JSON 페이로드 입력 또는 붙여넣기** 텍스트 상자에서 JSON 예제에 붙여넣습니다. **완료** 를 선택하여 스키마를 만듭니다.

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

    **JSON 예제**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>구성의 이 시점에서 CRM 시스템에 연결하거나 이메일 알림을 구성할 수 있습니다. 선택한 내용에 따라 나머지 지침을 따릅니다.

### <a name="connect-to-a-crm-system"></a>CRM 시스템에 연결

1. **+ 새 단계** 를 선택합니다.
1. **커넥터 및 작업 검색** 이 표시된 곳에서 검색하여 선택한 CRM 시스템을 선택합니다. 작업이 표시된 **작업** 탭에서 작업을 선택하여 새 레코드를 만듭니다. 다음 화면은 **새 레코드 만들기(Dynamics 365)** 를 예제로 보여 줍니다.

    ![새 레코드 만들기](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. CRM 시스템과 연결된 **조직 이름** 을 입력합니다. **엔터티 이름** 드롭다운 목록에서 **잠재 고객** 을 선택합니다.

    ![잠재 고객 선택](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power Automate에는 잠재 고객 정보를 입력하기 위한 양식이 나와 있습니다. 동적 콘텐츠 추가하도록 선택하여 입력 요청의 항목을 매핑할 수 있습니다. 다음 화면에는 **OfferTitle** 이 예제로 나와 있습니다.

    ![동적 콘텐츠 추가](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. 원하는 필드를 매핑한 다음, **저장** 을 선택하여 흐름을 저장합니다. HTTP POST URL이 만들어지고 **HTTP 요청을 받은 경우** 창에 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용하여 이 URL을 복사합니다. 전체 URL의 일부를 누락하지 않도록 하려면 복사 컨트롤을 사용하는 것이 중요합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요하므로 이 URL을 저장합니다.

    ![HTTP 요청을 받은 경우](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>이메일 알림 설정

1. JSON 스키마를 완료했으므로 **+ 새 단계** 를 선택합니다.
1. **작업 선택** 에서 **작업** 을 선택합니다.
1. **작업** 탭에서 **이메일 보내기(Office 365 Outlook)** 를 선택합니다.

    >[!NOTE]
    >다른 이메일 공급자를 사용하려면 작업으로 **이메일 알림 보내기(메일)** 를 검색하여 선택합니다.

    ![이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. **이메일 보내기** 창에서 다음과 같은 필수 필드를 구성합니다.

   - **받는 사람** : 잠재 고객이 전송될 유효한 이메일 주소를 하나 이상 입력합니다.
   - **주체** : Power Automate에서는 다음 화면의 **LeadSource** 와 같이 동적 콘텐츠를 추가하는 옵션이 제공됩니다. 필드 이름을 입력하여 시작합니다. 그런 다음, 팝업 창에서 동적 콘텐츠 선택 목록을 선택합니다. 

        >[!NOTE] 
        > 필드 이름을 추가할 때 각 이름 뒤에 콜론(:)을 붙인 다음, **Enter** 키를 선택하여 새 행을 만들 수 있습니다. 필드 이름을 추가한 후, 동적 선택 목록에서 연결된 각 매개 변수를 추가할 수 있습니다.

        ![동적 콘텐츠를 사용하여 이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **본문** : 동적 콘텐츠 목록에서 원하는 정보를 이메일의 본문에 추가합니다. 예를 들어, LastName, FirstName, Email 및 Company입니다. 이메일 알림 설정을 완료하면 다음 화면의 예제와 같이 표시됩니다.


       ![이메일 알림 예제](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. **저장** 을 선택하여 흐름을 마칩니다. HTTP POST URL이 만들어지고 **HTTP 요청을 받은 경우** 창에 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용하여 이 URL을 복사합니다. 전체 URL의 일부를 누락하지 않도록 하려면 이 컨트롤을 사용하는 것이 중요합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요하므로 이 URL을 저장합니다.

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>테스트

[Postman](https://app.getpostman.com/app/download/win64)을 사용하여 구성을 테스트할 수 있습니다. Windows에서 Postman의 온라인 다운로드를 사용할 수 있습니다. 

1. Postman을 시작하고 **새로 만들기** > **요청** 을 선택하여 테스트 도구를 설정합니다. 

   ![테스트 도구 설정 요청](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. **요청 저장** 양식을 채운 다음, 생성한 폴더에 저장합니다.

   ![요청 저장 양식](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. 드롭다운 목록에서 **POST** 를 선택합니다. 

   ![내 흐름 테스트](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. **요청 URL 입력** 이라고 표시된 곳에 Power Automate에서 만든 흐름의 HTTP POST URL을 붙여넣습니다.

   ![HTTP POST URL 붙여넣기](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. [Power Automate](https://flow.microsoft.com/)로 돌아갑니다. Power Automate 메뉴 모음에서 **내 흐름** 으로 이동하여 잠재 고객을 보내기 위해 만든 흐름을 찾습니다. 흐름 이름 옆에 있는 줄임표를 선택하여 더 많은 옵션을 확인하고 **편집** 을 선택합니다.


1. 오른쪽 위 모서리에서 **테스트** 를 선택하고, **내가 트리거 작업 수행** 을 선택한 다음, **테스트** 를 선택합니다. 화면 위쪽에 테스트가 시작되었음을 나타내는 표시가 나타납니다.

   ![내가 트리거 작업 수행 옵션](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Postman 앱으로 돌아가서 **보내기** 를 선택합니다.

   ![보내기 단추](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. 흐름으로 돌아가서 결과를 확인합니다. 모든 것이 예상 대로 작동하는 경우 흐름이 성공했음을 나타내는 메시지가 표시됩니다.

   ![결과 확인](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. 또한 이메일을 수신해야 합니다. 이메일 수신함을 확인합니다. 

    >[!NOTE] 
    >테스트에서 이메일을 확인할 수 없는 경우 스팸 및 정크 폴더를 확인합니다. 다음 화면에서는 이메일 알림을 구성할 때 추가한 필드 레이블만 확인할 수 있습니다. 제품에서 생성된 실제 잠재 고객인 경우 본문 및 제목 줄에서 잠재 고객 연락처의 실제 정보를 볼 수도 있습니다.

   ![받은 이메일](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>잠재 고객을 HTTPS 엔드포인트로 보내도록 제안 구성

게시 포털에서 제품에 대한 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 수행합니다.

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 제품을 선택하고 **제품 설정** 탭으로 이동합니다.

1. **잠재 고객** 섹션에서 **연결** 를 선택합니다.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="잠재 고객":::

1. **연결 세부 정보** 팝업 창에서 **잠재 고객 대상** 에 대한 **HTTPS 엔드포인트** 를 선택합니다. 이전 단계를 수행하여 만든 흐름의 HTTP POST URL을 **HTTPS 엔드포인트 URL** 필드로 붙여넣습니다.
    ![연결 세부 정보 연락처 이메일](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. **연락처 이메일** 에서 새 잠재 고객이 수신될 때 이메일 알림을 받을 회사의 사용자에 대한 이메일 주소를 입력합니다. 세미콜론으로 구분하여 여러 이메일을 입력할 수 있습니다.

1. **확인** 을 선택합니다.

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 **유효성 검사** 단추를 선택합니다. 성공하면 잠재 고객 대상에서 테스트 잠재 고객이 생깁니다.

>[!NOTE] 
>제품에 대한 잠재 고객을 받으려면 먼저 나머지 제품 구성을 끝내고 게시해야 합니다.

잠재 고객이 생성되면 Microsoft에서 흐름으로 잠재 고객을 보냅니다. 잠재 고객은 사용자가 구성한 CRM 시스템 또는 이메일 주소로 라우팅됩니다.
