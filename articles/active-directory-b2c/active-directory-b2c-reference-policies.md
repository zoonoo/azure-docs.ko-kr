---
title: "Azure Active Directory B2C: 기본 제공 정책 | Microsoft Docs"
description: "Azure Active Directory B2C의 확장 가능한 정책 프레임워크 및 다양한 정책 형식을 만드는 방법에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mbaldwin
editor: bryanla
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e6943eb619963dd56b1c3909a57ae7d6cefd0ddc
ms.contentlocale: ko-kr
ms.lasthandoff: 06/21/2017


---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: 기본 제공 정책


Azure Active Directory(Azure AD) B2C의 확장할 수 있는 정책 프레임워크는 서비스의 핵심 장점입니다. 정책은 등록, 로그인 또는 프로필 편집과 같은 소비자 ID 환경을 완벽하게 설명합니다. 예를 들어 등록 정책을 사용하면 다음 설정을 구성하여 동작을 제어할 수 있습니다.

* 소비자가 응용 프로그램에 등록하는 데 사용할 수 있는 계정 유형(Facebook과 같은 소셜 계정 또는 이메일 주소와 같은 로컬 계정)
* 등록 시 소비자로부터 수집할 특성(예: 이름, 우편 번호 및 신발 크기)
* Azure Multi-Factor Authentication 사용
* 모든 등록 페이지의 모양과 느낌
* 정책 실행이 완료될 때 응용 프로그램에서 받는 정보(토큰에서 클레임으로 매니페스트됨)

테넌트에 다른 형식의 여러 정책을 만들고 필요에 따라 응용 프로그램에서 사용할 수 있습니다. 응용 프로그램에 정책을 다시 사용할 수 있습니다. 이러한 유연성을 통해 개발자가 코드를 변경하지 않거나 최소로 변경하여 소비자 ID 환경을 정의하고 수정할 수 있습니다.

간단한 개발자 인터페이스를 통해 정책을 사용할 수 있습니다. 응용 프로그램은 표준 HTTP 인증 요청을 사용하여 정책을 트리거하고(요청에 정책 매개 변수 전달) 사용자 지정 토큰을 응답으로 받습니다. 예를 들어 등록 정책을 호출하는 요청과 로그인 정책을 호출하는 요청 간의 유일한 차이점은 "p" 쿼리 문자열 매개 변수에 사용되는 정책 이름에 있습니다.

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

정책 프레임워크에 대한 자세한 내용은 [Enterprise Mobility 및 보안 블로그의 Azure AD B2C에 대한 블로그 게시물](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)(영문)을 참조하세요.

## <a name="create-a-sign-up-or-sign-in-policy"></a>등록 또는 로그인 정책 만들기
이 정책은 단일 구성으로 소비자 등록 및 로그인 환경을 모두 처리합니다. 소비자는 컨텍스트에 따라 올바른 경로(등록 또는 로그인)를 진행합니다. 

또한 정책은 성공적으로 등록하거나 로그인하면 응용 프로그램에서 받는 토큰의 내용도 설명합니다. 등록 또는 로그인 정책에 대한 코드 샘플은 [이 문서에서 사용할 수 있습니다](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  등록 및 로그인 정책 대신 이 정책을 사용하는 것이 좋습니다.  

1. [먼저 다음 단계에 따라 Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. **등록 또는 로그인 정책**을 선택합니다.

3. 블레이드의 위쪽에서 **+추가**를 선택합니다.

4. **이름**은 응용 프로그램에서 사용하는 등록 정책의 이름을 결정합니다. 예를 들어 **SiUpIn**을 입력합니다.

5. **ID 공급자**를 선택한 다음 **전자 메일 등록**을 선택합니다. 또한 소셜 ID 공급자가 이미 구성되어 있는 경우 필요에 따라 해당 소셜 ID 공급자를 선택할 수 있습니다. **확인**을 클릭합니다.

6. **등록 특성**을 선택합니다. 여기서 등록하는 동안 소비자로부터 수집하려는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택합니다. **확인**을 클릭합니다.

7. **응용 프로그램 클레임**을 선택합니다. 여기서 등록 또는 로그인 환경이 성공적이면 응용 프로그램으로 다시 보낸 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **ID 공급자**, **우편 번호**, **새 사용자** 및 **사용자의 개체 ID**를 선택합니다.

8. **만들기**를 선택합니다. **등록 또는 로그인 정책** 블레이드에서 만든 정책이 **B2C_1_SiUpIn**(**B2C\_1\_** 조각이 자동으로 추가됨)으로 표시됩니다.

9. **B2C_1_SiUpIn**을 클릭하여 해당 정책을 엽니다.

10. **응용 프로그램** 드롭다운 메뉴에서 **Contoso B2C 앱**을 선택합니다. **회신 URL/리디렉션 URI** 드롭다운 메뉴에서 `https://localhost:44321/`을 선택합니다.

11. **지금 실행**을 선택합니다. 새 브라우저 탭이 열리고 등록 또는 로그인 소비자 환경을 통해 실행할 수 있습니다.
    
    > [!NOTE]
    > 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
    > 
    > 

## <a name="create-a-sign-up-policy"></a>등록 정책 만들기
응용 프로그램에서 등록을 사용하도록 설정하려면 등록 정책을 만들어야 합니다. 이 정책은 등록하는 동안 소비자가 경험한 환경 및 성공적인 등록 후에 응용 프로그램에서 받는 토큰의 내용을 설명합니다.

1. [먼저 다음 단계에 따라 Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. **등록 정책**을 선택합니다.

3. 블레이드의 위쪽에서 **+추가**를 선택합니다.

4. **이름**은 응용 프로그램에서 사용하는 등록 정책의 이름을 결정합니다. 예를 들어 **SiUp**을 입력합니다.

5. **ID 공급자**를 선택한 다음 **전자 메일 등록**을 선택합니다. 또한 소셜 ID 공급자가 이미 구성되어 있는 경우 필요에 따라 해당 소셜 ID 공급자를 선택할 수 있습니다. **확인**을 클릭합니다.

6. **등록 특성**을 선택합니다. 여기서 등록하는 동안 소비자로부터 수집하려는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택한 다음 **확인**을 클릭합니다.

7. **응용 프로그램 클레임**을 선택합니다. 여기서 등록 환경이 성공적이면 응용 프로그램으로 다시 보낸 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **ID 공급자**, **우편 번호**, **새 사용자** 및 **사용자의 개체 ID**를 선택합니다.

8. **만들기**를 선택합니다. **등록 정책** 블레이드에서 만든 정책이 **B2C_1_SiUp**(**B2C\_1\_** 조각이 자동으로 추가됨)으로 표시됩니다.

9. **B2C_1_SiUp**을 선택하여 해당 정책을 엽니다.

10. **응용 프로그램** 드롭다운에서 **Contoso B2C 앱**을 선택하고, **회신 URL/리디렉션 URI** 드롭다운에서 `https://localhost:44321/`을 선택합니다.

11. **지금 실행**을 선택합니다. 새 브라우저 탭이 열리고 응용 프로그램에 등록한 사용자 환경을 실행할 수 있습니다.
    
    > [!NOTE]
    > 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
    > 
    > 

## <a name="create-a-sign-in-policy"></a>로그인 정책 만들기
응용 프로그램에서 로그인을 사용하도록 설정하려면 로그인 정책을 만들어야 합니다. 이 정책은 로그인하는 동안 소비자가 경험한 환경 및 성공적인 로그인 후에 응용 프로그램에서 받는 토큰의 내용을 설명합니다.

1. [먼저 다음 단계에 따라 Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. **로그인 정책**을 선택합니다.

3. 블레이드의 위쪽에서 **+추가**를 선택합니다.

4. **이름**은 응용 프로그램에서 사용하는 로그인 정책의 이름을 결정합니다. 예를 들어 **SiIn**을 입력합니다.

5. **ID 공급자**를 선택한 다음 **로컬 계정 로그인**을 선택합니다. 또한 소셜 ID 공급자가 이미 구성되어 있는 경우 필요에 따라 해당 소셜 ID 공급자를 선택할 수 있습니다. **확인**을 클릭합니다.

6. **응용 프로그램 클레임**을 선택합니다. 여기서 로그인 환경이 성공적이면 응용 프로그램으로 다시 보낸 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **ID 공급자**, **우편 번호** 및 **사용자의 개체 ID**를 선택한 다음 **확인**을 클릭합니다.

7. **만들기**를 선택합니다. **로그인 정책** 블레이드에서 만든 정책이 **B2C_1_SiIn**(**B2C\_1\_** 조각이 자동으로 추가됨)으로 표시됩니다.

8. **B2C_1_SiIn**을 클릭하여 해당 정책을 엽니다.

9. **회신 URL/리디렉션 URI** 드롭다운 메뉴에서 **Contoso B2C 앱** 및 `https://localhost:44321/`을 선택합니다.

10. **지금 실행**을 선택합니다. 새 브라우저 탭이 열리고 응용 프로그램에 로그인한 사용자 환경을 실행할 수 있습니다.
    
    > [!NOTE]
    > 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>프로필 편집 정책 만들기
응용 프로그램에서 프로필 편집을 사용하도록 설정하려면 프로필 편집 정책을 만들어야 합니다. 이 정책은 프로필을 편집하는 동안 소비자가 경험한 환경 및 성공적인 완료 후에 응용 프로그램에서 받는 토큰의 내용을 설명합니다.

1. [먼저 다음 단계에 따라 Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

1. **프로필 편집 정책**을 선택합니다.

2. 블레이드의 위쪽에서 **+추가**를 선택합니다.

3. **이름**은 응용 프로그램에서 사용하는 프로필 편집 정책의 이름을 결정합니다. 예를 들어 **SiPe**를 입력합니다.

4. **ID 공급자**를 선택한 다음 **로컬 계정 로그인**을 선택합니다. 또한 소셜 ID 공급자가 이미 구성되어 있는 경우 필요에 따라 해당 소셜 ID 공급자를 선택할 수 있습니다. **확인**을 클릭합니다.

5. **프로필 특성**을 선택합니다. 여기서 소비자가 보고 편집할 수 있는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택한 다음 **확인**을 선택합니다.

6. **응용 프로그램 클레임**을 선택합니다. 여기서 프로필 편집 환경이 성공적이면 응용 프로그램으로 다시 보낸 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름** 및 **우편 번호**를 선택합니다.

8. **만들기**를 선택합니다. **프로필 편집 정책** 블레이드에서 만든 정책이 **B2C_1_SiPe**(**B2C\_1\_** 조각이 자동으로 추가됨)로 표시됩니다.

9. **B2C_1_SiPe**를 클릭하여 해당 정책을 엽니다.

10. **응용 프로그램** 드롭다운 메뉴에서 **Contoso B2C 앱**을 선택합니다. **회신 URL/리디렉션 URI** 드롭다운 메뉴에서 `https://localhost:44321/`을 선택합니다.

11. **지금 실행**을 선택합니다. 새 브라우저 탭이 열리고 응용 프로그램에서 프로필 편집 사용자 환경을 실행할 수 있습니다.
    
    > [!NOTE]
    > 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
    > 
    > 

## <a name="create-a-password-reset-policy"></a>암호 재설정 정책 만들기
응용 프로그램에서 세분화된 암호 재설정을 사용하도록 설정하려면 암호 재설정 정책을 만들어야 합니다. [Azure Active Directory B2C: 소비자를 위해 셀프 서비스 암호 재설정 설정](active-directory-b2c-reference-sspr.md)에서 지정한 테넌트 전체 암호 재설정은 로그인 정책에도 계속 적용할 수 있습니다. 

이 정책은 암호를 재설정하는 동안 소비자가 경험한 환경 및 성공적인 암호 재설정 후에 응용 프로그램에서 받는 토큰의 내용을 설명합니다.

1. [먼저 다음 단계에 따라 Azure Portal의 B2C 기능 블레이드로 이동합니다.](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)

2. **암호 재설정 정책**을 선택합니다.

3. 블레이드의 위쪽에서 **+추가**를 선택합니다.

4. **이름**은 응용 프로그램에서 사용하는 암호 재설정 정책의 이름을 결정합니다. 예를 들어 **SSPR**을 입력합니다.

5. **ID 공급자**를 선택한 다음 **이메일 주소를 사용하여 암호 재설정**을 선택합니다. **확인**을 클릭합니다.

6. **응용 프로그램 클레임**을 선택합니다. 여기서 암호 재설정 환경이 성공적이면 응용 프로그램으로 다시 보낸 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **사용자의 개체 ID**를 선택합니다.

7. **만들기**를 선택합니다. **암호 재설정 정책** 블레이드에서 만든 정책이 **B2C_1_SSPR**(**B2C\_1\_** 조각이 자동으로 추가됨)로 표시됩니다.

8. **B2C_1_SSPR**을 클릭하여 해당 정책을 엽니다.

9. **응용 프로그램** 드롭다운 메뉴에서 **Contoso B2C 앱**을 선택합니다. **회신 URL/리디렉션 URI** 드롭다운 메뉴에서 `https://localhost:44321/`을 선택합니다.

10. **지금 실행**을 선택합니다. 새 브라우저 탭이 열리고 응용 프로그램에서 암호 재설정 사용자 환경을 실행할 수 있습니다.
    
    > [!NOTE]
    > 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
    > 
    > 

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>등록 또는 로그인 정책을 암호 재설정 정책과 연결하려면 어떻게 하나요?
등록 또는 로그인 정책을 만드는 경우(로컬 계정 사용) 환경의 첫 번째 페이지에서 **암호 찾기** 링크가 표시됩니다. 이 링크를 클릭해도 암호 재설정 정책이 자동으로 트리거되지는 않습니다. 

대신 **`AADB2C90118`** 오류 코드가 앱에 반환됩니다. 앱에서 특정 암호 재설정 정책을 호출하여 이 오류 코드를 처리해야 합니다. 자세한 내용은 [정책을 연결하는 방법을 보여 주는 샘플](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)(영문)을 참조하세요.

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>등록 정책 또는 로그인 정책 또는 등록 정책 및 로그인 정책 중 어느 것을 사용해야 합니까?
등록 정책 및 로그인 정책에 대한 등록 정책이나 로그인 정책을 사용하는 것이 좋습니다.  

등록 정책 또는 로그인 정책에는 로그인 정책보다 많은 기능이 있습니다. 또한 페이지 UI 사용자 지정 기능을 사용할 수 있으며 지역화를 더 효율적으로 지원합니다. 

정책을 지역화할 필요가 없고, 브랜딩에 대한 약간의 사용자 지정 기능만 필요하고, 암호 재설정을 기본 제공하려는 경우에는 로그인 정책을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [토큰, 세션 및 Single Sign-On 구성](active-directory-b2c-token-session-sso.md)
* [소비자를 등록하는 동안 전자 메일 확인 사용 안 함](active-directory-b2c-reference-disable-ev.md)


