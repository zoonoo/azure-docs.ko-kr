---
title: 정책 키 개요-Azure Active Directory B2C
description: 토큰, 클라이언트 암호, 인증서 및 암호를 서명 하 고 유효성을 검사 하는 Azure Active Directory B2C 사용할 수 있는 암호화 정책 키 유형에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30348d7ca12ded2d1f4b0522a7cabeadf0553a07
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953358"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 정책 키 개요

Azure Active Directory B2C (Azure AD B2C)는 암호 및 인증서를 정책 키 형식으로 저장 하 여 통합 하는 서비스와의 트러스트를 설정 합니다. 이러한 트러스트는 다음으로 구성 됩니다.

- 외부 ID 공급자
- [REST API services](restful-technical-profile.md) 와 연결
- 토큰 서명 및 암호화

 이 문서에서는 Azure AD B2C에서 사용 하는 정책 키에 대해 알아야 할 사항을 설명 합니다.

> [!NOTE]
> 현재 정책 키의 구성은 [사용자 지정 정책](./custom-policy-get-started.md) 으로만 제한 됩니다.

**정책 키** 메뉴의 Azure Portal에서 서비스 간의 신뢰를 설정 하기 위한 암호 및 인증서를 구성할 수 있습니다. 키는 대칭 또는 비대칭 일 수 있습니다. *대칭* 암호화 또는 개인 키 암호화는 공유 암호를 사용 하 여 데이터를 암호화 하 고 해독 하는 데 사용 됩니다. *비대칭* 암호화 또는 공개 키 암호화는 신뢰 당사자 응용 프로그램과 공유 되는 공개 키와 Azure AD B2C에만 알려진 개인 키로 구성 된 키 쌍을 사용 하는 암호화 시스템입니다.

## <a name="policy-keyset-and-keys"></a>정책 키 집합 및 키

Azure AD B2C의 정책 키에 대 한 최상위 리소스는 **키 집합** 컨테이너입니다. 각 키 집합에는 하나 이상의 **키** 가 포함 되어 있습니다. 키에는 다음과 같은 특성이 있습니다.

| attribute |  필수 | 설명 |
| --- | --- |--- |
| `use` | 예 | 사용법: 공개 키의 용도를 식별 합니다. 데이터 암호화 `enc` 또는 데이터에 대 한 서명 확인 `sig` .|
| `nbf`| 예 | 활성화 날짜 및 시간입니다. |
| `exp`| 예 | 만료 날짜 및 시간입니다. |

PKI 표준에 따라 키 활성화 및 만료 값을 설정 하는 것이 좋습니다. 보안 또는 정책 상의 이유로 이러한 인증서를 정기적으로 회전 해야 할 수도 있습니다. 예를 들어 매년 모든 인증서를 회전 하는 정책이 있을 수 있습니다.

키를 만들려면 다음 방법 중 하나를 선택할 수 있습니다.

- **수동** -정의한 문자열로 암호를 만듭니다. 비밀은 대칭 키입니다. 활성화 및 만료 날짜를 설정할 수 있습니다.
- **생성** 됨-키를 자동으로 생성 합니다. 활성화 및 만료 날짜를 설정할 수 있습니다. 두 가지 옵션 중이 있습니다.
  - **Secret** -대칭 키를 생성 합니다.
  - **RSA** -키 쌍 (비대칭 키)을 생성 합니다.
- **업로드** -인증서 또는 PKCS12 키를 업로드 합니다. 인증서는 개인 및 공개 키 (비대칭 키)를 포함 해야 합니다.

## <a name="key-rollover"></a>키 롤오버

보안을 위해 Azure AD B2C는 정기적으로 또는 응급 상황에서 즉시 키를 롤아웃할 수 있습니다. Azure AD B2C와 통합 된 모든 응용 프로그램, id 공급자 또는 REST API는 발생 빈도에 관계 없이 키 롤오버 이벤트를 처리 하도록 준비 해야 합니다. 그렇지 않으면 응용 프로그램 또는 Azure AD B2C에서 만료 된 키를 사용 하 여 암호화 작업을 수행 하려고 하면 로그인 요청이 실패 합니다.

Azure AD B2C 키 집합에 여러 키가 있는 경우 다음 기준에 따라 한 번에 하나의 키만 활성화 됩니다.

- 키 활성화는 **활성화 날짜** 를 기반으로 합니다.
  - 키는 활성화 날짜를 기준으로 오름차순으로 정렬 됩니다. 활성화 날짜가 앞에 추가 된 키는 목록에서 더 아래에 나타납니다. 활성화 날짜가 없는 키는 목록의 맨 아래에 있습니다.
  - 현재 날짜와 시간이 키의 활성화 날짜 보다 크면 Azure AD B2C는 키를 활성화 하 고 이전 활성 키 사용을 중지 합니다.
- 현재 키의 만료 시간이 경과 하 고 키 컨테이너에 유효 *하지 않음 이전* 및 *만료* 시간을 사용 하는 새 키가 포함 된 경우 새 키가 자동으로 활성화 됩니다.
- 현재 키의 만료 시간이 경과 하 고 키 컨테이너에 유효 *하지 않음 이전* 및 *만료* 시간을 사용 하는 새 키가 포함 되어 *있지* 않으면 Azure AD B2C는 만료 된 키를 사용할 수 없습니다. Azure AD B2C는 사용자 지정 정책의 종속 구성 요소 내에서 오류 메시지를 발생 시킵니다. 이 문제를 방지 하기 위해 정품 인증 및 만료 날짜 없이 기본 키를 안전한 네트워크로 만들 수 있습니다.
- Openid connect Connect의 잘 알려진 구성 끝점의 키 끝점 (JWKS URI)은 키가 [JwtIssuer 기술 프로필](./jwt-issuer-technical-profile.md)에서 참조 될 때 키 컨테이너에 구성 된 키를 반영 합니다. OIDC 라이브러리를 사용 하는 응용 프로그램은 올바른 키를 사용 하 여 토큰의 유효성을 검사할 수 있도록이 메타 데이터를 자동으로 인출 합니다. 자세한 내용은 항상 최신 토큰 서명 키를 자동으로 가져오는 [Microsoft 인증 라이브러리](../active-directory/develop/msal-b2c-overview.md)를 사용 하는 방법을 알아보세요.

## <a name="policy-key-management"></a>정책 키 관리

키 컨테이너 내에서 현재 활성 키를 가져오려면 Microsoft Graph API [Getactivekey](/graph/api/trustframeworkkeyset-getactivekey) 끝점을 사용 합니다.

서명 및 암호화 키를 추가 하거나 삭제 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. 개요 페이지의 **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키** 선택 
    1. 새 키를 추가 하려면 **추가** 를 선택 합니다.
    1. 새 키를 제거 하려면 키를 선택 하 고 **삭제** 를 선택 합니다. 키를 삭제 하려면 삭제할 키 컨테이너의 이름을 입력 합니다. 키를 삭제 하 고 suffix .bak를 사용 하 여 키의 복사본을 만듭니다 Azure AD B2C 합니다.

### <a name="replace-a-key"></a>키 바꾸기

키 집합의 키는 교체 하거나 제거할 수 없습니다. 기존 키를 변경 해야 하는 경우:

- 현재 날짜 및 시간으로 설정 된 **활성화 날짜** 를 사용 하 여 새 키를 추가 하는 것이 좋습니다. Azure AD B2C 새 키를 활성화 하 고 이전 활성 키 사용을 중지 합니다.
- 또는 올바른 키를 사용 하 여 새 키 집합을 만들 수 있습니다. 새 키 집합을 사용 하도록 정책을 업데이트 한 후 이전 키 집합을 제거 합니다. 

## <a name="next-steps"></a>다음 단계

- Microsoft Graph를 사용 하 여 [키 집합](microsoft-graph-operations.md#trust-framework-policy-keyset) 및 [정책 키](microsoft-graph-operations.md#trust-framework-policy-key) 배포를 자동화 하는 방법을 알아봅니다.