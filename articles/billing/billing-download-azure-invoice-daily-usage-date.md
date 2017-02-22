---
title: "Azure 청구서 및 일간 사용 현황 데이터를 받는 방법 | Microsoft Docs"
description: "Azure 청구서 및 일간 사용 현황 데이터를 받는 방법 설명"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 4d974590ee7677a4f3fcebd9e2474c99a974f663
ms.openlocfilehash: 60ac57fd22f237b7fe62773486fe46913aca8dd0


---
# <a name="how-to-get-your-azure-billing-invoice-and-daily-usage-data"></a>Azure 청구서 및 일간 사용 현황 데이터를 받는 방법
Azure 청구서를 전자 메일로 받을 추가 수취인을 옵트인하고 구성할 수 있습니다. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 청구서를 다운로드할 수도 있습니다. 또한 [Azure 계정 센터](https://account.windowsazure.com)에서 사용 현황 csv를 다운로드할 수 있습니다. 계정 관리자만 청구서 및 사용 현황 정보를 확인할 권한이 있습니다. 구독의 계정 관리자를 확인하려면 [Azure 구독의 소유권 양도 - FAQ](billing-subscription-transfer.md#faq)를 참조하세요.

## <a name="get-your-invoice-over-email-pdf"></a>전자 메일로 청구서 받기(.pdf)
1. [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다. 소유하고 있는 각 구독에 대해 옵트인해야 합니다. **청구서**를 클릭한 다음 **전자 메일로 청구서 받기**를 클릭합니다. 계정 관리자 아닌 경우 이 옵션이 표시되지 않을 수 있습니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. **옵트인**을 클릭하여 조건에 동의합니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 규약에 동의하면 추가 수취인을 구성할 수 있습니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
월별 명세서 알림 전자 메일에서도 이 블레이드 딥 링크에 액세스할 수 있습니다.

### <a name="i-cant-access-the-email-settings-blade"></a>전자 메일 설정 블레이드에 액세스할 수 없습니다.
* 이 설정을 구성하려면 계정 관리자여야 합니다. 어떤 뜻인지 잘 모르겠나요? [여기를 참조하세요](billing-add-change-azure-subscription-administrator.md).
* 월별 청구서가 있는데 전자 메일을 받지 못한 경우 [통신 전자 메일이 올바르게 설정](https://account.windowsazure.com/profile)되었는지 확인하세요.
* 지원 서비스, 기업 계약, Azure in Open 등의 특정 구독에는 이 기능이 제공되지 않을 수도 있습니다.

## <a name="download-invoice-from-azure-portal-pdf"></a>Azure Portal에서 청구서 다운로드(.pdf)

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 허브 메뉴에서 **구독**을 선택합니다. 

    ![구독 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. **구독** 블레이드에서 보려는 구독을 선택하고 **청구 및 사용량**을 선택합니다. 

    ![청구 및 사용 현황 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. **청구 및 사용량** 블레이드에서 **청구서 다운로드**를 클릭하여 pdf 청구서 사본을 봅니다. 

    ![청구 기간, 다운로드 옵션 및 각 청구 기간별 총 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. 청구 기간을 클릭하여 일간 사용 현황을 볼 수 있습니다. 

청구서에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요.

## <a name="download-usage-from-the-account-center-csv"></a>계정 센터에서 사용 현황 다운로드(.csv)
1. [Azure 계정 센터](https://account.windowsazure.com/subscriptions)에 계정 관리자로 로그인합니다.
2. 원하는 송장 및 사용 정보에 대한 구독을 선택합니다.
3. **청구 내역**을 선택합니다. 

    ![청구 내역 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 이전&6;개의 청구 기간 및 현재 미결제한 기간의 명세서를 볼 수 있습니다. 

    ![청구 기간, 청구서 및 일간 사용 현황 다운로드 옵션, 각 청구 기간에 대한 총 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. **현재 명세서 보기** 를 선택하여 예상 요금을 볼 수 있습니다. 이 정보는 매일 업데이트만 될 뿐 모든 사용량을 포함하지 않습니다. 월별 청구서는 이 예상과 다를 수 있습니다.

    ![현재 명세서 보기 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![현재 예상 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. **사용량 다운로드**를 선택하여 일간 사용 데이터를 CSV 파일로 다운로드 할 수 있습니다. 사용할 수 있는 두 가지 버전이 표시되면 버전 2를 다운로드합니다.

    ![사용 현황 다운로드 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

일간 사용 현황에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요.

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> 왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?
몇 가지 이유로 인해 청구서가 보이지 않을 수 있습니다.
- 구독에 월별 크레딧 금액이 있고 이 금액을 초과하지 않았거나 무료 평가판을 사용하는 경우입니다. 납부할 금액이 없으면 청구서가 생성되지 않습니다.
- Azure를 구독한 지 30일 이내입니다.
- 청구서가 아직 생성되지 않았습니다. 청구 기간이 끝날 때까지 기다립니다.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.
계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.




<!--HONumber=Feb17_HO3-->


