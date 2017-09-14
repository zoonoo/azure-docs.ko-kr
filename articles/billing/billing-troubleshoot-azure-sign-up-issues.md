---
title: "Azure 등록 문제 해결 | Microsoft Docs"
description: "몇 가지 일반적인 Azure 설정 문제를 해결하는 방법에 대해 설명합니다."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: af8a7bbc4bf007dfa5bef7ceb9cf940ad752239a
ms.contentlocale: ko-kr
ms.lasthandoff: 09/06/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Azure의 등록 문제 해결
Azure에 등록할 수 없는 경우 이 문서에 있는 팁을 사용하여 일반적인 문제를 해결하세요. 등록 시 신용 카드 관련 문제가 발생하면 [Azure 등록 시 사용자의 직불 카드 또는 신용 카드가 거부되었습니다.](billing-credit-card-fails-during-azure-sign-up.md)를 참조하세요. Azure 계정이 있지만 로그인할 수 없는 경우 [내 Azure 구독 관리를 위해 로그인할 수 없습니다.](billing-cannot-login-subscription.md)를 참조하세요.

## <a name="error-we-cannot-proceed-with-signup-due-to-an-issue-with-your-account-please-contact-billing-support"></a>오류 "계정에서 발생한 문제로 인해 계속 로그인할 수 없습니다. 청구 지원 담당자에게 문의하세요." Please contact billing support." 

이 문제를 해결하려면 다음 단계를 수행합니다.

1. 계정 관리자 자격 증명을 사용하여 [Azure 계정 센터](https://account.azure.com)에 로그인합니다. 
2. **프로필**을 클릭하고 **세부 정보 편집**을 클릭합니다.
3. 모든 주소 필드가 완료되었고 유효한지 확인합니다. 
4. Azure 구독을 등록할 때 신용 카드 등록 중에 입력한 요금 청구서 주소가 은행의 레코드와 일치하는지 확인합니다.

오류가 계속 나타나면 다른 브라우저를 사용하여 등록을 시도하세요.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>진행 표시줄이 "카드로 ID 확인" 섹션에서 중지됩니다.

카드를 통해 본인 여부 확인을 완료하려면 브라우저에서 타사 쿠키를 허용해야 합니다.

![등록 중 중지된 카드로 ID 확인 섹션의 스크린샷](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

다음 단계를 사용하여 브라우저의 쿠키 설정을 업데이트하세요.

1. Chrome을 사용하는 경우 **설정** > **고급 설정 표시** > **개인 정보** > **콘텐츠 설정**으로 이동합니다. **타사 쿠키 및 사이트 데이터 차단**을 선택 해제합니다.
2. Edge를 사용하는 경우 **설정** > **고급 설정 보기** > **쿠키**로 이동합니다. **쿠키를 차단하지 않음**을 선택합니다.
3. Azure 등록 페이지를 새로 고치고 해당 문제가 해결되었는지 확인합니다.
4. 새로 고쳐도 문제가 해결되지 않을 경우에는 브라우저를 중단했다가 다시 시작하여 다시 시도하세요.

## <a name="credit-card-form-doesnt-support-my-billing-address"></a>신용 카드 양식이 내 청구 주소를 지원하지 않습니다.
청구 주소는 **내 정보** 섹션에서 선택한 국가의 주소여야 합니다. 올바른 국가를 선택했는지 확인합니다.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>계정 확인을 등록할 때 문자 메시지 또는 호출이 표시되지 않았습니다
일반적으로 훨씬 더 빠르지만, 확인 코드가 배달되는 데 최대 4분까지 걸릴 수 있습니다. 확인을 위해 입력한 전화 번호는 계정에 대한 연락처 번호로 저장되지 않습니다.

다음은 몇 가지 추가 팁입니다.
* VOIP 전화 번호는 전화 인증 프로세스에 사용할 수 없습니다.
* 드롭다운 메뉴에서 선택한 국가 코드를 포함하여 입력한 전화 번호를 다시 한번 점검하십시오.
* 전화가 문자 메시지(SMS)를 수신하지 못하는 경우 **전화 걸기** 옵션을 시도합니다.
* 전화가 미국 기반 번호에서 전화 또는 SMS 메시지를 받을 수 있는지 확인합니다.

문자 메시지 또는 전화를 받은 경우 수신한 코드를 텍스트 상자에 입력합니다.

## <a name="credit-card-declined-or-not-accepted"></a>신용 카드가 거부되거나 승인되지 않습니다.
가상 카드, 선불 카드 또는 직불 카드는 Azure 구독의 지불 옵션으로 허용되지 않습니다. 카드가 거절될만한 다른 이유를 확인하려면 [Azure 등록 시 사용자의 직불 카드 또는 신용 카드가 거부되었습니다.](billing-credit-card-fails-during-azure-sign-up.md)를 참조하세요.

## <a name="free-trial-is-not-available"></a>"무료 평가판은 제공되지 않습니다."
이전에 Azure 구독을 사용한 적이 있나요? Azure 사용 약관은 Azure에 새로 추가된 사용자에 대해서만 무료 평가판 활성화하도록 제한합니다. 다른 유형의 Azure 구독이 있는 경우 무료 평가판을 활성화할 수 없습니다. [종량제 구독](https://azure.microsoft.com/offers/ms-azr-0003p/) 등록을 고려합니다.

## <a name="i-saw-a-charge-on-my-free-trial-account"></a>평가판 계정에 요금이 부과되었습니다.
등록 후 신용 카드 계정에 소액의 확인용 금액이 표시될 수 있습니다. 이 표시는 3~5일 이내에 제거됩니다. 비용 관리가 염려되는 경우 [예상치 못한 비용 방지](https://docs.microsoft.com/azure/billing/billing-getting-started)에 대해 자세히 알아봅니다.

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN, BizSpark, BizSparkPlus 또는 MPN 같은 Azure 혜택 계획을 활성화할 수 없습니다.
올바른 로그인 자격 증명을 사용해야 합니다. 그런 다음 혜택 프로그램에서 자격이 있는지 확인합니다. 

* MSDN
  * [MSDN 계정 페이지](https://msdn.microsoft.com/subscriptions/manage/default.aspx)에서 자격 상태를 확인합니다.
  * 자신의 상태를 확인할 수 없는 경우 [MSDN 구독 고객 서비스 센터](https://msdn.microsoft.com/subscriptions/contactus.aspx)에 문의하세요.
* BizSpark
  * [BizSpark 포털](https://www.microsoft.com/bizspark/default.aspx#start-two) 에 로그인하고 BizSpark 및 BizSpark Plus에 대한 자격 상태를 확인합니다.
  * 상태를 확인할 수 없는 경우 [BizSpark 포럼에서 도움을 받을](http://aka.ms/bzforums)수 있습니다.
* MPN
  * [MPN 포털](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) 에 로그인하고 자격 상태를 확인합니다. 적절한 [클라우드 플랫폼 역량](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)을 가지고 있는 경우 추가 혜택의 자격이 있을 수 있습니다.
  * 자신의 상태를 확인할 수 없는 경우 [MPN 지원](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)에 문의하세요.

## <a name="cant-activate-new-azure-in-open-subscription"></a>새 Azure In Open 구독을 활성화할 수 없습니다.
Azure In Open 구독을 만들려면 하나 이상의 Azure In Open 토큰이 연결된 유효한 OSA(Online Service Activation) 키가 있어야 합니다. OSA 키가 없는 경우 [Microsoft Pinpoint](http://pinpoint.microsoft.com/)에 나열된 Microsoft 파트너 중 한 곳에 문의하세요.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.
다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

