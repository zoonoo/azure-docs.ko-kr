---
title: HTTPS 끝점을 사용 하 여 리드 관리 구성 | Azure Marketplace
description: HTTP 끝점을 사용 하 여 Microsoft AppSource 및 Azure Marketplace 리드를 처리 하는 방법을 알아봅니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770155"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS 엔드포인트를 사용하여 잠개 고객 관리 구성

HTTPS 끝점을 사용 하 여 Microsoft AppSource 및 Azure Marketplace 잠재 고객을 처리할 수 있습니다. 이러한 잠재 고객은 CRM (고객 관계 관리) 시스템에 기록 되거나 전자 메일 알림으로 전송 될 수 있습니다. 이 문서에서는 [Microsoft Power 자동화](https://powerapps.microsoft.com/automate-processes/) 자동화 서비스를 사용 하 여 리드 관리를 구성 하는 방법을 설명 합니다.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Microsoft 파워 자동화를 사용 하 여 흐름 만들기

1. [전원 자동화](https://flow.microsoft.com/) 웹 페이지를 엽니다. **로그인**을 선택하거나 **무료 등록**을 선택하여 무료 Flow 계정을 만듭니다.

1. 로그인하고 메뉴 모음에서 **내 흐름**을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![내 흐름](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. **+ 새로 만들기**아래에서 **+ 인스턴트**를 선택 합니다.
    > [!div class="mx-imgBorder"]
    > ![빈 페이지에서 만들기](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. 흐름 이름을 지정한 다음 **이 흐름을 트리거하는 방법 선택**에서 **HTTP 요청을 받을 때**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![HTTP 요청을 받은 트리거 선택](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. 흐름 단계를 클릭 하 여 확장 합니다.

    > [!div class="mx-imgBorder"]
    > ![흐름 단계 확장](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. 다음 방법 중 하나를 사용 하 여 **요청 본문 JSON 스키마**를 구성 합니다.

   - 이 문서의 끝에 나오는 [JSON 스키마](#json-schema)를 **요청 본문 JSON 스키마** 텍스트 상자에 복사합니다.
   - **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다. **샘플 JSON 페이로드 입력 및 붙여넣기** 텍스트 상자에서 [JSON 예제](#json-example)에 붙여넣습니다. **완료**를 선택하여 스키마를 만듭니다.

   >[!Note]
   >흐름의 이 시점에서 CRM 시스템에 연결하거나 이메일 알림을 구성할 수 있습니다.

### <a name="to-connect-to-a-crm-system"></a>CRM 시스템에 연결하려면

1. **+ 새 단계**를 선택합니다.
2. 새 레코드를 만드는 작업과 함께 CRM 시스템을 선택합니다. 다음 화면 캡처는 **Dynamics 365 - 새 레코드 만들기**를 예제로 보여줍니다.

    ![새 레코드 만들기](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. 커넥터의 연결 입력인 **조직 이름**을 입력합니다. **엔터티 이름** 드롭다운 목록에서 **잠재 고객**을 선택합니다.

    ![잠재 고객 선택](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow에는 잠재 고객 정보를 입력하기 위한 양식이 나와 있습니다. 동적 콘텐츠 추가하도록 선택하여 입력 요청의 항목을 매핑할 수 있습니다. 다음 화면 캡처에는 **OfferTitle**이 예제로 나와 있습니다.

    ![동적 콘텐츠 추가](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. 원하는 필드를 매핑한 후 **저장**을 선택하여 흐름을 저장합니다.

6. HTTP POST URL이 요청에 만들어집니다. 이 URL을 복사하고 HTTPS 엔드포인트로 사용합니다.

    ![HTTP POST URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>이메일 알림을 설정하려면

1. **+ 새 단계**를 선택합니다.
2. **작업 선택**에서 **작업**을 선택합니다.
3. **작업** 아래에서 **전자 메일 보내기**를 선택합니다.

    ![이메일 작업 추가](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. **이메일 보내기**에서 다음과 같은 필수 필드를 구성합니다.

   - **받는 사람** - 유효한 이메일 주소를 하나 이상 입력합니다.
   - **제목** - Flow에서는 다음 화면 캡처의 **LeadSource**와 같이 동적 콘텐츠를 추가하는 옵션이 제공됩니다.

     ![동적 콘텐츠를 사용하여 이메일 작업 추가](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **본문** - 동적 콘텐츠 목록에서 원하는 정보를 이메일의 본문에 추가합니다. 예를 들어, LastName, FirstName, Email 및 Company입니다.

   이메일 알림 설정을 완료하면 다음 화면 캡처에 나오는 예제와 같이 표시됩니다.

   ![이메일 작업 추가](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. **저장**을 선택하여 흐름을 마칩니다.

6. HTTP POST URL이 요청에 만들어집니다. 이 URL을 복사하고 HTTPS 엔드포인트로 사용합니다.

    ![HTTP POST URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>잠재 고객을 HTTPS 엔드포인트로 보내도록 제안 구성

제품의 잠재 고객 관리 정보를 구성할 때 **잠재 고객 대상**으로 **HTTPS 엔드포인트**를 선택하고 이전 단계에서 복사한 HTTP POST URL을 붙여넣습니다.  

![동적 콘텐츠 추가](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

잠재 고객이 생성 되 면 Microsoft는 사용자가 구성한 CRM 시스템 또는 전자 메일 주소로 라우팅되는 전원 자동화 흐름으로 잠재 고객을 보냅니다.

## <a name="json-schema-and-example"></a>JSON 스키마 및 예제

JSON 테스트 예제에서는 다음 스키마를 사용합니다.

### <a name="json-schema"></a>JSON 스키마

``` json
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
    "Description": {
      "id": "/properties/Description",
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

다음 JSON 예제를 복사 하 고 편집 하 여 흐름에서 테스트로 사용할 수 있습니다.

### <a name="json-example"></a>JSON 예제

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

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 Cloud 파트너 포털에서 [잠재 고객](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)을 구성합니다.
