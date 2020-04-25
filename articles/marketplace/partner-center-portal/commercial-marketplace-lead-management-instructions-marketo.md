---
title: Marketo에서 리드 관리 구성 | Azure Marketplace
description: Azure Marketplace 고객에 대 한 Marketo에 대 한 리드 관리를 구성 합니다.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: b142e0ab1aaa242157e207ceecf958be51bb1721
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133649"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo의 잠재 고객 관리 구성

이 문서에서는 Marketo CRM 시스템을 설정 하 여 상업적 marketplace 제안의 판매 리드를 처리 하는 방법을 설명 합니다.

## <a name="set-up-your-marketo-crm-system"></a>Marketo CRM 시스템 설정

1. Marketo에 로그인합니다.

1. **Design Studio**를 선택합니다.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  **새 양식**을 선택합니다.

    ![Marketo 새 양식](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  **새 양식** 대화 상자에서 필수 필드를 입력 한 다음 **만들기**를 선택 합니다.

    ![Marketo 새 양식 만들기](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  **필드 세부 정보** 페이지에서 **마침**을 선택 합니다.

    ![Marketo 양식 마침](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  승인 후 닫기.

1. **MarketplaceLeadBackend** 탭에서 **Embed 코드**를 선택 합니다. 

    ![Marketo Embed 코드](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo embed 태그에 다음 예제와 같은 코드가 표시됩니다.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Embed 코드 폼에 표시 된 다음 필드의 값을 복사 합니다. 이러한 값을 사용 하 여 다음 단계에서 잠재 고객을 받도록 제품을 구성 합니다. Marketo Embed 코드 예제에서 필요한 Id를 가져오기 위한 지침으로 다음 예제를 사용 합니다.

    - 서버 ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - 양식 ID = **1179**

    이러한 값을 확인 하는 또 다른 방법은 다음과 같습니다.

    - Marketo 인스턴스의 URL (예:)에 서버 ID가 `serverID.marketo.com`있습니다.
    - **Munchkin 계정 ID** 필드 또는 Marketo REST API 호스트 하위 도메인 `https://{Munchkin ID}.mktorest.com`의 첫 번째 부분에서 **관리자** > **Munchkin** 메뉴로 이동 하 여 구독의 Munchkin ID를 가져옵니다.
    - 양식 ID는 marketplace에서 잠재 고객을 라우팅하는 7 단계에서 만든 Embed 코드 양식의 ID입니다.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Marketo에 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다. 

1. 제품에 대 한 **제품 설정** 페이지로 이동 합니다.

1. **리드 관리** 섹션에서 **연결** 을 선택 합니다. 

    ![리드 관리 섹션 연결 단추](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. **연결 정보** 팝업 창에서 **리드 대상**에 대해 **Marketo** 를 선택 합니다.

    ![리드 대상 선택](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. **서버 id**, **Munchkin 계정 ID**및 **양식 id**를 제공 합니다.

    > [!NOTE]
    > 제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다. 

1. **연락처 전자 메일**에 새 잠재 고객이 수신 될 때 전자 메일 알림을 받을 회사의 사용자에 대 한 전자 메일 주소를 입력 합니다. 세미콜론으로 구분 하 여 여러 전자 메일 주소를 제공할 수 있습니다.

1. **확인**을 선택합니다.

   선행 대상에 성공적으로 연결 되었는지 확인 하려면 **유효성 검사** 단추를 선택 합니다. 성공 하면 잠재 고객 대상에 테스트 리드를 갖게 됩니다.

   ![연결 정보 팝업 창](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
