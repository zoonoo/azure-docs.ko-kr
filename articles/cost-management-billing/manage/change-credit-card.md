---
title: Azure에 대한 신용 카드 변경
description: Azure 구독 지불에 사용하는 신용 카드를 변경하는 방법에 대해 설명합니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 6ae55e0075883de08af516b71089f00353975b34
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992843"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Azure에 대한 신용 카드 추가, 업데이트 또는 제거

이 문서는 신용 카드로 Azure 온라인에 등록한 고객에게 적용됩니다.

Azure Portal에서 새 신용 카드를 추가하거나, 기존 신용 카드를 업데이트하거나, 사용하지 않는 신용 카드를 삭제할 수 있습니다. 이러한 변경을 수행하려면 [계정 관리자](billing-subscription-transfer.md#whoisaa)여야 합니다.

[Microsoft 고객 계약](#check-access-to-a-microsoft-customer-agreement)의 경우 결제 방법은 청구 프로필과 연결됩니다. [청구 프로필에 대한 기본 결제 방법을 변경](#change-payment-method-for-a-billing-profile)하는 방법에 대해 알아봅니다. Azure에 등록한 사용자만 결제 방법을 업데이트할 수 있습니다.

**청구서로 지불(수표/전신 송금)로 전환하시겠습니까?** [청구서로 Azure 구독 비용 지불](pay-by-invoice.md)을 참조하세요.

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Azure 구독에 새 신용 카드 추가

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.

    ![검색을 보여주는 스크린샷](./media/change-credit-card/search.png)

1. 신용 카드를 추가하려는 구독을 선택합니다.
1. **결제 방법**을 선택합니다.

    ![선택한 결제 방법 관리 옵션을 보여 주는 스크린샷](./media/change-credit-card/payment-methods-blade-x.png)

1. 왼쪽 위 모서리에서 "+" 기호를 선택하여 카드를 추가합니다. 오른쪽에 신용 카드 양식이 나타납니다.
1. 신용 카드 세부 정보를 입력합니다.

    ![새 카드 추가를 보여주는 스크린샷](./media/change-credit-card/sub-add-new-x.png)

1. 이 카드를 활성 결제 방법으로 만들려면 양식 위쪽에서 **이 방법을 내 활성 결제 방법으로 설정** 옆에 있는 확인란을 선택합니다. 이 카드는 선택한 구독과 동일한 카드를 사용하는 모든 구독의 활성 결제 방법이 됩니다.

1. **다음**을 선택합니다.

신용 카드를 추가한 후 오류가 표시되면 [Azure 등록 시 신용 카드 거부](../../billing/billing-credit-card-fails-during-azure-sign-up.md)를 참조하세요.

## <a name="update-existing-credit-card"></a>기존 신용 카드 업데이트

신용 카드가 갱신되고 번호가 동일하게 유지되는 경우 만료 날짜처럼 기존 신용 카드 세부 정보를 업데이트합니다. 카드 분실, 도난 또는 만료로 인해 카드 번호가 변경되는 경우 [결제 방법으로 신용 카드 추가](#addcard) 섹션의 단계를 따릅니다. CVV를 업데이트할 필요가 없습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.

    ![검색을 보여주는 스크린샷](./media/change-credit-card/search.png)

1. **결제 방법**을 선택합니다.

    ![선택한 결제 방법 관리 옵션을 보여 주는 스크린샷](./media/change-credit-card/payment-methods-blade-x.png)

1. 편집하려는 신용 카드를 클릭합니다. 오른쪽에 신용 카드 양식이 나타납니다.

    ![선택한 신용 카드를 보여주는 스크린샷](./media/change-credit-card/edit-card-x.png)

1. 신용 카드 세부 정보를 업데이트합니다.
1. **저장**을 선택합니다.

## <a name="use-a-different-credit-card"></a>다른 신용 카드 사용

둘 이상의 구독에 동일한 활성 결제 방법이 있는 경우 이러한 구독 중에서 활성 결제 방법을 변경하면 다른 구독에 대한 활성 결제 방법도 업데이트됩니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.

    ![검색을 보여주는 스크린샷](./media/change-credit-card/search.png)

1. 신용 카드를 추가하려는 구독을 선택합니다.
1. **결제 방법**을 선택합니다.

    ![선택한 결제 방법 관리 옵션을 보여 주는 스크린샷](./media/change-credit-card/payment-methods-blade-x.png)

1. 활성 결제 방법으로 설정할 카드 옆에 있는 확인란을 선택합니다.
1. **활성 설정**을 클릭합니다.
    ![선택한 신용 카드 및 활성 설정을 보여주는 스크린샷](./media/change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>계정에서 신용 카드 제거

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 페이지의 왼쪽에서 **Cost Management + 청구**를 선택합니다.

    ![검색을 보여주는 스크린샷](./media/change-credit-card/search.png)

1. **청구**에서 **결제 방법**을 선택합니다.

    ![선택한 결제 방법 관리 옵션을 보여 주는 스크린샷](./media/change-credit-card/payment-methods-blade-x.png)

1. 제거하려는 카드 옆에 있는 확인란을 선택합니다.
1. **삭제**를 클릭합니다.

신용 카드가 다른 Microsoft 구독에 대한 활성 결제 방법인 경우 Azure 계정에서 제거할 수 없습니다. 이 신용 카드에 연결된 모든 구독에 대해 활성 결제 방법을 변경한 후 다시 시도하십시오.

## <a name="change-payment-method-for-a-billing-profile"></a>청구 프로필의 결제 방법 변경

청구 프로필에 대한 결제 방법을 변경하려면 Azure에 등록 한 사람이어야 합니다.

기본 결제 방법을 수표/전신 송금으로 전환하려면 [청구 프로필을 수표/전신 송금으로 전환](pay-by-invoice.md)하는 방법을 알아보세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.
1. 왼쪽 메뉴에서 **청구 프로필**을 클릭합니다.

    ![메뉴의 청구 프로필을 보여주는 스크린샷](./media/change-credit-card/billing-profile.png)

1. 청구 프로필을 선택합니다.
1. 왼쪽 메뉴에서 **결제 방법**을 선택합니다.

   ![메뉴의 결제 방법을 보여주는 스크린샷](./media/change-credit-card/billing-profile-payment-methods.png)

1. 기본 결제 방법 위에서 **변경**을 클릭합니다.

    ![변경 단추를 보여주는 스크린샷](./media/change-credit-card/customer-led-switch-credit-card.png)

1. 기존 카드를 선택하거나 새 카드를 추가합니다.

## <a name="troubleshooting"></a>문제 해결
가상 카드 또는 선불 카드는 지원하지 않습니다. 유효한 신용 카드를 추가하거나 업데이트할 때 오류가 발생하면 프라이빗 모드에서 브라우저를 열어 보세요.

## <a name="frequently-asked-questions"></a>질문과 대답
다음 섹션에서는 신용 카드 정보를 변경하는 방법에 관한 자주 묻는 질문에 대답합니다.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>내 구독을 사용할 수 없습니다. 지금 내 신용 카드를 제거할 수 없는 이유는 무엇입니까?

구독을 비활성화하거나 취소한 후 구독을 영구적으로 삭제하기 전에 90일 동안 기다립니다. 구독을 다시 활성화하려는 경우에 대비해서 보존 기간 동안 파일에 결제 방법을 유지합니다. 이 기간이 지나면 구독이 영구적으로 삭제됩니다.

90일의 보존 기간이 끝나기 전에 신용 카드를 제거해야 할 경우 [구독을 다시 활성화](subscription-disabled.md)합니다. 다시 활성화할 수 없는 경우 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>“로그인 세션이 만료되었습니다. 다시 로그인하려면 여기를 클릭하세요.” 메시지를 계속해서 받는 이유는 무엇입니까?

로그아웃하고 다시 로그인했는데도 이 오류 메시지가 계속 표시되는 경우 프라이빗 브라우징 세션으로 다시 시도해 보세요.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>보유한 각 구독에 대해 서로 다른 카드를 사용하려면 어떻게 할까요?

아쉽게도 구독이 동일한 카드를 이미 사용 중인 경우 서로 다른 카드를 사용하도록 구분할 수 없습니다. 그러나 새 구독에 등록할 때 해당 구독에 대해 새 지불 방법을 사용하도록 선택할 수 있습니다.

### <a name="how-do-i-make-payments"></a>지불하려면 어떻게 해야 할까요?

결제 방법으로 신용 카드를 설정한 경우 각 청구 기간 후에 카드로 자동으로 청구됩니다. 수행할 작업은 없습니다.

[청구서로 지불](pay-by-invoice.md)하는 경우 청구서 아래쪽에 나열된 위치로 결제 금액을 보냅니다.

### <a name="how-do-i-change-the-tax-id"></a>세금 ID를 변경하려면 어떻게 할까요?

세금 ID를 추가 또는 업데이트하려면 [Azure 계정 센터](https://account.azure.com/Profile)에서 프로필을 업데이트한 다음, **세금 레코드**를 선택합니다. 이 세금 ID는 면세 계산에 사용되며 청구서에 표시됩니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- 비용을 절감할 수 있는지 확인하기 위해 [Azure Reservations](../reservations/save-compute-costs-reservations.md)에 대해 알아봅니다.
