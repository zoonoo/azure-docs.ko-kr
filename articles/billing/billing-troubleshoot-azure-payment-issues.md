---
title: Azure 결제 문제 해결
description: Microsoft Azure portal 또는 계정 센터에서 지불 정보 계정을 업데이트할 때의 문제 해결
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
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657087"
---
# <a name="troubleshoot-azure-payment-issues"></a>Azure 결제 문제 해결

Microsoft Azure portal 또는 Azure 계정 센터에서 지불 정보 계정을 업데이트 하려고 할 때 문제가 발생할 수 있습니다. 문제를 해결 하기 전에 다음 지침을 고려 하십시오.

- Azure 계정 프로필에 대해 제공한 정보 (연락처 전자 메일 주소, 주소 주소 및 전화 번호 포함)가 올바른지 확인 합니다.
- 신용 카드 정보가 정확한지 확인합니다.
- 동일한 정보의 Microsoft 계정이 이미 있는지 확인합니다.

## <a name="issues"></a>문제

오류를 해결 하려면 Azure에 등록 하려고 할 때 발생 하는 문제를 선택 합니다.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>저장 된 청구 지불 방법에서 신용 카드를 제거할 수 없습니다.

설계상 활성 구독에서 신용 카드를 제거할 수 없습니다.

기존 카드를 삭제 해야 하는 경우에는 새 카드를 구독에 추가 해야 기존 결제 방법을 삭제할 수 있습니다. 또는 구독을 취소 해야 합니다. 그러면 구독이 영구적으로 삭제 되 고 카드가 제거 됩니다.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>새 지불 방법을 추가한 후 이전 지불 방법을 삭제할 수 없음

새 결제 방법이 구독에 연결되지 않은 것일 수 있습니다. 결제 방법을 구독과 연결 하는 데 도움이 되도록 [Azure에 대 한 신용 카드 또는 직불 카드 추가, 업데이트 또는 제거](billing-how-to-change-credit-card.md)를 참조 하세요.

거부 된 카드와 관련 된 문제를 해결 하려면 [Azure 등록 시 거부 된 카드 문제를 해결 하는 방법](billing-troubleshoot-declined-card.md)을 참조 하세요.

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>*지불 방법 오류를 삭제할 수* 없으므로 지불 방법을 삭제할 수 없습니다.

이는 처리 중인 잔액 때문에 발생 합니다. 지불 방법을 삭제 하기 전에 처리 중인 잔액을 모두 지웁니다.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>내 계정 아래의 구독을 확인 하 여 지불 방법을 업데이트할 수 없습니다.

구독에 사용 되는 것과 다른 전자 메일 ID를 사용 하 고 있을 수 있습니다.

이 문제를 해결 하려면 [Azure Portal 또는 Azure 계정 센터에 대 한 구독을 찾을 수 없음 로그인 오류](billing-no-subscriptions-found.md)를 참조 하세요.

### <a name="unable-to-make-payment-for-a-subscription"></a>구독에 대 한 결제를 수행할 수 없습니다.

다음 오류 메시지가 표시 되는 경우: *결제 기한이 지났습니다. 지불 방법* 에 문제가 있거나 *죄송 합니다. 정보를 저장할 수 없습니다. 브라우저를 닫고 다시 시도 하세요.* 귀하의 금융 기관에 의해 카드가 거부 되었기 때문에 카드에 대 한 결제는 보류 중입니다.

신용 카드의 잔액이 충분 한지 확인 하 여 지불 합니다. 그렇지 않으면 다른 카드를 사용 하 여 결제를 수행 하거나 금융 기관에 연락 하 여 문제를 해결 합니다.

다음 문제에 대 한 은행이 있는지 확인 하세요.

- 국제 트랜잭션을 사용할 수 없습니다.
- 카드에는 신용 한도가 있으며 잔액은 합의 이어야 합니다.
- 카드에서 반복 결제를 사용할 수 있습니다.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>브라우저 문제로 인해 결제 방법을 변경할 수 없음 (브라우저가 응답 하지 않거나 로드 되지 않음)

모든 활성 Azure 세션에서 로그 아웃 한 다음 microsoft edge [에서 Inprivate 찾아보기 문서](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) 의 단계에 따라 microsoft Edge 또는 Internet Explorer 내에서 inprivate 세션을 시작 합니다.

개인 세션에서 신용 카드를 변경 하 여 신용 카드 정보를 업데이트 하거나 변경 하 [는 방법](billing-how-to-change-credit-card.md) 의 단계를 수행 합니다.

다음 작업을 수행해 볼 수도 있습니다.

- 브라우저 새로 고침
- 다른 브라우저 사용
- 캐시 된 쿠키 삭제

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>지불 방법을 업데이트 한 후에도 내 구독이 비활성화 됩니다.

이 문제는 처리 중인 잔액 때문에 발생 합니다. 지불 방법을 삭제 하기 전에 처리 중인 잔액을 모두 지웁니다.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>XML 오류 응답 페이지로 인해 지불 방법을 변경할 수 없습니다.

[Azure Portal](https://portal.azure.com/) 를 사용 하 여 새 신용 카드를 추가 하는 경우이 메시지가 표시 됩니다.

카드 세부 정보를 추가 하려면 계정 관리자의 전자 메일 주소를 사용 하 여 Azure 계정 포털에 로그인 합니다.

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
