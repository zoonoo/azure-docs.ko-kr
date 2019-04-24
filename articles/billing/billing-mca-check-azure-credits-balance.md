---
title: Microsoft 고객 계약에 대 한 Azure 크레딧 잔액을 추적할 | Microsoft Docs
description: Microsoft 고객 계약에 대 한 Azure 크레딧 잔액을 확인 하는 방법을 알아봅니다.
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
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372263"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 Azure 크레딧 잔액을 추적합니다

Azure portal에서 Microsoft 고객 계약에 대 한 Azure 크레딧 잔액을 확인할 수 있습니다. 크레딧을 사용 하 여 크레딧을 적용 되는 제품에 대 한 비용을 지불 합니다.

크레딧을에 포함 되지 않는 제품을 사용 또는 사용 현황을 크레딧 잔액을 초과 하는 경우 요금이 청구 됩니다. 자세한 내용은 참조 하세요. [제품을 Azure 크레딧은 적용 되지 않습니다.](#products-that-arent-covered-by-azure-credits)

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

## <a name="check-credit-balance-in-the-azure-portal"></a>Azure portal에서 크레딧 잔액 확인

1. [Azure Portal]( https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**에서 검색합니다.

   ![비용 관리 + 청구 포털의 검색을 보여 주는 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. 청구 프로필로 이동합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 선택 **청구 프로필** 및 청구 프로필을 사용 합니다.

4. 선택 **크레딧은**합니다.

5. Azure 크레딧은 페이지에는 다음 정보가 표시 됩니다.

   ![크레딧 잔액 및 청구 프로필에 대 한 트랜잭션 스크린 샷](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 용어               | 정의                           |
   |--------------------|--------------------------------------------------------|
   | 예상 잔액  | 모든 고려 청구 후 및 보류 중인 트랜잭션이 있는 크레딧의 예상된 크기 |
   | 현재 잔액    | 마지막 청구서부터 크레딧 양입니다. 보류 중인 트랜잭션이 포함 되지 않습니다. |
   | 트랜잭션       | Azure 크레딧 잔액을 받는 모든 청구 트랜잭션 |

   예상된 잔액이 0으로 떨어지면 크레딧에서 다루는 제품을 포함 한 모든 사용량에 대 한 요금이 청구 됩니다.

6. 선택 **크레딧 목록** 크레딧 청구 프로필에 대 한 목록을 보려면. 다음 정보를 제공 하는 크레딧을 목록:

   ![청구 프로필에 대 한 크레딧 목록 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 용어                 | 정의                           |
   |----------------------|--------------------------------------------------------|
   | 원본               | 크레딧 획득 원본 |
   | 시작 날짜           | 크레딧을 획득 하는 경우 날짜 |
   | 만료일      | 크레딧 만료 날짜 |
   | 잔액              | 마지막 청구서 기준으로 분산 |
   | 원래 크기      | 크레딧의 금액을 원래 |
   | 상태               | 크레딧의 현재 상태입니다. 사용, 만료 되었거나 만료 상태 활성화 수 있습니다. |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Microsoft 고객 계약에서 크레딧은 사용 하는 방법

Microsoft 고객 계약에 대 한 대금 청구 계정, 청구 프로필을 사용 하 여 송장 및 지불 방법 관리. 월별 청구서는 청구 프로필 각각에 대해 생성 됩니다 하 고 결제 방법을 사용 하 여 청구서 금액을 지불 합니다.

Azure 크레딧은 결제 방법 중 하나입니다. 서비스 수준 크레딧 판촉 크레딧 등 Microsoft에서 신용을 가져옵니다. 이 크레딧은 청구 프로필에 할당 됩니다. 청구 프로필에 대 한 송장이 생성 되 면 크레딧은 비용을 지불 해야 하는 횟수를 계산 총 청구 크기를 자동으로 적용 됩니다. 나머지 시간 검사와 같은 다른 지불 방법으로 지불 또는 전송 연결 수 있습니다.

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 크레딧은에 포함 되지 않는 제품

 다음 제품이 Azure 크레딧이 다루지 않습니다. 신용 잔액에 관계 없이 이러한 제품의 사용에 대 한 요금이 부과 됩니다.

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Registered User
- OpenLogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise(월간)
- Visual Studio Enterprise(연간)
- Visual Studio Professional(월간)
- Visual Studio Professional(연간)
- Azure Marketplace 제품
- Azure 지원 계획

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대 한 대금 청구 계정 이해](billing-mca-overview.md)
- [Microsoft 고객 계약 청구서에서 용어 이해](billing-mca-understand-your-invoice.md)