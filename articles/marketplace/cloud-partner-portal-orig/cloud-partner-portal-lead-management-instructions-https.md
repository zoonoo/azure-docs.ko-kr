---
title: HTTPS 엔드포인트 | Microsoft Docs
description: Https의 잠재 고객 관리를 구성합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807150"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS 엔드포인트를 사용하여 잠개 고객 관리 구성

HTTPS 엔드포인트를 사용하여 CRM 시스템에 쓸 수 있는 Azure Marketplace 및 AppSource 잠재 고객을 처리할 수 있습니다. 이 문서에서는 Microsoft Flow 자동화 서비스를 사용하여 잠개 고객 관리를 구성하는 방법을 설명합니다.


## <a name="create-a-flow-using-microsoft-flow"></a>Microsoft Flow를 사용하여 흐름 만들기

1.  [Flow](https://flow.microsoft.com/) 웹 페이지를 엽니다. **로그인**을 선택하거나 **무료 등록**을 선택하여 무료 Flow 계정을 만듭니다.

2.  로그인하고 메뉴 모음에서 **내 흐름**을 선택합니다.

    ![내 흐름](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  **빈 페이지에서 만들기**를 선택합니다.

    ![빈 페이지에서 만들기](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  **요청/응답** 커넥터를 선택한 후 요청 트리거를 검색합니다. 

    ![빈 페이지에서 만들기](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. **요청** 트리거를 선택합니다.
    ![요청 트리거](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  이 문서의 끝에 나오는 **JSON 예제**를 **요청 본문 JSON 스키마**에 복사합니다.

7.  새 단계를 추가하고 새 레코드를 만드는 작업과 함께 CRM 시스템을 선택합니다. 다음 화면 캡처는 **Dynamics 365 - 새 레코드 만들기**를 예제로 보여 줍니다.

    ![새 레코드 만들기](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  커넥터의 연결 입력을 제공하고 **Leads** 엔터티를 선택합니다.

    ![잠재 고객 선택](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Flows는 잠재 고객 정보를 제공하기 위한 양식을 보여 줍니다. 동적 콘텐츠 추가하도록 선택하여 입력 요청의 항목을 매핑할 수 있습니다.

    ![동적 콘텐츠 추가](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  원하는 필드를 매핑한 후 **저장**을 선택하여 흐름을 저장합니다.

11. HTTP POST URL이 요청에 만들어집니다. 이 URL을 복사하고 HTTPS 엔드포인트로 사용합니다.

    ![HTTP POST URL](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>잠재 고객을 HTTPS 엔드포인트로 보내도록 제안 구성

제안의 잠재 고객 관리 정보를 구성할 때 잠재 고객 대상으로 **HTTPS 엔드포인트**를 선택한 후 이전 단계에서 복사한 HTTP POST URL을 붙여 넣습니다.  

![동적 콘텐츠 추가](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

잠재 고객이 생성되면 Microsoft는 Flow로 잠재 고객을 보냅니다. 그러면 이 잠재 고객은 사용자가 구성한 CRM 시스템으로 라우팅됩니다.


## <a name="json-example"></a>JSON 예제

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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
