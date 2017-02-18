---
title: "Azure 구독 취소 | Microsoft Docs"
description: "무료 평가판 구독과 같은 Azure 구독을 취소하는 방법을 설명합니다."
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: ccde3885f9412829cc6ff79b7efb4e3a2c099b5b


---
# <a name="cancel-your-azure-subscription"></a>Azure 구독 취소
계정 관리자 권한으로 Azure 구독을 취소할 수 있습니다. 구독을 취소하면 Azure 서비스 및 리소스에 대한 액세스 권한이 종료됩니다.

구독을 취소하기 전에 다음을 수행합니다.

* 데이터를 백업합니다. 예를 들어 Azure Storage 또는 SQL에 데이터를 저장하는 경우 복사본을 다운로드합니다. 가상 컴퓨터를 사용하는 경우 해당 이미지를 로컬로 저장합니다.
* 서비스를 종료합니다. [관리 포털의 페이지 리소스](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)로 이동한 후 실행 중인 가상 컴퓨터, 응용 프로그램 또는 기타 서비스를 모두 **중지**합니다.
* 데이터를 마이그레이션하는 것이 좋습니다. [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.

유료 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 취소해도 6개월 중 남은 기간에 대해 월별 요금이 계속 청구됩니다.

## <a name="cancel-subscription-via-the-azure-portal"></a>Azure 포털을 통해 구독 취소
1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

   > [!NOTE]
   > 계정 관리자만 구독을 취소할 수 있습니다. 구독의 계정 관리자를 알아내는 방법에 대한 자세한 내용은 [질문과 대답](billing-subscription-transfer.md#faq)을 참조하세요.
   >
   >

2. 허브 메뉴에서 **구독**을 선택합니다.

    ![구독 단추](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. 구독 블레이드에서 취소하고 싶은 구독을 선택하고 **Sub 취소**를 클릭하여 블레이드 취소를 시작합니다.

    ![cancel_ibiza](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. 구독 이름을 입력하고 취소 이유를 선택한 후 블레이드 취소의 아래쪽에 있는 **Sub 취소** 단추를 클릭합니다.

> [!NOTE]
> 취소가 포털에 반영되려면 최대 10분이 걸리지만 결제는 즉시 중지됩니다.
>
>

## <a name="cancel-subscription-via-the-azure-account-center"></a>Azure 계정 센터를 통해 구독 취소
1. [Azure 계정 센터](https://account.windowsazure.com/subscriptions) 에 계정 관리자로 로그온합니다.
2. **구독을 클릭하여 세부 정보 및 사용법**에서 취소하고자 하는 구독을 선택합니다.

    ![selectsub](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. 페이지의 오른쪽에서 **구독 취소**를 선택합니다.

    ![cancelsub](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. **예, 구독을 취소합니다**를 선택합니다.

    ![cancelbox](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. 그런 다음 ![checkbutton](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) 을 클릭하여 대화 창을 닫고 구독 페이지로 돌아갑니다.

사용자가 구독을 취소하더라도 다시 액세스해야 하거나 마음이 바뀐 경우를 대비해서 90일 동안 기다렸다가 데이터를 영구 삭제합니다. 자세한 내용은 [Microsoft 보안 센터 - 데이터 관리 방법](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)을 참조하세요.

## <a name="reactivate-subscription"></a>구독 다시 활성화
종량제 구독을 실수로 취소한 경우 [계정 센터에서 다시 활성화](billing-subscription-become-disable.md)할 수 있습니다.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.
계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.



<!--HONumber=Feb17_HO2-->


