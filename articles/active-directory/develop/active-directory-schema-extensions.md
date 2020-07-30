---
title: 클레임에서 Azure AD 스키마 확장 특성 사용
titleSuffix: Microsoft identity platform
description: 토큰 클레임의 응용 프로그램에 사용자 데이터를 보내기 위해 디렉터리 스키마 확장 특성을 사용 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 71dcad908884351950c89b590523ffa91d6d47f9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424782"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>클레임에서 디렉터리 스키마 확장 특성 사용

디렉터리 스키마 확장 특성은 사용자 개체 및 기타 디렉터리 개체 (예: 그룹, 테 넌 트 정보, 서비스 사용자)에 대 한 추가 데이터를 Azure Active Directory에 저장할 수 있는 방법을 제공 합니다.  사용자 개체의 확장 특성만 응용 프로그램에 대 한 클레임을 내보내는 데 사용할 수 있습니다. 이 문서에서는 토큰 클레임의 응용 프로그램에 사용자 데이터를 보내기 위해 디렉터리 스키마 확장 특성을 사용 하는 방법을 설명 합니다.

> [!NOTE]
> Microsoft Graph는 그래프 개체를 사용자 지정 하는 두 가지 다른 확장 메커니즘을 제공 합니다. 이를 Microsoft Graph 열린 확장 및 Microsoft Graph 스키마 확장 이라고 합니다. 자세한 내용은 [Microsoft Graph 설명서](/graph/extensibility-overview) 를 참조 하세요. 이러한 기능을 사용 하 여 Microsoft Graph 개체에 저장 된 데이터는 토큰의 클레임에 대 한 원본으로 사용할 수 없습니다.

디렉터리 스키마 확장 특성은 항상 테 넌 트의 응용 프로그램과 연결 되며 해당 이름으로 응용 프로그램의 *applicationId* 에서 참조 됩니다.

디렉터리 스키마 확장 특성에 대 한 식별자의 형식은 *Extension_xxxxxxxxx_AttributeName*입니다.  여기서 *xxxxxxxxx* 은 확장이 정의 된 응용 프로그램의 *applicationId* 입니다.

## <a name="registering-and-using-directory-schema-extensions"></a>디렉터리 스키마 확장 등록 및 사용
다음 두 가지 방법 중 하나로 디렉터리 스키마 확장 특성을 등록 하 고 채울 수 있습니다.

- AD Connect를 구성 하 여 해당 항목을 만들고 온-프레미스 AD에서 데이터를 동기화 합니다. [Azure AD Connect 디렉터리 확장 동기화](/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions)를 참조 하세요.
- Microsoft Graph를 사용 하 여 등록 하 고, 값을 설정 하 고, 디렉터리 스키마 확장 특성 [디렉터리 스키마 확장에서 읽기 ](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions) [AzureAD powershell cmdlet을 사용 하 여](/powershell/azure/active-directory/using-extension-attributes-sample?view=azureadps-2.0)개념 및/또는 powershell + 확장 특성 관리를 Graph API 합니다.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>AD Connect를 사용 하 여 만든 디렉터리 스키마 확장 특성의 데이터를 사용 하 여 클레임 내보내기
AD Connect를 사용 하 여 생성 및 동기화 된 디렉터리 스키마 확장 특성은 항상 AD Connect에서 사용 하는 응용 프로그램 ID와 연결 됩니다. 이러한 구성 요소는 갤러리를 사용 하 여 등록 된 SAML 응용 프로그램의 포털 UI에 있는 **엔터프라이즈** 응용 프로그램 구성에서 클레임으로 구성 하거나, **엔터프라이즈 응용**프로그램에서 비 갤러리 응용 프로그램 구성 환경으로 구성 하 고, 응용 프로그램 등록 환경을 통해 등록 된 응용 프로그램에 대 한 클레임 매핑 정책을 통해 클레임의 원본으로 사용할 수 있습니다.  AD Connect를 통해 만든 디렉터리 확장 특성이 디렉터리에 있으면 SAML SSO 클레임 구성 UI에 표시 됩니다.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>그래프 또는 PowerShell을 사용 하 여 응용 프로그램에 대해 만들어진 디렉터리 스키마 확장 특성의 데이터로 클레임 내보내기
Microsoft Graph 또는 PowerShell을 사용 하 여 응용 프로그램에 대 한 디렉터리 스키마 확장 특성이 등록 된 경우 (예를 들어 응용 프로그램 초기 설정 또는 프로 비전 단계를 통해) 사용자가 로그인 할 때 클레임의 사용자 개체에서 해당 특성의 데이터를 받도록 동일한 응용 프로그램을 Azure Active Directory 구성할 수 있습니다.  [선택적 클레임](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)을 사용 하 여 동일한 응용 프로그램에 등록 된 디렉터리 스키마 확장의 데이터를 수신 하도록 응용 프로그램을 구성할 수 있습니다.  응용 프로그램 매니페스트에서 설정할 수 있습니다.  이렇게 하면 다중 테 넌 트 응용 프로그램에서 고유한 용도에 대 한 디렉터리 스키마 확장 특성을 등록할 수 있습니다. 응용 프로그램이 테 넌 트로 프로 비전 되 면 해당 테 넌 트의 사용자에 대해 연결 된 디렉터리 스키마 확장을 설정 하 고 사용할 수 있게 됩니다.  테 넌 트에 구성 되 고 동의가 부여 되 면 그래프를 통해 데이터를 저장 하 고 검색 하는 데 사용할 수 있으며 Azure AD가 응용 프로그램에 내보내는 토큰의 클레임에 매핑할 수 있습니다.

디렉터리 스키마 확장 특성은 모든 응용 프로그램에 대해 등록 되 고 채워질 수 있습니다.

응용 프로그램에서 다른 응용 프로그램에 등록 된 확장 특성의 데이터가 포함 된 클레임을 전송 해야 하는 경우 클레임 [매핑 정책을](active-directory-claims-mapping.md) 사용 하 여 확장 특성을 클레임에 매핑해야 합니다.  디렉터리 스키마 확장 특성을 관리 하는 일반적인 패턴은 필요한 모든 스키마 확장에 대 한 등록 지점으로 사용할 특정 응용 프로그램을 만드는 것입니다.  실제 응용 프로그램 일 필요는 없으며,이 기법은 모든 확장의 이름에 동일한 응용 프로그램 ID가 있음을 의미 합니다.

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

여기서 *xxxxxxx* 은 확장이 등록 된 응용 프로그램 ID입니다.

> [!TIP]
> 개체에 대 한 디렉터리 확장 특성을 설정할 때 대/소문자 일관성은 중요 합니다.  확장 특성 이름은 설정할 때 대/소문자를 구분 하지 않지만 토큰 서비스에서 디렉터리를 읽을 때 대/소문자를 구분 합니다.  이름이 "LegacyId"이 고 이름이 "LegacyId" 인 다른 사용자 개체에 확장 특성이 설정 된 경우, "LegacyId" 라는 이름을 사용 하 여 특성을 클레임에 매핑하면 데이터는 성공적으로 검색 되 고 두 번째 사용자의 토큰에는 클레임이 포함 됩니다.
>
> 기본 제공 디렉터리 특성에 사용 되는 클레임 스키마의 "Id" 매개 변수는 디렉터리 확장 특성에 대 한 "ExtensionID"입니다.

## <a name="next-steps"></a>다음 단계
- [SAML 2.0 및 JWT (JSON 웹 토큰) 토큰에 사용자 지정 또는 추가 클레임을 추가](active-directory-optional-claims.md)하는 방법에 대해 알아봅니다. 
- [특정 앱에 대 한 토큰에서 내보낸 클레임을 사용자 지정](active-directory-claims-mapping.md)하는 방법을 알아봅니다.