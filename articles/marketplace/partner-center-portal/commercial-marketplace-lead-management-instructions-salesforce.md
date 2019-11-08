---
title: Salesforce에 대 한 리드 관리 구성 | Azure Marketplace
description: Azure Marketplace 고객에 대 한 Salesforce에서 리드 관리를 구성 합니다.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812147"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce의 잠재 고객 관리 구성

이 문서에서는 marketplace 제품에서 판매 리드를 처리 하도록 Salesforce 시스템을 설정 하는 방법을 설명 합니다.

## <a name="set-up-your-salesforce-system"></a>Salesforce 시스템 설정

1. Salesforce에 로그인합니다.
2. Salesforce 조명 환경을 사용 하는 경우.
    1. Salesforce 홈 페이지에서 **설치** 를 선택 합니다.
    ![Salesforce 설치](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 설치 페이지에서 **플랫폼 도구-> 기능 설정-> 마케팅 > 웹에서 리드**로 이동 하 여 탐색 합니다.
    ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Salesforce 클래식 환경을 사용 하는 경우:
    1. Salesforce 홈 페이지에서 **설치** 를 선택 합니다.
    Salesforce 클래식 설정 ![](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 설치 페이지에서 왼쪽 탐색을 탐색 하 여 **> 잠재 고객 > 웹-리드 > 사용자 지정**합니다.
    Salesforce 클래식 웹-리드](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png) ![

사용 중인 Salesforce 환경에 관계 없이 나머지 지침은 동일 합니다.

4. 웹- **리드 설치 페이지**에서 **웹-리드 만들기 양식** 단추를 선택 합니다.
5. **Web-to-Lead Setup**에서 **Create Web-to-Lead Form**을 선택합니다.
    Salesforce-웹-리드 설정 ![](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. 웹- **리드 만들기 폼**에서 `the Include reCAPTCHA in HTML` 설정이 선택 취소 되어 있는지 확인 하 고 **생성**을 선택 합니다. 
    Salesforce ![-웹-리드 양식 만들기](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. 일부 HTML 텍스트가 표시 됩니다. 텍스트 "oid"를 검색 하 고 HTML 텍스트에서 **oid 값** (따옴표 사이에 있는 텍스트만)을 복사 하 여 저장 합니다. 게시 포털의 **조직 식별자** 필드에이 값을 붙여넣습니다.
    Salesforce ![-웹-리드 양식 만들기](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. 선택 **완료**되었습니다.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Salesforce에 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다.

1. 제품에 대 한 **제품 설치** 페이지로 이동 합니다.
1. 리드 관리 섹션에서 **연결** 을 선택 합니다.
    ![리드 관리-연결](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 연결 정보 팝업 창에서 **잠재 고객 대상** 에 대해 **Salesforce** 를 선택 하 고 이전 단계를 수행 하 여 만든 웹에서 잠재 고객 양식의 `oid`를 **조직 식별자** 필드에 붙여넣습니다.

1. **저장**을 선택합니다. 

    >[!Note]
    >제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.

    ![연결 정보-리드 대상 선택](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![연결 정보-리드 대상 선택](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)