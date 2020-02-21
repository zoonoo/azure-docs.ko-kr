---
title: 사용자 마이그레이션 방법
titleSuffix: Azure AD B2C
description: 대량 가져오기 또는 원활한 마이그레이션 방법을 사용 하 여 다른 id 공급자의 사용자 계정을 Azure AD B2C로 마이그레이션합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 759379d99bbd422c43998997be1d52ecbf34966d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484294"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>사용자를 Azure AD B2C으로 마이그레이션

다른 id 공급자에서 Azure Active Directory B2C (Azure AD B2C)로 마이그레이션하려면 기존 사용자 계정을 마이그레이션해야 할 수도 있습니다. 여기서는 *대량 가져오기* 및 *원활한 마이그레이션*이라는 두 가지 마이그레이션 방법을 설명 합니다. 어느 방법을 사용 하 든 [MICROSOFT GRAPH API](manage-user-accounts-graph-api.md) 를 사용 하 여 Azure AD B2C에서 사용자 계정을 만드는 응용 프로그램이 나 스크립트를 작성 해야 합니다.

## <a name="bulk-import"></a>대량 가져오기

대량 가져오기 흐름에서 마이그레이션 응용 프로그램은 각 사용자 계정에 대해 다음 단계를 수행 합니다.

1. 현재 자격 증명 (사용자 이름 및 암호)을 포함 하 여 이전 id 공급자에서 사용자 계정을 읽습니다.
1. 현재 자격 증명을 사용 하 여 Azure AD B2C 디렉터리에 해당 계정을 만듭니다.

다음 두 가지 상황 중 하나에서 대량 가져오기 흐름을 사용 합니다.

- 사용자의 일반 텍스트 자격 증명 (사용자 이름 및 암호)에 액세스할 수 있습니다.
- 자격 증명은 암호화 되지만 암호를 해독할 수 있습니다.

프로그래밍 방식으로 사용자 계정을 만드는 방법에 대 한 자세한 내용은 [Microsoft Graph를 사용 하 여 Azure AD B2C 사용자 계정 관리](manage-user-accounts-graph-api.md)를 참조 하세요.

## <a name="seamless-migration"></a>원활한 마이그레이션

이전 id 공급자의 일반 텍스트 암호에 액세스할 수 없는 경우 원활한 마이그레이션 흐름을 사용 합니다. 예를 들면 다음과 같습니다.

- 암호는 해시 함수를 사용 하는 경우와 같이 단방향으로 암호화 된 형식으로 저장 됩니다.
- 암호는 액세스할 수 없는 방식으로 레거시 id 공급자에 의해 저장 됩니다. 예를 들어 id 공급자가 웹 서비스를 호출 하 여 자격 증명의 유효성을 검사 하는 경우

원활한 마이그레이션 흐름은 사용자 계정에 대 한 대량 마이그레이션은 여전히 필요 하지만 사용자 [지정 정책을](restful-technical-profile.md) 사용 하 여 처음 로그인 할 때 각 사용자의 암호를 설정 하는 [REST API](rest-api-claims-exchange-dotnet.md) (사용자가 만드는)를 쿼리 합니다.

따라서 원활한 마이그레이션 흐름에는 *대량 가져오기* 및 *자격 증명 설정*의 두 단계가 있습니다.

### <a name="phase-1-bulk-import"></a>1 단계: 대량 가져오기

1. 마이그레이션 응용 프로그램은 이전 id 공급자에서 사용자 계정을 읽습니다.
1. 마이그레이션 응용 프로그램은 Azure AD B2C 디렉터리에 해당 사용자 계정을 만들지만 암호를 *설정 하지*는 않습니다.

### <a name="phase-2-set-credentials"></a>2 단계: 자격 증명 설정

계정에 대 한 대량 마이그레이션이 완료 되 면 사용자 지정 정책 및 REST API 사용자가 로그인 할 때 다음을 수행 합니다.

1. 입력 한 전자 메일 주소에 해당 하는 Azure AD B2C 사용자 계정을 읽습니다.
1. 부울 확장 특성을 평가 하 여 계정에 마이그레이션에 대 한 플래그가 지정 되었는지 여부를 확인 합니다.
    - 확장 특성이 `True`을 반환 하는 경우 REST API를 호출 하 여 레거시 id 공급자에 대해 암호의 유효성을 검사 합니다.
      - REST API 암호가 잘못 된 것으로 확인 되 면 사용자에 게 친숙 한 오류를 반환 합니다.
      - REST API 암호가 올바른지 확인 하는 경우 Azure AD B2C 계정에 암호를 쓰고 부울 확장 특성을 `False`으로 변경 합니다.
    - 부울 확장 특성이 `False`을 반환 하는 경우 정상적으로 로그인 프로세스를 계속 합니다.

사용자 지정 정책 및 REST API 예제를 보려면 GitHub의 [원활한 사용자 마이그레이션 샘플](https://aka.ms/b2c-account-seamless-migration) 을 참조 하세요.

사용자 마이그레이션에 대 한 원활한 마이그레이션 방법의 순서도 다이어그램을 ![](./media/user-migration/diagram-01-seamless-migration.png)<br />*다이어그램: 원활한 마이그레이션 흐름*

## <a name="best-practices"></a>모범 사례

### <a name="security"></a>보안

원활한 마이그레이션 방법은 사용자 지정 REST API를 사용 하 여 레거시 id 공급자에 대 한 사용자 자격 증명의 유효성을 검사 합니다.

**무차별 암호 대입 공격에 대해 REST API를 보호 해야 합니다.** 공격자는 궁극적으로 사용자의 자격 증명을 추측 하기 위해 여러 암호를 제출할 수 있습니다. 이러한 공격을 해결 하려면 로그인 시도 횟수가 특정 임계값을 전달할 때 REST API에 대 한 요청 처리를 중지 합니다. 또한 [클라이언트 인증서](secure-rest-api-dotnet-certificate-auth.md)를 사용 하 여 Azure AD B2C와 REST API 간의 통신을 보호 합니다.

### <a name="user-attributes"></a>사용자 특성

레거시 id 공급자의 모든 정보를 Azure AD B2C 디렉터리로 마이그레이션해야 하는 것은 아닙니다. 마이그레이션하기 전에 Azure AD B2C에 저장할 적절 한 사용자 특성 집합을 식별 합니다.

- Azure AD B2C **에 저장**
  - 사용자 이름, 암호, 전자 메일 주소, 전화 번호, 회원 번호/식별자입니다.
  - 개인 정보 취급 방침 및 최종 사용자 사용권 계약에 대 한 동의 표식.
- Azure AD B2C에 저장 안 **함**
  - 신용 카드 번호, SSN (주민 등록 번호), 의료 레코드 또는 정부 또는 업계 규정 준수 본문이 규정 하는 기타 데이터와 같은 중요 한 데이터입니다.
  - 마케팅 또는 통신 기본 설정, 사용자 동작 및 정보입니다.

### <a name="directory-clean-up"></a>디렉터리 정리

마이그레이션 프로세스를 시작 하기 전에 디렉터리를 정리할 수 있는 기회를 얻을 수 있습니다.

- Azure AD B2C에 저장할 사용자 특성 집합을 확인 하 고 필요한 항목만 마이그레이션합니다. 필요한 경우 사용자에 대 한 추가 데이터를 저장 하는 [사용자 지정 특성](custom-policy-custom-attributes.md) 을 만들 수 있습니다.
- 여러 인증 소스가 있는 환경에서 마이그레이션하는 경우 (예: 각 응용 프로그램에 고유한 사용자 디렉터리가 있는 경우) Azure AD B2C의 통합 계정으로 마이그레이션합니다.
- 여러 응용 프로그램에 다른 사용자 이름이 있는 경우 id 컬렉션을 사용 하 여 모든 응용 프로그램을 Azure AD B2C 사용자 계정에 저장할 수 있습니다. 암호와 관련 하 여 사용자가 암호를 선택 하 고 디렉터리에 설정 하도록 합니다. 예를 들어 원활한 마이그레이션에서는 선택한 암호만 Azure AD B2C 계정에 저장 해야 합니다.
- 마이그레이션 전에 사용 하지 않는 사용자 계정을 제거 하거나 오래 된 계정을 마이그레이션하지 마십시오.

### <a name="password-policy"></a>암호 정책

마이그레이션하는 계정이 Azure AD B2C에 의해 적용 되는 [강력한 암호](../active-directory/authentication/concept-sspr-policy.md) 강도 보다 약한 암호 강도의 경우 강력한 암호 요구 사항을 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [암호 정책 속성](manage-user-accounts-graph-api.md#password-policy-property)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

GitHub의 [b2c/사용자 마이그레이션](https://github.com/azure-ad-b2c/user-migration) 리포지토리에는 원활한 마이그레이션 사용자 지정 정책 예제 및 REST API 코드 예제가 포함 되어 있습니다.

[원활한 사용자 마이그레이션 사용자 지정 정책 & REST API 코드 샘플](https://aka.ms/b2c-account-seamless-migration)
