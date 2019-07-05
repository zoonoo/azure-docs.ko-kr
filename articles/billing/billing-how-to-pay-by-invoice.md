---
title: 청구서로 Azure 구독 비용 지불
description: Azure 구독용 청구서로 지불 하는 방법에 설명 합니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491198"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>청구서로 Azure 구독 비용 지불

청구서로 지불 하도록 전환 하는 경우 즉, 전신 확인 하 여 송장 날짜 로부터 30 일 이내 청구서를 지불 합니다. Azure 구독에 대 한 청구서로 지불 하도록 허용 되도록, Azure 지원에 요청을 제출 합니다. 요청이 승인 되 면 전환할 수 청구서 지불 (확인/통신 전송)에 [Azure portal](https://portal.azure.com)합니다.

> [!IMPORTANT]
> * 청구서 지불 (확인/통신 전송)만 비즈니스 계정에 대해 제공 됩니다.
> * 청구서 지불으로 전환 하기 전에 모든 미결제 요금을 지불 해야 합니다.

## <a name="request-to-pay-by-invoice"></a>청구서로 지불 하도록 요청

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. **도움말 + 지원** > **새 지원 요청**을 선택합니다.

    ![도움말 및 지원 링크](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. 선택 **청구** 으로 **문제 유형**합니다. 합니다 *문제 유형* 지원 요청 범주입니다. 청구서로 지불 지원 계획을 선택한 다음 선택 하려는 구독을 선택 **다음**합니다.

3. **문제 유형** 상자에서 **청구서로 지불**을 선택합니다. 합니다 *문제 유형* 지원 요청 subcategory 됩니다.

4. **세부 정보** 상자에 다음 정보를 입력하고 **다음**을 선택합니다.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - 합니다 **회사 이름** 하 고 **주소를 회사** Azure 계정에 대해 제공한 정보와 일치 해야 합니다. 참조를 확인 하거나 정보를 업데이트 하려면 [Azure 계정 프로필 정보 변경](billing-how-to-change-azure-account-profile.md)합니다.
    - 크레딧 한도 승인 하기 전에 Azure portal에서 청구 연락처 정보를 추가 해야 합니다. 연락처 세부 정보를 회사의 Accounts Payable 또는 재무 부서 관련 되어야 합니다. 청구 연락처 정보를 업데이트 하려면로 이동 [Azure 계정 센터](https://account.azure.com/Profile)합니다.

5. 연락처 정보와 기본 연락 방법을 확인한 다음 **만들기**를 클릭합니다.

를 해야 하는 크레딧의 금액을 인해 신용 검사를 실행 해야 하는 경우 신용 응용 프로그램을 확인 하는 것이 보내드립니다.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>청구서 지불 (확인/통신 전송)으로 전환

청구서로 지불 하도록 승인 되 면 Azure portal에서 청구서 지불 (확인/전신)로 전환할 수 있습니다.

프로그램 Microsoft Online Services 계정이 있는 경우에 검사/통신 전송 하기 위해 Azure 구독을 전환할 수 있습니다. Microsoft 고객 계약에 있는 경우에 검사/연결 전송 요금 청구 프로필을 전환할 수 있습니다. [계정 유형을 확인 하는 방법을 알아봅니다](#check-access-to-a-microsoft-customer-agreement)합니다.

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>확인/연결 전송에 Azure 구독 전환

청구서 지불 (확인/전신)로 Azure 구독을 전환 하려면 다음 단계를 수행 합니다. **신용 카드를 다시 전환할 수 없습니다 청구서 지불 (확인/전신)로 전환 하면**합니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색할 **비용 관리 + 청구**합니다.

    ![검색을 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/search.png)

1. 청구서 지불으로 전환 하려는 구독을 선택 합니다.
1. **결제 방법**을 선택합니다.
1. 명령 모음에서을 클릭 합니다 **청구서로 지불** 단추입니다.

    ![송장 단추 지불을 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>청구 프로필 확인/연결 전송 전환

확인/연결 전송 청구 프로필을 전환 하려면 다음 단계를 수행 합니다. Azure에 등록 한 사용자만의 청구 프로필을 기본 지불 방법을 변경할 수는 note 하십시오.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.
1. 왼쪽 메뉴에서 클릭 **청구 프로필**합니다.

    ![메뉴에서 청구 프로필을 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. 청구 프로필을 선택 합니다.
1. 왼쪽 메뉴에서 선택 **지불 방법을**합니다.

   ![메뉴에서 지불 방법을 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. 확인/전신 송금로 지불 하도록 자격이 라는 파란색 배너를 클릭 합니다.

    ![확인/통신 전환할 파란색 배너를 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

- 필요한 경우에 청구 연락처 정보를 업데이트 합니다 [Azure 계정 센터](https://account.azure.com/Profile)합니다.
