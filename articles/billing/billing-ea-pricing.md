---
title: 조직의 Azure 가격 책정 보기 및 다운로드
description: 조직의 가격 책정으로 가격 책정 또는 예상 비용을 살펴보고 다운로드하는 방법을 알아봅니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 486a6b46b6c4590b7f49cd8aba449204cd8f4fac
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709695"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>조직의 Azure 가격 책정 보기 및 다운로드

Azure EA(기업계약) 또는 [Microsoft 고객 계약](#check-your-access-to-a-microsoft-customer-agreement)이 있는 Azure 고객은 Azure Portal의 가격 책정을 보고 다운로드할 수 있습니다.

## <a name="ea-pricing"></a>EA 가격 책정

엔터프라이즈 관리자가 조직에 대해 설정한 정책에 따라 특정 관리 역할만 조직의 EA 가격 정보에 대한 액세스 권한을 제공합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](billing-understand-ea-roles.md)를 참조하세요.

1. 엔터프라이즈 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. *Cost Management + 청구*를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. 청구 계정에서 **사용량 + 요금**을 선택합니다.

   ![청구 아래의 사용량 및 요금을 보여 주는 스크린샷](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. 해당 월의 ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-ea-pricing/download-icon.png) **다운로드**를 선택합니다.

1. **가격표** 아래에서 **csv 다운로드**를 선택합니다.

   ![가격표 csv 다운로드 단추를 보여 주는 스크린샷](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="microsoft-customer-agreement-pricing"></a>Microsoft 고객 계약 가격 책정

가격 책정을 보고 다운로드하려면 청구 프로필 소유자, 기여자, 독자 또는 청구서 관리자여야 합니다. Microsoft 고객 계약의 청구 역할에 대해 자세히 알아보려면 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

### <a name="download-price-sheets-for-the-current-billing-period"></a>현재 청구 기간에 대한 가격표 다운로드

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. *Cost Management + 청구*를 검색합니다.
1. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 먼저 선택해야 할 수도 있습니다.
1. **개요** 영역에서 월간 누계 요금 아래의 다운로드 링크를 찾습니다.
1. **Azure 가격표**를 선택합니다.
![개요에서 다운로드를 보여 주는 스크린샷](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>청구 요금에 대한 가격표 다운로드

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. *Cost Management + 청구*를 검색합니다.
1. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 먼저 선택해야 할 수도 있습니다.
1. **청구서**를 선택합니다.
1. 청구서 그리드에서 다운로드하려는 가격표에 해당하는 청구서의 행을 찾습니다.
1. 행 끝에 있는 줄임표(`...`)를 클릭합니다.
![선택한 줄임표를 보여 주는 스크린샷](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. 선택한 청구서에서 서비스에 대한 가격을 확인하려면 **청구서 가격표**를 선택합니다.
1. 지정된 청구 기간에 대한 모든 Azure 서비스의 가격을 확인하려면 **Azure 가격표**를 선택합니다.

![가격표가 있는 상황에 맞는 메뉴를 보여 주는 스크린샷](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Azure 가격 책정 계산기가 있는 비용 예상

조직의 가격 책정을 사용하여 Azure 가격 책정 계산기로 비용을 예상할 수도 있습니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)로 이동합니다.
1. 오른쪽 맨 위에서 **로그인**을 선택합니다.
1. **프로그램 및 제품** > **라이선스 프로그램** 아래에서 **EA(기업계약)** 를 선택합니다.
1. **프로그램 및 제품** > **선택한 계약** 아래에서 **선택된 항목 없음**을 선택합니다.

    ![가격표 csv 다운로드 단추를 보여 주는 스크린샷](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. 조직을 선택합니다.
1. **적용**을 선택합니다.
1. 제품을 검색한 다음, 예상에 추가합니다.
1. 표시된 예상 가격은 선택한 조직의 가격을 기준으로 합니다.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>다음 단계

EA 고객인 경우 다음을 참조하세요.

- [Azure의 EA 청구 정보에 대한 액세스 관리](billing-manage-access.md)
- [Microsoft Azure 청구서 보기 및 다운로드](billing-download-azure-invoice.md)
- [Microsoft Azure 사용량 및 요금 보기 및 다운로드](billing-download-azure-daily-usage.md)
- [기업계약 고객에 대한 청구서 이해](billing-understand-your-bill-ea.md)

Microsoft 고객 계약이 있는 경우 다음을 참조하세요.

- [Microsoft 고객 계약에 대한 가격표에서 사용 약관 이해](billing-mca-understand-pricesheet.md)
- [Microsoft Azure 청구서 보기 및 다운로드](billing-download-azure-invoice.md)
- [Microsoft Azure 사용량 및 요금 보기 및 다운로드](billing-download-azure-daily-usage.md)
- [청구 프로필에 대한 세금 문서 보기 및 다운로드](billing-mca-download-tax-document.md)
- [청구 프로필 청구서의 요금 이해](billing-mca-understand-your-bill.md)
