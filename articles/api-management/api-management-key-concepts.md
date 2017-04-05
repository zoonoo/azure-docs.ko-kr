---
title: "Azure API Management 개요 및 키 개념 | Microsoft Docs"
description: "API, 제품, 역할, 그룹 및 기타 API 관리의 주요 개념에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: e71da405-835a-48f3-956f-45c1a85698d7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: 47358c6c209488d7a12e8afbf7a2d9b3f872f0de
ms.lasthandoff: 01/31/2017

---
# <a name="what-is-api-management"></a>API 관리란?
API 관리를 사용하여 조직은 외부, 파트너 및 내부의 개발자에게 API를 게시하여 데이터 및 서비스의 잠재성을 활용할 수 있습니다. 모든 곳의 비즈니스는 디지털 플랫폼으로 운영을 확장함으로써 새로운 채널을 생성하고, 새로운 고객을 찾고, 기존 고객과 더 깊은 관계를 구축하고자 합니다. API 관리는 개발자 참여, 비즈니스 통찰력, 분석, 보안과 보호 등을 통해 성공적인 API 프로그램을 보장하는 핵심적인 역량을 제공합니다.

Azure API 관리 개요에 관한 다음 비디오를 시청하고 최소한의 작업으로 액세스 제어, 속도 제한, 모니터링, 이벤트 로깅, 응답 캐싱 등 다양한 기능을 API에 추가하기 위해 API 관리를 사용하는 방법을 알아봅니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-API-Management-Overview/player]
> 
> 

API 관리를 사용하려면 관리자가 API를 만듭니다. 각 API는 하나 이상의 작업으로 구성되며, 각 API는 하나 이상의 제품에 추가할 수 있습니다. API를 사용하려면 개발자는 해당 API가 포함된 제품을 구독합니다. 그런 다음 적용 중인 사용 정책에 따라 API의 작업을 호출할 수 있습니다.

이 항목에서는 API 관리의 주요 개념을 간략하게 설명합니다.

> [!NOTE]
> 자세한 내용은 [클라우드 기반 API 관리: API 기능을 활용](http://j.mp/ms-apim-whitepaper) PDF 백서를 참조하세요. CITO Research의 API 관리에 대한 이 소개 백서는 다음을 설명합니다. 
> 
> * 공통 API 요구 사항 및 특징
> * API 분리 및 외관 제공
> * 개발자가 시작 및 신속하게 실행
> * 액세스 보안
> * 분석 및 메트릭
> * API 관리 플랫폼으로 제어 및 파악
> * 클라우드 대 온-프레미스 솔루션 사용
> * Azure API 관리
> 
> 

## <a name="apis"> </a>API 및 작업
API는 API 관리 서비스 인스턴스의 기반입니다. 각 API는 개발자가 사용할 수 있는 작업 집합을 나타냅니다. 각 API에는 API를 구현하는 백 엔드 서비스에 대한 참조가 포함되어 있으며, 해당 작업은 백 엔드 서비스에 의해 구현되는 작업에 매핑됩니다. API 관리의 작업은 매우 다양하게 구성할 수 있으며 URL 매핑, 쿼리 및 경로 매개 변수, 요청 및 응답 콘텐츠, 작업 응답 캐싱 등을 더 효율적으로 제어할 수 있습니다. 속도 제한, 할당량 및 IP 제한 정책 또한 API 또는 개별 작업 수준에서 구현할 수 있습니다.

자세한 내용은 [API를 만드는 방법][How to create APIs] 및 [API에 작업을 추가하는 방법][How to add operations to an API]을 참조하세요.

## <a name="products"> </a> 제품
제품은 API가 개발자에게 표시되는 방식입니다. API 관리에서 제품은 하나 이상의 API를 가지며 제목, 설명, 사용 약관 등으로 구성됩니다. 제품은 **개방형** 또는 **보호된** 제품일 수 있습니다. 보호된 제품은 사용하기 전에 구독해야 하는 반면, 개방형 제품은 구독하지 않고 사용할 수 있습니다. 제품을 개발자가 사용할 수 있게 되면 제품을 게시할 수 있습니다. 게시되고 나면 개발자가 볼 수 있습니다(보호된 제품의 경우 구독할 수 있음). 구독 승인은 제품 수준에서 구성되며 관리자 승인을 요구하거나 자동 승인될 수 있습니다.

그룹은 제품을 개발자에게 표시하는 옵션을 관리하는 데 사용됩니다. 제품은 그룹에 대한 표시 여부를 부여하고, 개발자는 자신이 속한 그룹에게 표시되는 제품을 보고 구독할 수 있습니다. 

자세한 내용은 [제품을 만들고 게시하는 방법][How to create and publish a product] 및 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="groups"> </a> 그룹
그룹은 제품을 개발자에게 표시하는 옵션을 관리하는 데 사용됩니다. API 관리에는 다음과 같은 변경할 수 없는 시스템 그룹이 있습니다.

* **관리자** - Azure 구독 관리자가 이 그룹의 구성원입니다. 관리자는 API 관리 서비스 인스턴스를 관리하며 개발자가 사용하는 API, 작업 및 제품을 만듭니다.
* **개발자** - 인증된 개발자 포털 사용자가 이 그룹에 속합니다. 개발자는 API를 사용하여 응용 프로그램을 빌드하는 고객입니다. 개발자는 개발자 포털에 액세스할 수 있는 권한을 받으며 API의 작업을 호출하는 응용 프로그램을 빌드합니다.
* **게스트** - API 관리 인스턴스의 개발자 포털을 방문하는 인증되지 않은 개발자 포털 사용자(예: 잠재 고객)가 이 그룹에 속합니다. 예를 들어 API를 볼 수 있지만 호출할 수는 없는 기능과 같이 특정 읽기 전용 액세스 권한을 받을 수 있습니다.

이러한 시스템 그룹 외에도 관리자는 사용자 지정 그룹을 만들거나 [연관된 Azure Active Directory 테넌트에서 외부 그룹을 가져올 수 있습니다](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). 사용자 지정 및 외부 그룹은 시스템 그룹과 함께 사용되어 개발자에게 API 제품에 대한 표시 여부 및 액세스를 제공합니다. 예를 들어, 특정 파트너 조직과 관련된 개발자를 위한 하나의 사용자 지정 그룹을 만들고 관련 API만을 포함한 제품에서 API에 대한 액세스를 허용합니다. 사용자는 두 그룹 이상의 구성원이 될 수 있습니다.

자세한 내용은 [그룹을 만들고 사용하는 방법][How to create and use groups]을 참조하세요.

## <a name="developers"> </a> 개발자
개발자는 API 관리 서비스 인스턴스의 사용자 계정을 나타냅니다. 개발자는 관리자가 만들거나 참여하도록 초대할 수 있으며 [개발자 포털][Developer portal]에서 등록할 수도 있습니다. 각 개발자는 하나 이상의 그룹의 구성원이며 해당 그룹에 대한 가시성을 부여하는 제품을 구독할 수 있습니다.

개발자가 제품을 구독하면 제품의 기본 키 및 보조 키를 부여받습니다. 이 키는 제품의 API를 호출할 때 사용됩니다.

자세한 내용은 [개발자를 만들거나 초대하는 방법][How to create or invite developers] 및 [그룹을 개발자와 연결하는 방법][How to associate groups with developers]을 참조하세요.

## <a name="policies"> </a> 정책
정책은 게시자가 구성을 통해 API의 동작을 변경할 수 있게 하는 API 관리의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 많이 사용되는 명령문에는 XML에서 JSON으로 형식 변환, 개발자로부터 들어오는 호출의 양을 제한하는 호출 속도 한도 등이 포함되며 다양한 다른 정책도 사용할 수 있습니다.

정책이 다르게 지정하지 않는 한 정책 식은 어떤 API 관리 정책에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다. [제어 흐름](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) 및 [변수 설정](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) 정책 등의 일부 정책은 정책 식을 기반으로 합니다. 자세한 내용은 [고급 정책](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies) 및 [정책 식](https://msdn.microsoft.com/library/azure/dn910913.aspx)을 참조하고 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

API Management 정책의 전체 목록을 보려면 [정책 참조][Policy reference]를 참조하세요. 정책 사용 및 구성에 대한 자세한 내용은 [API Management 정책][API Management policies]을 참조하세요. 속도 제한 및 할당량 정책을 사용하여 제품을 만드는 방법에 대한 자습서는 [고급 제품 설정을 만들고 구성하는 방법][How create and configure advanced product settings]을 참조하세요. 데모는 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="developer-portal"> </a> 개발자 포털
개발자 포털은 개발자가 API에 대해 알아보고, 작업을 보고 호출하고, 제품을 구독할 수 있는 곳입니다. 잠재 고객은 개발자 포털을 방문하고 API 및 작업을 보고 등록할 수 있습니다. 개발자 포털의 URL은 Azure 클래식 포털에서 해당 API 관리 서비스 인스턴스에 대한 대시보드에서 찾을 수 있습니다.

사용자 지정 콘텐츠를 추가하고, 스타일을 사용자 지정하고, 브랜딩을 추가하여 개발자 포털의 모양을 사용자 지정할 수 있습니다.

## <a name="api-management-and-the-api-economy"></a>API 관리 및 API 경제
API 관리에 대해 자세히 알아보려면 Microsoft Ignite 2015 conference에서 다음 프레젠테이션을 시청하세요.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3708/player]
> 
> 

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: api-management-howto-product-with-rules.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance





