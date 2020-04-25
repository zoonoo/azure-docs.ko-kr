---
title: Salesforce에 대 한 리드 관리 구성 | Azure Marketplace
description: Azure Marketplace 고객에 대 한 Salesforce에서 리드 관리를 구성 합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: e0fbb09370e198772b4fc485b3c0fe8a56da4226
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133626"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce의 잠재 고객 관리 구성

이 문서에서는 상용 marketplace 제품에서 판매 리드를 처리 하도록 Salesforce 시스템을 설정 하는 방법을 설명 합니다.

> [!NOTE]
> Azure Marketplace는 **Country** 필드의 값 목록과 같은 미리 채워진 목록을 지원 하지 않습니다. 계속 하기 전에 설정 된 목록이 없는지 확인 합니다. 또는 잠재 고객을 받도록 [HTTPS 끝점](./commercial-marketplace-lead-management-instructions-https.md) 또는 [Azure 테이블](./commercial-marketplace-lead-management-instructions-azure-table.md) 을 구성할 수 있습니다.

## <a name="set-up-your-salesforce-system"></a>Salesforce 시스템 설정

1. Salesforce에 로그인합니다.
1. Salesforce 조명 환경을 사용 하는 경우:
    1. Salesforce 홈 페이지에서 **설정** 을 선택 합니다.

       ![Salesforce 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. **설치** 페이지에서 **플랫폼 도구** > **기능 설정** > **마케팅** > **웹-리드**로 이동 합니다.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

1. Salesforce 클래식 환경을 사용 하는 경우:

    1. Salesforce 홈 페이지에서 **설정** 을 선택 합니다.

       ![Salesforce 클래식 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. **설치** 페이지에서 **빌드** > **사용자 지정** > **Leads** > **웹-잠재 고객을**선택 합니다.

        ![Salesforce 클래식 웹-리드](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

나머지 지침은 사용 중인 Salesforce 환경에 관계 없이 동일 합니다.

1. 웹- **리드 설치** 페이지에서 **웹-리드 만들기 양식** 단추를 선택 합니다.
1. **웹-리드 설정**에서 **웹-리드 양식 만들기**를 선택 합니다.

    ![Salesforce 웹-리드 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. **웹-리드 양식 만들기**에서 `Include reCAPTCHA in HTML` 설정이 선택 취소 되어 있는지 확인 하 고 **생성**을 선택 합니다.

    ![Salesforce 웹-리드 양식 창 만들기](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. 몇 가지 HTML 텍스트가 표시 됩니다. 텍스트 "oid"를 검색 하 고 HTML 텍스트에서 **"oid" 값** 을 복사 하 여 (따옴표 사이에 있는 텍스트만) 저장 합니다. 게시 포털의 **조직 식별자** 필드에이 값을 붙여넣습니다.

    ![Salesforce HTML "oid" 값을 표시 하는 웹-리드 폼 만들기](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. **마침**을 선택 합니다.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Salesforce에 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다.

1. 제품에 대 한 **제품 설정** 페이지로 이동 합니다.
1. **리드 관리** 섹션에서 **연결** 을 선택 합니다.

    ![리드 관리 섹션 연결 단추](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. **연결 정보** 팝업 창에서 **잠재 고객 대상** 에 대해 **Salesforce** 를 선택 하 고 만든 웹에서 `oid` 잠재 고객 양식의 값을 **조직 식별자** 필드에 붙여넣습니다.

1. **연락처 전자 메일**에 새 잠재 고객이 수신 될 때 전자 메일 알림을 받을 회사의 사용자에 대 한 전자 메일 주소를 입력 합니다. 세미콜론으로 구분 하 여 여러 전자 메일을 제공할 수 있습니다.

1. **확인**을 선택합니다.

선행 대상에 성공적으로 연결 되었는지 확인 하려면 **유효성 검사** 단추를 선택 합니다. 성공 하면 잠재 고객 대상에 테스트 리드를 갖게 됩니다.

>[!NOTE]
>제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.

![연결 정보 팝업 창 리드 대상 상자 선택](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![연결 정보 팝업 창 연락처 전자 메일 유효성 검사 상자](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
