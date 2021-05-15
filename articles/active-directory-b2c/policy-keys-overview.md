---
title: 정책 키 개요-Azure Active Directory B2C
description: 토큰, 클라이언트 암호, 인증서 및 암호를 서명하고 유효성을 검사하는 Azure Active Directory B2C 사용할 수 있는 암호화 정책 키 유형에 대해 알아보세요.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a41717e9be0918dead9f77a5f5472494d734b38a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717534"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 정책 키 개요

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C)는 암호 및 인증서를 정책 키 형식으로 저장하여 통합하는 서비스와의 신뢰를 설정합니다. 이러한 신뢰는 다음으로 구성됩니다.

- 외부 ID 공급자
- [REST API services](restful-technical-profile.md) 와 연결
- 토큰 서명 및 암호화

 이 문서에서는 Azure AD B2C에서 사용하는 정책 키에 대해 알아야 할 사항을 설명합니다.

> [!NOTE]
> 현재 정책 키의 구성은 [사용자 지정 정책](./user-flow-overview.md)에만 제한됩니다.

**정책 키** 메뉴의 Azure Portal에서 서비스 간의 신뢰를 설정 하기 위한 암호 및 인증서를 구성할 수 있습니다. 키는 대칭 키 또는 비대칭 키일 수 있습니다. *대칭* 암호화 또는 개인 키 암호화는 공유 암호를 사용하여 데이터 암호화 및 암호 해독에 사용됩니다. *비대칭* 암호화 또는 공개 키 암호화는 신뢰 당사자 애플리케이션과 공유되는 공개 키와 Azure AD B2C에만 알려진 개인 키로 구성된 키 쌍을 사용하는 암호화 시스템입니다.

## <a name="policy-keyset-and-keys"></a>정책 키 집합 및 키

Azure AD B2C의 정책 키에 대한 최상위 리소스는 **키 집합** 컨테이너입니다. 각각의 키 집합에는 하나 이상의 **키** 가 포함되어 있습니다. 키는 다음과 같은 특성을 지닙니다.

| attribute |  필수 | 설명 |
| --- | --- |--- |
| `use` | 예 | 사용법: 공개 키의 용도를 확인합니다. 데이터 암호화 `enc` 또는 데이터에 대한 서명 확인 `sig` .|
| `nbf`| No | 활성화 날짜 및 시간. |
| `exp`| No | 만료 날짜 및 시간. |

PKI 표준에 따른 키 활성화 및 만료 값 설정을 권장합니다. 보안 또는 정책상의 이유로 이러한 인증서를 주기적으로 갱신해야 할 수 있습니다. 예를 들어 매년 모든 인증서를 갱신하는 정책이 있을 수 있습니다.

키 생성을 위해 다음 방법 중 하나를 선택할 수 있습니다.

- **수동 생성** -정의한 문자열로 암호를 생성합니다 암호는 대칭 키입니다. 활성화 및 만료 날짜를 설정할 수 있습니다.
- **자동 생성** -키가 자동으로 생성됩니다. 활성화 및 만료 날짜를 설정할 수 있습니다. 두 가지 옵션 중이 있습니다.
  - **암호** -대칭 키를 생성합니다.
  - **RSA** -키 쌍(비대칭 키)을 생성합니다.
- **업로드** -인증서 또는 PKCS12 키를 업로드합니다. 인증서는 개인 및 공개 키(비대칭 키)를 포함해야 합니다.

## <a name="key-rollover"></a>키 롤오버

보안을 위해 Azure AD B2C는 주기적으로 또는 응급 상황이 발생할 경우 즉시 키를 롤아웃할 수 있습니다. Azure AD와 통합되는 모든 애플리케이션은 발생 빈도에 관계 없이 키 롤오버 이벤트를 처리하기 위해 준비할 준비가 되어 있어야 합니다. 그렇지 않은 경우 애플리케이션 또는 Azure AD B2C에서 만료된 키를 사용하여 암호화 작업을 시도할 경우 로그인 요청은 실패합니다.

Azure AD B2C 키 집합에 여러 키가 있는 경우 다음 기준에 따라 한 번에 하나의 키만 활성화됩니다.

- 키 활성화는 **활성화 날짜** 를 기반으로 합니다.
  - 키는 활성화 날짜를 기준으로 오름차순으로 정렬됩니다. 활성화 날짜가 이후인 키는 목록에서 아래쪽에 표시됩니다. 활성화 날짜가 없는 키는 목록의 맨 아래쪽에 있습니다.
  - 현재 날짜와 시간이 키의 활성화 날짜 보다 크면 Azure AD B2C가 해당 키를 활성화하고 이전에 활성된 키의 사용을 중지합니다.
- 현재 키의 만료 시간이 경과하고 키 컨테이너에 *이전 시간* 및 *만료* 시간이 아닌 유효한 새 키가 포함된 경우 새로운 키가 자동으로 활성화됩니다.
- 현재 키의 만료 시간이 경과하고 키 컨테이너에 *이전 시간* 및 *만료* 시간이 아닌 유효한 새 키가 포함되어 *있지* 않으면 Azure AD B2C는 만료된 키를 사용할 수 없습니다. Azure AD B2C는 사용자 지정 정책의 종속된 구성 요소에서 오류 메시지를 표시합니다. 이 문제를 예방하려면 안전망으로 활성화 및 만료 날짜가 없는 기본 키를 생성할 수 있습니다.
- Openid Connect의 친숙한 구성 엔드포인트의 키 엔드포인트(JWKS URI)는 [JwtIssuer 기술 프로필](./jwt-issuer-technical-profile.md)에서 참조되는 경우 키 컨테이너에 구성된 키를 반영합니다. OIDC 라이브러리를 사용하는 애플리케이션은 올바른 키를 사용하여 토큰의 유효성을 검사할 수 있도록 이 메타 데이터를 자동으로 인출합니다. 자세한 내용을 위해 항상 최신 토큰 서명 키를 자동으로 페치하는 [Microsoft 인증 라이브러리](../active-directory/develop/msal-b2c-overview.md) 사용 방법을 알아보세요.

## <a name="policy-key-management"></a>정책 키 관리

키 컨테이너 내에서 현재 활성화된 키를 가져오려면 Microsoft Graph API [Getactivekey](/graph/api/trustframeworkkeyset-getactivekey) 엔드포인트를 사용합니다.

서명 및 암호화 키를 추가하거나 삭제하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. 개요 페이지의 **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키** 선택 
    1. 새 키를 추가하려면 **추가** 를 선택합니다.
    1. 새 키를 제거하려면 키를 선택하고 **삭제** 를 선택합니다. 키를 삭제 하려면 삭제할 키의 컨테이너 이름을 입력합니다. Azure AD B2C는 키를 삭제하고 suffix .bak을 이용하여 키의 복사본을 생성합니다.

### <a name="replace-a-key"></a>키 교체

키 집합의 키는 교체하거나 제거할 수 없습니다. 기존의 키를 변경해야 하는 경우:

- **활성화 날짜** 가 현재 날짜 및 시간으로 설정된 새로운 키를 추가하는 것을 권장합니다. Azure AD B2C는 새로운 키를 활성화하고 이전에 활성화된 키의 사용을 중지합니다.
- 또는 올바른 키로 새로운 키 집합을 생성할 수 있습니다. 새로운 키 집합을 사용하도록 정책을 업데이트한 후 이전의 키 집합을 제거합니다. 

## <a name="next-steps"></a>다음 단계

- Microsoft Graph를 사용하여 [키 집합](microsoft-graph-operations.md#trust-framework-policy-keyset) 및 [정책 키](microsoft-graph-operations.md#trust-framework-policy-key) 배포의 자동화 방법을 알아보세요

::: zone-end
