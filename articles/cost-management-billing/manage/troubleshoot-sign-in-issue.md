---
title: Azure 구독 로그인 문제 해결
description: Azure Portal 또는 Azure 계정 센터에 로그인할 수 없는 문제를 해결하는 데 도움이 됩니다.
author: v-miegge
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 4213df57423f5211517b019a8b58be99d2e12e7a
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409334"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Azure 구독 로그인 문제 해결

이 가이드는 Azure Portal 또는 Azure 계정 센터에 로그인할 수 없는 문제를 해결하는 데 도움이 됩니다.

## <a name="issues"></a>문제

### <a name="page-hangs-in-the-loading-status"></a>페이지가 로딩 상태에서 중지됨

인터넷 브라우저 페이지가 응답하지 않는 경우 Azure Portal에 액세스할 수 있을 때까지 다음 각 단계를 시도해 보세요.

- 페이지를 새로 고칩니다.
- 다른 인터넷 브라우저를 사용합니다.
- 브라우저에 대해 프라이빗 검색 모드를 사용합니다. Internet Explorer: **도구** > **안전** > **InPrivate 브라우징**을 클릭한 다음, [Azure Portal](https://portal.azure.com/) 또는 [Azure 계정 센터](https://account.azure.com/Subscriptions)에 로그인합니다.

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>다른 사용자로 자동으로 로그인됨

인터넷 브라우저에서 둘 이상의 사용자 계정을 사용 중인 경우 이 문제가 발생할 수 있습니다.

이 문제를 해결하려면 다음 방법 중 하나를 사용해 보세요.

- 캐시의 선택을 취소하고 인터넷 쿠키를 삭제합니다. Internet Explorer에서 **도구** > **인터넷 옵션** > **삭제**를 클릭합니다. 임시 파일, 쿠키, 암호 및 검색 기록에 대한 확인란이 선택되었는지 확인한 후 삭제를 클릭합니다.
- Internet Explorer 설정을 재설정하여 수행한 모든 개인 설정을 되돌립니다. **도구** > **인터넷 옵션** > **고급** >을 클릭하고 **개인 설정 삭제** 상자 > **재설정**을 선택합니다.
- 브라우저에 대해 프라이빗 검색 모드를 사용합니다. Internet Explorer:  **도구** > **안전** > **InPrivate 브라우징**을 클릭한 다음, [Azure Portal](https://portal.azure.com/) 또는 [Azure 계정 센터](https://account.azure.com/Subscriptions)에 로그인합니다.

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>로그인할 수 있지만 *구독을 찾을 수 없음*이 표시됩니다.

잘못된 디렉터리에서 선택했거나 사용자 계정에 충분한 권한이 없는 경우 이 문제가 발생합니다.

**시나리오 1:** [Azure Portal](https://portal.azure.com/)에서 오류 메시지가 표시됨

이 문제를 해결하려면

- 오른쪽 위에 있는 계정을 클릭하여 올바른 Azure 디렉터리를 선택했는지 확인합니다.
- 올바른 Azure 디렉터리를 선택했으나 여전히 오류 메시지가 표시되면 계정을 [소유자로 추가](add-change-subscription-administrator.md)합니다.

**시나리오 2:** [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에서 오류 메시지가 표시됨

사용한 계정이 계정 관리자인지 확인합니다. 계정 관리자가 누구인지 확인하려면 다음 단계를 따르세요.

1. [Azure Portal의 구독 보기](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에 로그인합니다.

2. 확인하려는 구독을 선택한 다음 **설정**에서 확인합니다.

3. **속성**을 선택합니다. 구독의 계정 관리자는 **계정 관리자** 상자에 표시됩니다.

## <a name="additional-help-resources"></a>추가 도움말 리소스

Azure 청구 및 구독에 대한 기타 문제 해결 문서

- [거부된 카드](troubleshoot-declined-card.md)
- [구독 등록 문제](troubleshoot-azure-sign-up.md)
- [구독을 찾을 수 없음](no-subscriptions-found.md)
- [Enterprise 비용 보기 사용 안 함](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>도움이 필요한 경우 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- [Azure Billing 설명서](../../billing/index.md)
