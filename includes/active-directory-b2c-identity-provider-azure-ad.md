---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678061"
---
## <a name="register-an-azure-ad-app"></a>Azure AD 앱 등록

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 조직의 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 조직 Azure AD 테넌트(예: contoso.com)가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독 필터를** 선택한 다음 Azure AD 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 응용 프로그램의 **이름을** 입력합니다. `Azure AD B2C App`)을 입력합니다.
1. 이 응용 프로그램에 **대해서만 이 조직 디렉터리에서 계정의** 기본 선택을 수락합니다.
1. 리디렉션 **URI의**경우 **Web**값을 수락하고 모든 소문자에 다음 URL을 `your-B2C-tenant-name` 입력하며 여기서 Azure AD B2C 테넌트의 이름으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`)을 입력합니다.

1. **등록**을 선택합니다. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.
1. **인증서 & 비밀을**선택한 다음 새 클라이언트 **보안**입니다.
1. 비밀에 대한 **설명을** 입력하고 만료를 선택한 다음 **추가를**선택합니다. 이후 단계에서 사용할 비밀의 **값을** 기록합니다.

### <a name="configuring-optional-claims"></a>선택적 클레임 구성

Azure AD에서 `family_name` 클레임 `given_name` 및 클레임을 얻으려면 Azure 포털 UI 또는 응용 프로그램 매니페스트에서 응용 프로그램에 대한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 앱에 선택적 클레임을 제공하는 방법을 참조하세요.](/active-directory/develop/active-directory-optional-claims.md)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory**를 검색하고 선택합니다.
1. **관리** 섹션에서 앱 **등록을 선택합니다.**
1. 목록에서 선택적 클레임을 구성할 응용 프로그램을 선택합니다.
1. **관리** 섹션에서 토큰 **구성을**선택합니다.
1. **선택적 클레임 추가를**선택합니다.
1. 토큰 **유형의**경우 **ID를**선택합니다.
1. 추가할 선택적 `family_name` 클레임을 `given_name`선택하고 .
1. **추가**를 클릭합니다.