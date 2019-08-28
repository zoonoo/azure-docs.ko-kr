---
title: Azure 등록 문제 해결
description: Microsoft Azure portal 계정 센터에서 새 계정을 등록 하려고 할 때 발생 하는 문제 해결
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657074"
---
# <a name="troubleshoot-azure-sign-up"></a>Azure 등록 문제 해결

Microsoft Azure portal 또는 Azure 계정 센터에서 새 계정을 등록 하려고 하면 문제가 발생할 수 있습니다. 문제를 해결 하기 전에 먼저 다음을 확인 합니다.

- Azure 계정 프로필에 대해 제공한 정보 (연락처 전자 메일 주소, 주소 주소 및 전화 번호 포함)가 올바릅니다.
- 신용 카드 정보가 올바릅니다.
- 동일한 정보를 포함 하는 Microsoft 계정이 아직 없습니다.

## <a name="resolutions"></a>해결 방법

오류를 해결 하려면 Azure에 등록 하려고 할 때 발생 하는 문제를 선택 합니다.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>오류: *계정 문제로 인해 등록을 진행할 수 없습니다. 청구 지원에 문의 하세요.*

이 문제를 해결하려면 다음 단계를 수행합니다.

1. 계정 관리자 자격 증명을 사용하여 [Azure 계정 센터](https://account.azure.com/Profile)에 로그인합니다.

2. **세부 정보 편집**을 선택합니다.

3. 모든 주소 필드가 완료되었고 유효한지 확인합니다.

4. Azure 구독에 등록할 때 신용 카드 등록에 대 한 청구 주소가 은행 기록과 일치 하는지 확인 합니다.

오류 메시지가 계속 표시 되는 경우 다른 브라우저를 사용 하 여 등록 해 보세요.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>*카드의 id 확인* 섹션에서 진행률 표시줄이 중단 됩니다.

카드를 통해 본인 여부 확인을 완료하려면 브라우저에서 타사 쿠키를 허용해야 합니다.

![카드로 ID 검증](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
다음 단계를 사용하여 브라우저의 쿠키 설정을 업데이트하세요.

1. Chrome을 사용 하는 경우 **설정** > **고급 설정** > 표시**개인 정보** > **콘텐츠 설정**을 선택 합니다. **타사 쿠키 및 사이트 데이터 차단을**선택 취소 합니다.

2. Microsoft Edge를 사용 하는 경우 **설정** > **고급 설정** > 보기**쿠키** > 는**쿠키를 차단 하지 않습니다**.를 선택 합니다.

3. Azure 등록 페이지를 새로 고치고 문제가 해결 되었는지 확인 합니다.

4. 새로 고칠 때 문제가 해결 되지 않으면 브라우저를 종료 하 고 다시 시작한 후 다시 시도 하십시오.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>신용 카드 양식이 내 청구 주소를 지원하지 않습니다.

청구 주소는 **사용자 정보** 섹션에서 선택한 국가에 위치 해야 합니다. 올바른 국가를 선택 했는지 확인 합니다.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>계정 확인을 등록할 때 문자 메시지 또는 호출이 표시되지 않았습니다

프로세스는 일반적으로 신속 하지만 확인 코드를 배달 하는 데 최대 4 분이 걸릴 수 있습니다. 확인을 위해 입력 하는 전화 번호는 계정에 대 한 연락처 번호로 저장 되지 않습니다.

다음은 몇 가지 추가 팁입니다.

- 전화 인증 프로세스에는 VoiP (Voice over IP) 전화 번호를 사용할 수 없습니다.
- 드롭다운 메뉴에서 선택한 국가 코드를 포함 하 여 입력 한 전화 번호를 두 번 확인 합니다.
- 전화가 문자 메시지(SMS)를 수신하지 못하는 경우 **전화 걸기** 옵션을 시도합니다.
- 전화는 미국 기반 전화 번호에서 전화 또는 SMS 메시지를 받을 수 있는지 확인 합니다.

문자 메시지 또는 전화 통화를 받으면 텍스트 상자에 표시 되는 코드를 입력 합니다.

### <a name="credit-card-declined-or-not-accepted"></a>신용 카드가 거부되거나 승인되지 않습니다.

가상 또는 선불 신용 카드 또는 직불 카드는 Azure 구독에 대 한 결제로 수락 되지 않습니다. 카드가 거부 될 수 있는 다른 원인을 확인 하려면 [Azure 등록 시](https://support.microsoft.com/help/4042960)직불 카드 또는 신용 카드가 거부 됨을 참조 하세요.

### <a name="free-trial-is-not-available"></a>무료 평가판을 사용할 수 없습니다.

이전에 Azure 구독을 사용한 적이 있나요? Azure 사용 약관은 Azure에 새로 추가된 사용자에 대해서만 무료 평가판 활성화하도록 제한합니다. 다른 유형의 Azure 구독이 있는 경우 무료 평가판을 활성화할 수 없습니다. [종량제 구독](https://azure.microsoft.com/offers/ms-azr-0003p/) 등록을 고려합니다.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>평가판 계정에 요금이 부과되었습니다.

등록 한 후 신용 카드 계정에 대해 약간의 확인이 포함 된 것을 볼 수 있습니다. 이는 3 ~ 5 일 이내에 제거 됩니다. 비용 관리가 염려되는 경우 [예상치 못한 비용 방지](billing-getting-started.md)에 대해 자세히 알아봅니다.

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN, BizSpark, BizSparkPlus 또는 MPN와 같은 Azure 혜택 계획을 활성화할 수 없습니다.

올바른 로그인 자격 증명을 사용 하 고 있는지 확인 합니다. 그런 다음 혜택 프로그램을 확인 하 여 자격이 있는지 확인 합니다.

- MSDN 
  - [MSDN 계정 페이지](https://msdn.microsoft.com/subscriptions/manage/default.aspx)에서 자격 상태를 확인 합니다.
  - 상태를 확인할 수 없는 경우 [MSDN Subscription 고객 서비스 센터](https://msdn.microsoft.com/library/aa493452.aspx)에 문의 하세요.
- Microsoft for Startups
  - Microsoft for microsoft [portal](https://startups.microsoft.com/#start-two) 에 로그인 하 여 Microsoft가 microsoft에 대 한 자격을 확인 합니다.
  - 사용자의 상태를 확인할 수 없는 경우 [Microsoft 시작 포럼](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)에서 도움을 받을 수 있습니다.
- MPN 
  - [MPN 포털](https://mspartner.microsoft.com/Pages/Locale.aspx) 에 로그인 하 여 자격 상태를 확인 합니다. 적절한 [클라우드 플랫폼 역량](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)을 가지고 있는 경우 추가 혜택의 자격이 있을 수 있습니다.
  - 상태를 확인할 수 없는 경우 [MPN 지원](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)에 문의 하세요.

### <a name="cant-activate-new-azure-in-open-subscription"></a>새 Azure In Open 구독을 활성화할 수 없습니다.

Azure In Open 구독을 만들려면 하나 이상의 Azure In Open 토큰이 연결 된 유효한 OSA (온라인 서비스 활성화) 키가 있어야 합니다. OSA 키가 없는 경우 [microsoft](http://pinpoint.microsoft.com/)에 나열 된 microsoft 파트너 중 하나에 문의 하세요.

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>오류: *Azure 구독에 대 한 자격이 없습니다.*

이 문제를 해결 하려면 다음 항목이 true 인지 여부를 다시 확인 합니다.

- Azure 계정 프로필에 대해 제공한 정보 (연락처 전자 메일 주소, 주소 주소 및 전화 번호 포함)가 올바릅니다.
- 신용 카드 정보가 올바릅니다.
- 동일한 정보를 사용 하는 Microsoft 계정이 아직 없습니다.

### <a name="error-your-current-account-type-is-not-supported"></a>오류: *현재 계정 유형은 지원 되지 않습니다.*

이 문제는 계정이 [관리 되지 않는 AZURE ad 디렉터리](../active-directory/users-groups-roles/directory-self-service-signup.md)에 등록 되어 있고 조직의 azure ad 디렉터리에 있지 않은 경우에 발생할 수 있습니다. 

이 문제를 해결 하려면 다른 계정을 사용 하 여 Azure 계정을 등록 하거나 관리 되지 않는 AD 디렉터리를 사용 합니다. 자세한 내용은 [Azure Active Directory에서 관리자 권한으로 관리 되지 않는 디렉터리 가져오기](../active-directory/users-groups-roles/domains-admin-takeover.md)를 참조 하세요.
 
## <a name="additional-help-resources"></a>추가 도움말 리소스

Azure 청구 및 구독에 대 한 기타 문제 해결 문서

- [거부된 카드](billing-troubleshoot-declined-card.md)
- [구독 로그인 문제](billing-troubleshoot-sign-in-issue.md)
- [구독을 찾을 수 없음](billing-no-subscriptions-found.md)
- [Enterprise 비용 보기 사용 안 함](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>도움이 필요 하면 문의해 주세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- [Azure 청구 설명서](index.md)
