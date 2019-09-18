---
title: Microsoft 고객 계약에 대한 Azure 크레딧 잔액 추적
description: Microsoft 고객 계약에 대한 Azure 크레딧 잔액을 확인하는 방법을 알아봅니다.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490957"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Microsoft 고객 계약 Azure 크레딧 잔액 추적

Azure Portal에서 Microsoft 고객 계약에 대한 Azure 크레딧 잔액을 확인할 수 있습니다. 크레딧이 적용되는 요금을 지불하는 데 크레딧을 사용합니다.

크레딧이 적용되지 않는 제품을 사용하거나 사용량이 크레딧 잔액을 초과하는 경우 요금이 청구됩니다. 자세한 내용은 [Azure 크레딧이 적용되지 않는 제품]을 참조하세요(#products-that-arent-covered-by-azure-credits).

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>크레딧 잔액 확인

1. [Azure Portal]( https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

    ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  왼쪽에서 **Azure 크레딧**을 선택합니다. 액세스 권한에 따라 청구 계정 또는 청구 프로필을 선택한 다음, **Azure 크레딧**을 선택해야 할 수도 있습니다.

4. Azure 크레딧 페이지에는 다음 정보가 표시됩니다.

   ![청구 프로필에 대한 크레딧 잔액 및 트랜잭션 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 용어               | 정의                           |
   |--------------------|--------------------------------------------------------|
   | 예상 잔액  | 청구된 트랜잭션 및 보류 중인 트랜잭션을 모두 고려한 후의 예상되는 크레딧 금액 |
   | 현재 잔액    | 마지막 청구서의 크레딧 금액입니다. 보류 중인 트랜잭션이 포함되지 않습니다. |
   | 트랜잭션       | Azure 크레딧 잔액에 영향을 주는 모든 청구 트랜잭션 |

   예상 잔액이 0이 되면 크레딧이 포함되는 제품을 비롯한 모든 사용량에 대해 요금이 청구됩니다.

6. 청구 프로필에 대한 크레딧 목록을 보려면 **크레딧 목록**을 선택합니다. 크레딧 목록은 다음과 같은 정보를 제공합니다.

   ![청구 프로필에 대한 크레딧 목록 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 용어 | 정의 |
   |---|---|
   | 예상 잔액 | 현재 잔액에서 청구되지 크레딧 적격 요금을 뺀 후의 Azure 크레딧 금액|
   | 현재 잔액 | 청구되지 않은 크레딧 적격 요금을 고려하기 전에 보유한 Azure 크레딧 금액입니다. 마지막 청구서에서 받은 새 Azure 크레딧을 크레딧 잔액에 추가하여 계산됩니다.|
   | 원본 | 크레딧의 취득 원본 |
   | 시작 날짜 | 크레딧을 취득한 날짜 |
   | 만료 날짜 | 크레딧이 만료되는 날짜 |
   | Balance | 마지막 청구서의 잔액 |
   | 원래 금액 | 원래 크레딧 금액 |
   | 상태 | 크레딧의 현재 상태입니다. 상태는 활성, 사용됨, 만료됨 또는 곧 만료될 예정임일 수 있습니다. |

## <a name="how-credits-are-used"></a>크레딧을 사용하는 방법

Microsoft 고객 계약의 청구 계정에는 청구서 및 결제 방법을 관리하는 데 청구 프로필을 사용합니다. 각 청구 프로필에 대해 월별 청구서가 생성되며 결제 방법을 사용하여 청구서 금액을 지불합니다.

Azure 크레딧은 지불 방법 중 하나입니다. Microsoft에서 판촉 크레딧 및 서비스 수준 크레딧과 같은 크레딧을 받을 수 있습니다. 이러한 크레딧은 청구 프로필에 할당됩니다. 청구 프로필에 대한 청구서가 생성되면 크레딧이 총 청구 금액에 자동으로 적용되어 지불해야 하는 금액이 계산됩니다. 수표 또는 전신 송금과 같은 다른 지불 방법으로 남은 금액을 지불합니다.

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 크레딧이 적용되지 않는 제품

 다음 제품은 Azure 크레딧이 적용되지 않습니다. 크레딧 잔액에 관계 없이 이러한 제품 사용에 대한 요금이 청구됩니다.

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
- Azure 지원 플랜

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대한 청구 계정 이해](billing-mca-overview.md)
- [Microsoft 고객 계약 청구서의 용어 이해](billing-mca-understand-your-invoice.md)
