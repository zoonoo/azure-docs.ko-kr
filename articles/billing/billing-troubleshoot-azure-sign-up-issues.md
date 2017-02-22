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
ms.date: 02/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: f058194302949d1f5ba7d86e9a693ba229bdc9a2
ms.openlocfilehash: b913fd0d944568e422ffcf86791910a746367add


---
# <a name="i-cant-sign-up-for-azure"></a>Azure에 등록할 수 없습니다.
Azure에 등록할 수 없는 경우 몇 가지를 확인하여 문제를 해결할 수 있습니다.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>진행 표시줄이 "카드로 ID 확인" 섹션에서 중지됩니다.

Azure 등록 중 "카드로 ID 확인" 섹션이 있습니다. 진행률 표시줄이 중지될 경우:

![등록 중 중지된 카드로 ID 확인 섹션의 스크린샷](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

이 문제는 사용자의 브라우저에 대해 타사 쿠키가 차단된 경우에 발생합니다.

### <a name="suggestion"></a>제안

1. 브라우저 설정에서 타사 쿠키를 허용합니다.
  * Edge에서 Settings(설정) -> View advanced settings(고급 설정 보기) -> Cookies(쿠키)로 이동한 후 “Don't block cookies”(쿠키 차단 안 함)를 선택합니다.
  * Chrome에서 Settings(설정) -> Show advanced settings(고급 설정 표시) -> Privacy(프라이버시) -> Content settings(콘텐츠 설정)로 이동한 후 "Block third-party cookies and site data"(타사 쿠키 및 사이트 데이터 차단)를 선택 해제합니다.
2. Azure 등록 페이지를 새로 고치고 해당 문제가 해결되었는지 확인합니다.
3. 새로 고쳐도 문제가 해결되지 않을 경우에는 브라우저를 중단했다가 다시 시작하여 다시 시도하세요.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>계정 확인을 등록할 때 문자 메시지 또는 호출이 표시되지 않았습니다
* 텍스트 코드가 전달될 때까지 최대&4;분이 걸릴 수 있습니다.
* 전화 번호로 SMS를 받을 수 있는지 확인합니다.
* 드롭다운 메뉴의 국가 코드 선택 등 입력한 전화 번호를 다시 한 번 점검하십시오.
* 휴대 전화로 “문자로 인증”을 사용하는 경우 문자 메시지를 수신하거나 “전화로 인증" 대안을 선택하는 경우 전화 통화를 수신할 수 있습니다.
* 문자 메시지를 수신하면 텍스트 상자에 코드를 삽입하고 확인 단추를 클릭하여 계속 진행합니다.

### <a name="suggestions"></a>제안
* 휴대폰으로 문자 메시지(SMS)를 받지 못하는 경우 "전화로 인증" 대체 확인 방법을 사용합니다.
* SMS와 "전화로 인증" 방법을 모두 사용하여 전화 확인 단계가 실패한 경우 다른 전화 번호를 사용합니다.
* VOIP 전화 번호를 휴대폰 인증 프로세스에 사용할 수 없습니다.

## <a name="credit-card-declined-or-not-accepted"></a>신용 카드가 거부되거나 승인되지 않습니다.
등록 시 사용 중인 지불 방법이 지원되는 방법인지 확인합니다. 또한 [직불 카드 또는 신용 카드가 Azure 등록 시 거부](billing-credit-card-fails-during-azure-sign-up.md)되는 이유를 자세히 알아볼 수 있습니다.

## <a name="free-trial-is-not-available"></a>"무료 평가판은 제공되지 않습니다."
이전에 Azure 구독을 사용한 적이 있나요? Azure 사용 약관은 Azure에 새로 추가된 사용자에 대해서만 무료 평가판 활성화하도록 제한합니다. 다른 유형의 Azure 구독이 있는 경우 무료 평가판을 활성화할 수 없습니다.

### <a name="suggestion"></a>제안
* [종량제 구독](https://azure.microsoft.com/offers/ms-azr-0003p/) 등록을 고려합니다.

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN, BizSpark, BizSparkPlus 또는 MPN 같은 Azure 혜택 계획을 활성화할 수 없습니다.
올바른 자격 증명을 사용하고 있는지 확인하고, 선택한 요금제에 대한 자격이 있는지 혜택 프로그램 채널을 통해 확인하세요.

* MSDN
  * [MSDN 계정 페이지](https://msdn.microsoft.com/subscriptions/manage/default.aspx)에서 자격 상태를 확인합니다.
  * 사용자의 상태를 확인할 수 없는 경우 [MSDN 구독 고객 서비스 센터](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * [MPN 포털](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) 에 로그인하고 자격 상태를 확인합니다. 적절한 [클라우드 플랫폼 역량](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)을 가지고 있는 경우 추가 혜택의 자격이 있을 수 있습니다.
  * 사용자의 상태를 확인할 수 없는 경우 [MPN 지원](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)에 문의하세요.
* BizSpark
  * [BizSpark 포털](https://www.microsoft.com/bizspark/default.aspx#start-two) 에 로그인하고 BizSpark 및 BizSpark Plus에 대한 자격 상태를 확인합니다.
  * 상태를 확인할 수 없는 경우 [BizSpark 팀에 문의하세요.](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

## <a name="cant-activate-new-azure-in-open-subscription"></a>새 Azure In Open 구독을 활성화할 수 없습니다.
새 Azure In Open 구독을 만들려면 적어도 하나 이상의 Azure In Open 토큰이 연결된 유효한 OSA 키가 있어야 합니다.

### <a name="suggestion"></a>제안
OSA 키가 없는 경우 [Microsoft Pinpoint](http://pinpoint.microsoft.com/)에 나열된 Microsoft 파트너 중 하나에 문의하세요.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.
다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.



<!--HONumber=Feb17_HO2-->


