---
title: Azure 청구에 대한 액세스 관리 | Microsoft Docs
description: 팀 멤버의 Azure 청구 정보에 대한 액세스를 제공하는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491159"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Azure의 청구 정보에 대한 액세스 관리

Azure portal에서 계정에 대 한 청구 정보에 대 한 액세스를 다른 사용자가 제공할 수 있습니다. 청구 역할 유형 및 청구 정보에 액세스할 수 있도록 지침 대금 청구 계정 유형에 따라 달라 집니다. 대금 청구 계정 유형을 결정을 참조 하세요 [대금 청구 계정 유형을 확인](#check-the-type-of-your-billing-account)합니다.

문서는 Microsoft Online Service 프로그램 계정 사용 하 여 고객에 게 적용 됩니다. Azure 고객으로 EA (기업 계약)를 엔터프라이즈 관리자는 엔터프라이즈 포털에서 부서 관리자 및 계정 소유자 권한을 제공할 수 있습니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](billing-understand-ea-roles.md)를 참조하세요. Microsoft 고객 계약 사용자 인 경우 하세요 [Azure의 관리자 역할 이해 Microsoft 고객 계약](billing-understand-mca-roles.md)합니다. 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Microsoft Online Service 프로그램 계정에 대 한 계정 관리자

계정 관리자는 Microsoft Online 서비스 프로그램 대금 청구 계정에 대 한 유일한 소유자입니다. Azure에 등록 하는 사람에 게 역할이 할당 됩니다. 계정 관리자 구독을 만들고, 청구서 보기 또는 구독에 대 한 청구 변경 하는 등 다양 한 청구 작업을 수행할 수 있습니다.

## <a name="give-others-access-to-view-billing-information"></a>청구 정보를 볼 수 있는 권한을 다른 사용자에 게

계정 관리자 권한을 부여할 수 있습니다 다른 Azure 청구 정보에 대 한 액세스 계정 구독에서 다음 역할 중 하나를 할당 하 여 합니다.

- 서비스 관리자
- 공동 관리자
- 소유자
- 참가자
- 판독기
- 청구 읽기 권한자

이러한 역할에서 청구 정보에 액세스할 수 합니다 [Azure portal](https://portal.azure.com/)합니다. 이러한 역할에 할당 된 사용자를 사용할 수도 있습니다는 [청구 Api](billing-usage-rate-card-overview.md) 청구서 및 사용량 세부 정보를 프로그래밍 방식으로 가져오려면.

역할을 할당하려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

\* * EA 고객 인 경우 계정 소유자는 팀의 다른 사용자에 게 위의 역할을 할당할 수 있습니다. 하지만 청구 정보를 보려면 이러한 사용자에 대 한 엔터프라이즈 관리자 AO 보기 요금 엔터프라이즈 포털에서 설정 해야 합니다.


### <a name="opt-in"></a> 사용자가 청구서를 다운로드하도록 허용

계정 관리자가에 할당 된 후 적절 한 역할을 다른 사용자는 Azure portal에서 청구서 다운로드에 대 한 액세스 설정 해야 합니다. 2016년 12월보다 오래된 청구서는 계정 관리자만 사용할 수 있습니다.

1. 에 로그인 합니다 [Azure portal](https://portal.azure.com/), 계정 관리자는

1. **Cost Management + 청구**에서 검색합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. 선택 **구독** 왼쪽 창에서. 사용자 액세스에 따라 청구 범위를 선택한 다음 선택 해야 할 수 있습니다 **구독**합니다.
 
    ![보여 주는 스크린샷 구독 선택](./media/billing-manage-access/billing-select-subscriptions.png)

1. 선택 **청구서** 차례로 **송장에 대 한 액세스**합니다.

    ![청구서에 대한 액세스를 위임하는 방법을 보여 주는 스크린샷](./media/billing-manage-access/AA-optin.png)

1. **켬**을 선택하고 저장합니다.

    ![청구서 액세스에 대한 위임을 설정/해제하는 방법을 보여 주는 스크린샷](./media/billing-manage-access/AA-optinAllow.png)

또한 계정 관리자는 전자 메일을 통해 청구서가 전송되도록 구성할 수도 있습니다. 자세한 내용은 [전자 메일로 청구서 받기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요.

## <a name="give-read-only-access-to-billing"></a>청구에 대한 읽기 전용 액세스 부여

구독 청구 정보에 대한 읽기 전용 액세스가 필요하며 Azure 서비스 관리 또는 생성 기능은 필요하지 않은 사람에게는 청구 읽기 권한자 역할을 할당합니다. 이 역할은 Azure 구독에 대해 재무 및 비용 관리를 담당하는 조직의 사용자에게 적합합니다.

청구 읽기 권한자 기능은 미리 보기로 제공되며 비전역 클라우드는 아직 지원하지 않습니다.

1. 에 로그인 합니다 [Azure portal](https://portal.azure.com/), 계정 관리자는

1. **Cost Management + 청구**에서 검색합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. 선택 **구독** 왼쪽 창에서. 사용자 액세스에 따라 청구 범위를 선택한 다음 선택 해야 할 수 있습니다 **구독**합니다.
 
    ![보여 주는 스크린샷 구독 선택](./media/billing-manage-access/billing-select-subscriptions.png)

1. **액세스 제어(IAM)** 를 선택합니다.
1. 선택 **추가** 페이지의 위쪽에서.

    ![역할 할당을 추가 하는 클릭을 보여 주는 스크린샷](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. **역할** 드롭 다운 목록에서 **청구 읽기 권한자**를 선택합니다.
1. **선택** 텍스트 상자에 추가하려는 사용자의 이름이나 이메일을 입력합니다.
1. 사용자를 선택합니다.
1. **저장**을 선택합니다.
    ![역할 할당을 추가 하는 클릭을 보여 주는 스크린샷](./media/billing-manage-access/billing-save-role-assignment.png)

1. 몇 분 후 사용자는 구독에 대 한 청구 읽기 권한자 역할을 할당 됩니다.

\* * EA 고객 인 경우 계정 소유자 또는 부서 관리자 팀 멤버에 게 청구 읽기 권한자 역할을 할당할 수 있습니다. 단 청구 읽기 권한자가 부서나 계정에 대한 청구 정보를 보려면, 엔터프라이즈 관리자가 Enterprise 포털에서 **AO 요금 보기** 또는 **DA 요금 보기** 정책을 사용하도록 설정해야 합니다.

## <a name="check-the-type-of-your-billing-account"></a>대금 청구 계정 유형을 확인합니다
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>다음 단계

- 소유자 또는 참가자 등의 다른 역할의 사용자는 청구 정보에 액세스할 수만 있고 Azure 서비스에는 액세스할 수 없습니다. 이러한 역할을 관리하려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
- 역할에 대한 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.
