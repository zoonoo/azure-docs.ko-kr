---
title: Azure Active Directory를 사용 하 여 응용 프로그램에 대 한 그룹 클레임 구성 | Microsoft Docs
description: Azure AD를 사용 하 여 사용 하기 위해 그룹 클레임을 구성 하는 방법에 대 한 정보입니다.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 622a3ce0f80bd09bd09fa7ff097f68155318142d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080359"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Azure Active Directory (공개 미리 보기)를 사용 하 여 응용 프로그램에 대 한 그룹 클레임 구성

Azure Active Directory 응용 프로그램에서 사용할 수 있도록 토큰에 사용자 그룹 멤버 자격 정보를 제공할 수 있습니다.  두 가지 주요 패턴이 지원 됩니다.

- 해당 Azure Active Directory 개체 식별자 (OID) (일반 공급)로 식별 되는 그룹
- Active Directory (AD) 동기화 그룹 및 사용자 (공개 미리 보기)에 대 한 SAMAccountName 또는 GroupSID로 식별 되는 그룹

> [!Note]
> 이름 및 온-프레미스 보안 식별자 (Sid) 사용에 대 한 지원은 AD FS에서 기존 응용 프로그램을 이동할 수 있도록 설계 되었습니다.    Azure AD에서 관리 그룹 이러한 클레임을 내보내는 데 필요한 특성을 포함 하지 않습니다.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-idps"></a>AD FS 및 다른 Idp에서 마이그레이션하는 응용 프로그램에 대 한 그룹 클레임

AD FS를 사용 하 여 인증 하도록 구성 된 많은 응용 프로그램 그룹 멤버 자격 정보 Windows AD 그룹 특성의 형태로 사용 합니다.   이러한 특성은 도메인에서 정규화 된 이름 수는 SAMAccountName, 그룹 또는 Windows 그룹 SID입니다.  AD FS와 페더레이션 되어 있는 응용 프로그램을 AD FS는 사용자의 그룹 멤버 자격을 검색할 TokenGroups 함수를 사용 합니다.

AD FS에서 앱을 수신할 토큰 맞도록 그룹 및 역할 클레임을 내보낼 수 있습니다 그룹의 Azure Active Directory objectID 아닌 SAMAccountName 정규화 된 도메인을 포함 합니다.

그룹 클레임에 대 한 지원 되는 형식은 다음과 같습니다.

- **Azure Active Directory GroupObjectId** (모든 그룹에 대 한 사용 가능)
- **SAMAccountName** (Active Directory에서 동기화 그룹에 대 한 사용 가능)
- **NetbiosDomain\samAccountName** (Active Directory에서 동기화 그룹에 대 한 사용 가능)
- **DNSDomainName\samAccountName** (Active Directory에서 동기화 그룹에 대 한 사용 가능)

> [!NOTE]
> SAMAccountName 및 OnPremisesGroupSID 특성 에서만 Active Directory에서 동기화 된 그룹 개체에 제공 됩니다.   Azure Active Directory 또는 office 365에서 만든 그룹에 사용할 수 없습니다.   온-프레미스 그룹 특성에 종속 된 응용 프로그램을 이용 하면 동기화 된 그룹에만 얻으십시오.

## <a name="options-for-applications-to-consume-group-information"></a>그룹 정보를 사용 하는 응용 프로그램에 대 한 옵션

그룹 정보를 얻으려면 응용 프로그램에 대 한 한 가지 방법은 인증된 된 사용자의 그룹 멤버 자격을 검색 하기 위해 그래프 그룹 끝점을 호출 하는 것입니다. 이 호출을 통해 사용자의 멤버는 관련 된 많은 수의 그룹 및 응용 프로그램은 사용자가 멤버인 모든 그룹 열거 해야 하는 경우에 사용할 수는 모든 그룹입니다.  그룹 열거형 토큰 크기 제한 무관 합니다.

그러나 기존 응용 프로그램이 이미 수신한 토큰에 클레임을 통해 그룹 정보를 사용 하는, Azure Active Directory는 다양 한 응용 프로그램의 요구에 맞게 다양 한 클레임 옵션을 사용 하 여 구성할 수 있습니다.  이때 다음 옵션을 사용할 수 있습니다.

- 그룹 멤버 자격을 사용 하 여 (여부 그룹 멤버 자격 가져옴 그래프 또는 토큰에서) 하는 응용 프로그램 권한 부여 목적을 위해 때 변경할 수 없는 및 Azure Active Directory에서 고유 및 모든 그룹에 사용할 수 있는 그룹의 ObjectID를 사용 하는 것이 좋습니다. .
- 권한 부여에 대 한 그룹 SAMAccountName을 사용 하는 경우 정규화 된 도메인 이름을; 사용  가 발생 하는 경우 가능성이 낮은 있었습니다 이름 충돌 합니다. 자체적으로 SAMAccountName을 Active Directory 도메인 내에서 고유 하지만 둘 이상의 Active Directory 도메인을 Azure Active Directory 테 넌 트와 동기화 되 면 가능성이 있는 이름이 같은 둘 이상의 그룹에 대 한.
- 사용 하는 것이 좋습니다 [응용 프로그램 역할](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) 그룹 멤버 자격 및 응용 프로그램 간의 간접 참조 계층을 제공 합니다.   그런 다음 응용 프로그램 역할 clams 토큰에 따라 하는 내부 권한 부여 결정을 내립니다.
- Active Directory에서 동기화 된 그룹 특성을 가져올 응용 프로그램이 구성 되 고 그룹에는 이러한 특성이 포함 되어 있지 않습니다 하는 경우에 클레임에 포함 되지 않습니다.
- 그룹 클레임 토큰에서 중첩 된 그룹을 포함 합니다.   사용자가 멤버인 알리도록 알리도록 라우팅해야의 멤버인 경우 라우팅해야와 알리도록 사용자에 대 한 그룹 클레임 포함 됩니다. 중첩 된 그룹에 대 한 많은 사용량을 사용 하 여 조직 및 많은 수의 그룹 멤버 자격을 사용 하 여 사용자에 대 한 토큰에 나열 된 그룹 수가 토큰 크기를 증가할 수 있습니다.   Azure Active Directory는 SAML 어설션의 150에서 200 JWT에 대 한 토큰에 내보냅니다 그룹 수를 제한 합니다.

> Active Directory에서 동기화 되는 그룹 특성을 사용 하기 위한 필수 조건:   그룹은 Azure AD Connect를 사용 하 여 Active Directory에서 동기화 되어야 합니다.

Active Directory 그룹에 대 한 그룹 이름을 내보내는 데 Azure Active Directory를 구성 하는 방법은 다음 두 단계가 있습니다.

1. **Active Directory에서 그룹 이름을 동기화** 하기 전에 Azure Active Directory에서 그룹 이름을 내보낼 수 있습니다 또는 온-프레미스 그룹 SID 그룹이 나 역할 클레임, 필수 특성을 Active Directory에서 동기화 해야 합니다.  Azure AD Connect 버전 1.2.70 실행 해야 이상.   이전 버전 1.2.70 Azure AD Connect Active Directory에서 번째 그룹 개체를 동기화 하는 있지만 기본적으로 필요한 그룹 이름 특성을 포함 하지 않습니다.  현재 버전으로 업그레이드 해야 합니다.

2. **응용 프로그램 등록 토큰에서 그룹 클레임을 포함 하도록 Azure Active Directory에서 구성** 그룹 클레임 수 갤러리 또는 비 갤러리 SAML SSO 응용 프로그램의 경우 포털의 엔터프라이즈 응용 프로그램 섹션에서 구성 또는 응용 프로그램 등록 섹션에서 응용 프로그램 매니페스트를 사용합니다.  응용 프로그램 매니페스트 참조 "Azure Active Directory 응용 프로그램 등록 그룹 특성에 대 한 구성" 아래에서 그룹 클레임을 구성 합니다.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>SSO 구성을 사용 하 여 SAML 응용 프로그램에 대 한 그룹 클레임 구성

갤러리 또는 비 갤러리 SAML 응용 프로그램에 대 한 그룹 클레임을 구성 하려면 엔터프라이즈 응용 프로그램을 열고 응용 프로그램 목록에서 클릭 Single Sign On 구성을 선택 합니다.

"토큰에서 반환 된 그룹" 옆에 있는 편집 아이콘 선택

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

라디오 단추를 사용 하 여 토큰에 포함 될는 그룹을 선택 합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 선택 | 설명 |
|----------|-------------|
| **모든 그룹** | 보안 그룹 및 배포를 내보내는 나열 합니다.   사용자가 할당 하는 'wids' 클레임에 내보내지는 디렉터리 역할 및 역할 클레임에 내보내지는 사용자가 할당 하는 모든 응용 프로그램 역할에도 발생 합니다. |
| **보안 그룹** | 사용자가 그룹 클레임에 속하는 보안 그룹을 내보냅니다. |
| **메일 그룹** | 사용자가 멤버인 그룹을 내보냅니다. |
| **디렉터리 역할** | 'Wids' 클레임 (클레임을 내보내지 않습니다 그룹)으로 내보낼 때 사용자가 디렉터리 역할을 할당 하는 경우 |

예를 들어 사용자가 멤버인 모든 보안 그룹을 내보낼, 보안 그룹을 선택

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

### <a name="advanced-options"></a>고급 옵션

그룹 클레임에 내보내지는 서 고급 옵션의 설정을 수정할 수 있습니다.

그룹 클레임 이름의 사용자 지정:  선택 하면 그룹 클레임에 대 한 다른 클레임 유형에 지정할 수 있습니다.   네임 스페이스 필드에서 클레임에 대 한 이름 필드와 선택적 네임 스페이스에 클레임 유형을 입력 합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Active Directory를 사용 하 여 그룹을 내보낼 Azure AD Objectid 대신 특성 'Id 대신 이름으로 그룹을 반환 합니다.' 상자를 선택한 드롭다운 목록에서 형식을 선택 합니다.  이 그룹 이름이 포함 된 문자열 값을 사용 하 여 클레임에 있는 개체 ID를 대체 합니다.   Active Directory에서 동기화 그룹에만 클레임에 포함 됩니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

일부 응용 프로그램은 '역할' 클레임에 표시 하려면 그룹 멤버 자격 정보를 필요 합니다. 필요에 따라 역할로 '그룹을 역할 클레임 내보낼' 확인란을 선택 하 여 사용자의 그룹을 내보낼 수 있습니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 역할로 그룹 데이터를 내보내는 옵션을 사용 하는 경우 그룹에만 역할 클레임에 표시 됩니다.  사용자가 할당 하는 모든 응용 프로그램 역할은 역할 클레임에 나타나지 않습니다.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>그룹 특성에 대 한 Azure AD 응용 프로그램 등록 구성

그룹 클레임을 구성할 수도 있습니다는 [선택적 클레임](../../active-directory/develop/active-directory-optional-claims.md) 섹션을 [응용 프로그램 매니페스트](../../active-directory/develop/reference-app-manifest.md)합니다.

1. 포털에서 Azure Active Directory-> 응용 프로그램-> 등록 선택-> 응용 프로그램 매니페스트->

2. groupMembershipClaim 변경 하 여 그룹 구성원 자격 클레임을 사용 하도록 설정

   유효한 값은:

   - "All"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   예: 

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   그룹의 그룹 Objectid를 내보낼 수는 기본적으로 클레임 값입니다.  역할 클레임 형식을 변경 하려면 또는 온 프레미스 그룹 특성을 포함 하는 클레임 값을 수정 하려면 OptionalClaims 구성을 같이 사용 합니다.

3. 그룹 이름을 구성에 대 한 선택적 클레임을 설정 합니다.

   하려는 경우 토큰에서 그룹에 선택적 클레임 섹션에서 AD 그룹 특성 지정에 토큰 유형이 선택적 클레임을 적용 해야 하는 온-프레미스에서 요청한 선택적 클레임 및 필요한 추가 속성의 이름을 포함 합니다.  여러 토큰 형식은 나열할 수 있습니다.

   - OIDC ID 토큰에 대 한 idToken
   - OAuth/OIDC 액세스 토큰에 대 한 accessToken
   - SAML 토큰에 대 한 Saml2Token 합니다.

   > [!NOTE]
   > 형식 토큰 SAML1.1 및 SAML2.0 둘 다에 적용 됩니다 Saml2Token 형식은

   각 관련 토큰 형식에 대 한 매니페스트에서 OptionalClaims 섹션을 사용 하려면 그룹 클레임을 수정 합니다. OptionalClaims 스키마는 다음과 같습니다.

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
   | **name:** | "Groups" 해야 합니다. |
   | **source:** | 사용되지 않습니다. 생략 하거나 null을 지정 |
   | **essential:** | 사용되지 않습니다. 생략 하거나 false를 지정 합니다. |
   | **additionalProperties:** | 추가 속성 목록입니다.  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

   AdditionalProperties "sam_account_name", "dns_domain_and_sam_account_name" 중 하나에 "netbios_domain_and_sam_account_name"는 필요 합니다.  둘 이상 있으면 첫 번째 사용 되 고 나머지는 무시 됩니다.

   일부 응용 프로그램은 역할 클레임에 사용자의 그룹 정보가 필요 합니다.  그룹에서 역할 클레임에 클레임을 클레임 유형을 변경 하려면 "emit_as_roles" 추가 속성을 추가 합니다.  그룹 값은 역할 클레임에 발생 합니다.

   > [!NOTE]
   > "Emit_as_roles"을 사용 하는 경우 사용자가 할당 되는 모든 응용 프로그램 역할 구성 역할 클레임에 표시 되지 않습니다

### <a name="examples"></a>예

그룹 dnsDomainName\SAMAccountName 형태로 OAuth 액세스 토큰에서 그룹 이름으로 내보내기

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

SAML 및 OIDC ID 토큰에서 역할 클레임으로 netbiosDomain\samAccountName 형식으로 반환 될 그룹 이름을 내보낼:

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

[하이브리드 ID란?](whatis-hybrid-identity.md)