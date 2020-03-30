---
title: 사용자 마이그레이션 접근 방식
titleSuffix: Azure AD B2C
description: 대량 가져오기 또는 원활한 마이그레이션 방법을 사용하여 다른 ID 공급자에서 Azure AD B2C로 사용자 계정을 마이그레이션합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332343"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>사용자를 Azure AD B2C로 마이그레이션합니다.

다른 ID 공급자에서 Azure Active Directory B2C(Azure AD B2C)로 마이그레이션하려면 기존 사용자 계정을 마이그레이션해야 할 수도 있습니다. 여기서는 *대량 가져오기* 및 원활한 *마이그레이션에*대해 설명합니다. 두 방법 중 하나를 사용하면 [Microsoft 그래프 API를](manage-user-accounts-graph-api.md) 사용하여 Azure AD B2C에서 사용자 계정을 만드는 응용 프로그램이나 스크립트를 작성해야 합니다.

## <a name="bulk-import"></a>대량 가져오기

대량 가져오기 흐름에서 마이그레이션 응용 프로그램은 각 사용자 계정에 대해 다음 단계를 수행합니다.

1. 현재 자격 증명(사용자 이름 및 암호)을 포함하여 이전 ID 공급자의 사용자 계정을 읽습니다.
1. Azure AD B2C 디렉터리에서 현재 자격 증명을 사용하여 해당 계정을 만듭니다.

다음 두 가지 경우 중 하나에서 대량 가져오기 흐름을 사용합니다.

- 사용자의 일반 텍스트 자격 증명(사용자 이름 및 암호)에 액세스할 수 있습니다.
- 자격 증명은 암호화되지만 암호를 해독할 수 있습니다.

프로그래밍 방식으로 사용자 계정을 만드는 것에 대한 자세한 내용은 [Microsoft 그래프를 사용하는 Azure AD B2C 사용자 계정 관리를](manage-user-accounts-graph-api.md)참조하십시오.

## <a name="seamless-migration"></a>원활한 마이그레이션

이전 ID 공급자의 일반 텍스트 암호에 액세스할 수 없는 경우 원활한 마이그레이션 흐름을 사용합니다. 예를 들어 다음과 같은 경우를 예로 들 수 있습니다.

- 암호는 해시 함수와 같이 단방향 암호화 형식으로 저장됩니다.
- 암호는 액세스할 수 없는 방식으로 레거시 ID 공급자에 의해 저장됩니다. 예를 들어 ID 공급자가 웹 서비스를 호출하여 자격 증명의 유효성을 검사하는 경우를 예로 들 수 있습니다.

원활한 마이그레이션 흐름에는 여전히 사용자 계정의 대량 마이그레이션이 필요하지만 [사용자 지정 정책을](custom-policy-get-started.md) 사용하여 첫 번째 로그인 시 각 사용자의 암호를 설정하기 위해 REST [API(사용자가](custom-policy-rest-api-intro.md) 만든)를 쿼리합니다.

따라서 원활한 마이그레이션 흐름에는 *대량 가져오기* 및 자격 증명 설정의 두 단계가 *있습니다.*

### <a name="phase-1-bulk-import"></a>1단계: 대량 가져오기

1. 마이그레이션 응용 프로그램은 이전 ID 공급자의 사용자 계정을 읽습니다.
1. 마이그레이션 응용 프로그램은 Azure AD B2C 디렉터리에서 해당 사용자 계정을 생성하지만 *암호를 설정하지는 않습니다.*

### <a name="phase-2-set-credentials"></a>2단계: 자격 증명 설정

계정의 대량 마이그레이션이 완료되면 사용자 지정 정책 및 REST API는 사용자가 로그인할 때 다음을 수행합니다.

1. 입력한 전자 메일 주소에 해당하는 Azure AD B2C 사용자 계정을 읽습니다.
1. 부울 확장 특성을 평가하여 계정에 마이그레이션 플래그가 지정되어 있는지 확인합니다.
    - 확장 특성이 `True`반환되는 경우 REST API를 호출하여 레거시 ID 공급자에 대한 암호의 유효성을 검사합니다.
      - REST API에서 암호가 올바르지 않은 것으로 판단되면 사용자에게 친숙한 오류를 반환합니다.
      - REST API에서 암호가 올바른지 확인하면 Azure AD B2C 계정에 암호를 쓰고 부울 `False`확장 특성을 로 변경합니다.
    - 부울 확장 특성이 `False`반환되면 정상적으로 로그인 프로세스를 계속합니다.

예제 사용자 지정 정책 및 REST API를 보려면 GitHub에서 [원활한 사용자 마이그레이션 샘플을](https://aka.ms/b2c-account-seamless-migration) 참조하세요.

![사용자 마이그레이션에 대한 원활한 마이그레이션 접근 방식의 순서도 다이어그램](./media/user-migration/diagram-01-seamless-migration.png)<br />*다이어그램: 원활한 마이그레이션 흐름*

## <a name="best-practices"></a>모범 사례

### <a name="security"></a>보안

원활한 마이그레이션 방법은 사용자 지정 REST API를 사용하여 레거시 ID 공급자에 대해 사용자의 자격 증명을 검증합니다.

**무차별 암호 대입 공격으로부터 REST API를 보호해야 합니다.** 공격자는 결국 사용자의 자격 증명을 추측하기 위해 여러 암호를 제출할 수 있습니다. 이러한 공격을 무효화하려면 로그인 시도 횟수가 특정 임계값을 초과하면 REST API에 대한 요청 제공을 중지합니다. 또한 Azure AD B2C와 REST API 간의 통신을 보호합니다. 프로덕션을 위해 RESTful API를 보호하는 방법에 대해 알아보려면 [보안 RESTful API를](secure-rest-api.md)참조하십시오.

### <a name="user-attributes"></a>사용자 특성

레거시 ID 공급자의 모든 정보를 Azure AD B2C 디렉터리로 마이그레이션할 수 없습니다. 마이그레이션하기 전에 Azure AD B2C에 저장할 적절한 사용자 특성 집합을 식별합니다.

- **Azure** AD B2C에 저장
  - 사용자 이름, 암호, 이메일 주소, 전화 번호, 회원 번호 / 식별자.
  - 개인 정보 보호 정책 및 최종 사용자 라이선스 계약에 대한 동의 마커입니다.
- Azure AD B2C에 **저장하지 마십시오.**
  - 신용 카드 번호, 사회 보장 번호(SSN), 의료 기록 또는 정부 또는 업계 규정 준수 기관에서 규제하는 기타 데이터와 같은 중요한 데이터.
  - 마케팅 또는 커뮤니케이션 기본 설정, 사용자 행동 및 인사이트.

### <a name="directory-clean-up"></a>디렉터리 정리

마이그레이션 프로세스를 시작하기 전에 디렉터리를 정리할 수 있습니다.

- Azure AD B2C에 저장할 사용자 특성 집합을 식별하고 필요한 항목만 마이그레이션합니다. 필요한 경우 사용자 [지정 특성을](custom-policy-custom-attributes.md) 만들어 사용자에 대한 더 많은 데이터를 저장할 수 있습니다.
- 인증 소스가 여러 개인 환경에서 마이그레이션하는 경우(예: 각 응용 프로그램에 자체 사용자 디렉터리 있음) Azure AD B2C의 통합 계정으로 마이그레이션합니다.
- 여러 응용 프로그램에 다른 사용자 이름이 있는 경우 ID 컬렉션을 사용 하 여 Azure AD B2C 사용자 계정에 모든 응용 프로그램을 저장할 수 있습니다. 암호와 관련하여 사용자가 암호를 선택하고 디렉터리에서 설정하도록 합니다. 예를 들어 원활한 마이그레이션을 사용하면 선택한 암호만 Azure AD B2C 계정에 저장해야 합니다.
- 마이그레이션하기 전에 사용하지 않은 사용자 계정을 제거하거나 오래된 계정을 마이그레이션하지 마십시오.

### <a name="password-policy"></a>암호 정책

마이그레이션하려는 계정이 Azure AD B2C에서 적용하는 [강력한 암호 강도보다](../active-directory/authentication/concept-sspr-policy.md) 암호 강도가 약한 경우 강력한 암호 요구 사항을 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [암호 정책 속성을](manage-user-accounts-graph-api.md#password-policy-property)참조하십시오.

## <a name="next-steps"></a>다음 단계

GitHub의 [Azure-ad-b2c/사용자 마이그레이션](https://github.com/azure-ad-b2c/user-migration) 리포지토리에는 원활한 마이그레이션 사용자 지정 정책 예제및 REST API 코드 샘플이 포함되어 있습니다.

[원활한 사용자 마이그레이션 사용자 지정 정책 & REST API 코드 샘플](https://aka.ms/b2c-account-seamless-migration)
