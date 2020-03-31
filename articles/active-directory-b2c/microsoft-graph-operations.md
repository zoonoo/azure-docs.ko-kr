---
title: 지원되는 마이크로소프트 그래프 작업
titleSuffix: Azure AD B2C
description: 사용자, 사용자 흐름, ID 공급자, 사용자 지정 정책, 정책 키 등을 포함하여 Azure AD B2C 리소스 관리를 위해 지원되는 Microsoft 그래프 작업의 인덱스입니다.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184251"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Azure AD B2C에서 사용할 수 있는 마이크로소프트 그래프 작업

다음 Microsoft 그래프 API 작업은 사용자, ID 공급자, 사용자 흐름, 사용자 정책 및 정책 키를 포함하여 Azure AD B2C 리소스의 관리를 위해 지원됩니다.

다음 섹션의 각 링크는 해당 작업에 대한 Microsoft Graph API 참조 내의 해당 페이지를 대상으로 합니다.

## <a name="user-management"></a>사용자 관리

- [사용자 나열](https://docs.microsoft.com/graph/api/user-list)
- [소비자 사용자 만들기](https://docs.microsoft.com/graph/api/user-post-users)
- [사용자 받기](https://docs.microsoft.com/graph/api/user-get)
- [사용자 업데이트](https://docs.microsoft.com/graph/api/user-update)
- [사용자 삭제](https://docs.microsoft.com/graph/api/user-delete)

Microsoft 그래프 API를 사용 하 여 Azure AD B2C 사용자 계정 관리에 대 한 자세한 내용은 Microsoft 그래프를 사용 하 여 [Azure AD B2C 사용자 계정 관리를](manage-user-accounts-graph-api.md)참조 하십시오.

## <a name="identity-providers-user-flow"></a>ID 공급자(사용자 흐름)

Azure AD B2C 테넌트에서 사용자 흐름에 사용할 수 있는 ID 공급자를 관리합니다.

- [Azure AD B2C 테넌트에 등록된 ID 공급자 목록](https://docs.microsoft.com/graph/api/identityprovider-list)
- [ID 공급자 만들기](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [ID 공급자 받기](https://docs.microsoft.com/graph/api/identityprovider-get)
- [ID 공급자 업데이트](https://docs.microsoft.com/graph/api/identityprovider-update)
- [ID 공급자 삭제](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>사용자 흐름

등록, 등록, 결합된 등록 및 로그인, 암호 재설정 및 프로필 업데이트에 대해 미리 빌드된 정책을 구성합니다.

- [사용자 흐름 목록](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [사용자 흐름 만들기](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [사용자 흐름 얻기](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [사용자 흐름 삭제](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>사용자 지정 정책

다음 작업을 통해 [사용자 지정 정책이라고](custom-policy-overview.md)하는 Azure AD B2C Trust Framework 정책을 관리할 수 있습니다.

- [테넌트에 구성된 모든 트러스트 프레임워크 정책 나열](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [신뢰 프레임워크 정책 만들기](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [기존 트러스트 프레임워크 정책의 속성 읽기](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [트러스트 프레임워크 정책을 업데이트하거나 만듭니다.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [기존 트러스트 프레임워크 정책 삭제](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>정책 키

ID 환경 프레임워크는 구성 요소 간의 신뢰를 설정하는 사용자 지정 정책에 참조된 비밀을 저장합니다. 이러한 암호는 대칭 또는 비대칭 키/값일 수 있습니다. Azure 포털에서 이러한 엔터티는 **정책 키로**표시됩니다.

Microsoft 그래프 API의 정책 키에 대한 최상위 리소스는 [신뢰할 수 있는 프레임워크 키집합입니다.](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset) 각 **키 집합에는** 하나 이상의 **키**가 포함되어 있습니다. 키를 만들려면 먼저 빈 키 집합을 만든 다음 키 집합에서 키를 생성합니다. 수동 비밀을 만들거나 인증서 또는 PKCS12 키를 업로드할 수 있습니다. 키는 생성된 비밀, 정의한 문자열(예: Facebook 응용 프로그램 비밀) 또는 업로드한 인증서일 수 있습니다. 키 집합에 여러 키가 있는 경우 키 중 하나만 활성화됩니다.

### <a name="trust-framework-policy-keyset"></a>트러스트 프레임워크 정책 키세트

- [트러스트 프레임워크 키세트 나열](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [트러스트 프레임워크 키세트 만들기](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [키 세트 받기](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [트러스트 프레임워크 키세트 업데이트](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [트러스트 프레임워크 키세트 삭제](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>트러스트 프레임워크 정책 키

- [키 세트에서 현재 활성 키 받기](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [키 집합에서 키 생성](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [문자열 기반 비밀 업로드](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509 인증서 업로드](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 형식 인증서 업로드](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>애플리케이션

- [애플리케이션 나열](https://docs.microsoft.com/graph/api/application-list)
- [애플리케이션 만들기](https://docs.microsoft.com/graph/api/resources/application)
- [응용 프로그램 업데이트](https://docs.microsoft.com/graph/api/application-update)
- [서비스 만들기주체](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [oauth2권한 부여 만들기](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [응용 프로그램 삭제](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>응용 프로그램 확장 속성

- [목록 확장 속성](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C는 사용자당 100개의 사용자 지정 특성을 보유할 수 있는 디렉터리를 제공합니다. 사용자 흐름의 경우 이러한 확장 속성은 [Azure 포털을 사용하여 관리됩니다.](custom-policy-custom-attributes.md) 사용자 지정 정책의 경우 Azure AD B2C는 정책이 확장 속성에 값을 처음 쓸 때 속성을 만듭니다.

## <a name="audit-logs"></a>감사 로그

- [감사 로그 목록](https://docs.microsoft.com/graph/api/directoryaudit-list)

Microsoft 그래프 API를 통해 Azure AD B2C 감사 로그에 액세스하는 자세한 내용은 [Azure AD B2C 감사 로그 액세스](view-audit-logs.md)를 참조하십시오.
