---
title: Azure Active Directory를 사용 하 여 응용 프로그램에 대 한 그룹 클레임 구성 | 마이크로 소프트 문서
description: Azure AD와 함께 사용할 그룹 클레임을 구성하는 방법에 대한 정보입니다.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408405"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Azure Active Directory(공개 미리 보기)를 사용 하 여 응용 프로그램에 대 한 그룹 클레임을 구성 합니다.

Azure Active Directory는 응용 프로그램 내에서 사용할 수 있는 토큰의 사용자 그룹 구성원 정보를 제공할 수 있습니다.  두 가지 주요 패턴이 지원됩니다.

- Azure Active Directory 개체 식별자(OID) 특성으로 식별된 그룹(일반적으로 사용 가능)
- AD(활성 디렉터리) 동기화된 그룹 및 사용자에 대한 sAMAccountName 또는 GroupSID 특성으로 식별된 그룹(공개 미리 보기)

> [!IMPORTANT]
> 이 미리 보기 기능에 대 한 주의 해야 할 몇 가지 주의 사항이 있습니다.
>
>- 온-프레미스에서 동기화된 sAMAccountName 및 SID(보안 식별자) 특성 의 사용 지원은 AD FS 및 기타 ID 공급자로부터 기존 응용 프로그램을 이동할 수 있도록 설계되었습니다. Azure AD에서 관리되는 그룹에는 이러한 클레임을 내관리하는 데 필요한 특성이 포함되어 있지 않습니다.
>- 대규모 조직에서는 사용자가 속한 그룹 수가 Azure Active Directory가 토큰에 추가하는 제한을 초과할 수 있습니다. SAML 토큰의 경우 150개 그룹, JWT의 경우 200개 이로 인해 예기치 않은 결과가 발생할 수 있습니다. 사용자에게 그룹 구성원 자격이 많은 경우 클레임에서 내보낸 그룹을 응용 프로그램의 관련 그룹으로 제한하는 옵션을 사용하는 것이 좋습니다.  
>- 새 응용 프로그램 개발또는 응용 프로그램을 구성할 수 있고 중첩된 그룹 지원이 필요하지 않은 경우 인앱 권한 부여는 그룹이 아닌 응용 프로그램 역할을 기반으로 하는 것이 좋습니다.  이렇게 하면 토큰으로 이동해야 하는 정보의 양이 제한되고, 더 안전하며, 사용자 할당을 앱 구성과 구분합니다.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>AD FS 및 기타 ID 공급자에서 마이그레이션하는 응용 프로그램에 대한 그룹 클레임

AD FS로 인증하도록 구성된 많은 응용 프로그램은 Windows AD 그룹 특성의 형태로 그룹 구성원 정보를 사용합니다.   이러한 특성은 도메인 이름또는 Windows 그룹 보안 식별자(GroupSID)로 정규화된 그룹 sAMAccountName입니다.  응용 프로그램이 AD FS와 페더레이션되면 AD FS는 TokenGroups 함수를 사용하여 사용자에 대한 그룹 구성원 자격을 검색합니다.

AD FS에서 이동된 앱은 동일한 형식의 클레임이 필요합니다. 그룹 및 역할 클레임은 그룹의 Azure Active Directory objectID가 아닌 활성 디렉터리에서 동기화된 도메인 정규화된 sAMAccountName 또는 그룹SID를 포함하는 Azure Active Directory에서 내보내질 수 있습니다.

그룹 클레임에 대해 지원되는 형식은 다음과 같습니다.

- **Azure Active Directory 그룹 ObjectId(모든** 그룹에 사용 가능)
- **sAMAccountName(Active** Directory에서 동기화된 그룹에 사용 가능)
- **NetbiosDomain\sAMAccountName(Active** Directory에서 동기화된 그룹에 사용 가능)
- **DNSDomainName\sAMAccountName(Active** Directory에서 동기화된 그룹에 사용 가능)
- **온-프레미스 그룹 보안 식별자(Active** Directory에서 동기화된 그룹에 사용 가능)

> [!NOTE]
> sAMAccountName 및 온프레미스 그룹 SID 특성은 Active Directory에서 동기화된 그룹 개체에서만 사용할 수 있습니다.   Azure Active Directory 또는 Office365에서 만든 그룹에서는 사용할 수 없습니다.   Azure Active Directory에서 동기화된 온-프레미스 그룹 특성에 대해 동기화된 그룹에 대해서만 구성된 응용 프로그램은 동기화된 그룹에 대해서만 가져옵니다.

## <a name="options-for-applications-to-consume-group-information"></a>그룹 정보를 사용하는 응용 프로그램에 대한 옵션

응용 프로그램은 MS Graph 그룹 끝점을 호출하여 인증된 사용자에 대한 그룹 정보를 얻을 수 있습니다. 이 호출을 통해 사용자가 속한 모든 그룹을 많은 수의 관련 그룹이 있는 경우에도 사용할 수 있습니다.  그런 다음 그룹 열거형은 토큰 크기 제한과 는 독립적입니다.

그러나 기존 응용 프로그램에서 클레임을 통해 그룹 정보를 사용할 것으로 예상되는 경우 Azure Active Directory는 여러 가지 클레임 형식으로 구성할 수 있습니다.  다음 옵션을 살펴보세요.

- 응용 프로그램 내 권한 부여 를 위해 그룹 구성원자격을 사용하는 경우 그룹 ObjectID를 사용하는 것이 좋습니다. 그룹 ObjectID는 Azure Active Directory에서 변경할 수 없고 고유하며 모든 그룹에 사용할 수 있습니다.
- 온-프레미스 그룹 sAMAccountName을 사용 하 여 권한 부여를 위해 도메인 자격을 갖춘 이름을 사용 하는 경우;  이름이 충돌할 가능성이 적습니다. sAMAccountNameActive Directory 도메인 내에서 고유할 수 있지만 하나 이상의 Active Directory 도메인이 Azure Active Directory 테넌트와 동기화되는 경우 두 개 이상의 그룹이 동일한 이름을 가질 수 있습니다.
- 응용 [프로그램 역할을](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) 사용하여 그룹 구성원 자격과 응용 프로그램 간에 간접 계층을 제공하는 것이 좋습니다.   그런 다음 응용 프로그램은 토큰의 역할 조개에 따라 내부 권한 부여 결정을 내보합니다.
- Active Directory에서 동기화된 그룹 특성을 얻도록 응용 프로그램이 구성되고 그룹에 해당 특성이 포함되지 않은 경우 클레임에는 포함되지 않습니다.
- 토큰의 그룹 클레임에는 응용 프로그램에 할당된 그룹으로 그룹 클레임을 제한하는 옵션을 사용하는 경우를 제외하고 중첩된 그룹이 포함됩니다.  사용자가 GroupB의 구성원이고 GroupB가 GroupA의 구성원인 경우 사용자에 대한 그룹 클레임에는 GroupA와 GroupB가 모두 포함됩니다. 조직의 사용자가 그룹 구성원 자격이 많은 경우 토큰에 나열된 그룹 수가 토큰 크기를 늘릴 수 있습니다.  Azure Active Directory는 토큰에서 방출할 그룹 수를 SAML 어설션의 경우 150개, JWT의 경우 200개로 제한합니다.  사용자가 더 많은 수의 그룹의 구성원인 경우 그룹이 생략되고 그룹 정보를 얻기 위한 Graph 끝점에 대한 링크가 대신 포함됩니다.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Active Directory에서 동기화된 그룹 특성을 사용하기 위한 필수 구성 조건

ObjectId 형식을 사용하는 경우 그룹 멤버 자격 클레임은 모든 그룹에 대해 토큰으로 내보내질 수 있습니다. 그룹 클레임을 ObjectId 그룹 이외의 형식으로 사용하려면 Azure AD Connect를 사용하여 Active Directory에서 그룹을 동기화해야 합니다.

Active Directory 그룹에 대한 그룹 이름을 내보내도록 Azure Active Directory를 구성하는 두 단계가 있습니다.

1. **Active Directory에서 그룹 이름 동기화** Azure Active Directory가 그룹 또는 역할 클레임에서 그룹 이름 또는 온프레미스 그룹 SID를 내보내야 합니다.  Azure AD Connect 버전 1.2.70 이상을 실행해야 합니다.   이전 버전의 Azure AD Connect 1.2.70은 Active Directory에서 그룹 개체를 동기화하지만 필요한 그룹 이름 특성은 포함하지 않습니다.  현재 버전으로 업그레이드합니다.

2. **Azure Active Directory에서 그룹 클레임을 토큰에 포함하도록 응용 프로그램 등록을 구성합니다.** 그룹 클레임은 포털의 엔터프라이즈 응용 프로그램 섹션에서 구성하거나 응용 프로그램 등록 섹션에서 응용 프로그램 매니페스트를 사용할 수 있습니다.  응용 프로그램 매니페스트에서 그룹 클레임을 구성하려면 아래 "그룹 특성에 대한 Azure Active Directory 응용 프로그램 등록 구성"을 참조하십시오.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>SSO 구성을 사용하여 SAML 응용 프로그램의 토큰에 그룹 클레임 추가

갤러리 또는 비 갤러리 SAML 응용 프로그램에 대한 그룹 클레임을 구성하려면 **엔터프라이즈 응용 프로그램을**열고 목록에서 응용 프로그램을 클릭하고 단일 **사인온 구성을**선택한 다음 **사용자 특성 & 클레임을 선택합니다.**

그룹 **클레임 추가를 클릭합니다.**  

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

라디오 버튼을 사용하여 토큰에 포함할 그룹을 선택합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 선택 | 설명 |
|----------|-------------|
| **모든 그룹** | 보안 그룹 및 메일 그룹 및 역할을 내보엠보임합니다.  |
| **보안 그룹** | 사용자가 그룹 클레임의 구성원인 보안 그룹을 내보전합니다. |
| **디렉터리 역할** | 사용자에게 디렉터리 역할이 할당된 경우 '위드' 클레임으로 내보내지며(그룹 클레임은 내보내지지 않음) |
| **응용 프로그램에 할당된 그룹** | 응용 프로그램에 명시적으로 할당된 그룹만 내보전하고 사용자는 |

예를 들어 사용자가 속한 모든 보안 그룹을 내보하려면 보안 그룹을 선택합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Azure AD objectID 대신 Active Directory에서 동기화된 Active Directory 특성을 사용하여 그룹을 내보내려면 드롭다운에서 필요한 형식을 선택합니다. Active Directory에서 동기화된 그룹만 클레임에 포함됩니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

응용 프로그램에 할당된 그룹만 내보하려면 **응용 프로그램에 할당된 그룹을** 선택합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

응용 프로그램에 할당된 그룹이 토큰에 포함됩니다.  사용자가 멤버인 다른 그룹은 생략됩니다.  이 옵션을 사용하면 중첩된 그룹이 포함되지 않으며 사용자는 응용 프로그램에 할당된 그룹의 직접 구성원이어야 합니다.

응용 프로그램에 할당된 그룹을 변경하려면 엔터프라이즈 응용 프로그램 목록에서 응용 프로그램을 선택한 다음 응용 프로그램의 왼쪽 탐색 메뉴에서 **사용자 및 그룹을** **클릭합니다.**

응용 프로그램에 대한 그룹 할당 관리에 대한 자세한 내용은 [엔터프라이즈 앱에 사용자 또는 그룹을 할당하는](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) 문서를 참조하십시오.

### <a name="advanced-options"></a>고급 옵션

그룹 클레임이 내보내는 방법은 고급 옵션아래의 설정에 의해 수정될 수 있습니다.

그룹 클레임 이름 사용자 지정: 선택한 경우 그룹 클레임에 대해 다른 클레임 유형을 지정할 수 있습니다.   이름 필드에 클레임 유형과 네임스페이스 필드에 클레임에 대한 선택적 네임스페이스를 입력합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

일부 응용 프로그램에서는 그룹 구성원 정보가 '역할' 클레임에 표시되도록 요구합니다. 선택적으로 '역할 클레임 내역' 상자를 선택적으로 선택적으로 사용자의 그룹을 역할로 내보사할 수 있습니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 역할로 그룹 데이터를 내보전하는 옵션이 사용되는 경우 역할 클레임에 그룹만 표시됩니다.  사용자에게 할당된 모든 응용 프로그램 역할은 역할 클레임에 나타나지 않습니다.

### <a name="edit-the-group-claims-configuration"></a>그룹 클레임 구성 편집

그룹 클레임 구성이 사용자 속성 & 클레임 구성에 추가되면 그룹 클레임을 추가하는 옵션이 회색으로 표시됩니다.  그룹 클레임 구성을 변경하려면 **추가 클레임** 목록에서 그룹 클레임을 클릭합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>그룹 특성에 대한 Azure AD 응용 프로그램 등록 구성

그룹 클레임은 [응용 프로그램 매니페스트의](../../active-directory/develop/reference-app-manifest.md) [선택적 클레임](../../active-directory/develop/active-directory-optional-claims.md) 섹션에서 구성할 수도 있습니다.

1. 포털 ->Azure Active Directory -> 응용 프로그램 등록->응용 프로그램 >매니페스트 선택

2. 그룹 멤버 자격 클레임을 변경 하 여 그룹 구성원 클레임을 사용 합니다.

유효한 값은 다음과 같습니다.

| 선택 | 설명 |
|----------|-------------|
| **"모든 것"** | 보안 그룹, 메일 그룹 및 역할 내림표 |
| **"보안 그룹"** | 사용자가 그룹 클레임의 구성원인 보안 그룹을 내보전합니다. |
| **"디렉토리 역할** | 사용자에게 디렉터리 역할이 할당된 경우 '위드' 클레임으로 내보내지며(그룹 클레임은 내보내지지 않음) |
| **"응용 프로그램 그룹** | 응용 프로그램에 명시적으로 할당된 그룹만 내보전하고 사용자는 |

   예를 들어:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   기본적으로 그룹 ObjectID는 그룹 클레임 값에서 내보내집니다.  온-프레미스 그룹 특성을 포함하도록 클레임 값을 수정하거나 클레임 유형을 역할로 변경하려면 다음과 같이 OptionalClaims 구성을 사용합니다.

3. 그룹 이름 구성 선택적 클레임을 설정합니다.

   토큰의 그룹이 온-프레미스 AD 그룹 특성을 포함하도록 하려면 선택적 클레임 섹션에 적용할 토큰 유형 선택적 클레임을 지정합니다.  여러 토큰 유형을 나열할 수 있습니다.

   - OIDC ID 토큰에 대한 id토큰
   - OAuth/OIDC 액세스 토큰에 대한 액세스 토큰
   - Saml2토큰에 대한 SAML 토큰입니다.

   > [!NOTE]
   > Saml2Token 형식은 SAML1.1 및 SAML2.0 형식 토큰 모두에 적용됩니다.

   각 관련 토큰 유형에 대해 매니페스트에서 OptionalClaims 섹션을 사용하도록 그룹 클레임을 수정합니다. 선택적 클레임 스키마는 다음과 같습니다.

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 선택적 클레임 스키마 | 값 |
   |----------|-------------|
   | **이름:** | "그룹"이어야 합니다. |
   | **소스:** | 사용되지 않습니다. null을 생략하거나 지정 |
   | **필수:** | 사용되지 않습니다. 거짓 을 생략하거나 지정 |
   | **추가 속성:** | 추가 속성 목록입니다.  유효한 옵션은 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles"입니다. |

   추가속성 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name"중 하나만 필요합니다.  두 개 이상이 있으면 첫 번째 가 사용되고 다른 하나는 무시됩니다.

   일부 응용 프로그램에는 역할 클레임의 사용자에 대한 그룹 정보가 필요합니다.  클레임 유형을 그룹 클레임에서 역할 클레임으로 변경하려면 추가 속성에 "emit_as_roles"를 추가합니다.  그룹 값은 역할 클레임에서 내보내집니다.

   > [!NOTE]
   > "emit_as_roles"를 사용하는 경우 사용자에게 할당된 응용 프로그램 역할이 역할 클레임에 나타나지 않습니다.

### <a name="examples"></a>예

dnsDomainName\SAMAccountName 형식의 OAuth 액세스 토큰에서 그룹 이름으로 그룹 이름을 내보임

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

SAML 및 OIDC ID 토큰에서 클레임되는 역할로 netbiosDomain\samAccountName 형식으로 반환할 그룹 이름을 내보내려면 다음을 수행합니다.

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
 }
 ```

## <a name="next-steps"></a>다음 단계

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[역할 클레임 구성](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
