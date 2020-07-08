---
title: Microsoft 상업적 marketplace의 SaaS 등록 Api
description: Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품을 통합 하는 데 사용할 수 있는 처리 Api를 소개 합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: dsindona
ms.openlocfilehash: 70515ca04e870fa435f8e9f46122a8e0dcb9b588
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691359"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace의 SaaS 등록 Api

SaaS 처리 Api는 Isv (독립 소프트웨어 공급 업체) 라고도 하는 게시자를 사용 하 여 Microsoft AppSource, Azure Marketplace 및 Azure Portal에서 SaaS 응용 프로그램을 게시 하 고 판매할 수 있습니다. 이러한 Api를 통해 ISV 응용 프로그램은 direct, 파트너-led (대리점) 및 현장 led와 같은 모든 상거래 사용 채널에 참여할 수 있습니다.  이러한 Api와 통합 하는 것은 파트너 센터에서 불가능 SaaS 제품을 만들고 게시 하기 위한 요구 사항입니다.

Isv는 Isv 및 Microsoft 모두에 대해 동일한 구독 상태를 유지 하기 위해 SaaS 서비스 코드에를 추가 하 여 다음 API 흐름을 구현 해야 합니다.

* 방문 페이지 흐름: Microsoft는 marketplace에서 고객이 게시자의 SaaS 제안을 구매한 것을 게시자에 게 알립니다.
* 활성화 흐름: 게시자는 새로 구매한 SaaS 계정이 게시자 쪽에서 구성 되었음을 Microsoft에 알립니다.
* 업데이트 흐름: 구매한 요금제 및/또는 구매한 사용자의 수를 변경 합니다.
* 일시 중단 및 복원 흐름: 고객의 지불 방법이 더 이상 유효 하지 않을 경우 구매한 SaaS 제품을 일시 중단 합니다. 지불 방법으로 문제가 해결 되 면 일시 중단 된 제품을 복구할 수 있습니다.
* 웹 후크 흐름: microsoft는 Microsoft 쪽에서 고객이 트리거한 SaaS 구독 변경 내용 및 취소에 대해 게시자에 게 알립니다.

구매한 SaaS 구독을 취소 하는 경우 Microsoft 쪽에서 고객이 수행할 수 있으므로 통합은 선택 사항입니다.

SaaS 처리 Api와의 올바른 통합은 다음을 확인 하는 데 중요 합니다.

* 게시자의 SaaS 제품을 구매한 최종 고객은 Microsoft에 의해 올바르게 청구 됩니다.
* 최종 고객은 Marketplace에서 구매한 SaaS 구독을 구매, 구성, 사용 및 관리 하는 데 올바른 사용자 환경을 제공 하 고 있습니다.

이러한 Api를 통해 게시자의 제안은 모든 상거래 사용 채널에 참여할 수 있습니다.

* direct
* 파트너-led (대리점, CSP)
* 필드-led

재판매인 (CSP) 시나리오에서 CSP는 최종 고객을 대신 하 여 SaaS 제안을 구매 합니다. 고객은 SaaS 제품을 사용 해야 하지만 CSP는 다음을 수행 하는 엔터티입니다.

* 고객 요금 청구
* 구독 계획 변경/구매한 사용자의 수량
* 구독 취소

이 시나리오에서는 게시자가 API 호출 흐름을 다르게 구현할 필요가 없습니다.

CSP에 대 한 자세한 내용은을 참조 하세요 https://partner.microsoft.com/en-us/licensing .

>[!Warning]
>이 API의 현재 버전은 모든 새 SaaS 제품에 사용 되는 버전 2입니다. API의 버전 1은 더 이상 사용 되지 않으며 기존 제안을 지원 하기 위해 유지 관리 되 고 있습니다.

>[!Note]
>SaaS 처리 Api는 게시자의 백 엔드 서비스에서 호출 되는 용도로만 사용 됩니다. 게시자의 웹 페이지에서 직접 Api와 통합 하는 것은 지원 되지 않습니다. 서비스 간 인증 흐름이 사용 되어야 합니다.

## <a name="next-steps"></a>다음 단계

아직 수행 하지 않은 경우 [AZURE AD 응용 프로그램 등록](./pc-saas-registration.md)에 설명 된 대로 [Azure Portal](https://ms.portal.azure.com) 에서 SaaS 응용 프로그램을 등록 합니다.  그런 다음 개발용이 인터페이스의 최신 버전인 [SaaS 처리 API 버전 2](./pc-saas-fulfillment-api-v2.md)를 사용 합니다.
