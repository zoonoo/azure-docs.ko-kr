---
title: Azure API Management의 구독 | Microsoft Docs
description: Azure API Management의 구독 개념에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621761"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API Management의 구독

구독은 Azure API Management(APIM)에서 중요한 개념입니다. 구독은 API 소비자가 APIM 인스턴스를 통해 게시된 API에 대한 액세스 권한을 획득하는 가장 일반적인 방법입니다. 이 문서는 개념에 대한 개요를 제공합니다.

## <a name="what-is-subscriptions"></a>구독의 정의

APIM 통해 API를 게시할 때 이러한 API에 대한 액세스를 보호하는 가장 쉽고 일반적인 방법은 구독 키를 사용하는 것입니다. 즉, 게시된 API를 사용해야 하는 개발자는 이러한 API에 호출 시 HTTP 요청에 유효한 구독 키를 포함해야 합니다. 구독 키가 없으면 호출은 APIM 게이트웨이에서 즉시 거부되고 백엔드 서비스로 전달되지 않습니다.

API에 액세스하기 위해 구독 키를 얻으려면 구독이 필요합니다. 구독은 일반적으로 구독 키 쌍의 이름을 지정한 컨테이너입니다. 구독은 API 게시자의 승인 유무와 상관 없이 게시된 API를 사용해야 하는 개발자가 얻을 수 있습니다. 또한 API 게시자는 API 소비자를 대신해 직접 구독을 만들 수도 있습니다.

> [!TIP]
> 또한 APIM은 [OAuth2.0](api-management-howto-protect-backend-with-aad.md), [클라이언트 인증서](api-management-howto-mutual-certificates-for-clients.md) 및 [IP 허용 목록](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)을 포함해 API에 대한 액세스를 보호하기 위한 기타 메커니즘도 지원합니다.

## <a name="scope-of-subscriptions"></a>구독 범위

구독은 다양한 범위, 즉 제품, 모든 API 또는 개별 API와 연결할 수 있습니다.

### <a name="subscriptions-for-a-product"></a>제품에 대한 범위

일반적으로 APIM의 구독은 항상 단일 [API 제품](api-management-terminology.md) 범위와 연결되어 있습니다. 개발자는 개발자 포털에서 제품 목록을 찾은 후 사용하려는 제품에 대한 구독 요청을 제출합니다. 구독 요청이 승인되면(자동으로 또는 API 게시자를 통해) 개발자는 포함된 키를 사용하여 제품의 모든 API에 게시할 수 있습니다.

![제품 구독](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 특정 시나리오에서는 API 게시자가 구독 요건 없이 API 제품을 대중에 공개하길 원할 수 있습니다. Azure Portal에서 제품의 **설정** 페이지에서 **구독 필요** 옵션을 선택 취소할 수 있습니다. 결과적으로, 제품의 모든 API에 API 키 없이 액세스할 수 있습니다.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>모든 API 또는 개별 API에 대한 구독

> [!NOTE]
> 현재 이 기능은 API Management Consumption 계층에서만 사용 가능합니다.

APIM의 [소비](https://aka.ms/apimconsumptionblog) 계층을 도입했을 때, 키 관리를 간소화하기 위해 몇 가지 변경 사항을 적용했습니다. 첫째, 구독 범위를 두 개 더 추가했습니다(모든 API와 단일 API). 구독의 범위는 더 이상 API 제품으로 한정되지 않습니다. 이제 제품을 만들고 먼저 API에 추가하지 않아도 API(또는 APIM 인스턴스 내의 모든 API)에 액세스 권한을 부여하는 키를 만들 수 있습니다. 또한 각 APIM 인스턴스에는 이제 변경 불가능한 모든 API 구독이 기본 제공되어 테스트 콘솔 내에서 훨씬 쉽고 간편하게 API를 테스트하고 디버깅할 수 있습니다.

둘째, APIM는 이제 "독립 실행형" 구독을 허용합니다. 개발자 계정과 연결하는 데 더 이상 구독이 필요하지 않습니다. 구독을 여러 개발자 또는 팀에서 공유하는 시나리오에서는 유용합니다.

마지막으로, 이제 API 게시자가 Azure Portal에서 직접 [구독을 생성](api-management-howto-create-subscriptions.md)할 수 있습니다.

![유연성 있는 구독](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>다음 단계
API Management에 대한 자세한 내용:

+ API Management의 기타 [개념](api-management-terminology.md)에 대해 알아보기
+ [자습서](import-and-publish.md)에 따라 API Management에 대해 자세히 알아보기
+ 일반적인 질문은 [FAQ 페이지](api-management-faq.md) 확인