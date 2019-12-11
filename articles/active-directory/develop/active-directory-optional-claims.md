---
title: Azure AD 앱에 선택적 클레임 제공 | Microsoft
titleSuffix: Microsoft identity platform
description: Azure Active Directory에서 발급 한 SAML 2.0 및 JWT (JSON 웹 토큰) 토큰에 사용자 지정 또는 추가 클레임을 추가 하는 방법입니다.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967236"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>방법: Azure AD 앱에 선택적 클레임 제공

응용 프로그램 개발자는 Azure AD 응용 프로그램에서 선택적 클레임을 사용 하 여 응용 프로그램으로 전송 되는 토큰에서 원하는 클레임을 지정할 수 있습니다. 

선택적 클레임을 사용 하 여 다음을 수행할 수 있습니다.

- 응용 프로그램에 대 한 토큰에 포함할 추가 클레임을 선택 합니다.
- Azure AD가 토큰에서 반환 하는 특정 클레임의 동작을 변경 합니다.
- 응용 프로그램에 대 한 사용자 지정 클레임을 추가 하 고 액세스 합니다.

표준 클레임 목록은 [액세스 토큰](access-tokens.md) 및 [id_token](id-tokens.md) 클레임 설명서를 참조 하세요. 

선택적 클레임은 v 1.0 및 v2.0 형식 토큰 뿐만 아니라 SAML 토큰 에서도 지원 되지만, v 1.0에서 v 2.0으로 이동할 때 대부분의 값을 제공 합니다. V2.0 [Microsoft identity platform 끝점](active-directory-appmodel-v2-overview.md) 의 목표 중 하나는 클라이언트에서 최적의 성능을 보장 하기 위해 더 작은 토큰 크기입니다. 그 결과, 이전에 액세스 및 ID 토큰에 포함 된 몇 가지 클레임이 v2.0 토큰에는 없으며, 응용 프로그램 별로 구체적으로 요청 해야 합니다.

**표 1: 적용 가능성**

| 계정 유형 | v1.0 토큰 | v2.0 토큰  |
|--------------|---------------|----------------|
| 개인 Microsoft 계정  | –  | Támogatott |
| Azure AD-fiók      | Támogatott | Támogatott |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 및 v2.0 옵션 클레임 집합

사용할 응용 프로그램에 대해 기본적으로 사용할 수 있는 선택적 클레임 집합은 다음과 같습니다. 응용 프로그램에 대 한 사용자 지정 선택적 클레임을 추가 하려면 아래의 [디렉터리 확장](#configuring-directory-extension-optional-claims)을 참조 하세요. **액세스 토큰**에 클레임을 추가 *하는 경우* 응용 프로그램이 아닌 응용 프로그램 (웹 API) *에 대해* 요청 된 액세스 토큰에 적용 됩니다. 이렇게 하면 클라이언트에서 API에 액세스 하는 것과 상관 없이 API에 대 한 인증에 사용 되는 액세스 토큰에 올바른 데이터가 표시 됩니다.

> [!NOTE]
> 이러한 클레임의 대부분은 토큰 유형 열에 명시 된 경우를 제외 하 고는 v 1.0 및 v2.0 토큰의 경우 JWTs에 포함 될 수 있지만 SAML 토큰에는 포함 될 수 없습니다. 소비자 계정은 "사용자 유형" 열에 표시 된 이러한 클레임의 하위 집합을 지원 합니다.  나열 된 많은 클레임은 소비자 사용자에 게 적용 되지 않습니다 (테 넌 트가 없으므로 `tenant_ctry`에 값이 없음).  

**표 2: v1.0 및 v2.0 선택적 클레임 집합**

| Név                       |  Leírás   | 토큰 형식 | 사용자 유형 | Megjegyzések  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | 사용자가 마지막으로 인증 한 시간입니다. Openid connect Connect 사양을 참조 하세요.| JWT        |           |  |
| `tenant_region_scope`      | 리소스 테 넌 트의 지역입니다. | JWT        |           | |
| `home_oid`                 | 게스트 사용자의 경우 사용자의 홈 테 넌 트에 있는 사용자의 개체 ID입니다.| JWT        |           | |
| `sid`                      | 세션 별 사용자 로그 아웃에 사용 되는 세션 ID입니다. | JWT        |  개인 및 Azure AD 계정.   |         |
| `platf`                    | 디바이스 플랫폼    | JWT        |           | 장치 유형을 확인할 수 있는 관리 되는 장치로 제한 됩니다.|
| `verified_primary_email`   | 사용자의 PrimaryAuthoritativeEmail 원본      | JWT        |           |         |
| `verified_secondary_email` | 사용자의 SecondaryAuthoritativeEmail 원본   | JWT        |           |        |
| `enfpolids`                | 적용 되는 정책 Id입니다. 현재 사용자에 대해 평가 된 정책 Id의 목록입니다. | JWT |  |  |
| `vnet`                     | VNET 지정자 정보입니다. | JWT        |           |      |
| `fwd`                      | IP 주소입니다.| JWT    |   | 요청 클라이언트의 원래 IPv4 주소를 추가 합니다 (VNET 내에 있는 경우). |
| `ctry`                     | 사용자 국가 | JWT |  | Azure AD는 선택적 클레임 (있는 경우)을 `ctry` 반환 하 고 클레임 값은 FR-FR, JP, SZ 등의 표준 2 자로 된 국가 코드입니다. |
| `tenant_ctry`              | 리소스 테 넌 트의 국가 | JWT | | |
| `xms_pdl`          | 기본 데이터 위치   | JWT | | 다중 지역 테 넌 트의 경우이는 사용자가 거주 하는 지리적 지역을 표시 하는 세 문자로 된 코드입니다. 자세한 내용은 [기본 데이터 위치에 대 한 Azure AD Connect 설명서](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)를 참조 하세요.<br/>예: 아시아 태평양에 대 한 `APC`입니다. |
| `xms_pl`                   | 사용자 기본 설정 언어  | JWT ||설정 된 경우 사용자의 기본 설정 언어입니다. 게스트 액세스 시나리오의 홈 테 넌 트에서 원본으로 사용 됩니다. 형식이 지정 된 LL-CC ("en-us"). |
| `xms_tpl`                  | 테 넌 트 기본 설정 언어| JWT | | 설정 된 경우 리소스 테 넌 트의 기본 설정 언어입니다. 서식 있는 LL ("en"). |
| `ztdid`                    | 0 터치 배포 ID | JWT | | [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) 에 사용 되는 장치 id |
| `email`                    | 사용자가 있는 경우이 사용자의 주소 지정 가능한 전자 메일입니다.  | JWT, SAML | MSA, Azure AD | 사용자가 테 넌 트의 게스트 인 경우이 값은 기본적으로 포함 됩니다.  관리 되는 사용자 (테 넌 트 내에 있는 사용자)의 경우이 선택적 클레임을 통해 또는 Openid connect 범위를 사용 하 여 v2.0 에서만 요청 해야 합니다.  관리 되는 사용자의 경우 전자 메일 주소는 [Office 관리 포털](https://portal.office.com/adminportal/home#/users)에서 설정 해야 합니다.| 
| `groups`| 그룹 클레임에 대 한 선택적 서식 지정 |JWT, SAML| |[응용 프로그램 매니페스트에서](reference-app-manifest.md)설정 해야 하는 GroupMembershipClaims 설정과 함께 사용 됩니다. 자세한 내용은 아래에 있는 [그룹 클레임](#configuring-groups-optional-claims) 을 참조 하세요. 그룹 클레임에 대 한 자세한 내용은 [그룹 클레임을 구성 하는 방법을](../hybrid/how-to-connect-fed-group-claims.md) 참조 하세요.
| `acct`             | 테 넌 트의 사용자 계정 상태입니다. | JWT, SAML | | 사용자가 테 넌 트의 멤버인 경우 값은 `0`입니다. 게스트가 면 값이 `1`됩니다. |
| `upn`                      | UserPrincipalName 클레임입니다. | JWT, SAML  |           | 이 클레임은 자동으로 포함 되지만 추가 속성을 연결 하 여 게스트 사용자 사례에서 동작을 수정 하는 선택적 클레임으로 지정할 수 있습니다.  |

## <a name="v20-specific-optional-claims-set"></a>v2.0 별 선택적 클레임 집합

이러한 클레임은 항상 v 1.0 Azure AD 토큰에 포함 되지만 요청 하지 않는 한 v2.0 토큰에는 포함 되지 않습니다. 이러한 클레임은 JWTs (ID 토큰 및 액세스 토큰)에만 적용 됩니다. 

**표 3: v2.0 전용 선택적 클레임**

| JWT 클레임     | Név                            | Leírás                                | Megjegyzések |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-cím                      | 클라이언트에서 로그인 한 IP 주소입니다.   |       |
| `onprem_sid`  | 온-프레미스 보안 식별자 |                                             |       |
| `pwd_exp`     | 암호 만료 시간        | 암호가 만료 되는 날짜/시간입니다. |       |
| `pwd_url`     | 암호 변경 URL             | 사용자가 암호를 변경 하기 위해 방문할 수 있는 URL입니다.   |   |
| `in_corp`     | 회사 네트워크 내부        | 클라이언트가 회사 네트워크에서 로그인 하는 경우 신호를 보냅니다. 그렇지 않으면 클레임이 포함 되지 않습니다.   |  MFA의 [신뢰할 수 있는 ip](../authentication/howto-mfa-mfasettings.md#trusted-ips) 설정에 따라 달라 집니다.    |
| `nickname`    | Becenév                        | 이름 및 성에서 구분 되는 사용자의 추가 이름입니다. | 
| `family_name` | Vezetéknév                       | 사용자 개체에 정의 된 대로 사용자의 성, 성 또는 패밀리 이름을 제공 합니다. <br>"family_name": "서 면" | MSA 및 Azure AD에서 지원 됨   |
| `given_name`  | Utónév                      | 사용자 개체에 설정 된 대로 사용자의 첫 번째 또는 "지정 된" 이름을 제공 합니다.<br>"given_name": "Frank"                   | MSA 및 Azure AD에서 지원 됨  |
| `upn`         | Felhasználó egyszerű neve | Username_hint 매개 변수와 함께 사용할 수 있는 사용자의 식별자입니다.  사용자에 대 한 지 속성 식별자가 아니라 데이터를 키 하는 데 사용 하면 안 됩니다. | 클레임의 구성에 대해서는 아래의 [추가 속성](#additional-properties-of-optional-claims) 을 참조 하세요. |

### <a name="additional-properties-of-optional-claims"></a>선택적 클레임의 추가 속성

일부 선택적 클레임을 구성 하 여 클레임이 반환 되는 방식을 변경할 수 있습니다. 이러한 추가 속성은 일반적으로 다양 한 데이터 기대치를 사용 하 여 온-프레미스 응용 프로그램을 마이그레이션하는 데 도움이 됩니다. 예를 들어 `include_externally_authenticated_upn_without_hash`는 UPN의 해시 표시 (`#`)를 처리할 수 없는 클라이언트에 도움이 됩니다.

**표 4: 선택적 클레임을 구성 하기 위한 값**

| Tulajdonság neve  | 추가 속성 이름 | Leírás |
|----------------|--------------------------|-------------|
| `upn`          |                          | SAML 및 JWT 응답 모두와 v1.0 및 v2.0 토큰에 대해 사용할 수 있습니다. |
|                | `include_externally_authenticated_upn`  | 리소스 테 넌 트에 저장 된 게스트 UPN을 포함 합니다. Például: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | 위와 동일 합니다. 단, 해시 표시 (`#`)를 밑줄 (`_`)로 대체 한다는 점을 제외 하면 `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>추가 속성 예제

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

이 OptionalClaims 개체를 사용 하면 클라이언트에 반환 되는 ID 토큰이 추가 홈 테 넌 트 및 리소스 테 넌 트 정보와 함께 다른 upn을 포함 합니다. 사용자가 테 넌 트의 게스트 (인증에 다른 IDP를 사용) 인 경우에만 토큰의 클레임 `upn` 변경 됩니다. 

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

> [!IMPORTANT]
> 액세스 토큰은 **항상** 클라이언트가 아닌 리소스의 매니페스트를 사용 하 여 생성 됩니다.  따라서 요청 `...scope=https://graph.microsoft.com/user.read...` 리소스는 그래프입니다.  따라서 액세스 토큰은 클라이언트의 매니페스트가 아닌 그래프 매니페스트를 사용 하 여 만들어집니다.  응용 프로그램에 대 한 매니페스트를 변경 하면 그래프의 토큰이 다르게 표시 되지 않습니다.  `accessToken` 변경 내용이 적용 되는지 확인 하기 위해 다른 앱이 아닌 응용 프로그램에 대 한 토큰을 요청 합니다.  

UI 또는 응용 프로그램 매니페스트를 통해 응용 프로그램에 대 한 선택적 클레임을 구성할 수 있습니다.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. 인증 한 후에는 페이지의 오른쪽 위 모서리에서 Azure AD 테 넌 트를 선택 하 여 선택 합니다.
1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택 합니다.
1. **관리** 섹션에서 **앱 등록**을 선택 합니다.
1. 목록에서 선택적 클레임을 구성 하려는 응용 프로그램을 선택 합니다.

**UI를 통해 선택적 클레임 구성:**

[![UI를 사용 하 여 선택적 클레임을 구성 하는 방법을 보여 줍니다.](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. **관리** 섹션에서 **토큰 구성 (미리 보기)** 을 선택 합니다.
2. **선택적 클레임 추가**를 선택 합니다.
3. 구성 하려는 토큰 유형을 선택 합니다.
4. 추가할 선택적 클레임을 선택 합니다.
5. Kattintson a **Hozzáadás** parancsra.

**응용 프로그램 매니페스트를 통해 선택적 클레임 구성:**

[![응용 프로그램 매니페스트를 사용 하 여 선택적 클레임을 구성 하는 방법을 보여 줍니다.](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. **관리** 섹션에서 **매니페스트**를 선택 합니다. 웹 기반 매니페스트 편집기가 열리고 매니페스트를 편집할 수 있습니다. Másik lehetőségként a **Letöltés** lehetőséget választva a helyi gépen is szerkesztheti az alkalmazásjegyzéket, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra. 응용 프로그램 매니페스트에 대 한 자세한 내용은 [AZURE AD 응용 프로그램 매니페스트 이해 문서](reference-app-manifest.md)를 참조 하세요.

    다음 응용 프로그램 매니페스트 항목은 ID, 액세스 및 SAML 토큰에 auth_time, ipaddr 및 upn 선택적 클레임을 추가 합니다.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
       ```

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   기본적으로 group Objectid은 그룹 클레임 값에 내보내집니다.  온-프레미스 그룹 특성을 포함 하도록 클레임 값을 수정 하거나 클레임 유형을 역할로 변경 하려면 다음과 같이 OptionalClaims 구성을 사용 합니다.

3. 그룹 이름 구성 선택적 클레임을 설정 합니다.

   선택적 클레임 섹션에서 온-프레미스 AD 그룹 특성을 포함 하도록 토큰의 그룹을 지정 하려는 경우에는 선택적 클레임을 적용 해야 하는 토큰 유형, 요청 된 선택적 클레임의 이름 및 필요한 추가 속성을 지정 합니다.  여러 토큰 유형이 나열 될 수 있습니다.

   - OIDC ID 토큰에 대 한 idToken
   - OAuth/OIDC 액세스 토큰에 대 한 accessToken
   - SAML 토큰에 대 한 Saml2Token.

   > [!NOTE]
   > Saml2Token 형식은 SAML 1.1 및 SAML 2.0 형식 토큰에 모두 적용 됩니다.

   관련 된 각 토큰 유형에 대해 그룹 클레임을 수정 하 여 매니페스트의 OptionalClaims 섹션을 사용 합니다. OptionalClaims 스키마는 다음과 같습니다.

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | 선택적 클레임 스키마 | Value (Díj) |
   |----------|-------------|
   | **name:** | "그룹" 이어야 합니다. |
   | **원본** | 사용되지 않습니다. Null 생략 또는 지정 |
   | **essential:** | 사용되지 않습니다. False를 생략 하거나 지정 합니다. |
   | **AdditionalProperties** | 추가 속성의 목록입니다.  유효한 옵션은 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles"입니다. |

   AdditionalProperties에서 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" 중 하나만 필요 합니다.  둘 이상의가 있는 경우 첫 번째가 사용 되 고 나머지는 무시 됩니다.

   일부 응용 프로그램에는 역할 클레임의 사용자에 대 한 그룹 정보가 필요 합니다.  클레임 유형을 그룹 클레임에서 역할 클레임으로 변경 하려면 추가 속성에 "emit_as_roles"을 추가 합니다.  그룹 값은 역할 클레임에 내보내집니다.

   > [!NOTE]
   > "Emit_as_roles"을 사용 하는 경우 사용자가 할당 된 것으로 구성 된 응용 프로그램 역할은 역할 클레임에 표시 되지 않습니다.

**예:**

1) Dnsdomainnamenameformat 형식의 OAuth 액세스 토큰에서 그룹을 그룹 이름으로 내보냅니다.

    
    **UI 구성:**

    [![UI를 사용 하 여 선택적 클레임을 구성 하는 방법을 보여 줍니다.](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **응용 프로그램 매니페스트 항목:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) SAML 및 OIDC ID 토큰에서 역할 클레임으로 netbiosDomain\sAMAccountName 형식으로 반환 될 그룹 이름을 내보냅니다.

    **UI 구성:**

    [![UI를 사용 하 여 선택적 클레임을 구성 하는 방법을 보여 줍니다.](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **응용 프로그램 매니페스트 항목:**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>선택적 클레임 예

이 섹션에서는 응용 프로그램에 대 한 선택적 클레임 기능을 사용 하는 방법을 확인 하기 위해 시나리오를 살펴볼 수 있습니다.
선택적 클레임을 사용 하도록 설정 하 고 구성 하기 위해 응용 프로그램의 id 구성에 대 한 속성을 업데이트 하는 데 사용할 수 있는 여러 옵션이 있습니다.
-    **토큰 구성 (미리 보기)** UI를 사용할 수 있습니다 (아래 예제 참조).
-    **매니페스트** 를 사용할 수 있습니다 (아래 예제 참조). 매니페스트에 대 한 소개는 먼저 [AZURE AD 응용 프로그램 매니페스트 이해 문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) 를 참조 하세요.
-   [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) 를 사용 하 여 응용 프로그램을 업데이트 하는 응용 프로그램을 작성할 수도 있습니다. Graph API 참조 가이드의 [엔터티 및 복합 형식 참조](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) 는 선택적 클레임을 구성 하는 데 도움이 될 수 있습니다.

**예:** 아래 예제에서는 **토큰 구성 (미리 보기)** UI 및 **매니페스트** 를 사용 하 여 응용 프로그램에 적합 한 액세스, ID 및 SAML 토큰에 선택적 클레임을 추가 합니다. 응용 프로그램에서 받을 수 있는 각 토큰 유형에는 다음과 같은 다양 한 선택적 클레임이 추가 됩니다.
-    이제 ID 토큰에는 페더레이션된 사용자에 대 한 UPN이 전체 형식 (`<upn>_<homedomain>#EXT#@<resourcedomain>`)으로 포함 됩니다.
-    다른 클라이언트가이 응용 프로그램에 대해 요청 하는 액세스 토큰은 이제 auth_time 클레임을 포함 합니다.
-    이제 SAML 토큰에는 ab603c56068041afb2f6832e2a17e237 Ypeid 디렉터리 스키마 확장이 포함 됩니다 .이 예제에서는이 앱에 대 한 앱 ID가입니다. SAML 토큰은 `extension_skypeId`으로 Skype ID를 노출 합니다.

**UI 구성:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. 인증 한 후에는 페이지의 오른쪽 위 모서리에서 Azure AD 테 넌 트를 선택 하 여 선택 합니다.

1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택 합니다.

1. **관리** 섹션에서 **앱 등록**을 선택 합니다.

1. 목록에서 선택적 클레임을 구성 하려는 응용 프로그램을 찾아 클릭 합니다.

1. **관리** 섹션에서 **토큰 구성 (미리 보기)** 을 클릭 합니다.

1. **선택적 클레임 추가**를 선택 하 고 **ID** 토큰 유형을 선택한 다음 클레임 목록에서 **Upn** 을 선택 하 고 **추가**를 클릭 합니다.

1. **선택적 클레임 추가**를 선택 하 고 **, 액세스** 토큰 유형을 선택 하 고, 클레임 목록에서 **Auth_time** 을 선택한 다음, **추가**를 클릭 합니다.

1. 토큰 구성 개요 화면에서 **upn**옆의 연필 아이콘을 클릭 하 고 **외부에서 인증** 된 토글을 클릭 한 다음 **저장**을 클릭 합니다.

1. **선택적인 클레임 추가**를 선택 하 고 **, SAML** 토큰 유형을 선택 하 고, 클레임 목록에서 **extn.** 서 용을 선택 하 고 (사용자가 Azure AD 사용자 개체를 만든 경우에만 해당) **추가**를 클릭 합니다.

    [![UI를 사용 하 여 선택적 클레임을 구성 하는 방법을 보여 줍니다.](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**매니페스트 구성:**
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. 인증 한 후에는 페이지의 오른쪽 위 모서리에서 Azure AD 테 넌 트를 선택 하 여 선택 합니다.
1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택 합니다.
1. 목록에서 선택적 클레임을 구성 하려는 응용 프로그램을 찾아 클릭 합니다.
1. **관리** 섹션에서 **매니페스트** 를 클릭 하 여 인라인 매니페스트 편집기를 엽니다.
1. 이 편집기를 사용 하 여 매니페스트를 직접 편집할 수 있습니다. 매니페스트는 [응용 프로그램 엔터티]의 스키마를 따릅니다. (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) 및 자동으로 저장 된 매니페스트의 서식을 지정 합니다. 새 요소가 `OptionalClaims` 속성에 추가 됩니다.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
