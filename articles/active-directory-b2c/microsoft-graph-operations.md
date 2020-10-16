---
title: 지원 되는 Microsoft Graph 작업
titleSuffix: Azure AD B2C
description: 사용자, 사용자 흐름, id 공급자, 사용자 지정 정책, 정책 키 등을 비롯 한 Azure AD B2C 리소스 관리에 대해 지원 되는 Microsoft Graph 작업의 인덱스입니다.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d8d898d3825fa40cbfd13337067c24cf14f9a544
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102020"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Azure AD B2C에 사용할 수 있는 Microsoft Graph 작업

다음 Microsoft Graph API 작업은 사용자, id 공급자, 사용자 흐름, 사용자 지정 정책 및 정책 키를 비롯 한 Azure AD B2C 리소스 관리에 대해 지원 됩니다.

다음 섹션의 각 링크는 해당 작업에 대 한 Microsoft Graph API 참조 내의 해당 페이지를 대상으로 합니다.

## <a name="user-management"></a>사용자 관리

- [사용자 나열](https://docs.microsoft.com/graph/api/user-list)
- [소비자 사용자 만들기](https://docs.microsoft.com/graph/api/user-post-users)
- [사용자 가져오기](https://docs.microsoft.com/graph/api/user-get)
- [사용자 업데이트](https://docs.microsoft.com/graph/api/user-update)
- [사용자 삭제](https://docs.microsoft.com/graph/api/user-delete)

Microsoft Graph API를 사용 하 여 Azure AD B2C 사용자 계정을 관리 하는 방법에 대 한 자세한 내용은 [Microsoft Graph를 사용 하 여 Azure AD B2C 사용자 계정 관리](manage-user-accounts-graph-api.md)를 참조 하세요.

## <a name="user-phone-number-management"></a>사용자 전화 번호 관리

- [추가](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [가져오기](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [Update](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [Delete](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

Microsoft Graph API를 사용 하 여 사용자의 로그인 전화 번호를 관리 하는 방법에 대 한 자세한 내용은 [B2C 인증 방법](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy)을 참조 하세요.

## <a name="identity-providers-user-flow"></a>Id 공급자 (사용자 흐름)

Azure AD B2C 테 넌 트에서 사용자 흐름에서 사용할 수 있는 id 공급자를 관리 합니다.

- [Azure AD B2C 테 넌 트에 등록 된 id 공급자 나열](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Id 공급자 만들기](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Id 공급자 가져오기](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Id 공급자 업데이트](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Id 공급자 삭제](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>사용자 흐름

등록, 로그인, 결합 된 등록 및 로그인, 암호 재설정 및 프로필 업데이트에 대해 미리 작성 된 정책을 구성 합니다.

- [사용자 흐름 나열](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [사용자 흐름 만들기](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [사용자 흐름 가져오기](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [사용자 흐름 삭제](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>사용자 지정 정책

다음 작업을 사용 하 여 [사용자 지정 정책](custom-policy-overview.md)이라고 하는 Azure AD B2C 신뢰 프레임 워크 정책을 관리할 수 있습니다.

- [테 넌 트에서 구성 된 모든 신뢰 프레임 워크 정책을 나열 합니다.](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [신뢰 프레임 워크 정책 만들기](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [기존 보안 프레임 워크 정책의 속성을 읽습니다.](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [신뢰 프레임 워크 정책을 업데이트 하거나 만듭니다.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [기존 보안 프레임 워크 정책 삭제](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>정책 키

Id 경험 프레임 워크는 사용자 지정 정책에서 참조 되는 암호를 저장 하 여 구성 요소 간에 트러스트를 설정 합니다. 이러한 비밀은 대칭 또는 비대칭 키/값이 될 수 있습니다. Azure Portal에서 이러한 엔터티는 **정책 키**로 표시 됩니다.

Microsoft Graph API의 정책 키에 대 한 최상위 리소스는 [신뢰할 수 있는 프레임 워크 키 집합](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset)입니다. 각 키 **집합** 에는 하나 이상의 **키**가 포함 되어 있습니다. 키를 만들려면 먼저 빈 키 집합을 만든 다음 키 집합에 키를 생성 합니다. 수동 암호를 만들거나 인증서 또는 PKCS12 키를 업로드할 수 있습니다. 키는 생성 된 암호, 사용자가 정의 하는 문자열 (예: Facebook 응용 프로그램 암호) 또는 업로드 한 인증서 일 수 있습니다. 키 집합에 여러 키가 있는 경우 키 중 하나만 활성화 됩니다.

### <a name="trust-framework-policy-keyset"></a>보안 프레임 워크 정책 키 집합

- [신뢰 프레임 워크 키 집합이 비동기적 나열](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [키 집합이 비동기적 보안 프레임 워크 만들기](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [키 집합 가져오기](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [보안 프레임 워크 키 집합이 비동기적 업데이트](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [보안 프레임 워크 키 집합이 비동기적 삭제](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>보안 프레임 워크 정책 키

- [키 집합에서 현재 활성 키 가져오기](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [키 집합에 키 생성](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [문자열 기반 비밀 업로드](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509 인증서 업로드](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 형식 인증서 업로드](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>애플리케이션

- [애플리케이션 나열](https://docs.microsoft.com/graph/api/application-list)
- [응용 프로그램 만들기](https://docs.microsoft.com/graph/api/resources/application)
- [애플리케이션 업데이트](https://docs.microsoft.com/graph/api/application-update)
- [ServicePrincipal 만들기](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Oauth2Permission Grant 만들기](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [애플리케이션 삭제](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>응용 프로그램 확장 속성

- [확장 속성 나열](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C는 사용자당 100개의 사용자 지정 특성을 보유할 수 있는 디렉터리를 제공합니다. 사용자 흐름의 경우 이러한 확장 속성은 [Azure Portal를 사용 하 여 관리](custom-policy-custom-attributes.md)됩니다. 사용자 지정 정책의 경우 정책에서 확장 속성에 값을 처음으로 쓸 때 Azure AD B2C에서 속성을 만듭니다.

## <a name="audit-logs"></a>감사 로그

- [감사 로그 나열](https://docs.microsoft.com/graph/api/directoryaudit-list)

Microsoft Graph API를 사용 하 여 감사 로그 Azure AD B2C 액세스 하는 방법에 대 한 자세한 내용은 [Azure AD B2C 감사 로그 액세스](view-audit-logs.md)를 참조 하세요.
