---
title: SaaS 이행 API - 자주 묻는 질문 | Azure 마켓플레이스
description: Azure 마켓플레이스에서 SaaS 제품의 고객이 제공하는 검색 및 구매 환경입니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275699"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS 처리 API - FAQ

Azure 고객이 SaaS 제품을 구독할 수 있도록 하는 Azure Marketplace와의 통합 요구 사항이 나열됩니다.

## <a name="discovery-experience"></a>검색 경험

제안이 게시되면 Azure 사용자는 Azure 마켓플레이스에서 SaaS 오퍼를 검색할 수 있습니다. 고객은 제품 유형(SaaS)에 따라 제품을 필터링하고 관심 있는 SaaS 서비스를 검색할 수 있습니다.

## <a name="purchase-experience"></a>구매 경험

사용자가 특정 SaaS 서비스에 관심이 있으면 사용자는 Azure Marketplace에서 구독할 수 있습니다.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure 사용자가 Azure 마켓플레이스에서 SaaS 제품을 구독한다는 것은 무엇을 의미합니까?

즉, 사용자는 SaaS 서비스와 관련된 사용 약관 및 개인 정보 취급 방침을 볼 수 있으며 Microsoft송장에 SaaS 오퍼링 게시자가 설정한 청구 조건에 따라 비용을 지불하는 데 동의할 수 있습니다. 사용자는 Azure에서 기존 지불 프로필을 사용하여 SaaS 서비스 사용량에 대한 비용을 지불할 수 있습니다.

이것은 많은 이유로 유익합니다. 이제 고객은 사용하려는 모든 ISV 소프트웨어를 검사하지 않고도 Microsoft 클라우드 플랫폼을 신뢰할 수 있는 소스로 사용하여 한 곳에서 검색하고 구독할 수 있습니다. 또한 고객은 각 ISV 소프트웨어를 독립적으로 명시적으로 지불할 필요 없이 기존 결제 프로필을 사용할 수 있습니다.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>쿠폰을 구독할 때 사용자에게 자동으로 요금이 청구되나요?

SaaS 오퍼를 구독하는 동안 사용자는 Microsoft 플랫폼을 통해 SaaS 서비스의 소비비용을 지불하기로 동의했습니다. 그러나 요금은 쿠폰이 소비된 경우에만 시작됩니다. 사용자는 SaaS 오퍼로 이동하여 오퍼 를 사용하려면 계정 생성을 확인해야 합니다. 그런 다음 이 고객 SaaS 구독에 대한 청구를 시작하도록 Microsoft에 알립니다.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>사용자가 SaaS 제품을 구독할 때 어떻게 알림을 받게 되나요?

오퍼를 구독한 후 Azure 사용자는 Azure에서 모든 오퍼를 검색하고 관리할 수 있습니다. 기본적으로 새로 구독된 SaaS 오퍼의 상태는 **'프로비저닝, 이행 보류 중'입니다.** 이 상태에서 Azure 사용자는 Azure Portal에서 SaaS 구독 관리 환경을 탐색하기 위해 **'계정 구성'에**대한 작업을 묻는 메시지가 표시됩니다.

사용자가 **'계정 구성'을**클릭하면 SaaS 서비스 웹 사이트로 리디렉션됩니다. 해당 URL이 탐색되는 URL은 오퍼를 게시할 때 게시자가 제공합니다. 이 페이지를 게시자의 방문 페이지라고 합니다. Azure 사용자는 Azure의 기존 AAD 자격 증명을 기반으로 SaaS 방문 페이지에 로그인할 수 있어야 합니다.

Azure 사용자가 방문 페이지로 리디렉션되면 토큰이 쿼리 URL에 추가됩니다. 이 토큰은 수명이 짧으며 24시간 동안 유효합니다. 그런 다음 이 토큰의 존재를 감지하고 Microsoft의 API를 호출하여 토큰과 연결된 컨텍스트를 더 많이 얻을 수 있습니다.

![고객 구독 흐름](media/saas-metering-service-integration-flow-a.png)

SaaS 제품의 수명 주기에서 거래 시나리오를 처리하기 위한 API 계약에 대한 자세한 내용은 [SaaS 주문 처리 API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) 문서를 참조하십시오.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>사용자가 Azure에서 구독하는 SaaS 오퍼를 어떻게 알 수 있습니까?

API에 `Resolve` 대한 응답에는 SaaS 구독과 관련된 제안 및 계획 정보가 포함됩니다.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure 사용자는 이 Azure 구독과 연결된 계획을 어떻게 변경할 수 있습니까?

* Azure 사용자는 SaaS 환경에서 또는 Microsoft 플랫폼을 통해 SaaS 구독과 연결된 계획을 직접 변경할 수 있습니다.

* 전환은 결제 주기에서 언제든지 수행할 수 있습니다. 귀하는 일단 인정되면 효력이 발생하는 모든 개종을 인정해야 합니다.

* 선불**플랜(월별** 또는 **연간)** 요금은 비례 배분됩니다. 전환 시점까지 내보낸 초과분은 다음 인보이스에 청구됩니다. 새 계획에 따라 새 초과분이 내보내집니다.

>[!Note]
>특정 전환 경로를 지원하지 않으려면 다운그레이드를 차단할 수 있습니다.

아래 시퀀스는 Azure 고객이 SaaS 환경에서 계획을 변경할 때 흐름을 캡처합니다.

![고객 계획 변경 흐름](media/saas-metering-service-integration-flow-b.png)

아래 순서는 Azure 고객이 Microsoft 스토어프론트에서 계획을 변경할 때 흐름을 캡처합니다.

![고객 점포 프론트 플랜 변경 흐름](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure 사용자는 Azure 구독과 연결된 요금제에서 어떻게 구독을 취소할 수 있습니까?

Azure 사용자는 구입한 SaaS 제품중 SaaS 환경에서 직접 또는 Microsoft 플랫폼을 통해 구독을 취소할 수 있습니다. 사용자가 구독을 취소하면 다음 결제 주기에서 더 이상 요금이 청구되지 않습니다.

아래 시퀀스는 Azure 고객이 SaaS 환경에서 SaaS 제품을 구독 취소할 때의 흐름을 캡처합니다.

![SaaS 경험에서 고객 구독 취소](media/saas-metering-service-integration-flow-d.png)

아래 시퀀스는 Azure 사용자가 Microsoft 스토어프론트에서 구독을 취소할 때의 흐름을 캡처합니다.

![Microsoft 스토어프론트에서 고객 구독 취소](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [마켓플레이스 계량 서비스 API를](./marketplace-metering-service-apis.md) 참조하십시오.
