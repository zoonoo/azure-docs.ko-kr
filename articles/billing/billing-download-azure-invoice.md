---
title: Microsoft Azure 청구서 보기 및 다운로드
description: Microsoft Azure 청구서를 보고 다운로드 하는 방법을 설명 합니다.
keywords: 청구서, 청구서 다운로드, azure 청구서, azure 사용 현황
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321776"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Microsoft Azure 청구서 보기 및 다운로드

대부분의 구독에 대한 청구서는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 다운로드하거나 메일로 전송 받을 수 있습니다. 기업계약이 체결된 Azure 고객(EA 고객)의 경우 조직의 청구서를 다운로드할 수 없습니다. 등록에 대한 청구서를 받도록 설정된 사람에게 청구서가 전송됩니다.

특정 역할에만 송장을 볼 수 있는 권한이 있습니다. 예를 들어 계정 관리자 또는 엔터프라이즈 관리자가 있습니다. 청구 정보에 액세스 하는 방법에 대 한 자세한 내용은 [역할을 사용 하 여 Azure 청구에 대 한 액세스 관리](billing-manage-access.md)를 참조 하세요.

[Microsoft 고객 계약이](#check-your-access-to-a-microsoft-customer-agreement)있는 경우 청구서를 받으려면 다음 역할 중 하나가 있어야 합니다.

- 청구 프로필 소유자
- 참가자
- 판독기
- 송장 관리자

Microsoft 고객 계약의 청구 역할에 대 한 자세한 내용은 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조 하세요.

## <a name="noinvoice"></a>청구서를 받지 못하는 이유

몇 가지 이유로 인해 청구서가 보이지 않을 수 있습니다.

- Azure를 구독한 지 30일 이내입니다.

- Azure는 청구서 기간이 끝날 때 사용자를 청구 합니다. 따라서 송장이 아직 생성 되지 않았을 수 있습니다. 청구 기간이 끝날 때까지 기다립니다.

- 청구서를 볼 수 있는 권한이 없습니다. Microsoft 고객 계약이 있는 경우 청구 프로필 소유자, 참가자, 읽기 권한자 또는 송장 관리자 여야 합니다. 다른 구독의 경우 계정 관리자가 아니면 이전 송장이 표시 되지 않을 수 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](billing-manage-access.md)를 참조하세요.

- 구독에 대 한 무료 평가판 또는 월별 크레딧 금액이 있는 경우 월별 크레딧 금액을 초과 하는 경우에만 송장을 받게 됩니다. Microsoft 고객 계약이 있는 경우 항상 송장을 받게 됩니다.

## <a name="download-your-azure-invoices-pdf"></a>Azure 청구서 다운로드 (.pdf)

대부분의 구독의 경우 Azure Portal에서 송장을 다운로드할 수 있습니다. Microsoft 고객 계약이 있는 경우 [Microsoft 고객 계약에 대 한 청구서 다운로드](#download-invoices-for-a-microsoft-customer-agreement)를 참조 하세요.

### <a name="download-invoices-for-an-individual-subscription"></a>개별 구독에 대 한 청구서 다운로드

1. [청구서에 대 한 액세스 권한이 있는 사용자](billing-manage-access.md)로 Azure Portal의 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 에서 구독을 선택 합니다.

2. **청구서**를 선택합니다.

    ![청구 및 사용 현황 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. **청구서 다운로드**를 클릭하여 PDF 청구서 사본을 확인합니다. **사용할 수 없음**이 표시될 경우 [왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?](#noinvoice)를 참조하세요.

    ![청구 기간, 다운로드 옵션 및 각 청구 기간별 총 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 청구 기간을 클릭하여 일간 사용 현황을 볼 수도 있습니다.

청구서에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요. 비용 관리에 대 한 도움이 필요한 경우 [Azure 청구 및 비용 관리를 사용 하 여 예기치 않은 비용 방지](billing-getting-started.md)를 참조 하세요.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 청구서 다운로드

Microsoft 고객 계약의 각 [청구 프로필](billing-mca-overview.md#billing-profiles) 에 대해 송장이 생성 됩니다. Azure Portal에서 송장을 다운로드 하려면 청구 프로필 소유자, 참가자, 독자 또는 송장 관리자 여야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Cost Management + 청구*를 검색 합니다.
1. 청구 프로필을 선택 합니다. 액세스에 따라 먼저 청구 계정을 선택 해야 할 수 있습니다.
1. **청구서**를 선택합니다.
1. 송장 그리드에서 다운로드 하려는 청구서의 행을 찾습니다.
1. 행의 끝에`...`있는 줄임표 ()를 클릭 합니다.
    ![행의 끝에 있는 줄임표 (...)를 보여 주는 스크린샷](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. 다운로드 상황에 맞는 메뉴에서 **송장**을 선택 합니다.

    ![상황에 맞는 메뉴를 보여 주는 스크린샷](./media/billing-download-azure-invoice/contextmenu.png)

마지막 청구 기간에 대 한 송장이 표시 되지 않으면 [마지막 청구 기간에 대 한 송장이 표시 되지 않는 이유](#noinvoice) 를 참조 하세요.

## <a name="get-your-invoice-in-email-pdf"></a>전자 메일로 청구서 받기(.pdf)

Azure 청구서를 전자 메일로 받을 추가 수취인을 옵트인하고 구성할 수 있습니다. 지원 제안, 기업 계약, Azure in Open 등의 특정 구독에는 이 기능이 제공되지 않을 수도 있습니다. Microsoft 고객 계약이 있는 경우 다음 섹션인 [메일에서 청구 프로필 송장 가져오기](#get-your-subscriptions-invoices-in-email)를 참조 하세요.

### <a name="get-your-subscriptions-invoices-in-email"></a>전자 메일에서 구독의 청구서 받기

1. 구독 [페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택 합니다. 소유하고 있는 각 구독에 대해 옵트인합니다. **청구서**를 클릭한 다음 **전자 메일로 청구서 받기**를 클릭합니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. **옵트인**을 클릭하고 조건에 동의합니다.

    ![옵트인 흐름 2단계를 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 규약에 동의 하면 추가 받는 사람을 구성할 수 있습니다. 받는 사람이 제거된 경우 전자 메일 주소는 더 이상 저장되지 않습니다. 마음이 바뀌면 다시 추가 해야 합니다.

    ![옵트인 흐름 3단계를 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

이 단계를 따른 후 전자 메일을 받지 못하면 [프로필의 통신 기본 설정](https://account.windowsazure.com/profile)에서 전자 메일 주소가 올바른지 확인합니다.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>이메일에서 구독의 청구서를 가져오는 옵트아웃

전자 메일로 송장을 받을 수 있도록 옵트아웃 하려면 이전 단계를 수행 하 고 전자 **메일로 받은 청구서 옵트아웃 (opt out**)을 클릭 합니다. 그러면 청구서를 메일로 받기 위해 설정된 메일 주소가 모두 제거됩니다. 다시 옵트인 하면 받는 사람을 다시 구성할 수 있습니다.

 ![옵트아웃 흐름을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>전자 메일로 Microsoft 고객 계약 청구서 받기

Microsoft 고객 계약이 있는 경우 청구서를 전자 메일로 받으려면 옵트인 (opt in) 할 수 있습니다. 모든 청구 프로필 소유자, 참가자, 독자 및 송장 관리자는 전자 메일로 송장을 받습니다. 독자는 전자 메일 청구서 기본 설정을 업데이트할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색 합니다.
1. 청구 프로필을 선택 합니다. 액세스에 따라 먼저 청구 계정을 선택 해야 할 수 있습니다.
1. **설정** 아래에서 **속성**을 선택합니다.
1. **전자 메일 청구서**에서 **전자 메일 청구서 기본 설정 업데이트**를 선택 합니다.

    ![전자 메일 송장 속성을 보여 주는 스크린샷](./media/billing-download-azure-invoice/billingprofile-email.png)

1. **옵트인을**선택 합니다.
1. **업데이트**를 클릭합니다.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>전자 메일에서 Microsoft 고객 계약 청구서 가져오기 옵트아웃

전자 메일로 송장을 받을 수 있도록 옵트아웃 하려면 위의 단계를 따르고 **옵트아웃 (opt out**)을 클릭 합니다. 모든 소유자, 참가자, 독자 및 송장 관리자는 전자 메일을 통해 송장을 받을 수 없습니다. 독자 라면 전자 메일 청구서 기본 설정을 변경할 수 없습니다.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스 권한 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

청구서 및 요금에 대 한 자세한 내용은 다음을 참조 하세요.

- [Microsoft Azure 사용량과 요금을 보고 다운로드 합니다.](billing-download-azure-daily-usage.md)
- [Microsoft Azure 요금 청구 방식 이해](billing-understand-your-bill.md)
- [Azure 청구서의 용어 이해](billing-understand-your-invoice.md)
- [Microsoft Azure 세부 사용량의 용어 이해](billing-understand-your-usage.md)
- [조직의 Azure 가격 책정 보기](billing-ea-pricing.md)

Microsoft 고객 계약이 있는 경우 다음을 참조 하세요.

- [청구 프로필에 대 한 청구서의 요금 이해](billing-mca-understand-your-bill.md)
- [청구 프로필에 대 한 청구서의 용어 이해](billing-mca-understand-your-invoice.md)
- [청구 프로필에 대 한 Azure 사용량 및 요금 파일 이해](billing-mca-understand-your-usage.md)
- [청구 프로필에 대 한 세금 문서 보기 및 다운로드](billing-mca-download-tax-document.md)
- [조직의 Azure 가격 책정 보기](billing-ea-pricing.md)
