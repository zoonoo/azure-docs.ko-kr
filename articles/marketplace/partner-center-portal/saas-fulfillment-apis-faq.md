---
title: SaaS 처리 Api-FAQ | Azure Marketplace
description: Azure Marketplace에서 SaaS 제품 고객의 검색 및 구매 환경.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: b324d3b9dca710dca6f5f99ad50ce4d973a42d2a
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869548"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS 처리 API - FAQ

Azure 고객이 SaaS 제품을 구독할 수 있도록 하는 Azure Marketplace 통합 요구 사항이 나열 되어 있습니다.

## <a name="discovery-experience"></a>검색 환경

제품이 게시 되 면 Azure 사용자는 Azure Marketplace에서 SaaS 제품을 검색할 수 있습니다. 고객은 제품 유형 (SaaS)을 기반으로 제품을 필터링 하 고 관심이 있는 SaaS 서비스를 검색할 수 있습니다.

## <a name="purchase-experience"></a>구매 환경

사용자가 특정 SaaS 서비스에 관심이 있으면 사용자가 Azure Marketplace에서 구독할 수 있습니다.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure 사용자가 Azure Marketplace에서 SaaS 제품을 구독 하는 것은 무엇을 의미 하나요?

이는 사용자가 SaaS 서비스와 관련 된 사용 약관 및 개인 정보 취급 방침을 볼 수 있으며, Microsoft 청구서에 있는 SaaS 제품의 게시자 인 청구 약관에 따라 요금을 지불 하는 데 동의 하는 것을 의미 합니다. 사용자는 Azure에서 기존 결제 프로필을 사용 하 여 SaaS 서비스 소비에 대 한 비용을 지불할 수 있습니다.

이는 여러 가지 이유로 유용 합니다. 이제 고객은 사용 하려는 모든 ISV 소프트웨어를 심사 않고도 신뢰할 수 있는 원본으로 Microsoft 클라우드 플랫폼을 사용 하 여 한 곳에서 검색 하 고 구독할 수 있습니다. 또한 고객은 각 ISV 소프트웨어를 독립적으로 명시적으로 지불 하지 않고도 기존 결제 프로필을 사용할 수 있습니다.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>제품이 구독 될 때 사용자가 자동으로 청구 되나요?

SaaS 제품을 구독 하는 동안 사용자는 Microsoft 플랫폼을 통해 SaaS 서비스의 사용량에 대 한 요금을 지불 하기로 했습니다. 그러나 요금은 제품이 사용 되는 경우에만 시작 됩니다. 사용자는 SaaS 제품으로 이동한 후 계정 만들기를 확인 하 여 제품 사용을 시작 해야 합니다. 그런 다음이 고객 SaaS 구독에 대 한 청구를 시작 하도록 Microsoft에 알려 줍니다.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>사용자가 SaaS 제품을 구독 하는 경우 어떤 알림이 표시 되나요?

제품을 구독 한 후 Azure 사용자는 Azure에서 모든 제품을 검색 하 고 관리할 수 있습니다. 기본적으로 새로 가입한 SaaS 제안의 상태는 **' 프로 비전 중, 대기 중 '** 입니다. 이 상태에서 Azure 사용자는 Azure Portal에서 SaaS 구독 관리 환경을 탐색 하기 위해 **' 계정 구성 '** 작업을 수행 하 라는 메시지가 표시 됩니다.

사용자가 **' 계정 구성 '** 을 클릭 하면 SaaS 서비스 웹 사이트로 리디렉션됩니다. 이러한 URL은 제안을 게시할 때 게시자에서 제공 됩니다. 이 페이지를 게시자의 방문 페이지 라고 합니다. Azure 사용자는 Azure에서 기존 AAD 자격 증명을 기반으로 SaaS 방문 페이지에 로그인 할 수 있어야 합니다.

Azure 사용자가 방문 페이지로 리디렉션되는 경우 토큰이 쿼리 URL에 추가 됩니다. 이 토큰은 수명이 짧고 24 시간 동안 유효 합니다. 그런 다음이 토큰이 있는지 검색 하 고 Microsoft의 API를 호출 하 여 토큰과 연결 된 추가 컨텍스트를 가져올 수 있습니다.

![고객 구독 흐름](media/saas-metering-service-integration-flow-a.png)

SaaS 제품의 수명 주기에서 transact-sql 시나리오를 처리 하기 위한 API 계약에 대 한 자세한 내용은 [saas 처리 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) 문서를 참조 하세요.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>사용자가 Azure에서 구독 하는 SaaS 제품을 어떻게 알 수 있나요?

`Resolve` API에 대 한 응답에는 SaaS 구독과 관련 된 제안 및 계획 정보가 포함 됩니다.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure 사용자가이 Azure 구독과 연결 된 계획을 변경 하려면 어떻게 해야 하나요?

* Azure 사용자는 SaaS 환경에서 직접 또는 Microsoft 플랫폼을 통해 SaaS 구독과 관련 된 계획을 변경할 수 있습니다.

* 변환은 언제 든 지 청구 주기에서 수행할 수 있습니다. 승인 된 후 적용 되는 모든 변환을 승인 해야 합니다.

* 선불 요금제 (**월간** 또는 **연간**) 요금은 비례 배분 됩니다. 변환 시간까지 내보내지는 모든 초과분은 다음 청구서에서 청구 됩니다. 새 계획을 기반으로 새 과잉를 내보냅니다.

>[!Note]
>특정 변환 경로를 지원 하지 않으려면 다운 그레이드을 차단할 수 있습니다.

아래 시퀀스는 Azure 고객이 SaaS 환경에서 계획을 변경 하는 경우 흐름을 캡처합니다.

![고객 계획 변경 흐름](media/saas-metering-service-integration-flow-b.png)

아래 시퀀스는 Azure 고객이 Microsoft의 storefront에서 계획을 변경 하는 경우 흐름을 캡처합니다.

![Customer storefront 계획 변경 흐름](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure 사용자가 Azure 구독과 관련 된 계획을 구독 취소 하려면 어떻게 해야 하나요?

Azure 사용자는 SaaS 환경에서 직접 또는 Microsoft 플랫폼을 통해 구매한 SaaS 제품의 구독을 취소할 수 있습니다. 사용자가 구독을 취소 한 후에는 다음 청구 주기부터 더 이상 요금이 청구 되지 않습니다.

아래 시퀀스는 Azure 고객이 SaaS 환경에서 SaaS 제품을 구독 취소 하는 경우 흐름을 캡처합니다.

![SaaS 환경의 고객 구독 취소](media/saas-metering-service-integration-flow-d.png)

아래 시퀀스는 Azure 사용자가 Microsoft의 storefront에서 구독을 취소 한 경우 흐름을 캡처합니다.

![Microsoft storefront에서 고객 구독 취소](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Marketplace 계량 서비스 api](./marketplace-metering-service-apis.md) 를 참조 하세요.
