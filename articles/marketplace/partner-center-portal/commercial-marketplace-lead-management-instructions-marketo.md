---
title: Marketo에서 잠재 고객 관리 구성 | Azure 마켓플레이스
description: Azure 마켓플레이스 고객을 위한 Marketo에 대한 잠재 고객 관리를 구성합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 04eae529efcf9509d8cd43a7629bb0b76b593cd0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252489"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo의 잠재 고객 관리 구성

이 문서에서는 마켓플레이스 오퍼에서 판매 잠재 고객을 처리하도록 Marketo CRM 시스템을 설정하는 방법에 대해 설명합니다.

## <a name="set-up-your-marketo-crm-system"></a>Marketo CRM 시스템 설정

1. Marketo에 로그인합니다.
2. **Design Studio**를 선택합니다.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  **새 양식**을 선택합니다.
    ![Marketo 새 양식](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  새 양식에서 필수 필드에 정보를 입력한 다음 **만들기**를 선택합니다.
    ![Marketo 새 양식 만들기](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  필드 세부 정보에서 **마침**을 선택합니다.
    ![Marketo 양식 마침](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  승인하고 닫습니다.

7. *마켓플레이스LeadBacked* 탭에서 **포함 코드를**선택합니다. 

    ![Embed 태그](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo embed 태그에 다음 예제와 같은 코드가 표시됩니다.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. 포함 코드 양식에 표시된 아래 필드의 값을 복사합니다. 다음 단계에서 잠재 고객을 수신하도록 오퍼를 구성하려면 이러한 값을 사용합니다. 다음 예제를 참조하여 Marketo embed 태그 예제에서 필요한 ID를 가져옵니다.

    - 서버 ID = **ys12**
    - 먼치킨 ID = **123-PQR-789**
    - 양식 ID = **1179**

    **이러한 값을 파악하는 또 다른 방법**

    - 서버 ID는 Marketo 인스턴스의 URL(예: "")에서`serverID.marketo.com`찾을 수 있습니다.
    - "Munchkin 계정 ID" 필드의 관리자>Munchkin 메뉴로 이동하거나 Marketo REST API 호스트 하위 도메인의 첫 번째 `https://{Munchkin ID}.mktorest.com`부분으로 이동하여 구독의 뭉칭 ID를 가져옵니다.
    - 양식 ID는 7단계에서 생성한 포함 코드 양식의 ID로, 마켓플레이스에서 잠재 고객을 라우팅합니다.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>잠재 고객을 Marketo로 보낼 오퍼 구성

게시 포털에서 오퍼의 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 따르십시오. 

1. **오퍼의 제안 설정** 페이지로 이동합니다.
1. 잠재 고객 관리 섹션에서 **연결을** 선택합니다. 

    ![잠재 고객 관리 - 연결](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 연결 세부 정보 팝업 창에서 잠재 고객 대상에 대한 **Marketo를** 선택합니다.

    ![잠재 고객 대상 선택](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. 서버 **ID,** **계정**ID 및 **양식 ID를**제공합니다.

    >[!Note]
    >오퍼의 나머지 부분을 구성을 완료하고 게시해야 쿠폰에 대한 잠재 고객을 받을 수 있습니다. 

5. **연락처 이메일** - 새 잠재 고객으로 연결될 때 이메일 알림을 받아야 하는 회사의 사람들에게 이메일을 제공합니다. 세미콜론으로 구분하여 여러 이메일을 제공할 수 있습니다.
6. **확인**을 선택합니다.

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 유효성 검사 단추를 클릭합니다. 성공하면 잠재 고객 대상에 테스트 잠재 고객도 있습니다.

![연결 정보](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)