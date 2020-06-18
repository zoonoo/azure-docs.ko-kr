---
title: Salesforce의 잠재 고객 관리 - Microsoft 상업용 Marketplace
description: Salesforce를 사용하여 Microsoft AppSource 및 Azure Marketplace에 대한 잠재 고객을 구성하는 방법을 알아봅니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7fbe27845164eb7711d3531173fb440c5a3570f4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849141"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce의 잠재 고객 관리 구성

이 문서에서는 Microsoft AppSource 및 Azure Marketplace에서 제품의 판매 잠재 고객을 처리하도록 Salesforce 시스템을 설정하는 방법을 설명합니다.

> [!NOTE]
> Azure Marketplace는 **국가** 필드의 값 목록과 같은 미리 채워진 목록을 지원하지 않습니다. 계속하기 전에 설정된 목록이 없는지 확인합니다. 잠재 고객을 수신하도록 [HTTPS 엔드포인트](./commercial-marketplace-lead-management-instructions-https.md) 또는 [Azure 테이블](./commercial-marketplace-lead-management-instructions-azure-table.md)을 구성할 수도 있습니다.

## <a name="set-up-your-salesforce-system"></a>Salesforce 시스템 설정

1. Salesforce에 로그인합니다.
1. **Web-to-Lead** 설정으로 이동합니다. 
    
    Salesforce 간편 환경을 사용하는 경우
    1. Salesforce 홈페이지에서 **Setup**을 선택합니다.

       ![Salesforce 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. **Setup** 페이지에서 **Platform Tools** > **Feature Settings** > **Marketing** > **Web-to-Lead**로 이동합니다.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Salesforce 클래식 환경을 사용하는 경우

    1. Salesforce 홈페이지에서 **Setup**을 선택합니다.

       ![Salesforce 클래식 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. **Setup** 페이지에서 **Build** > **Customize** > **Leads** > **Web-to-Lead**를 선택합니다.

        ![Salesforce 클래식 Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   나머지 단계는 두 Salesforce 환경에서 동일합니다.

1. **Web-to-Lead Setup** 페이지에서 **Create Web-to-Lead Form** 단추를 선택합니다.
1. **Web-to-Lead Setup**에서 **Create a Web-to-Lead Form**을 선택합니다.

    ![Salesforce Web-to-Lead 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. **Create a Web-to-Lead Form**에서 `Include reCAPTCHA in HTML` 설정을 선택 취소했는지 확인하고 **Generate**를 선택합니다.

    ![Salesforce Create a Web-to-Lead Form 창](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. HTML 텍스트가 표시됩니다. 텍스트 "oid"를 검색하고 HTML 텍스트에서 **"oid" 값**을 복사한 후(따옴표 사이에 있는 텍스트만) 저장합니다. 게시 포털의 **Organization Identifier** 필드에 이 값을 붙여 넣습니다.

    ![HTML "oid" 값을 표시하는 Salesforce Create a Web-to-Lead Form](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. **Finished**를 선택합니다.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Salesforce로 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대한 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 수행합니다.

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 제품을 선택하고 **제품 설정** 탭으로 이동합니다.

1. **잠재 고객** 섹션에서 **연결**를 선택합니다.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="잠재 고객":::

1. **Connection details** 팝업 창에서 **Lead destination**으로 **Salesforce**를 선택하고 만든 Web-to-Lead Form의 `oid` 값을 **Organization identifier** 필드에 붙여 넣습니다.

    ![Connection details 팝업 창 Validate Contact email 박스](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. **연락처 메일**에서 새 잠재 고객이 수신될 때 메일 알림을 받을 회사의 사용자에 대한 메일 주소를 입력합니다. 세미콜론으로 구분하여 여러 메일을 입력할 수 있습니다.

1. **확인**을 선택합니다.

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 **유효성 검사**를 선택합니다. 성공하면 잠재 고객 대상에서 테스트 잠재 고객이 생깁니다.

>[!NOTE]
>제품에 대한 잠재 고객을 받으려면 먼저 나머지 제품 구성을 끝내고 게시해야 합니다.
