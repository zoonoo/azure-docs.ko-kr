---
title: Azure Active Directory B2C 관리
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C 테넌트를 관리하는 방법을 알아봅니다. Azure AD B2C에서 지원되는 Azure AD 기능, 관리자 역할을 사용하여 리소스를 관리하는 방법 및 회사 계정과 게스트 사용자를 Azure AD B2C 테넌트에 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/03/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 47947945759faee9f38393f06f7ec9396d9c715b
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108749108"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테넌트 관리

Azure AD B2C(Azure Active Directory B2C)에서 테넌트는 소비자 사용자의 디렉터리를 나타냅니다. 각 Azure AD B2C 테넌트는 다른 Azure AD B2C 테넌트와는 전혀 다르고 별개입니다. Azure AD B2C 테넌트는 이미 있을 수 있는 Azure Active Directory 테넌트와 다릅니다. 이 문서에서는 Azure AD B2C 테넌트를 관리하는 방법에 대해 알아봅니다.

## <a name="supported-azure-ad-features"></a>지원되는 Azure AD 기능

Azure AD B2C는 Azure AD 플랫폼을 사용합니다. Azure AD B2C 테넌트에서 사용할 수 있는 Azure AD 기능은 다음과 같습니다.

|기능  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [그룹](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | 그룹은 관리 및 사용자 계정을 관리하는 데 사용할 수 있습니다.| 그룹은 관리 계정을 관리하는 데 사용할 수 있습니다. [소비자 계정](user-overview.md#consumer-user)은 그룹을 지원하지 않습니다. |
| [외부 ID 게스트 초대](../active-directory//external-identities/add-users-administrator.md)| 게스트 사용자를 초대하고, Facebook 및 Google 계정을 사용하여 페더레이션 및 로그인과 같은 외부 ID 기능을 구성할 수 있습니다. | 애플리케이션에 액세스하거나 테넌트를 관리하기 위해 Microsoft 계정 또는 Azure AD 사용자만 게스트로 Azure AD 테넌트에 초대할 수 있습니다. [소비자 계정](user-overview.md#consumer-user)의 경우 Azure AD B2C 사용자 흐름과 사용자 지정 정책을 사용하여 사용자를 관리하고, Google 또는 Facebook과 같은 외부 ID 공급자에 가입하거나 로그인합니다. |
| [역할 및 관리자](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| 관리 및 사용자 계정에 대해 완벽하게 지원됩니다. | 역할은 [소비자 계정](user-overview.md#consumer-user)에서 지원되지 않습니다. 소비자 계정에는 Azure 리소스에 대한 액세스 권한이 없습니다.|
| [사용자 지정 도메인 이름](../active-directory/roles/permissions-reference.md#) |  Azure AD 사용자 지정 도메인은 관리 계정에만 사용할 수 있습니다. | [소비자 계정](user-overview.md#consumer-user)은 사용자 이름, 전화 번호 또는 모든 이메일 주소를 사용하여 로그인할 수 있습니다. [사용자 지정 도메인](custom-domain.md)은 리디렉션 URL에서 사용할 수 있습니다.|
| [조건부 액세스](../active-directory/roles/permissions-reference.md#) | 관리 및 사용자 계정에 대해 완벽하게 지원됩니다. | Azure AD 조건부 액세스 기능의 하위 집합은 [소비자 계정](user-overview.md#consumer-user)에서 지원됩니다. Azure AD B2C [사용자 지정 도메인](conditional-access-user-flow.md)을 구성하는 방법에 대해 알아보세요.|

## <a name="other-azure-resources-in-your-tenant"></a>테넌트의 다른 Azure 리소스

Azure AD B2C 테넌트에서는 가상 머신, Azure 웹앱 또는 Azure 함수와 같은 다른 Azure 리소스를 프로비전할 수 없습니다. 이러한 리소스는 Azure AD 테넌트에서 만들어야 합니다.

## <a name="azure-ad-b2c-accounts-overview"></a>Azure AD B2C 계정 개요

Azure AD B2C 테넌트에서 다음과 같은 유형의 계정을 만들 수 있습니다.

Azure AD B2C 테넌트에는 [Azure Active Directory B2C의 사용자 계정 개요](user-overview.md) 문서에서 설명한 대로 만들 수 있는 여러 유형의 계정이 있습니다.

- **회사 계정** - 회사 계정은 테넌트의 리소스에 액세스할 수 있으며, 관리자 역할이 있는 경우 테넌트를 관리할 수 있습니다.
- **게스트 계정** - 게스트 계정은 Microsoft 계정 또는 Azure Active Directory 사용자만 될 수 있으며, 애플리케이션에 액세스하거나 테넌트를 관리하는 데 사용될 수 있습니다.
- **소비자 계정** - 소비자 계정은 Azure AD B2C에 등록한 애플리케이션의 사용자가 사용합니다.

이러한 계정 유형에 대한 자세한 내용은 [Azure Active Directory B2C의 사용자 계정 개요](user-overview.md)를 참조하세요. Azure AD B2C 테넌트를 관리하기 위해 할당되는 모든 사용자는 Azure AD 사용자 계정이 있어야 Azure 관련 서비스에 액세스할 수 있습니다. 이러한 사용자는 Azure AD B2C 테넌트에서 [계정(회사 계정)을 만들거나](#add-an-administrator-work-account) 게스트 사용자로 Azure AD B2C 테넌트에 [초대](#invite-an-administrator-guest-account)하여 추가할 수 있습니다.

## <a name="use-roles-to-control-resource-access"></a>역할을 사용하여 리소스 액세스 제어

액세스 제어 전략을 계획하는 경우 리소스에 액세스하는 데 필요한 최소한의 권한 있는 역할을 사용자에게 할당하는 것이 가장 좋습니다. 다음 표에서는 Azure AD B2C 테넌트의 기본 리소스와 해당 리소스를 관리하는 사용자에게 가장 적합한 관리 역할에 대해 설명합니다.

|리소스  |설명  |역할  |
|---------|---------|---------|
|[애플리케이션 등록](tutorial-register-applications.md) | Azure AD B2C 내에서 웹, 모바일 및 네이티브 애플리케이션 등록의 모든 측면을 만들고 관리합니다.|[애플리케이션 관리자](../active-directory/roles/permissions-reference.md#application-administrator)|
|[ID 공급자](add-identity-provider.md)| [로컬 ID 공급자](identity-provider-local.md)와 외부 소셜 또는 엔터프라이즈 ID 공급자를 구성합니다. | [외부 ID 공급자 관리자](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API 커넥터](add-api-connector.md)| 사용자 흐름을 웹 API와 통합하여 사용자 환경을 사용자 지정하고 외부 시스템과 통합합니다.|[외부 ID 사용자 흐름 관리자](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[회사 브랜딩](customize-ui.md#configure-company-branding)| 사용자 흐름 페이지를 사용자 지정합니다.| [전역 관리자](../active-directory/roles/permissions-reference.md#global-administrator)|
|[사용자 특성](user-flow-custom-attributes.md)| 모든 사용자 흐름에 사용할 수있는 사용자 지정 특성을 추가하거나 삭제합니다.| [외부 ID 사용자 흐름 특성 관리자](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|사용자 관리| 이 문서에서 설명한 대로 [소비자 계정](manage-users-portal.md) 및 관리 계정을 관리합니다.| [사용자 관리자](../active-directory/roles/permissions-reference.md#user-administrator)|
|역할 및 관리자| Azure AD B2C 디렉터리에서 역할 할당을 관리합니다. Azure AD B2C 역할에 할당할 수 있는 그룹을 만들고 관리합니다. |[전역 관리자](../active-directory/roles/permissions-reference.md#global-administrator), [권한 있는 역할 관리자](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[사용자 흐름](user-flow-overview.md)|가입, 로그인 및 프로필 편집과 같은 일반적인 ID 작업을 빠르게 구성하고 사용하도록 설정합니다.| [외부 ID 사용자 흐름 관리자](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[사용자 지정 정책](user-flow-overview.md)| Azure AD B2C에서 모든 사용자 지정 정책을 만들고, 읽고, 업데이트하고, 삭제합니다.| [B2C IEF 정책 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[정책 키](policy-keys-overview.md)|사용자 지정 정책에 사용되는 토큰, 클라이언트 암호, 인증서 및 암호에 서명하고 유효성을 검사하기 위한 암호화 키를 추가하고 관리합니다.|[B2C IEF 키 세트 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>관리자 추가(회사 계정)

새 관리 계정을 만들려면 다음 단계를 수행합니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. **새 사용자** 를 선택합니다.
1. **사용자**  페이지에서 이 사용자에 대한 정보를 입력합니다.

   - **이름**. 필수 요소. 새 사용자의 이름 및 성입니다. 예를 들어, *Mary Parker* 입니다.
   - **사용자 이름**. 필수 요소. 새 사용자의 사용자 이름입니다. 예: `mary@contoso.com`.
     사용자 이름의 도메인 부분에는 초기 기본 도메인 이름( *\<yourdomainname>.onmicrosoft.com*)을 사용해야 합니다.
   - **그룹** - 원할 경우, 하나 이상의 기존 그룹에 사용자를 추가할 수 있습니다. 사용자를 나중에 그룹에 추가할 수도 있습니다. 
   - **디렉터리 역할**: 사용자에 대한 Azure AD 관리 권한이 필요한 경우 Azure AD 역할에 추가할 수 있습니다. Azure AD에서 사용자에게 전역 관리자 또는 하나 이상의 제한된 관리자 역할을 할당할 수 있습니다. 역할 할당에 대한 자세한 내용은 [역할을 사용하여 리소스 액세스 제어](#use-roles-to-control-resource-access)를 참조하세요.
   - **작업 정보**: 여기서 사용자에 대한 추가 정보를 추가하거나 나중에 추가할 수 있습니다. 

1. **암호** 상자에 제공된 자동 생성된 암호를 복사합니다. 처음으로 로그인하는 사용자에게 이 암호를 제공해야 합니다.
1. **만들기** 를 선택합니다.

사용자가 만들어지고 Azure AD B2C 테넌트에 추가됩니다. Azure AD B2C 테넌트에서 고유한 하나 이상의 회사 계정을 전역 관리자 역할에 할당하는 것이 좋습니다. 이 계정은 비상 계정으로 간주할 수 있습니다.

## <a name="invite-an-administrator-guest-account"></a>관리자 초대(게스트 계정)

새 게스트 사용자를 초대하여 테넌트를 관리할 수도 있습니다. 이 ID의 수명 주기는 외부에서 관리할 수 있으므로 조직에 Azure AD가 있는 경우에도 게스트 계정은 기본 설정 옵션입니다. 

사용자를 초대하려면 다음 단계를 수행합니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. **새 게스트 계정** 을 선택합니다.
1. **사용자**  페이지에서 이 사용자에 대한 정보를 입력합니다.

   - **이름**. 필수 요소. 새 사용자의 이름 및 성입니다. 예를 들어, *Mary Parker* 입니다.
   - **이메일 주소**. 필수 요소. 초대하려는 사용자의 이메일 주소입니다. 예: `mary@contoso.com`.   
   - **개인 메시지**. 초대 이메일에 포함될 개인 메시지를 추가합니다.
   - **그룹** - 원할 경우, 하나 이상의 기존 그룹에 사용자를 추가할 수 있습니다. 사용자를 나중에 그룹에 추가할 수도 있습니다.
   - **디렉터리 역할**: 사용자에 대한 Azure AD 관리 권한이 필요한 경우 Azure AD 역할에 추가할 수 있습니다. Azure AD에서 사용자에게 전역 관리자 또는 하나 이상의 제한된 관리자 역할을 할당할 수 있습니다. 역할 할당에 대한 자세한 내용은 [역할을 사용하여 리소스 액세스 제어](#use-roles-to-control-resource-access)를 참조하세요.
   - **작업 정보**: 여기서 사용자에 대한 추가 정보를 추가하거나 나중에 추가할 수 있습니다.

1. **만들기** 를 선택합니다.

사용자에게 초대 이메일을 보냅니다. 로그인하려면 사용자가 초대를 수락해야 합니다.

### <a name="resend-the-invitation-email"></a>초대 이메일 다시 보내기

게스트가 초대 이메일을 받지 못했거나 초대가 만료된 경우 초대를 다시 보낼 수 있습니다. 초대 이메일을 보내는 대신 게스트에게 초대를 수락하는 데 사용할 수 있는 직접 링크를 제공할 수 있습니다. 초대를 다시 보내고 직접 링크를 가져오려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. 초대를 다시 보낼 사용자를 검색하여 선택합니다.
1. **사용자 | 프로필** 페이지의 **ID** 아래에서 **(관리)** 를 선택합니다.
    
    ![게스트 계정 초대 이메일을 다시 보내는 방법을 보여 주는 스크린샷](./media/tenant-management/guest-account-resend-invite.png)

1. **초대 다시 보내기** 에 대해 **예** 를 선택합니다. **초대를 다시 보내시겠습니까?** 가 표시되면 **예** 를 선택합니다.
1. Azure AD B2C에서 초대를 보냅니다. 초대 URL을 복사하여 게스트에 직접 제공할 수도 있습니다.
    
    ![초대 URL을 가져오는 방법을 보여 주는 스크린샷](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>역할 할당 추가

역할은 [사용자를 만들거나](#add-an-administrator-work-account) [게스트 사용자를 초대](#invite-an-administrator-guest-account)할 때 할당할 수 있습니다. 사용자에 대한 역할을 추가하거나, 변경하거나, 제거할 수 있습니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. 역할을 변경하려는 사용자를 선택합니다. 그런 다음, **할당된 역할** 을 선택합니다.
1. **할당 추가** 를 선택하고, 할당할 역할(예: *애플리케이션 관리자*)을 선택한 다음, **추가** 를 선택합니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

사용자의 역할 할당을 제거해야 하는 경우 다음 단계를 수행합니다.

1. **Azure AD B2C** 를 선택하고 **사용자** 를 선택한 다음, 사용자를 검색하여 선택합니다.
1. **할당된 역할** 을 선택합니다. 제거할 역할(예: *애플리케이션 관리자*)을 선택한 다음, **할당 제거** 를 선택합니다.

## <a name="review-administrator-account-role-assignments"></a>관리자 계정 역할 할당 검토

감사 프로세스의 일부로 일반적으로 Azure AD B2C 디렉터리의 특정 역할에 할당된 사용자를 검토합니다. 다음 단계를 사용하여 현재 권한 있는 역할이 할당된 사용자를 감사합니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 아래에서 **역할 및 관리자** 를 선택합니다.
1. 역할(예: **전역 관리자**)을 선택합니다. **역할 | 할당** 페이지에 해당 역할의 사용자가 나열됩니다.

## <a name="delete-an-administrator-account"></a>관리자 계정 삭제

기존 사용자를 삭제하려면 *전역 관리자* 역할 할당이 있어야 합니다. 전역 관리자는 다른 관리자를 포함하여 모든 사용자를 삭제할 수 있습니다. *사용자 관리자* 는 관리자가 아닌 사용자를 삭제할 수 있습니다.

1. Azure AD B2C 디렉터리에서 **사용자** 를 선택한 다음, 삭제하려는 사용자를 선택합니다.
1. **삭제** 를 선택한 다음, **예** 를 선택하여 삭제를 확인합니다.

사용자가 삭제되고 **사용자 - 모든 사용자** 페이지에 더 이상 표시되지 않습니다. 이 사용자는 다음 30일 동안 **삭제된 사용자** 페이지에서 볼 수 있으며 해당 기간 복원할 수 있습니다. 사용자를 복원하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 최근에 삭제된 사용자 복원 또는 제거](../active-directory/fundamentals/active-directory-users-restore.md)를 참조하세요.

## <a name="protect-administrative-accounts"></a>관리 계정 보호

보안을 강화하기 위해 MFA(다단계 인증)를 사용하여 모든 관리자 계정을 보호하는 것이 좋습니다. MFA는 로그인 중에 사용자에게 모바일 디바이스의 확인 코드 또는 Microsoft Authenticator 앱의 요청과 같은 더 많은 형식의 식별을 요구하는 ID 확인 프로세스입니다.

![로그인 스크린샷에서 사용 중인 인증 방법](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

[Azure AD 보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)을 사용하도록 설정하여 모든 관리 계정에서 MFA를 사용하도록 적용할 수 있습니다.



## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 Azure Active Directory B2C 테넌트 만들기](tutorial-create-tenant.md)

