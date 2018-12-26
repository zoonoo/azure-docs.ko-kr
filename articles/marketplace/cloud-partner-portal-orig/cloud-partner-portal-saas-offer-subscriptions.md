---
title: SaaS - Azure를 통한 판매 | Microsoft Docs
description: SaaS 응용 프로그램의 구독 청구 모델 및 구현 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808095"
---
<a name="saas---sell-through-azure"></a>SaaS - Azure를 통한 판매
=========================

이 문서에서는 SaaS 응용 프로그램의 구독 청구 모델과 Cloud 파트너 포털에서 구현하는 방법을 설명합니다.


<a name="overview"></a>개요
--------

Azure Marketplace에서 SaaS 제안을 게시하고 수익을 거둘 수 있습니다. 고객에게는 이제 Azure 청구를 통해 비용이 직접 부과될 수 있으며, 활성화한 모든 리소스 및 서비스에 대한 단일 청구서가 Azure로부터 제공됩니다. 게시할 경우 SaaS 구독이 있으면 다음과 같은 이점이 있습니다.

-   **일관된 게시 환경** -- Azure Marketplace에서 SaaS 제안 또는 다른 제안을 이미 게시했으면 동일한 게시 포털을 통해 게시를 진행할 수 있습니다.
-   **검색을 위한 새 상점** - 게시된 모든 제안은 Azure Portal 내의 Azure Marketplace 확장뿐만 아니라 외부 Azure Marketplace 상점에도 표시됩니다.
-   **통합 청구** - 이제 Azure가 판매하는 모든 지역에서 판매할 수 있으며 Azure에서 대금 청구를 관리해줍니다.
-   **통합된 잠재 고객 창출** - 이제 Azure 사용자가 Azure Marketplace를 사용하여 SaaS 서비스를 구독할 때마다 원하는 CRM에서 Azure의 잠재 고객을 자동으로 받을 수 있습니다.
-   **Microsoft 판매자와 공동 판매** -- 양방향 리드 공유, 카탈로그 우선 목록, 그리고 Microsoft 판매자와 공동으로 진행되는 거래 참여의 자격과 기회를 얻을 수 있습니다.

<a name="billing-models"></a>청구 모델
--------------

현재 지원되는 유일한 청구 모델은 SaaS 서비스의 구독당 월별 고정 요금입니다.

**참고:** 향후 추가 청구 모델을 사용할 수 있습니다.

각 SaaS 제안에는 하나 이상의 요금제가 적용될 수 있습니다(예: Basic 또는 Premium). 각 요금제에는 가격과 기본 메타데이터가 관련되어 있습니다.

사용자가 요금제 정의를 완전히 제어할 수 있습니다. 예를 들어, Basic 요금제 구성 방식을 제어할 수 있습니다. 요금제는 사용자가 정의하며, 요금제를 게시할 때 해당 설명 텍스트를 제공할 수 있습니다.

요금제와 관련된 가격은 Azure 사용자가 서비스 사용을 위해 지불하는 월별 고정 요금입니다.

### <a name="what-is-not-supported-today"></a>현재 지원되지 않는 방식은 어느 것인가요?

-   사용자 지정 단위 기준 청구 - 요청 수에 따른 고정 가격을 예로 들 수 있습니다.
-   사용자 수 할당 기준 청구 - Azure 사용자가 사용자 수에 따라 라이선스를 구입할 수 있도록 하는 경우가 여기에 해당합니다.

<a name="end-to-end-flow"></a>종단 간 흐름
---------------

최종 사용자 관점에서 바라본 SaaS 구독 제안 흐름입니다.

**참고:** 이 흐름 설명에서는 ‘Contoso 데모 SaaS’라는 SaaS 제안을 Azure Marketplace에 게시했다고 가정합니다.

![SaaS 구독 사용자 흐름](media/saas-offer-subscription/saas-subscription-user-flow.png)

Azure 사용자는 SaaS 서비스와 다음과 같이 상호 작용할 수 있습니다.

-   Azure Marketplace에서 SaaS 서비스 검색
-   Azure에서 SaaS 서비스 구독
-   Azure Portal에서 SaaS 서비스로 이동
-   SaaS 서비스에서 계정 생성 및 SaaS에서 계정 관리(요금제 변경/삭제)
-   Azure Portal에서 SaaS 서비스 구독 취소

<a name="discover-your-saas-service-in-azure-marketplace"></a>Azure Marketplace에서 SaaS 서비스 검색
-----------------------------------------------

사용자가 Azure Portal에서 Azure Marketplace를 시작하면 'SaaS(Software as a service)'라는 범주가 표시됩니다.

![범주 메뉴를 사용하여 SaaS 검색](media/saas-offer-subscription/saas-category-menu.png)

SaaS 서비스를 검색할 수도 있습니다.

![검색을 사용하여 SaaS 검색](media/saas-offer-subscription/saas-cpp-search.png)

사용자는 서비스의 세부 정보를 보고 **만들기**를 클릭할 수 있습니다.

![SaaS 구독 만들기](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>Azure에서 SaaS 서비스 구독

그런 후 사용자는 Azure에서 SaaS 서비스를 구독할 수 있습니다.

![SaaS 서비스 구독](media/saas-offer-subscription/saas-subscribe-signup.png)

사용자는 SaaS 서비스를 구독하는 경우 다음 정보를 제공합니다.

-   이름 - 사용자가 Azure에서 이 SaaS 구독 인스턴스를 검색하거나 관리할 수 있는 이름입니다.
-   구독 - SaaS 서비스에 대한 청구를 연결하려는 구독 컨텍스트입니다.
-   요금제 - 구독하려는 SaaS 서비스 요금제입니다.

제안 게시의 일부로 제공되는 사용 약관 문서도 사용자가 SaaS 서비스를 구독하기 전에 사용자에게 표시됩니다.

이제 **구독**을 클릭하여 서비스를 구독할 수 있습니다.
Azure에서는 구독이 완료된 후에 포털에 알림을 보냅니다.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Azure Portal에서 SaaS 서비스로 이동

그런 후 **리소스로 이동**을 클릭하여 해당 SaaS 구독 인스턴스를 보거나 관리합니다.

![SaaS 인스턴스 보기 또는 관리](media/saas-offer-subscription/saas-go-to-resource.png)

사용자는 먼저 SaaS 서비스에서 해당 계정을 구성해야 한다는 알림을 받습니다. SaaS 서비스에는 청구 시작을 Azure에 알린 후, 사용자가 SaaS 서비스 사이트에서 계정을 만들면 해당 요금이 청구되기 시작합니다.

![SaaS 인스턴스 구성](media/saas-offer-subscription/saas-configure-account.png)

사용자는 **계정 구성**을 클릭하면 SaaS 서비스 엔드포인트로 리디렉션됩니다. 이 리디렉션 동안 토큰이 쿼리 매개 변수로 전달됩니다. 예: 

![SaaS 계정 토큰](media/saas-offer-subscription/saas-account-token.png)

이 토큰 값을 기록해 둡니다. 이 토큰은 일시적으로 존재하며 Azure에서 구독 식별자를 가져오기 위해 확인되어야 합니다.

<a name="creating-a-saas-offer-subscription"></a>SaaS 제안 구독 만들기
----------------------------------

이러한 환경을 구축하기 위해 다음 두 가지 작업이 필요합니다.

-   SaaS 서비스 웹 사이트를 Microsoft SaaS API에 연결합니다. 이 문서 [Azure를 통해 SaaS 판매 - API](./cloud-partner-portal-saas-subscription-apis.md)에서 이 연결을 구축하는 방법을 설명합니다.  
-   Cloud 파트너 포털의 **기술 정보** 섹션에서 **Sell through Azure**(Azure를 통해 판매)를 사용하도록 설정하고 모든 구성 세부 정보를 제공합니다.

![새 SaaS 제안 기술 정보](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

다음은 **기술 정보** 섹션의 모든 필수 필드에 대한 설명입니다.

|  **제안 필드**                 |  **설명**                                   |
|  ----------------                 |  -------------------------------------             |
| 미리 보기 구독 ID          | 제안이 공개적으로 사용 가능해지기 전에 미리 보기에서 제안을 테스트하는 데 사용되는 모든 Azure 구독 식별자입니다. |
| 시작하기 지침      | SaaS 앱에 연결하도록 지원하기 위해 고객과 공유하는 지침입니다. 기본 HTML 사용(예: `<p>`, `<h1>`, `<li>` 등의 태그)이 허용됩니다.  |
| 방문 페이지 URL                  | Azure Portal로부터 획득한 후 고객이 방문하도록 설정하는 사이트 URL입니다. 이 URL은 Microsoft와의 거래를 용이하게 하기 위한 연결 API를 수신할 엔드포인트로도 사용됩니다.  |
| 연결 웹후크                | Microsoft가 고객을 대신하여 사용자에게 보내야 하는 모든 비동기 이벤트(예: Azure 구독이 무효화됨)에 대해 연결 웹후크를 제공하도록 요구합니다. 웹후크 시스템이 아직 준비되지 않은 경우 가장 단순한 구성은 게시되는 모든 이벤트를 수신 대기한 후 적절히 처리하는 HTTP 엔드포인트 논리 앱을 유지하는 것입니다.  자세한 내용은 [Logic Apps의 HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)을 참조하세요. |
| Azure AD 테넌트 ID 및 앱 ID     | 사용자는 두 서비스 간 연결이 인증된 통신을 통해 설정되는지 검증할 수 있도록 Azure Portal 내에서 Active Directory 앱을 만들어야 합니다. 이러한 필드에 대해, AD 앱을 만들고, 필요한 해당 테넌트 ID 및 앱 ID를 붙여 넣습니다. |
|  |  |


마지막으로, **Sell through Azure**(Azure를 통해 판매)를 선택하는 경우 **Plans**(요금제)라는 섹션이 추가됩니다. 요금제는 Sell through Azure(Azure를 통해 판매)를 선택한 경우에만 필요합니다. 이 섹션에서는 SaaS 앱이 지원하는 특정 요금제 및 해당 가격을 나열합니다. 현재, 1개월 또는 3개월 동안 무료 액세스를 허용하는 월별 가격 책정을 사용하고 있습니다. 이러한 요금제 및 가격은 사용자 고유의 SaaS 앱 사이트의 정확한 요금제 및 가격과 일치해야 합니다.
