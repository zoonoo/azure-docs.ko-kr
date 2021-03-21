---
title: 로컬 계정 id 공급자 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 테 넌 트에서 사용자 흐름을 설정 하는 경우 로컬 계정 인증에 사용할 수 있는 id 유형 (메일, 사용자 이름, 전화 번호)을 정의 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557231"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>사용자 흐름에 대 한 전화 등록 및 로그인 설정 (미리 보기)

> [!NOTE]
> 사용자 흐름의 전화 등록 및 로그인 및 복구 전자 메일 기능은 공개 미리 보기로 제공 됩니다.

전자 메일 및 사용자 이름 외에도 전화 등록을 추가 하 고 로컬 계정 id 공급자에 로그인 하 여 전화 번호를 등록 옵션으로 설정할 수 있습니다. 로컬 계정에 대 한 전화 등록 및 로그인을 사용 하도록 설정한 후 사용자 흐름에 전화 등록을 추가할 수 있습니다.

사용자 흐름에서 전화 등록 및 로그인을 설정 하려면 다음 단계를 수행 해야 합니다.

- 사용자의 id로 전화 번호를 수락 하려면 로컬 계정 id 공급자에서 [전화 번호 등록 및 로그인 테 넌 트 전체를 구성](#configure-phone-sign-up-and-sign-in-tenant-wide) 합니다. 

- 사용자가 전화 번호를 사용 하 여 응용 프로그램에 등록할 수 있도록 하려면 [사용자 흐름에 전화](#add-phone-sign-up-to-a-user-flow) 번호를 추가 합니다.

- 사용자가 휴대폰을 사용 하지 않을 때 계정을 복구 하는 데 사용할 수 있는 전자 메일을 사용자가 지정할 수 있도록 하려면 [복구 전자 메일 프롬프트 (미리 보기)를 사용 하도록 설정 합니다](#enable-the-recovery-email-prompt-preview) .

전화 등록으로 사용자 흐름을 구성 하는 경우 MFA (multi-factor authentication)는 기본적으로 사용 되지 않습니다. 전화 등록을 사용 하 여 사용자 흐름에서 MFA를 사용 하도록 설정할 수 있지만 전화 번호가 기본 식별자로 사용 되기 때문에 두 번째 인증 요소에 사용할 수 있는 유일한 옵션은 전자 메일 일회용 암호입니다.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>전화 등록 및 로그인 테 넌 트 전체 구성

전자 메일 등록은 로컬 계정 id 공급자 설정에서 기본적으로 사용 하도록 설정 됩니다. 전자 메일 등록, 사용자 이름 또는 전화 번호를 선택 하거나 선택 취소 하 여 테 넌 트에서 지원할 id 유형을 변경할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

4. **관리** 아래에서 **ID 공급자** 를 선택합니다.

5. ID 공급자 목록에서 **로컬 계정** 을 선택합니다.

   ![Id 공급자 로컬 계정 선택](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. **로컬 IDP 구성** 페이지에서 고객이 Azure AD B2C 테 넌 트에서 로컬 계정을 만드는 데 사용할 수 있는 허용 가능한 id 유형 중 하나로 **선택 되어 있는지 확인 합니다** .

   ![허용 되는 id 유형 선택](media/phone-authentication-user-flows/configure-local-idp.png)

1. **저장** 을 선택합니다.

## <a name="add-phone-sign-up-to-a-user-flow"></a>사용자 흐름에 전화 등록 추가

로컬 계정에 대 한 id 옵션으로 전화 등록을 추가한 후에는 최신 **권장** 사용자 흐름 버전인 경우에만 사용자 흐름에 추가할 수 있습니다. 다음은 새 사용자 흐름에 전화 등록을 추가 하는 방법을 보여 주는 예제입니다. 그러나 기존 권장 버전 사용자 흐름에 전화 등록을 추가할 수도 있습니다 (   >  *사용자 흐름 이름*  >  **id 공급자**  >  **로컬 계정 전화 등록** 을 사용자 흐름 선택). 

새 사용자 흐름에 전화 등록을 추가 하는 방법을 보여 주는 예제는 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

    ![B2C 테넌트, 디렉터리 및 구독 창, Azure Portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
4. **정책** 아래에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.

    ![새 사용자 흐름 단추가 강조 표시된 포털의 사용자 흐름 페이지](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. **사용자 흐름 만들기** 탭에서 **가입 및 로그인** 사용자 흐름을 선택합니다.

    ![가입 및 로그인 흐름이 강조 표시된 사용자 흐름 페이지 선택](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다. (사용자 흐름 버전에 대해 [자세히 알아보세요](user-flow-versions.md).)

    ![사용자 흐름 만들기 단추](./media/phone-authentication-user-flows/select-version.png)

7. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *signupsignin1* 과 같습니다.
8. **Id 공급자** 섹션의 **로컬 계정** 에서 **전화 등록** 을 선택 합니다.

   ![사용자 흐름 전화 등록 옵션 선택 됨](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. **소셜 id 공급자** 에서이 사용자 흐름을 허용 하려는 다른 id 공급자를 선택 합니다.

   > [!NOTE]
   > MFA (multi-factor authentication)는 등록 사용자 흐름에 대해 기본적으로 사용 되지 않습니다. 전화 등록 사용자 흐름에 대해 MFA를 사용 하도록 설정할 수 있지만 전화 번호가 기본 식별자로 사용 되기 때문에 두 번째 인증 요소에 사용할 수 있는 유일한 옵션은 전자 메일 일회용 암호입니다.

1. **사용자 특성 및 토큰 클레임** 섹션에서 등록 중에 사용자 로부터 수집 하 여 전송 하려는 클레임 및 특성을 선택 합니다. 예를 들어 **자세히 보기** 를 선택하고 **국가/지역**, **표시 이름** 및 **우편 번호** 의 특성 및 클레임을 선택합니다. **확인** 을 선택합니다.

1. **만들기** 를 선택하여 사용자 흐름을 추가합니다. *B2C_1* 이라는 접두사가 이름 앞에 자동으로 붙습니다.

## <a name="enable-the-recovery-email-prompt-preview"></a>복구 전자 메일 프롬프트 사용 (미리 보기)

사용자 흐름에 대해 전화 등록 및 로그인을 사용 하도록 설정 하면 복구 전자 메일 기능을 사용 하도록 설정 하는 것도 좋습니다. 이 기능을 사용 하면 사용자가 휴대폰을 사용 하지 않을 때 계정을 복구 하는 데 사용할 수 있는 전자 메일 주소를 제공할 수 있습니다. 이 메일 주소는 계정 복구에만 사용 됩니다. 이 파일은 로그인 하는 데 사용할 수 없습니다.

- 복구 전자 메일 메시지가 표시 되 면 처음 등록 하는 사용자에 게 백업 전자 메일을 확인 하 라는 메시지가 **표시 됩니다.** 이전에 복구 전자 메일을 제공 하지 않은 사용자는 다음에 로그인 하는 동안 백업 전자 메일을 확인 하 라는 메시지가 표시 됩니다.

- 복구 전자 메일이 **꺼져** 있는 경우 사용자가 등록 하거나 로그인 하면 복구 전자 메일 프롬프트가 표시 되지 않습니다.
 
사용자 흐름 속성에서 복구 전자 메일 프롬프트를 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 시작 하기 전에 위에서 설명한 대로 [사용자 흐름에 전화 등록을 추가](#add-phone-sign-up-to-a-user-flow) 했는지 확인 합니다.

### <a name="to-enable-the-recovery-email-prompt"></a>복구 전자 메일 프롬프트를 사용 하도록 설정 하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
4. Azure AD B2C의 **정책** 에서 **사용자 흐름** 을 선택 합니다.
5. 목록에서 사용자 흐름을 선택 합니다.
6. **설정** 아래에서 **속성** 을 선택합니다.
7. **전화 번호 등록 및 로그인 (미리 보기)에 대 한 복구 전자 메일 확인을 사용** 하려면 다음을 선택 합니다.

   - **에서** 등록 및 로그인 중에 복구 전자 메일 메시지를 표시 합니다.
   - 을 (를) **해제** 하 여 복구 전자 메일 메시지를 숨깁니다.

    ![복구 전자 메일 사용 설정을 사용 하는 사용자 흐름 속성](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. **저장** 을 선택합니다.

### <a name="to-test-the-recovery-email-prompt"></a>복구 전자 메일 메시지를 테스트 하려면

전화 등록 및 로그인을 사용 하도록 설정 하 고 사용자 흐름에서 복구 전자 메일 메시지를 표시 한 후 사용자 **흐름 실행** 을 사용 하 여 사용자 환경을 테스트할 수 있습니다.

1. **정책** > **사용자 흐름** 을 차례로 선택한 다음, 만든 사용자 흐름을 선택합니다. 사용자 흐름 개요 페이지에서 **사용자 흐름 실행** 을 선택합니다.

2. **애플리케이션** 에 대해 1단계에서 등록한 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 다음 동작을 확인 합니다.

   - 처음 등록 하는 사용자에 게 복구 전자 메일을 제공 하 라는 메시지가 표시 됩니다. 
   - 이미 등록 되었지만 복구 전자 메일을 제공 하지 않은 사용자는 로그인 할 때이를 제공 하도록 요청 됩니다.

4. 전자 메일 주소를 입력 하 고 **확인 코드 보내기** 를 선택 합니다. 입력 한 전자 메일 받은 편지 함으로 코드가 전송 되는지 확인 합니다. 코드를 검색 하 고 **확인 코드** 상자에 입력 합니다. 그런 다음 **코드 확인** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [외부 id 공급자 추가](add-identity-provider.md)
- [사용자 흐름 만들기](tutorial-create-user-flows.md)