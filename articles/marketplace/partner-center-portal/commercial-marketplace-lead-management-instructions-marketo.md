---
title: Marketo에서 리드 관리 구성 | Azure Marketplace
description: Azure marketplace 고객에 대 한 Marketo에 대 한 리드 관리를 구성 합니다.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 9470a593f5db564689a0b26601a201874c215aa2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902092"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo의 잠재 고객 관리 구성

이 문서에서는 marketplace 제품에서 판매 리드를 처리 하도록 Marketo CRM 시스템을 설정 하는 방법을 설명 합니다.

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

7. *MarketplaceLeadBacked* 탭에서 **Embed 코드**를 선택 합니다. 

    ![Embed 태그](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo embed 태그에 다음 예제와 같은 코드가 표시됩니다.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Embed 코드 형식에 표시 된 아래 필드의 값을 복사 합니다. 이러한 값을 사용 하 여 다음 단계에서 잠재 고객을 받도록 제품을 구성 합니다. 다음 예제를 참조하여 Marketo embed 태그 예제에서 필요한 ID를 가져옵니다.

    - 서버 ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - 양식 ID = **1179**

    **이러한 값을 파악 하는 또 다른 방법**

    - Marketo 인스턴스의 URL (예: "`serverID.marketo.com`")에 서버 ID가 있습니다.
    - "Munchkin 계정 ID" 필드의 관리 > Munchkin 메뉴로 이동 하거나 Marketo REST API 호스트 하위 도메인 `https://{Munchkin ID}.mktorest.com`의 첫 번째 부분에서 구독의 Munching ID를 가져옵니다.
    - 양식 ID는 marketplace에서 잠재 고객을 라우팅하는 7 단계에서 만든 Embed 코드 양식의 ID입니다.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Marketo에 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다. 

1. 제품에 대 한 **제품 설치** 페이지로 이동 합니다.
1. 리드 관리 섹션에서 **연결** 을 선택 합니다. 

    ![리드 관리-연결](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 연결 정보 팝업 창에서 리드 대상에 대해 **Marketo** 를 선택 합니다.

    ![리드 대상 선택](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. **서버 id**, **Munching 계정 ID**및 **양식 id**를 제공 합니다.

    >[!Note]
    >제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다. 

