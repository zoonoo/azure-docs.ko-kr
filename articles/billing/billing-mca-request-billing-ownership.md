---
title: Azure 구독의 소유권 청구 | Microsoft Docs
description: Azure 구독의 청구 소유권을 다른 사용자에 게 요청 하는 방법에 알아봅니다.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371921"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>다른 사용자의 Azure 구독의 소유권 청구

기존 청구 소유자로 조직 그대로 유지 하거나 청구 계정을 통해 구독에 대 한 요금을 지불 하려는 경우 Azure 구독의 소유권 좋습니다.

다른 청구 계정에 기존 소유자 로부터 Azure 구독의 소유권을 요청을 보낼 수 있습니다. 소유권 구독의 청구 책임 송장 섹션 전송 합니다.

청구 소유권을 요청 하려면 이어야 합니다는 **청구서 섹션 소유자** 또는 **송장 섹션 참가자**합니다. 자세한 내용은 참조 하세요 [송장 섹션 역할 작업](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)합니다.

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

## <a name="request-billing-ownership-in-the-azure-portal"></a>Azure portal에서 청구 소유권을 요청

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**에서 검색합니다.

   ![비용 관리 + 청구에 대 한 Azure portal 검색을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 청구서 섹션으로 이동합니다. 사용자 액세스에 따라 대금 청구 계정 또는 청구 프로필을 선택 해야 합니다. 대금 청구 계정 또는 프로필에서 선택 **섹션에서는 송장** 와 다음 송장 부분을 지정 합니다.
   <!-- Todo - Add a screenshot -->

4. 선택 **요청을 전송할** 왼쪽 아래에서에서.

5. 페이지 맨 위에서 **추가**를 선택합니다.

6. 청구 소유권을 요청 하는 사용자의 이메일 주소를 입력 합니다. 사용자는 Microsoft Online 서비스 프로그램 대금 청구 계정에 계정 관리자 또는 엔터프라이즈 규약에 대해 계정 소유자 여야 합니다. 자세한 내용은 [Azure portal에서 청구 계정 보기](billing-view-all-accounts.md)합니다.

   ![추가할 새 전송 요청을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. 선택 **전송 요청 보내기**합니다.

8. 사용자 전자 메일을 전송 요청을 검토 하기 위한 지침을 가져옵니다.

   ![해당 표시 검토 전송 요청 전자 메일 스크린 샷](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. 전송 요청을 승인, 사용자 전자 메일의 링크를 선택 하 고 지침을 따릅니다.

    ![해당 표시 검토 전송 요청 전자 메일 스크린 샷](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Azure portal에서 전송 요청의 상태를 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**에서 검색합니다.

   ![비용 관리 + 청구에 대 한 Azure portal 검색을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 청구서 섹션으로 이동합니다. 사용자 액세스에 따라 대금 청구 계정 또는 청구 프로필을 선택 해야 합니다. 대금 청구 계정 또는 프로필에서 선택 **섹션에서는 송장** 와 다음 송장 부분을 지정 합니다.
   <!-- Todo - Add a screenshot -->

4. 선택 **요청을 전송할** 왼쪽 아래에서에서.

5. 전송 요청 페이지에는 다음 정보가 표시 됩니다.

    ![전송 요청의 목록을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |열|정의|
   |---------|---------|
   |요청 날짜|이전 요청이 전송 된 경우 날짜|
   |받는 사람|청구 소유권을 전송 요청을 전송 하는 사용자의 전자 메일 주소|
   |만료 날짜|요청 만료 날짜|
   |상태|전송 요청의 상태|

    이전 요청이 다음 상태 중 하나일 수 있습니다.

   |상태|정의|
   |---------|---------|
   |진행 중|사용자는 전송 요청을 수락 하지 않은|
   |처리 중|사용자는 전송 요청을 승인 합니다. 사용자가 선택한 구독에 대 한 결제를 송장 섹션 전송 되는지|
   |Completed| 사용자가 선택한 청구서 섹션으로 전송 되는 구독에 대 한 청구|
   |오류와 함께 완료|요청이 완료 되지만 사용자가 선택한 일부 구독에 대 한 청구 전송할 수 없습니다.|
   |만료됨|사용자가 시간에 요청을 허용 하지 않은 및 만료|
   |Canceled|전송 요청에 대 한 액세스를 사용 하 여 사용자가 취소 요청|
   |거부|전송 요청을 거부 하는 사용자|

6. 세부 정보를 보려면 이전 요청을 선택 합니다. 전송 세부 정보 페이지에는 다음 정보가 표시 됩니다.
   <!-- Todo - Add a screenshot -->

   |열  |정의|
   |---------|---------|
   |요청 ID를 전송 합니다.|전송 요청에 대 한 고유 ID입니다. 지원 요청을 신속 하 게 Azure 지원을 통해 지원 요청을 제출 하는 경우 공유 ID|
   |이전 요청 날짜|이전 요청이 전송 된 경우 날짜|
   |요청한 전송|전송 요청을 전송한 사용자의 전자 메일 주소|
   |이전 요청 만료 날짜| 이전 요청 만료 날짜|
   |받는 사람의 전자 메일 주소|청구 소유권을 전송 요청을 전송 하는 사용자의 전자 메일 주소|
   |받는 사람에 게 보낸 이전 링크|사용자에 게 전송 된 전송 요청을 검토 하는 url|

## <a name="additional-information"></a>추가 정보

다음 섹션에서는 구독을 전송 하는 방법에 대 한 추가 정보를 제공 합니다.

### <a name="no-service-downtime"></a>서비스 가동 중지 시간이 없습니다.

Azure 구독의 서비스에에서 계속 중단 없이 실행 됩니다. 전송할 사용자가 선택한 Azure 구독에 대 한 청구 관계를 전환할만 합니다.

### <a name="disabled-subscriptions"></a>해제 된 구독

비활성화 된 구독은 양도할 수 없습니다. 해당 청구 소유권을 이전할 활성 상태의 구독 이어야 합니다.

### <a name="azure-resources-transfer"></a>Azure 리소스 전송

구독에서 모든 리소스는 Vm, 디스크 및 웹 사이트 전송 등입니다.

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 제품 전송

Azure Marketplace 제품 해당 구독이 함께 전송합니다.

### <a name="azure-reservations-transfer"></a>Azure 예약 전송

Azure 예약 구독을 사용 하 여 자동으로 이동 하지 않습니다. [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 예약을 이동 합니다.

### <a name="access-to-azure-services"></a>Azure 서비스에 대 한 액세스

Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 설정 된 Azure 리소스에 대 한 액세스를 전환 하는 동안 적용 되지 않습니다.

### <a name="azure-support-plan"></a>Azure 지원 계획

Azure 지원 구독을 사용 하 여 전송 하지 않습니다. 사용자는 모든 Azure 구독을 전송, 하는 경우 해당 지원 플랜을 취소 하도록 요청 합니다.

### <a name="charges-for-transferred-subscription"></a>전송 된 구독에 대 한 요금

구독의 청구 원래 소유자가 전송이 완료 되는 지점까지 보고 된 모든 요금을 지불 합니다. 송장 섹션 양도 시간부터 보고 요금 담당 합니다. 양도 하기 전에 발생 하지만 나중에 보고 되는 약간의 요금 있을 수 있습니다. 이러한 요금은 청구서 섹션에 표시 합니다.

### <a name="supported-offers"></a>지원 되는 제품

형식 또는 CSP 제공 점을 제외 하 고 제품의 구독을 전송할 수 있습니다.

### <a name="cancel-a-transfer-request"></a>이전 요청을 취소 합니다.

요청 승인 되거나 거부 될 때까지 전송 요청을 취소할 수 있습니다. 전송 요청을 취소 하려면 전송 세부 정보 페이지로 이동 하 고 페이지 맨 아래에서 선택 취소 합니다.

### <a name="software-as-a-service-saas-transfer"></a>서비스 (SaaS) 전송으로는 소프트웨어

SaaS 제품 구독을 사용 하 여 전송 하지 않습니다. 사용자에 게 [연락처 Azure 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) SaaS 제품의 청구 소유권을 전송 합니다. 청구 소유권을와 함께 사용자 리소스 소유권을 전송할 수도 있습니다. 리소스 소유권을 삭제 하 고 제품의 세부 정보 보기와 같은 관리 작업을 수행할 수 있습니다. 사용자는 리소스 소유권을 이전할 SaaS 제품에는 리소스 소유자 여야 합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- Azure 구독의 청구 소유권 송장 섹션으로 전송 됩니다. 이러한 구독에 대 한 요금은 기록해 합니다 [Azure portal](https://portal.azure.com)합니다.
- 다른 사용자가 보고 이러한 구독에 대 한 청구를 관리 하는 권한을 부여 합니다. 자세한 내용은 [섹션에서는 역할 및 태스크를 송장](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)합니다.
