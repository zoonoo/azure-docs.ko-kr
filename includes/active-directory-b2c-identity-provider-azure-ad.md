---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82925447"
---
## <a name="register-an-azure-ad-app"></a>Azure AD 앱 등록

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 조직의 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 조직 Azure AD 테 넌 트를 포함 하는 디렉터리 (예: contoso.com)를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독 필터** 를 선택 하 고 Azure AD 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예: `Azure AD B2C App`
1. 이 응용 프로그램에 대해서 **만이 조직 디렉터리에서** 기본 선택 된 계정을 적용 합니다.
1. **리디렉션 URI**의 경우 **웹**의 값을 그대로 사용 하 고 다음 URL을 소문자로 입력 합니다 `your-B2C-tenant-name` . 여기서은 Azure AD B2C 테 넌 트의 이름으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    예: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

1. **등록**을 선택합니다. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.
1. **인증서 & 암호**를 선택 하 고 **새 클라이언트 암호**를 선택 합니다.
1. 비밀에 대 한 **설명을** 입력 하 고 만료를 선택한 다음 **추가**를 선택 합니다. 이후 단계에서 사용할 암호의 **값** 을 기록 합니다.

### <a name="configuring-optional-claims"></a>선택적 클레임 구성

Azure AD에서 `family_name` 및 `given_name` 클레임을 가져오려는 경우 Azure Portal UI 또는 애플리케이션 매니페스트에서 애플리케이션에 대한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 앱에 선택적 클레임을 제공하는 방법](/azure/active-directory/develop/active-directory-optional-claims)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory**를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록**을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **토큰 구성**을 선택합니다.
1. **선택적 클레임 추가**를 선택합니다.
1. **토큰 형식**에서 **ID**를 선택 합니다.
1. 추가할 선택적 클레임을 선택 하 고을 선택 `family_name` `given_name` 합니다.
1. **추가**를 클릭합니다.