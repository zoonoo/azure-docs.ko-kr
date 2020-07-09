---
title: Marketo의 잠재 고객 관리 - Microsoft 상업용 Marketplace
description: Marketo CRM 시스템을 사용하여 Microsoft AppSource 및 Azure Marketplace에서 잠재 고객을 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: be1f21f927b01d66e19dc5e97b38e5c35e6664cb
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120265"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Marketo를 사용하여 상업용 Marketplace 잠재 고객 관리

이 문서에서는 Microsoft AppSource 및 Azure Marketplace에서 제품의 판매 잠재 고객을 처리하도록 Marketo CRM 시스템을 설정하는 방법을 설명합니다.

## <a name="set-up-your-marketo-crm-system"></a>Marketo CRM 시스템 설정

1. Marketo에 로그인합니다.

1. **Design Studio**를 선택합니다.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  **새 양식**을 선택합니다.

    ![Marketo 새 양식](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  **새 양식** 대화 상자에서 필수 필드에 정보를 입력한 다음, **만들기**를 선택합니다.

    ![Marketo 새 양식 만들기](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  **필드 세부 정보** 페이지에서 **마침**을 선택합니다.

    ![Marketo 양식 마침](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  승인하고 닫습니다.

1. **MarketplaceLeadBackend** 탭에서 **Embed 태그**를 선택합니다. 

    ![Marketo Embed 태그](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo embed 태그에 다음 예제와 같은 코드가 표시됩니다.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Embed 코드 양식에 표시된 다음 필드의 값을 복사합니다. 이러한 값을 사용하여 다음 단계에서 잠재 고객을 수신하도록 제품을 구성합니다. 다음 예제를 참조하여 Marketo embed 태그 예제에서 필요한 ID를 가져옵니다.

    - 서버 ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - 양식 ID = **1179**

    이러한 값을 확인하는 또 다른 방법은 다음과 같습니다.

    - Marketo 인스턴스의 URL(예: `serverID.marketo.com`)에서 서버 ID를 찾을 수 있습니다.
    - **Munchkin 계정 ID** 필드 또는 Marketo REST API 호스트 하위 도메인 `https://{Munchkin ID}.mktorest.com`의 첫 번째 부분에서 **관리** > **Munchkin** 메뉴로 이동하여 구독의 Munchkin ID를 가져옵니다.
    - 양식 ID는 Marketplace에서 잠재 고객을 라우팅하기 위해 7단계에서 만든 Embed 코드 양식의 ID입니다.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Marketo로 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대한 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 수행합니다. 

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 제품을 선택하고 **제품 설정** 탭으로 이동합니다.

1. **잠재 고객** 섹션에서 **연결**를 선택합니다.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="잠재 고객":::

1. **연결 세부 정보** 팝업 창에서 **잠재 고객 대상**으로 **Marketo**를 선택합니다.

    ![잠재 고객 대상 선택](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. **서버 ID**, **Munchkin 계정 ID**및 **양식 ID**를 제공합니다.

    > [!NOTE]
    > 제품에 대한 잠재 고객을 받으려면 먼저 나머지 제품 구성을 끝내고 게시해야 합니다. 

1. **연락처 메일**에서 새 잠재 고객이 수신될 때 메일 알림을 받을 회사의 사용자에 대한 메일 주소를 입력합니다. 세미콜론으로 구분하여 여러 메일 주소를 입력할 수 있습니다.

1. **확인**을 선택합니다.

   잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 **유효성 검사**를 선택합니다. 성공하면 잠재 고객 대상에서 테스트 잠재 고객이 생깁니다.

   ![연결 세부 정보 팝업 창](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
