---
title: 클레임의 Azure AD 스키마 확장 특성 사용
titleSuffix: Microsoft identity platform
description: 디렉터리 스키마 확장 특성을 사용하여 토큰 클레임의 애플리케이션에 사용자 데이터를 보내는 방법을 설명합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755734"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>클레임에서 디렉터리 스키마 확장 특성 사용

디렉터리 스키마 확장 특성은 사용자 개체 및 기타 디렉터리 개체(예: 그룹, 테넌트 정보, 서비스 사용자)에 대한 추가 데이터를 Azure Active Directory에 저장할 수 있는 방법을 제공합니다.  사용자 개체의 확장 특성만 애플리케이션에 대한 클레임을 내보내는 데 사용할 수 있습니다. 이 문서에서는 토큰 클레임의 애플리케이션에 사용자 데이터를 보내기 위해 디렉터리 스키마 확장 특성을 사용하는 방법을 설명합니다.

> [!NOTE]
> Microsoft Graph는 Graph 개체를 사용자 지정하는 두 가지 다른 확장 메커니즘을 제공합니다. 이를 Microsoft Graph 열린 확장 및 Microsoft Graph 스키마 확장이라고 합니다. 자세한 내용은 [Microsoft Graph 설명서](/graph/extensibility-overview)를 참조하세요. 이러한 기능을 사용하여 Microsoft Graph 개체에 저장된 데이터는 토큰의 클레임에 대한 원본으로 사용할 수 없습니다.

디렉터리 스키마 확장 특성은 항상 테넌트의 애플리케이션과 연결되며 해당 이름에서 애플리케이션의 *applicationId* 에 의해 참조됩니다.

디렉터리 스키마 확장 특성에 대한 식별자의 형식은 *Extension_xxxxxxxxx_AttributeName* 입니다.  여기서 *xxxxxxxxx* 는 확장이 정의된 애플리케이션의 *applicationId* 입니다.

## <a name="registering-and-using-directory-schema-extensions"></a>디렉터리 스키마 확장 등록 및 사용
다음 두 가지 방법 중 하나로 디렉터리 스키마 확장 특성을 등록하고 채울 수 있습니다.

- AD Connect를 구성하여 해당 항목을 만들고 온-프레미스 AD에서 데이터를 동기화합니다. [Azure AD Connect 동기화: 디렉터리 확장](../hybrid/how-to-connect-sync-feature-directory-extensions.md)을 참조하세요.
- Microsoft Graph를 사용하여 등록하고, 값을 설정하고, [스키마 확장](/graph/extensibility-overview)에서 읽습니다. [PowerShell cmdlet](/powershell/azure/active-directory/using-extension-attributes-sample)도 사용할 수 있습니다.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>AD Connect로 만든 디렉터리 스키마 확장 특성의 데이터를 사용하여 클레임 내보내기
AD Connect를 사용하여 만들어져 동기화된 디렉터리 스키마 확장 특성은 항상 AD Connect에서 사용하는 애플리케이션 ID와 연결됩니다. 이러한 특성은 **엔터프라이즈 애플리케이션** 에서 갤러리 또는 비 갤러리 애플리케이션 구성 환경을 사용하여 등록된 SAML 애플리케이션의 포털 UI에 있는 **엔터프라이즈 애플리케이션** 구성에서 클레임으로 구성하거나, 애플리케이션 등록 환경을 통해 등록된 애플리케이션에 대한 클레임 매핑 정책으로 클레임의 원본으로 사용할 수 있습니다.  AD Connect를 통해 만든 디렉터리 확장 특성이 디렉터리에 있으면 SAML SSO 클레임 구성 UI에 표시됩니다.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Graph 또는 PowerShell을 사용하는 애플리케이션에 대해 만들어진 디렉터리 스키마 확장 특성의 데이터를 사용하여 클레임 내보내기
Microsoft Graph 또는 PowerShell을 사용하여 애플리케이션에 대한 디렉터리 스키마 확장 특성이 등록된 경우(예를 들어 애플리케이션 초기 설정 또는 프로비저닝 단계를 통해) 사용자가 로그인할 때 클레임의 사용자 개체에서 해당 특성의 데이터를 받도록 해당 애플리케이션을 Azure Active Directory에서 구성할 수 있습니다.  [선택적 클레임](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)을 사용하여 동일한 애플리케이션에 등록된 디렉터리 스키마 확장의 데이터를 수신하도록 애플리케이션을 구성할 수 있습니다.  애플리케이션 매니페스트에서 설정할 수 있습니다.  이렇게 하면 다중 테넌트 애플리케이션에서 고유한 용도의 디렉터리 스키마 확장 특성을 등록할 수 있습니다. 애플리케이션이 테넌트로 프로비저닝되면 해당 테넌트의 사용자에 대해 연결된 디렉터리 스키마 확장을 설정하고 사용할 수 있게 됩니다.  테넌트에서 구성되고 동의를 받은 후에는 그래프를 통해 데이터를 저장하고 검색하는 데 사용할 수 있으며 Microsoft ID 플랫폼이 애플리케이션으로 내보내는 토큰의 클레임에 매핑할 수도 있습니다.

디렉터리 스키마 확장 특성은 모든 애플리케이션에 대해 등록되고 채워질 수 있습니다.

애플리케이션에서 다른 애플리케이션에 등록된 확장 특성의 데이터를 사용하여 클레임을 전송해야 하는 경우 [클레임 매핑 정책](active-directory-claims-mapping.md)을 사용하여 확장 특성을 해당 클레임에 매핑해야 합니다.  디렉터리 스키마 확장 특성을 관리하는 일반적인 패턴은 필요한 모든 스키마 확장에 대한 등록 지점으로 사용할 특정한 애플리케이션을 만드는 것입니다.  실제 애플리케이션일 필요는 없으며, 이 기술은 모든 확장의 이름에 동일한 애플리케이션 ID가 있음을 의미합니다.

예를 들어 다음은 OAuth/OIDC 토큰의 디렉터리 스키마 확장 특성에서 단일 클레임을 내보내는 클레임 매핑 정책입니다.

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

여기서 *xxxxxxx* 는 확장 등록에 사용된 애플리케이션 ID입니다.

> [!TIP]
> 개체에 대한 디렉터리 확장 특성을 설정할 때 대/소문자 일관성은 중요합니다.  확장 특성 이름은 설정할 때 대/소문자를 구분하지 않지만 토큰 서비스에서 디렉터리를 읽을 때는 대/소문자를 구분합니다.  한 사용자 개체에 이름이 "LegacyId"로 설정되고 다른 사용자 개체에는 이름이 "legacyid"로 확장 특성이 설정된 경우, "LegacyId"라는 이름을 사용하여 특성을 클레임에 매핑하면 첫 번째 사용자의 토큰에서 데이터는 성공적으로 검색되지만 두 번째 사용자의 토큰에서는 검색되지 않을 것입니다.
>
> 기본 제공 디렉터리 특성에 사용되는 클레임 스키마의 "Id" 매개 변수는 디렉터리 확장 특성에 대한 "ExtensionID"입니다.

## <a name="next-steps"></a>다음 단계
- [SAML 2.0 및 JWT(JSON 웹 토큰)에 사용자 지정 또는 추가 클레임](active-directory-optional-claims.md)을 추가하는 방법에 대해 알아보세요.
- [특정 앱에 대한 토큰에서 내보낸 클레임을 사용자 지정](active-directory-claims-mapping.md)하는 방법에 대해 알아봅니다.