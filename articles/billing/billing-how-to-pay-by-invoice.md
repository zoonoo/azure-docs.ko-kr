---
title: 청구서로 Azure 구독 요금 지불
description: 청구서로 Azure 구독에 대 한 요금을 지불 하는 방법을 설명 합니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: 9e4e05acd88e9b0f0c17d4dd4caf5eb5a883d63d
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012590"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>청구서로 Azure 구독에 대 한 요금 지불

청구서로 지불로 전환 하는 경우 청구서 날짜의 30 일 이내에 확인/회선 전송으로 청구서를 지불 하는 것입니다. 청구서로 Azure 구독에 대한 요금을 지불하려면 Azure 지원에 요청을 제출합니다. 요청이 승인 되 면 [Azure Portal](https://portal.azure.com)에서 송장 지불 (확인/실시간 전송)으로 전환할 수 있습니다.

> [!IMPORTANT]
> * 청구서 지불 (확인/실시간 전송)은 비즈니스 계정에만 사용할 수 있습니다.
> * 청구서 지불로 전환 하기 전에 모든 미해결 요금을 지불 합니다.
> * 현재, 청구서 결제는 중국의 글로벌 Azure를 지원 하지 않습니다.

## <a name="request-to-pay-by-invoice"></a>청구서로 지불 요청

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. **도움말 + 지원** > **새 지원 요청**을 선택합니다.

    ![도움말 및 지원 링크](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. **문제 유형**으로 **청구** 를 선택 합니다. *문제 유형은* 지원 요청 범주입니다. 청구서로 지불 하려는 구독을 선택 하 고 지원 플랜을 선택한 후 **다음**을 선택 합니다.

3. **문제 유형**으로 **결제** 를 선택 합니다. *문제 유형은* 지원 요청 하위 범주입니다.

4. **문제 하위 유형** 으로 **청구서로 지불 하려면 전환을** 선택 합니다.

5. **세부 정보** 상자에 다음 정보를 입력하고 **다음**을 선택합니다.

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

    - **회사 이름과** **회사 주소** 는 Azure 계정에 대해 제공한 정보와 일치 해야 합니다. 정보를 보거나 업데이트 하려면 [Azure 계정 프로필 정보 변경](billing-how-to-change-azure-account-profile.md)을 참조 하세요.
    - 신용 한도를 승인 하기 전에 Azure Portal에서 청구 연락처 정보를 추가 합니다. 연락처 세부 정보는 회사의 외상 매입금 또는 재무 부서와 관련 되어 있어야 합니다. 청구 연락처 정보를 업데이트 하려면 [Azure 계정 센터](https://account.azure.com/Profile)으로 이동 합니다.

6. 연락처 정보와 기본 연락 방법을 확인한 다음 **만들기**를 클릭합니다.

필요한 크레딧 용량 때문에 크레딧 확인을 실행 해야 하는 경우 크레딧 확인 응용 프로그램을 보내 드리겠습니다.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>청구서 지불로 전환 (확인/실시간 전송)

청구서로 지불 하도록 승인 되 면 Azure Portal에서 송장 지불 (확인/실시간 전송)으로 전환할 수 있습니다.

Microsoft Online Services 프로그램 계정이 있는 경우 Azure 구독을 수표/전신 송금으로 전환할 수 있습니다. Microsoft 고객 규약을 사용 하 여 청구 프로필을 확인/회선 전송으로 전환할 수 있습니다. [계정 유형을 확인 하는 방법에 대해 알아봅니다](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Azure 구독을 확인/회선 전송으로 전환

아래 단계에 따라 Azure 구독을 청구서 지불 (확인/회선 전송)으로 전환 합니다. **청구서 지불 (확인/실시간 전송)으로 전환 하면 신용 카드로 다시 전환할 수 없습니다**.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색 합니다.

    ![검색을 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/search.png)

1. 청구서 지불로 전환하려는 구독을 선택합니다.
1. **결제 방법**을 선택합니다.
1. 명령 모음에서 **청구서로 지불** 단추를 클릭 합니다.

    ![송장으로 지불 단추를 표시 하는 스크린샷](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>청구 프로필을 확인/회선 전송으로 전환

청구 프로필을 확인/실시간 전송으로 전환 하려면 다음 단계를 수행 합니다. Azure에 등록 된 사람만 청구 프로필의 기본 지불 방법을 변경할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.
1. 왼쪽 메뉴에서 **청구 프로필**을 클릭 합니다.

    ![메뉴에서 청구 프로필을 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. 청구 프로필을 선택 합니다.
1. 왼쪽 메뉴에서 **지불 방법**을 선택 합니다.

   ![메뉴의 지불 메서드를 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. 확인/실시간 전송으로 지불할 수 있는 파란색 배너를 클릭 합니다.

    ![확인/유선으로 전환 하는 파란색 배너를 보여 주는 스크린샷](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

궁금한 사항이 있거나 도움이 필요 하면 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- 필요한 경우 [Azure 계정 센터](https://account.azure.com/Profile)에서 청구 연락처 정보를 업데이트 합니다.
