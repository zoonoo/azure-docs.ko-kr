---
title: Azure Active Directory B2C의 사용자 계정 개요 | Microsoft Docs
description: Azure Active Directory B2C의 사용자 계정에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42a38b47f9e49652b5fe80aa5bbd0047dc7d1e74
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511955"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 계정 개요

Azure AD(Azure Active Directory) B2C에서는 여러 유형의 계정을 사용할 수 있습니다. Azure Active Directory, Azure Active Directory B2B 및 Azure Active Directory B2C는 사용할 수 있는 사용자 계정의 유형을 공유합니다.

다음 유형의 계정을 사용할 수 있습니다.

- **회사 계정** - 회사 계정은 테넌트의 리소스에 액세스할 수 있으며, 관리자 역할이 있는 경우 테넌트를 관리할 수 있습니다.
- **게스트 계정** - 게스트 계정은 Microsoft 계정 또는 Azure Active Directory 사용자만 될 수 있으며, 애플리케이션에 액세스하거나 테넌트를 관리하는 데 사용될 수 있습니다. 
- **소비자 계정** - 소비자 계정은 Azure AD B2C 애플리케이션에서 가입 사용자 흐름을 진행하거나 Azure AD Graph API를 사용하여 생성되며, Azure AD B2C에 등록된 애플리케이션의 사용자에 의해 사용됩니다. 

## <a name="work-account"></a>회사 계정

회사 계정은 Azure AD를 기준으로 하는 모든 테넌트에 대해 동일한 방식으로 생성됩니다. 회사 계정을 만들려면 [빠른 시작: Azure Active Directory에 새 사용자 추가](../active-directory/fundamentals/add-users-azure-active-directory.md)의 정보를 사용할 수 있습니다. 회사 계정은 Azure Portal에서 **새 사용자** 옵션을 사용하여 생성됩니다.

새 회사 계정을 추가할 때는 다음 구성 설정을 고려해야 합니다.

- **이름** 및 **사용자 이름** - **이름** 속성에는 사용자의 이름과 성이 포함됩니다. **사용자 이름**은 사용자가 로그인할 때 입력하는 식별자입니다. 사용자 이름에는 전체 도메인이 포함됩니다. 사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 *your-domain.onmicrosoft.com* 또는 *contoso.com*과 같은 확인된 페더레이션되지 않은 [사용자 지정 도메인](../active-directory/fundamentals/add-custom-domain.md)이어야 합니다.
- **프로필** - 계정이 사용자 데이터 프로필로 설정됩니다. 이름, 성, 직위 및 부서 이름을 입력할 수 있습니다. 계정이 생성된 후 프로필을 편집할 수 있습니다.
- **그룹** - 그룹을 사용하여 한 번에 많은 사용자 또는 디바이스에 라이선스 또는 사용 권한을 할당하는 등 관리 작업을 수행합니다. 테넌트의 기존 [그룹](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)에 새 계정을 삽입할 수 있습니다. 
- **디렉터리 역할** - 사용자 계정이 테넌트의 리소스에 대해 가지는 액세스 수준을 지정해야 합니다. 다음 권한 수준을 사용할 수 있습니다.

    - **사용자** - 사용자는 할당된 리소스에 액세스할 수 있지만, 대부분의 테넌트 리소스를 관리할 수는 없습니다.
    - **전역 관리자** - 전역 관리자는 전체 테넌트 리소스에 대한 모든 권한을 보유합니다.
    - **제한된 관리자** - 사용자에 대한 관리 역할을 선택합니다. 선택할 수 있는 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../active-directory/users-groups-roles/directory-assign-admin-roles.md)을 참조하세요. 

### <a name="create-a-work-account"></a>회사 계정 만들기

다음 정보를 사용하여 새 회사 계정을 만들 수 있습니다.

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>사용자 프로필 업데이트

다음 정보를 사용하여 사용자의 프로필을 업데이트할 수 있습니다.

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>사용자 암호 다시 설정

다음 정보를 사용하여 사용자의 암호를 다시 설정할 수 있습니다. 

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>게스트 사용자

외부 사용자를 게스트 사용자로 테넌트에 초대할 수 있습니다. Azure AD B2C 테넌트에 게스트 사용자를 초대하기 위한 일반적인 시나리오는 관리 책임을 공유하는 것입니다. 게스트 계정 사용 예제를 보려면 [Azure Active Directory B2B 협업 사용자 속성](../active-directory/b2b/user-properties.md)을 참조하세요.

테넌트에 게스트 사용자를 초대하는 경우 받는 사람의 이메일 주소와 초대를 설명하는 메시지를 제공합니다. 초대 링크를 클릭하면 **시작** 단추가 선택되고 권한 검토가 수락되는 동의 페이지로 이동됩니다. 전자 메일 주소에 받은 편지함이 연결되어 있지 않으면 초대된 자격 증명을 사용하여 Microsoft 페이지로 이동한 후 동의 페이지로 이동할 수 있습니다. 그러면 사용자는 전자 메일의 링크를 클릭할 때와 동일한 방식으로 초대를 강제로 사용하게 됩니다. 예: `https://myapps.microsoft.com/B2CTENANTNAME`

[Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta)를 사용하여 게스트 사용자를 초대할 수도 있습니다.

## <a name="consumer-user"></a>소비자 사용자

소비자 사용자는 Azure AD B2C에서 보호하는 애플리케이션에 로그인할 수 있지만 Azure Portal과 같은 Azure 리소스에 액세스할 수 없습니다.  소비자 사용자는 로컬 계정이나 Facebook 또는 Twitter와 같은 페더레이션된 계정을 사용할 수 있습니다. 소비자 계정은 [가입 또는 로그인 사용자 흐름](../active-directory-b2c/active-directory-b2c-reference-policies.md)을 사용하여 생성됩니다.

사용자 지정 특성을 사용하여 소비자 사용자 계정이 생성될 때 수집되는 데이터를 지정할 수 있습니다. 자세한 내용은 [Azure Active Directory B2C에서 사용자 지정 특성 정의](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md)를 참조하세요.

[Azure AD Graph API 사용](active-directory-b2c-devquickstarts-graph-dotnet.md)의 **소비자 사용자 계정 만들기** 섹션에 포함된 정보를 사용하여 Azure AD B2C 소비자 계정을 만들 수 있습니다. 또한 동일한 문서의 **소비자 사용자 계정 업데이트** 섹션에 포함된 정보를 사용하여 계정 속성을 관리할 수 있습니다.

### <a name="migrate-consumer-user-accounts"></a>소비자 사용자 계정 마이그레이션

기존 소비자 사용자 계정을 ID 공급자에서 Azure AD B2C로 마이그레이션해야 할 수도 있습니다. 자세한 내용은 [사용자 마이그레이션](active-directory-b2c-user-migration.md) 또는 [소셜 ID가 있는 사용자 마이그레이션](active-directory-b2c-social-migration.md)을 참조하세요.
