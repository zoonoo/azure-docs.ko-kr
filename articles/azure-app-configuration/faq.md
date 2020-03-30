---
title: Azure 앱 구성 FAQ
description: Azure 앱 구성에 대한 자주 묻는 질문
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348781"
---
# <a name="azure-app-configuration-faq"></a>Azure 앱 구성 FAQ

이 문서에서는 Azure 앱 구성에 대한 자주 묻는 질문에 대한 답변을 답변합니다.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>앱 구성은 Azure 키 자격 증명 모음과 어떻게 다른가요?

앱 구성을 사용하면 개발자가 응용 프로그램 설정을 관리하고 기능 가용성을 제어할 수 있습니다. 복잡한 구성 데이터로 작업하는 많은 작업을 단순화하는 것을 목표로 합니다.

앱 구성지원:

- 계층 구조 네임스페이스
- 레이블 지정
- 광범위한 쿼리
- 일괄 처리 검색
- 전문 관리 운영
- 기능 관리 사용자 인터페이스

앱 구성은 키 볼트를 보완하며, 두 가지는 대부분의 응용 프로그램 배포에서 나란히 사용해야 합니다.

## <a name="should-i-store-secrets-in-app-configuration"></a>앱 구성에 비밀을 저장해야 하나요?

앱 구성은 강화된 보안을 제공하지만 Key Vault는 여전히 응용 프로그램 비밀을 저장하는 데 가장 적합한 장소입니다. Key Vault는 하드웨어 수준 암호화, 세분화된 액세스 정책 및 인증서 회전과 같은 관리 작업을 제공합니다.

키 볼트에 저장된 비밀을 참조하는 앱 구성 값을 만들 수 있습니다. 자세한 내용은 [ASP.NET 핵심 앱의 키 볼트 참조 사용을](./use-key-vault-references-dotnet-core.md)참조하십시오.

## <a name="does-app-configuration-encrypt-my-data"></a>앱 구성이 내 데이터를 암호화합니까?

예. 앱 구성은 보유한 모든 키 값을 암호화하고 네트워크 통신을 암호화합니다. 키 이름과 레이블은 구성 데이터를 검색하기 위한 인덱스로 사용되며 암호화되지 않습니다.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>앱 구성은 Azure 앱 서비스 설정과 어떻게 다른가요?

Azure App Service를 사용하면 각 앱 서비스 인스턴스에 대한 앱 설정을 정의할 수 있습니다. 이러한 설정은 응용 프로그램 코드에 환경 변수로 전달됩니다. 원하는 경우 설정을 특정 배포 슬롯과 연결할 수 있습니다. 자세한 내용은 [앱 설정 구성을](/azure/app-service/configure-common#configure-app-settings)참조하십시오.

반대로 Azure 앱 구성을 사용하면 여러 앱 간에 공유할 수 있는 설정을 정의할 수 있습니다. 여기에는 앱 서비스에서 실행되는 앱과 다른 플랫폼이 포함됩니다. 응용 프로그램 코드는 .NET 및 Java의 구성 공급자, Azure SDK를 통해 또는 REST API를 통해 직접 이러한 설정에 액세스합니다.

앱 서비스 및 앱 구성 간에 설정을 가져오고 내보낼 수도 있습니다. 이 기능을 사용하면 기존 앱 서비스 설정에 따라 새 앱 구성 저장소를 빠르게 설정할 수 있습니다. 앱 서비스 설정에 의존하는 기존 앱과 구성을 공유할 수도 있습니다.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>앱 구성에 저장된 키와 값에 대한 크기 제한이 있습니까?

단일 키 값 항목에 대해 10KB로 제한됩니다.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>여러 환경(테스트, 스테이징, 프로덕션 등)에 대한 구성을 저장하면 어떻게 해야 합니까?

매장별 수준에서 앱 구성에 액세스할 수 있는 사람을 제어할 수 있습니다. 다른 사용 권한이 필요한 각 환경에 대해 별도의 저장소를 사용합니다. 이 방법은 최상의 보안 격리를 제공합니다.

환경 간에 보안 격리가 필요하지 않은 경우 레이블을 사용하여 구성 값을 구분할 수 있습니다. [레이블을 사용하여 다양한 환경에 대해 서로 다른 구성을 사용하도록](./howto-labels-aspnet-core.md) 설정하면 완전한 예제를 볼 수 있습니다.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>앱 구성을 사용하는 권장 방법은 무엇입니까?

[모범 사례를](./howto-best-practices.md)참조하십시오.

## <a name="how-much-does-app-configuration-cost"></a>앱 구성 비용은 얼마인가요?

가격 책정 계층에는 두 가지가 있습니다.

- 무료 계층
- 표준 계층.

표준 계층이 도입되기 전에 저장소를 만든 경우 일반 공급 시 자동으로 프리 티어로 이동합니다. 표준 등급으로 업그레이드하거나 프리 티어에 남아 있을 수 있습니다.

저장소를 표준 계층에서 프리 티어로 다운그레이드할 수 없습니다. 프리 계층에서 새 저장소를 만든 다음 해당 저장소로 구성 데이터를 가져올 수 있습니다.

## <a name="which-app-configuration-tier-should-i-use"></a>어떤 앱 구성 계층을 사용해야 합니까?

두 앱 구성 계층은 구성 설정, 기능 플래그, 키 볼트 참조, 기본 관리 작업, 메트릭 및 로그를 비롯한 핵심 기능을 제공합니다.

다음은 계층을 선택하는 데 고려해야 할 사항입니다.

- **구독당 리소스**: 리소스는 단일 구성 저장소로 구성됩니다. 각 구독은 프리 티어의 하나의 구성 저장소로 제한됩니다. 구독에는 표준 계층에 구성 저장소가 무제한으로 있을 수 있습니다.
- **리소스당 저장소:** 프리 계층에서 각 구성 저장소는 10MB의 저장소로 제한됩니다. 표준 계층에서 각 구성 저장소는 최대 1GB의 저장소를 사용할 수 있습니다.
- **키 기록**: 앱 구성은 키에 대한 모든 변경 내용의 기록을 저장합니다. 프리 티어에서 이 기록은 7일 동안 저장됩니다. 표준 계층에서 이 기록은 30일 동안 저장됩니다.
- **일일 요청**: 프리 티어 매장은 하루에 1,000개의 요청으로 제한됩니다. 저장소가 1,000개의 요청에 도달하면 자정 UTC까지 모든 요청에 대해 HTTP 상태 코드 429를 반환합니다.

    표준 티어 매장의 경우 매일 처음 200,000건의 요청이 일일 요금에 포함됩니다. 추가 요청은 초과로 청구됩니다.

- **서비스 수준 계약**: 표준 계층의 SLA는 99.9%입니다. 프리 티어에는 SLA가 없습니다.
- **보안 기능**: 두 계층 모두 Microsoft 관리 키의 암호화, HMAC 또는 Azure Active Directory를 통한 인증, RBAC 지원 및 관리되는 ID를 포함한 기본 보안 기능을 포함합니다. 표준 계층은 개인 링크 지원 및 고객 관리 키의 암호화를 비롯한 고급 보안 기능을 제공합니다.
- **비용**: 표준 계층 상점은 일일 사용 요금이 있습니다. 일일 할당을 초과한 요청에 대해 초과 요금도 부과됩니다. 프리 티어 스토어를 사용하는 데는 비용이 들지 않습니다.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>매장을 프리 티어에서 스탠다드 티어로 업그레이드할 수 있습니까? 매장을 스탠다드 티어에서 프리 티어로 다운그레이드할 수 있나요?

언제든지 프리 티어에서 스탠다드 티어로 업그레이드할 수 있습니다.

저장소를 표준 계층에서 프리 티어로 다운그레이드할 수 없습니다. 프리 계층에서 새 저장소를 만든 다음 [구성 데이터를 해당 저장소로 가져올](howto-import-export-data.md)수 있습니다.

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>앱 구성에 대한 요청 수에 제한이 있습니까?

프리 계층의 구성 저장소는 하루에 1,000개의 요청으로 제한됩니다. 표준 계층의 구성 저장소는 요청 속도가 시간당 20,000개의 요청을 초과할 때 일시적인 제한이 발생할 수 있습니다.

저장소가 한도에 도달하면 기간이 만료될 때까지 이루어진 모든 요청에 대해 HTTP 상태 코드 429를 반환합니다. 응답의 헤더는 `retry-after-ms` 요청을 다시 시도하기 전에 제안된 대기 시간(밀리초)을 제공합니다.

응용 프로그램에서 HTTP 상태 코드 429 응답이 정기적으로 발생하는 경우 요청 수를 줄이기 위해 다시 디자인하는 것이 좋습니다. 자세한 내용은 [앱 구성에 대한 요청 축소를](./howto-best-practices.md#reduce-requests-made-to-app-configuration) 참조하십시오.

## <a name="my-application-receives-http-status-code-429-responses-why"></a>내 응용 프로그램은 HTTP 상태 코드 429 응답을 받습니다. 그 이유는

다음과 같은 상황에서 HTTP 상태 코드 429 응답을 받게 됩니다.

* 프리 티어의 저장소에 대한 일일 요청 한도를 초과합니다.
* 표준 계층의 저장소에 대한 높은 요청 비율로 인해 임시 제한.
* 과도한 대역폭 사용.
* 저장소 견적을 초과할 때 키를 만들거나 수정하려고 시도합니다.

요청이 실패한 구체적인 이유에 대해 429 응답본문을 확인합니다.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>새 릴리스 및 앱 구성과 관련된 기타 정보에 대한 공지를 수신하려면 어떻게 해야 합니까?

우리의 [GitHub 공지 사항 리포지토리에](https://github.com/Azure/AppConfiguration-Announcements)가입 .

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>문제를 신고하거나 제안을 하면 어떻게 해야 하나요?

[GitHub에서](https://github.com/Azure/AppConfiguration/issues)직접 연락할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure App Configuration 정보](./overview.md)
