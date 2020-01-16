---
title: 청구서로 Azure 구독 비용 지불
description: 청구서로 Azure 구독 비용을 지불하는 방법을 설명합니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: a0f012145788d2d1d4935e10691859e5aaf71255
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994338"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>청구서로 Azure 구독 비용 지불

지불 방법을 청구서로 전환하는 경우 청구서 날짜 30일 이내에 수표/전신 송금을 사용하여 결제해야 합니다. 청구서로 Azure 구독에 대한 요금을 지불하려면 Azure 지원에 요청을 제출합니다. 요청이 승인되면 [Azure Portal](https://portal.azure.com)에서 구독을 청구서 지불(수표/전신 송금)로 전환할 수 있습니다.

> [!IMPORTANT]
> * 청구서 지불(수표/전신 송금)은 비즈니스 계정에만 사용할 수 있습니다.
> * 청구서 지불로 전환하기 전에 미지불된 모든 요금을 결제합니다.
> * 현재는 중국의 글로벌 Azure에 대해 청구서 지불이 지원 되지 않습니다.

## <a name="request-to-pay-by-invoice"></a>청구서로 지불에 대한 요청

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 지원 요청을 제출 합니다. **도움말 + 지원**을 검색 하 고 선택 합니다.

    ![도움말 및 지원 검색, Microsoft Azure portal](./media/pay-by-invoice/search-for-help-and-support.png)

2. **새 지원 요청**을 선택합니다.

    ![새 지원 요청 링크, 도움말 및 지원 화면, Microsoft Azure portal](./media/pay-by-invoice/help-and-support.png)

2. **문제 유형**으로 **청구**를 선택합니다. *문제 유형*은 지원 요청 범주입니다. 청구서로 지불할 구독을 선택하고, 지원 플랜을 선택한 다음, **다음**을 선택합니다.

3. **결제**를 **문제 유형**으로 선택합니다. *문제 유형*은 지원 요청 하위 범주입니다.

4. **문제 하위 유형**으로 **청구서로 지불 하려면 전환을** 선택 합니다.

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

    - **회사 이름**과 **회사 주소**는 Azure 계정에 대해 제공한 정보와 일치해야 합니다. 정보를 보거나 업데이트하려면 [Azure 계정 프로필 정보 변경](change-azure-account-profile.md)을 참조하세요.
    - Azure Portal에서 청구 연락처 정보를 추가해야 크레딧 한도를 승인받을 수 있습니다. 연락처 세부 정보는 회사의 지급 계정 또는 재무 부서와 관련되어 있어야 합니다. 청구 연락처 정보를 업데이트하려면 [Azure 계정 센터](https://account.azure.com/Profile)로 이동합니다.

6. 연락처 정보와 기본 연락 방법을 확인 한 다음 **만들기**를 선택 합니다.

필요한 신용 수준으로 인해 신용 검사를 실행해야 할 경우 신용 검사 신청서를 보내드립니다.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>청구서 지불로 전환(수표/전신 송금)

청구서 지불이 승인되면 Azure Portal에서 청구서 지불(수표/전신 송금)로 전환할 수 있습니다.

Microsoft Online Services 프로그램 계정이 있는 경우 Azure 구독을 수표/전신 송금으로 전환할 수 있습니다. Microsoft 고객 계약을 사용하면 청구 프로필을 수표/전신 송금으로 전환할 수 있습니다. [계정 유형을 확인하는 방법을 알아봅니다](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Azure 구독을 수표/전신 송금으로 전환

Azure 구독을 청구서 지불(수표/전신 송금)로 전환하려면 다음 단계를 수행합니다. *청구서 지불(수표/전신 송금)로 전환한 후에는 신용 카드로 다시 전환할 수 없습니다.*

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 계정 관리자로 로그인 합니다. **Cost Management + 청구**를 검색 하 고 선택 합니다.

    ![Cost Management 및 청구를 검색 Microsoft Azure portal](./media/pay-by-invoice/search.png)

1. 청구서 지불로 전환하려는 구독을 선택합니다.
1. **결제 방법**을 선택합니다.
1. 명령 모음에서 **청구서로 지불** 단추를 선택 합니다.

    ![청구서로 지불 단추, 지불 방법, Microsoft Azure portal](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>청구 프로필을 수표/전신 송금으로 전환

청구 프로필을 수표/전신 송금으로 전환하려면 다음 단계를 수행합니다. 청구 프로필에 대한 기본 결제 방법을 변경하려면 Azure에 가입한 사람이어야 합니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 청구 정보를 확인 합니다. **Cost Management + 청구**를 검색 하 고 선택 합니다.
1. 메뉴에서 **청구 프로필**을 선택 합니다.

    ![청구 프로필 메뉴 항목, Cost Management 및 청구 Microsoft Azure portal](./media/pay-by-invoice/billing-profile.png)

1. 청구 프로필을 선택합니다.
1. **청구 프로필** 메뉴에서 **지불 방법**을 선택 합니다.

   ![결제 방법 메뉴 항목, 청구 프로필, Cost Management Microsoft Azure portal](./media/pay-by-invoice/billing-profile-payment-methods.png)

1. 확인/실시간 전송으로 지불할 수 있는 배너를 선택 합니다.

    ![확인/통신, 지불 방법, Microsoft Azure portal로 전환 하는 배너](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움이 필요하세요? 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- 필요한 경우 [Azure 계정 센터](https://account.azure.com/Profile)에서 청구 연락처 정보를 업데이트합니다.
