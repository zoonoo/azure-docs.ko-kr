---
title: "Azure Active Directory B2C: 액세스 토큰 요청 | Microsoft Docs"
description: "이 문서에서는 클라이언트 응용 프로그램을 설정하고 액세스 토큰을 획득하는 방법을 보여 줍니다."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 5d15ad7a4e75410390891b5e11f72c6a649ebf53
ms.lasthandoff: 03/23/2017


---


# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: 액세스 토큰 요청


액세스 토큰(**액세스\_토큰**으로 표시됨)은 클라이언트가 웹 API와 같은 [권한 부여 서버](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics)로 보호되는 리소스에 액세스하는 데 사용할 수 있는 보안 토큰의 형식입니다. 액세스 토큰은 [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens)로 표시되고 원하는 리소스 서버에 대한 정보 및 서버에 부여된 사용 권한을 포함합니다. 리소스 서버를 호출할 때 액세스 토큰은 HTTP 요청에 있어야 합니다.

이 문서에서는 클라이언트 응용 프로그램을 구성하고 `authorize` 및 `token` 끝점에서 **액세스\_토큰**을 획득하기 위한 요청을 만드는 방법을 설명합니다.

## <a name="prerequisite"></a>필수 요소

액세스 토큰을 요청하기 전에 먼저 웹 API를 등록하고 클라이언트 응용 프로그램에 부여할 수 있는 권한을 게시해야 합니다. [웹 API 등록](active-directory-b2c-app-registration.md) 섹션의 단계에 따라 시작합니다.

## <a name="granting-permissions-to-a-web-api"></a>웹 API에 대한 권한 부여

클라이언트 응용 프로그램에서 API에 대한 특정 사용 권한을 가지려면 클라이언트 응용 프로그램은 Azure Portal을 통해 이러한 사용 권한을 부여 받아야 합니다. 클라이언트 응용 프로그램에 권한을 부여하려면:

1. B2C 기능 블레이드의 **응용 프로그램** 메뉴로 이동합니다.
2. 클라이언트 응용 프로그램을 클릭합니다(없는 경우 [응용 프로그램 등록](active-directory-b2c-app-registration.md)).
3. **API 액세스**를 선택합니다.
4. **추가**를 클릭합니다.
5. 웹 API 및 부여하려는 범위(권한)를 선택합니다.
6. **확인**을 클릭합니다.

> [!NOTE]
> Azure AD B2C는 클라이언트 응용 프로그램 사용자의 동의를 묻지 않습니다. 대신 모든 동의는 위에서 설명한 응용 프로그램 간에 구성된 권한에 따라 관리자에 의해 제공됩니다. 응용 프로그램에 대한 권한 부여가 해지되면 이전에 해당 권한을 획득할 수 있었던 모든 사용자는 더 이상 권한을 획득할 수 없습니다.

## <a name="requesting-a-token"></a>토큰 요청

리소스 응용 프로그램에 대한 액세스 토큰을 가져오려면 클라이언트 응용 프로그램에서 요청의 **범위** 매개 변수에서 원하는 사용 권한을 지정해야 합니다. 예를 들어 `https://contoso.onmicrosoft.com/notes`의 앱 ID URI를 가진 리소스 응용 프로그램에 대한 "읽기" 권한을 얻으려면 범위는 `https://contoso.onmicrosoft.com/notes/read`가 됩니다. 다음은 `authorize` 끝점에 대한 인증 코드 요청의 예입니다.

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

같은 요청에 여러 권한을 얻기 위해 단일 **범위** 매개 변수에 공백으로 구분된 여러 항목을 추가할 수 있습니다. 예:

디코딩된 URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

인코딩된 URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

클라이언트 응용 프로그램에 부여된 것보다 더 많은 범위/권한을 리소스에 대해 요청할 수 있습니다. 이 경우 하나 이상의 권한이 부여되면 호출이 성공합니다. 결과 **액세스\_토큰**은 성공적으로 부여된 권한으로 채워진 해당 "scp" 클레임을 갖습니다.

> [!NOTE] 
> 동일한 요청에 두 개의 다른 웹 리소스에 대한 권한 요청을 지원하지 않습니다. 이러한 종류의 요청은 실패합니다.

### <a name="special-cases"></a>특수 사례

OpenID Connect 표준은 몇 가지 특별한 "범위" 값을 지정합니다. 다음과 같은 특별한 범위는 "사용자의 프로필에 액세스"에 대한 권한을 나타냅니다.

* **openid**: ID 토큰 요청
* **오프라인\_액세스**: 새로 고침 토큰을 요청합니다(인증 코드 흐름 사용).

`authorize` 요청에서 "응답\_형식" 매개 변수가 "토큰"을 포함하는 경우 "범위" 매개 변수는 부여될 수 있는 적어도 하나의 리소스 권한을 포함해야 합니다("openid" 및 "오프라인\_액세스" 이외). 그렇지 않은 경우 `authorize` 요청은 실패와 함께 종료됩니다.

## <a name="the-returned-token"></a>반환된 토큰

성공적으로 생성된 **액세스\_토큰**(`authorize` 또는 `token` 끝점에서)에 다음 클레임이 표시됩니다.

| 이름 | 클레임 | 설명 |
| --- | --- | --- |
|대상 |`aud` |토큰에서 액세스를 부여하는 단일 리소스의 \*응용 프로그램 ID\*입니다. |
|범위 |`scp` |리소스에 부여된 권한입니다. 여러 부여된 권한은 공백으로 구분됩니다. |
|권한 있는 주체 |`azp` |요청을 시작한 클라이언트 응용 프로그램의 \*응용 프로그램 ID\*입니다. |

API에서 **액세스\_토큰**을 받으면 [토큰의 유효성을 검사](active-directory-b2c-reference-tokens.md)하여 토큰이 인증되었으며 올바른 클레임을 가졌음을 증명해야 합니다.

Microsoft는 사용자 의견 및 제안을 항상 환영합니다! 이 토픽을 완료하기가 어렵거나 이 콘텐츠를 개선할 사항이 있는 경우 페이지의 맨 아래에 의견을 보내주시면 감사하겠습니다. 기능 요청이 있는 경우 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)에 추가해 주세요.
