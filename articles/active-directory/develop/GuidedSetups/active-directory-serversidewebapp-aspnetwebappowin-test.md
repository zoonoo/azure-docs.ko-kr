---
title: "Azure AD v2 ASP.NET 웹 서버 시작 - 테스트 | Microsoft Docs"
description: "OpenID Connect 표준을 사용하여 기존 웹 브라우저 기반 응용 프로그램을 사용하는 ASP.NET 솔루션에서 Microsoft 로그인 구현"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: bfa2563a6a58370d9a611440017441a751b46244
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>코드 테스트

`F5` 키를 눌러 Visual Studio에서 프로젝트를 실행합니다. 브라우저가 열리고 *http://localhost:{port}*로 이동합니다. 이 페이지에는 *Microsoft로 로그인* 단추가 표시됩니다. 계속해서 이 단추를 클릭하여 로그인합니다.

테스트할 준비가 되면 회사 또는 학교(Azure Active Directory)나 개인(live.com, outlook.com) 계정을 사용하여 로그인합니다. 

![Microsoft로 로그인 브라우저 창](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Microsoft로 로그인 브라우저 창](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>예상 결과
로그인하면 사용자는 Microsoft 응용 프로그램 등록 포털의 응용 프로그램 등록 정보에 지정된 HTTPS URL인 웹 사이트의 홈페이지로 리디렉션됩니다. 이제 이 페이지에 *Hello {User}*({User} 님, 안녕하세요?) 및 로그아웃 링크, 사용자의 클레임을 표시하는 링크(앞에서 만든 권한 부여 컨트롤러에 대한 링크)가 표시됩니다.

### <a name="see-users-claims"></a>사용자의 클레임 보기
사용자의 클레임을 보려면 하이퍼링크를 선택합니다. 그러면 인증된 사용자에게만 제공되는 보기 및 컨트롤러로 이동합니다.

#### <a name="expected-results"></a>예상 결과
 로그온한 사용자의 기본 속성을 포함하는 표가 표시됩니다.

| 속성 | 값 | 설명|
|---|---|---|
| 이름 | {User Full Name} | 사용자의 이름과 성
|사용자 이름 | <span>user@domain.com</span>| 로그온한 사용자를 식별하는 데 사용되는 사용자 이름
| 제목| {Subject}|웹에서 사용자 로그온을 고유하게 식별하는 문자열|
| 테넌트 ID| {Guid}| 사용자의 Azure Active Directory 조직을 고유하게 나타내는 *guid*.|

또한 인증 요청에 포함된 모든 사용자 클레임을 포함하는 표가 표시됩니다. 모든 클레임과 ID 토큰 및 설명 목록은 이 [문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "ID 토큰의 클레임 목록")을 참조하세요.


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>*[Authorize]* 특성이 있는 메서드 액세스 테스트(선택 사항)
이 단계에서는 익명 사용자로 인증된 컨트롤러 액세스를 테스트합니다.<br/>
사용자 로그아웃 링크를 선택하고 로그아웃 프로세스를 완료합니다.<br/>
이제 브라우저에서 http://localhost:{port}/authenticated를 입력하여 `[Authorize]` 특성으로 보호되는 컨트롤러에 액세스합니다.

#### <a name="expected-results"></a>예상 결과
보기를 보려면 인증하라는 메시지가 표시됩니다.

## <a name="additional-information"></a>추가 정보

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>전체 웹 사이트 보호
전체 웹 사이트를 보호하려면 `Global.asax` `Application_Start` 메서드의 `GlobalFilters`에 `AuthorizeAttribute`를 추가합니다.

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **한 조직의 사용자만 응용 프로그램에 로그인하도록 제한하는 방법**

> 기본적으로 Azure Active Directory와 통합된 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)도 응용 프로그램에 로그인할 수 있습니다. 

> 응용 프로그램에서 한 Azure Active Directory 조직의 로그인만 허용하도록 하려면 *web.config*의 `Tenant` 매개 변수를 `Common`에서 조직의 테넌트 이름(예: *contoso.onmicrosoft.com*)으로 바꿉니다. 그런 다음 *OWIN 시작 클래스*의 `ValidateIssuer` 인수를 `true`로 변경합니다.

> 특정 조직 목록의 사용자만 허용하려면 `ValidateIssuer`를 true로 설정하고 `ValidIssuers` 매개 변수를 사용하여 조직 목록을 지정합니다.

> 또 다른 방법으로 IssuerValidator 매개 변수를 사용하여 발급자의 유효성을 검사하는 사용자 지정 메서드를 구현합니다. `TokenValidationParameters`에 대한 자세한 내용은 [이](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN 문서") MSDN 문서를 참조하세요.

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]