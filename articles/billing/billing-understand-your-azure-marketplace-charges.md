---
title: Azure 외부 서비스 요금의 이해 | Microsoft Docs
description: 이전에는 Marketplace로 알려진 외부 서비스의 요금 청구, Azure의 요금에 대해 알아봅니다.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490348"
---
# <a name="understand-your-azure-external-services-charges"></a>Azure 외부 서비스 요금의 이해
외부 서비스는 Azure Marketplace의 타사 소프트웨어 공급 업체에 의해 게시됩니다. 예를 들어 SendGrid는 Azure에서 구입할 수 있지만 Microsoft에서 게시하지 않은 외부 서비스입니다. 일부 Microsoft 제품은 Azure Marketplace를 통해서도 판매됩니다.

## <a name="how-external-services-are-billed"></a>외부 서비스에 요금이 청구되는 방식

- [Microsoft 고객 계약](#check-access)이 있는 경우 타사 서비스에는 나머지 Azure 서비스가 청구됩니다.
- Microsoft 고객 계약이 없는 경우 외부 서비스는 Azure 서비스와는 별도로 청구됩니다.
- 각 외부 서비스에는 다른 청구 모델이 있습니다. 일부 서비스는 종량제 방식으로 청구되는 반면, 다른 서비스는 월별 고정 요금을 사용합니다.
- 외부 서비스에 대한 월별 무료 크레딧을 사용할 수 없습니다. [무료 크레딧](https://azure.microsoft.com/pricing/spending-limits/)을 포함하는 Azure 구독을 사용하는 경우 외부 서비스에 대한 요금에 적용될 수 없습니다. 새 외부 서비스 또는 리소스를 프로비전하면 다음 경고가 표시됩니다.

    ![Marketplace 구매 경고](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>청구서 보기 및 다운로드

[Microsoft 고객 계약](#check-access)이 있는 경우 타사 요금은 Azure 요금과 동일한 청구서에 표시됩니다. Azure Portal에서 [Azure 청구서를 보고 다운로드](billing-download-azure-invoice.md)하여 타사 요금을 확인하는 방법을 알아봅니다.

Microsoft 고객 계약이 없는 경우 타사 요금에 대해 별도의 청구서를 받습니다. Azure Marketplace 청구서는 다음 단계에 따라 Azure Portal에서 보고 다운로드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.
1. 왼쪽 메뉴에서 **청구서**를 선택합니다.
1. **Azure Marketplace 및 Reservations** 탭을 클릭합니다.  ![Azure Marketplace 및 Reservations 탭 모습](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. 구독 드롭다운에서 청구서를 보려는 외부 서비스가 포함된 구독을 선택합니다.

## <a name="external-spending-for-ea-customers"></a>EA 고객에 대한 외부 지출

EA 고객은 EA 포털에서 외부 서비스 지출을 살펴보고 다운로드할 수 있습니다. 시작 방법은 [EA 고객을 위한 Azure Marketplace](https://ea.azure.com/helpdocs/azureMarketplace) 를 참조하세요.

## <a name="manage-payment-for-external-services"></a>외부 서비스에 대한 결제 관리

외부 서비스를 구매할 때 리소스에 대한 Azure 구독을 선택합니다. 선택한 Azure 구독의 결제 방법은 외부 서비스에 대한 지불 방법이 됩니다. 외부 서비스에 대한 결제 방법을 변경하려면 해당 외부 서비스에 연결된 [Azure 구독의 지불 방법을 변경](billing-how-to-change-credit-card.md)해야 합니다. 다음 단계를 수행하여 외부 서비스 주문이 연결된 구독을 파악할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 리소스**를 클릭합니다.
     ![모든 리소스 메뉴 항목의 스크린샷](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 외부 서비스를 검색합니다.
1. **구독** 열에서 구독의 이름을 찾습니다.
    ![리소스에 대한 구독 이름의 스크린샷](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. 구독 이름을 클릭하고 [활성 결제 방법을 업데이트](billing-how-to-change-credit-card.md)합니다.

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>외부 서비스 주문 취소
외부 서비스 주문을 취소하려는 경우 [Azure Portal](https://portal.azure.com)에서 리소스를 삭제합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 리소스**를 클릭합니다.
    ![모든 리소스 메뉴 항목의 스크린샷](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 외부 서비스를 검색합니다.
1. 삭제하려는 리소스 옆에 있는 확인란을 선택합니다.
1. 명령 모음에서 **삭제**를 선택합니다.
    ![삭제 단추의 스크린샷](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. 확인 블레이드에서 *‘예’* 를 입력합니다.
    ![리소스 삭제](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. **삭제**를 클릭합니다.

## <a name="check-access"></a>액세스 권한 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [비용 분석 시작](../cost-management/quick-acm-cost-analysis.md)
