---
title: Azure 구독의 청구 소유권 가져오기
description: 다른 사용자의 Azure 구독에 대 한 청구 소유권을 요청 하는 방법을 알아봅니다.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019569"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>다른 계정에서 Azure 구독의 청구 소유권 얻기

기존 청구 소유자가 조직을 떠나는 경우 또는 청구 계정을 통해 구독 요금을 지불 하려는 경우 Azure 구독의 소유권을 가져올 수 있습니다. 소유권을 획득 하면 구독에 대 한 청구 책임이 계정에 전송 됩니다.

이 문서는 Microsoft 고객 계약에 대 한 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 대 한 액세스 권한이 있는지 확인](#check-for-access)합니다.

청구 소유권을 요청 하려면 **청구서 섹션 소유자** 또는 **송장 섹션 참가자**여야 합니다. 자세히 알아보려면 [청구서 섹션 역할 작업](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)을 참조 하세요.

## <a name="request-billing-ownership"></a>청구 소유권 요청

1. Microsoft 고객 계약의 청구 계정에 대해 청구서 섹션 소유자 또는 참가자로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

2. **Cost Management + 청구**를 검색 합니다.

   ![Cost management에 대 한 Azure Portal 검색 + 청구를 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 왼쪽에서 **송장 섹션** 을 선택 합니다. 액세스에 따라 청구 계정 또는 청구 프로필을 선택 해야 할 수 있습니다. 청구 계정 또는 프로필에서 **청구서 섹션**을 선택 합니다.
   
   ![송장 섹션 선택을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 목록에서 송장 섹션을 선택 합니다. 구독의 소유권을 모두 사용 하면이 청구서 섹션으로 청구 됩니다.

5. 왼쪽 아래에서 **전송 요청** 을 선택한 다음 **추가**를 선택 합니다.
 
   ![전송 요청 선택을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 청구 소유권을 요청 하는 사용자의 이메일 주소를 입력 합니다. 사용자는 Microsoft Online Service Program 청구 계정의 계정 관리자 이거나 기업계약의 계정 소유자 여야 합니다. 자세한 내용은 [Azure Portal에서 청구 계정 보기](billing-view-all-accounts.md)를 참조 하세요. **전송 요청 보내기**를 선택 합니다.

   ![전송 요청 전송을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. 사용자는 전송 요청을 검토 하는 지침이 포함 된 전자 메일을 가져옵니다.

   ![검토 전송 요청 전자 메일을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 전송 요청을 승인 하기 위해 사용자는 전자 메일의 링크를 선택 하 고 지침을 따릅니다.

    ![검토 전송 요청 전자 메일을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>전송 요청 상태를 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색 합니다.

   ![Cost management에 대 한 Azure Portal 검색 + 청구를 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 왼쪽에서 **송장 섹션** 을 선택 합니다. 액세스에 따라 청구 계정 또는 청구 프로필을 선택 해야 할 수 있습니다. 청구 계정 또는 프로필에서 **청구서 섹션**을 선택 합니다.
   
   ![송장 섹션 선택을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 전송 요청을 보낸 목록에서 청구서 섹션을 선택 합니다.

5. 왼쪽 아래에서 **전송 요청** 을 선택 합니다. 요청 전송 페이지에는 다음 정보가 표시 됩니다.

    ![전송 요청 목록을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Column|정의|
   |---------|---------|
   |요청 날짜|전송 요청이 전송 된 날짜입니다.|
   |받는 사람|청구 소유권을 전송 하는 요청을 보낸 사용자의 이메일 주소입니다.|
   |만료일|요청이 만료 되는 날짜입니다.|
   |상태|전송 요청의 상태|

    전송 요청은 다음 상태 중 하나를 가질 수 있습니다.

   |상태|정의|
   |---------|---------|
   |진행 중|사용자가 전송 요청을 수락 하지 않았습니다.|
   |처리 중|사용자가 전송 요청을 승인 했습니다. 사용자가 선택한 구독에 대 한 청구는 청구서 섹션으로 전송 됩니다.|
   |완료| 사용자가 선택한 구독에 대 한 청구는 청구서 섹션으로 전송 됩니다.|
   |완료 되었으나 오류가 발생 했습니다.|요청을 완료 했지만 사용자가 선택한 일부 구독에 대 한 청구를 전송 하지 못했습니다.|
   |만료됨|사용자가 시간에 요청을 수락 하지 않았고 만료 되었습니다.|
   |Canceled|전송 요청에 대 한 액세스 권한이 있는 누군가가 요청을 취소 했습니다.|
   |거부|사용자가 전송 요청을 거부 했습니다.|

7. 세부 정보를 보려면 전송 요청을 선택 합니다. 전송 세부 정보 페이지에는 다음 정보가 표시 됩니다.
   
   ![전송 된 구독의 목록을 보여 주는 스크린샷](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Column  |정의|
   |---------|---------|
   |전송 요청 ID|전송 요청에 대 한 고유 ID입니다. 지원 요청을 제출 하는 경우 Azure 지원과 ID를 공유 하 여 지원 요청을 신속 하 게 처리할 수 있습니다.|
   |전송 요청 됨|전송 요청이 전송 된 날짜입니다.|
   |전송 요청 기준|전송 요청을 보낸 사용자의 이메일 주소입니다.|
   |전송 요청 만료 날짜| 전송 요청이 만료 되는 날짜입니다.|
   |받는 사람의 이메일 주소|청구 소유권을 전송 하는 요청을 보낸 사용자의 이메일 주소입니다.|
   |전송 링크가 받는 사람에 게 전송 되었습니다.|전송 요청을 검토 하기 위해 사용자에 게 보낸 url입니다.|

## <a name="supported-subscription-types"></a>지원되는 구독 유형

아래 나열 된 구독 유형의 청구 소유권을 요청할 수 있습니다.

- [작업 팩](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure in Open 라이선스](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 후원](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [무료 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure 계획](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 후원 제안](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft 기업계약](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft 파트너 네트워크](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 플랫폼](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 구독자](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 구독자](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [구독자 Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [구독자 Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*구독에서 사용할 수 있는 모든 크레딧은 전송 후 새 계정에서 사용할 수 없습니다.

\*\*Azure 웹 사이트에서 등록 하는 동안 생성 된 계정의 구독에 대해서만 지원 됩니다.


## <a name="additional-information"></a>추가 정보

다음 섹션에서는 구독을 전송 하는 방법에 대 한 추가 정보를 제공 합니다.

### <a name="no-service-downtime"></a>서비스 가동 중지 시간이 없습니다.

구독의 Azure 서비스는 중단 없이 계속 실행 됩니다. 사용자가 이전에 선택 하는 Azure 구독에 대 한 청구 관계만 전환 합니다.

### <a name="disabled-subscriptions"></a>비활성화 된 구독

비활성화 된 구독은 전송할 수 없습니다. 청구 소유권을 전송 하려면 구독이 활성 상태 여야 합니다.

### <a name="azure-resources-transfer"></a>Azure 리소스 전송

Vm, 디스크 및 websites 전송과 같은 구독의 모든 리소스

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 제품 전송

Azure Marketplace 제품은 각 구독과 함께 전송 됩니다.

### <a name="azure-reservations-transfer"></a>Azure Reservations 전송

Azure Reservations는 구독과 함께 자동으로 이동 하지 않습니다. 예약을 이동 하려면 [Azure 지원에 문의 하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

### <a name="access-to-azure-services"></a>Azure 서비스에 대 한 액세스

(Azure RBAC (역할 기반 액세스 제어))를 사용 하 여 할당 된 기존 사용자, 그룹 또는 서비스 사용자에 대 한 액세스 [.. /role-based-access-control/overview.md]는 전환 중에는 영향을 받지 않습니다.

### <a name="azure-support-plan"></a>Azure 지원 계획

Azure 지원은 구독과 함께 전송 되지 않습니다. 사용자가 모든 Azure 구독을 전송 하는 경우 지원 계획을 취소 하도록 요청 하세요.

### <a name="charges-for-transferred-subscription"></a>전송 된 구독의 요금

구독의 원래 청구 소유자는 전송이 완료 된 시점까지 보고 된 모든 요금을 담당 합니다. 청구서 섹션은 전송 시간부터 보고 한 요금을 담당 합니다. 전송 하기 전에 발생 한 몇 가지 요금이 있을 수 있으며 나중에 보고 되었습니다. 이러한 요금은 청구서 섹션에 표시 됩니다.

### <a name="cancel-a-transfer-request"></a>전송 요청 취소

요청이 승인 되거나 거부 될 때까지 전송 요청을 취소할 수 있습니다. 전송 요청을 취소 하려면 [전송 정보 페이지로](#check-the-transfer-request-status) 이동 하 여 페이지 아래쪽에서 취소를 선택 합니다.

### <a name="software-as-a-service-saas-transfer"></a>SaaS (Software as a Service) 전송

SaaS 제품은 구독과 함께 전송 되지 않습니다. 사용자에 게 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 하 여 SaaS 제품의 청구 소유권을 양도 하도록 요청 하세요. 청구 소유권과 함께 사용자는 리소스 소유권을 전송할 수도 있습니다. 리소스 소유권을 사용 하면 제품의 세부 정보 삭제 및 보기와 같은 관리 작업을 수행할 수 있습니다. 리소스 소유권을 전송 하려면 사용자가 SaaS 제품에 대 한 리소스 소유자 여야 합니다.

## <a name="check-for-access"></a>액세스 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움이 필요하십니까? 지원 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- Azure 구독의 청구 소유권이 청구서 섹션으로 전송 됩니다. [Azure Portal](https://portal.azure.com)에서 이러한 구독에 대 한 요금을 추적 하세요.
- 다른 사용자에 게 이러한 구독에 대 한 청구를 보고 관리할 수 있는 권한을 부여 합니다. 자세한 내용은 [청구서 섹션 역할 및 작업](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)을 참조 하세요.
