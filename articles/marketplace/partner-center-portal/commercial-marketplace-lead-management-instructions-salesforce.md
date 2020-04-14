---
title: 세일즈포스를 위한 잠재 고객 관리 구성 | Azure 마켓플레이스
description: Azure 마켓플레이스 고객을 위한 Salesforce에서 잠재 고객 관리를 구성합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263097"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce의 잠재 고객 관리 구성

이 문서에서는 상업용 마켓플레이스 오퍼에서 판매 잠재 고객을 처리하도록 Salesforce 시스템을 설정하는 방법에 대해 설명합니다.

> [!Note]
> 마켓플레이스는 **국가** 필드의 값 목록과 같이 미리 채워진 목록을 지원하지 않습니다. 계속하기 전에 설정된 목록이 없는지 확인합니다. 또는 잠재 고객을 수신하도록 [HTTPS 끝점](./commercial-marketplace-lead-management-instructions-https.md) 또는 [Azure 테이블을](./commercial-marketplace-lead-management-instructions-azure-table.md) 구성할 수 있습니다.

## <a name="set-up-your-salesforce-system"></a>Salesforce 시스템 설정

1. Salesforce에 로그인합니다.
2. Salesforce 조명 환경을 사용하는 경우
    1. 세일즈포스 홈 페이지에서 **설정을** 선택합니다.
    ![Salesforce 설치](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 설정 페이지에서 왼쪽 탐색을 통해 **플랫폼 도구 >기능 설정 >마케팅 >웹-투-리드로**이동합니다.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Salesforce 클래식 환경을 사용하는 경우:
    1. 세일즈포스 홈 페이지에서 **설정을** 선택합니다.
    ![세일즈포스 클래식 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 설치 페이지에서 왼쪽 탐색을 통해 **->사용자 지정->잠재 고객 >웹-리드.**

        ![세일즈포스 클래식 웹 투 리드](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

나머지 지침은 사용 중인 Salesforce 경험에 관계없이 동일합니다.

4. 잠재 고객 설정 페이지에서 **웹-리드 양식 만들기** 단추를 **선택합니다.**
5. **Web-to-Lead Setup**에서 **Create Web-to-Lead Form**을 선택합니다.
    ![세일즈포스 - 웹-투-리드 설정](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. **웹-리드 양식 만들기에서**설정이 선택취소되어 있는지 확인하고 `the Include reCAPTCHA in HTML` **생성을**선택합니다. 
    ![세일즈포스 - 웹-투-리드 양식 만들기](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. 일부 HTML 텍스트가 표시됩니다. 텍스트 "oid"를 검색하고 HTML 텍스트 (인용 부호 사이의 텍스트만)에서 **oid 값을** 복사하여 저장합니다. 게시 포털의 **조직 식별자** 필드에 이 값을 붙여넣습니다.
    ![세일즈포스 - 웹-투-리드 양식 만들기](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **완료됨을**선택했습니다.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>잠재 고객을 Salesforce로 보내도록 오퍼 구성

게시 포털에서 오퍼의 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 따르십시오.

1. **오퍼의 제안 설정** 페이지로 이동합니다.
1. 잠재 고객 관리 섹션에서 **연결을** 선택합니다.
    ![잠재 고객 관리 - 연결](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 연결 세부 정보 팝업 창에서 **잠재 고객 대상에** 대한 `oid` **Salesforce를** 선택하고 조직 **식별자** 필드에 이전 단계를 수행하여 만든 웹-리드 양식에 붙여넣습니다.

1. **연락처 이메일** - 새 잠재 고객으로 연결될 때 이메일 알림을 받아야 하는 회사의 사람들에게 이메일을 제공합니다. 세미콜론으로 구분하여 여러 이메일을 제공할 수 있습니다.

1. **확인을 선택합니다.**

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 유효성 검사 단추를 클릭합니다. 성공하면 잠재 고객 대상에 테스트 잠재 고객도 있습니다.

>[!Note]
>오퍼의 나머지 부분을 구성을 완료하고 게시해야 쿠폰에 대한 잠재 고객을 받을 수 있습니다.

![연결 세부 정보 - 잠재 고객 대상 선택](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![연결 세부 정보 - 잠재 고객 대상 선택](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
