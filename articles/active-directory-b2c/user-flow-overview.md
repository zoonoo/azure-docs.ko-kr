---
title: Azure Active Directory B2C의 사용자 흐름 | Microsoft Docs
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 확장 가능한 정책 프레임워크 및 다양한 사용자 흐름을 만드는 방법을 자세히 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a7736602fafb740d1d76fa09fd26da25e4ff9f5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481600"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 흐름

응용 프로그램에 대 한 가장 일반적인 id 작업을 설정 하는 데 도움이 되도록 Azure AD B2C 포털에는 **사용자 흐름**이라고 하는 미리 정의 된 구성 가능한 정책이 포함 되어 있습니다. 사용자 흐름을 사용 하면 사용자가 로그인, 등록, 프로필 편집, 암호 재설정 등의 작업을 수행할 때 응용 프로그램과 상호 작용 하는 방법을 결정할 수 있습니다. 사용자 흐름을 사용 하 여 다음과 같은 기능을 제어할 수 있습니다.

- Facebook 등의 소셜 계정 또는 로컬 계정과 같은 로그인에 사용되는 계정 유형
- 소비자로부터 수집할 특성(예: 이름, 우편 번호 및 신발 크기)
- Azure Multi-Factor Authentication
- 사용자 인터페이스의 사용자 지정
- 애플리케이션이 토큰의 클레임으로 받는 정보

테넌트에 여러 유형의 많은 사용자 흐름을 만들고 필요에 따라 애플리케이션에서 사용할 수 있습니다. 애플리케이션에 사용자 흐름을 다시 사용할 수 있습니다. 이러한 유연성 덕분에 코드 변경 없이 또는 최소한의 변경으로 ID 환경을 정의하고 수정할 수 있습니다. 애플리케이션은 사용자 흐름 매개 변수를 포함하는 표준 HTTP 인증 요청을 사용하여 사용자 흐름을 트리거합니다. 사용자 지정 [토큰](tokens-overview.md)은 응답으로 수신됩니다.

다음 예제에서는 사용할 사용자 흐름을 지정하는 “p” 쿼리 문자열 매개 변수를 보여 줍니다.

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>사용자 흐름 버전

Azure AD B2C에는 다음과 같은 여러 유형의 사용자 흐름이 포함 됩니다.

- **가입 및 로그인** - 단일 구성으로 가입 및 로그인 환경을 둘 다 처리합니다. 사용자는 컨텍스트에 따라 올바른 경로로 안내됩니다. 또한 별도의 **등록** 또는 **로그인** 사용자 흐름이 포함 됩니다. 그러나 일반적으로 결합 된 등록 및 로그인 사용자 흐름을 사용 하는 것이 좋습니다.
- **프로필 편집** - 사용자가 해당 프로필 정보를 편집할 수 있습니다.
- **암호 재설정** - 사용자가 암호를 재설정할 수 있는지 여부 및 방법을 구성할 수 있습니다.

대부분의 사용자 흐름 유형에는 **권장** 버전과 **표준** 버전이 모두 있습니다. 자세한 내용은 [사용자 흐름 버전](user-flow-versions.md)을 참조 하세요.

> [!IMPORTANT]
> 이전에 Azure AD B2C 사용자 흐름으로 작업 한 경우 사용자 흐름 버전을 참조 하는 방법을 변경 했습니다. 이전에는 V1 (프로덕션 준비) 버전 및 V 1.1 및 V2 (preview) 버전이 제공 되었습니다. 이제 사용자 흐름을 두 가지 버전으로 통합 했습니다.
>
>- **권장** 사용자 흐름은 사용자 흐름의 새로운 미리 보기 버전입니다. 이러한 기능은 철저 하 게 테스트 되었으며 레거시 **V2** 및 **v 1.1** 버전의 모든 기능을 결합 합니다. 향후에는 새로운 권장 사용자 흐름이 유지 되 고 업데이트 됩니다. 이러한 새로운 권장 사용자 흐름으로 이동 하면 새 기능이 출시 될 때 새 기능에 액세스할 수 있습니다.
>- 이전의 **V1**이라고 하는 **표준** 사용자 흐름이 일반적으로 사용 가능한 프로덕션 맞춤형 사용자 흐름입니다. 사용자 흐름이 중요 하 고 안정적인 버전에 따라 달라 지는 경우 표준 사용자 흐름을 계속 사용할 수 있으므로 이러한 버전이 유지 되 고 업데이트 되지 않습니다.
>
>모든 레거시 미리 보기 사용자 흐름 (V 1.1 및 V2)은 **2021 년 8 월 1 일부 터**사용 중단에 대 한 경로에 있습니다. 가능 하면 항상 최신 기능과 업데이트를 활용할 수 있도록 가능한 한 빨리 [새로운 **권장** 사용자 흐름으로 전환](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) 하는 것이 좋습니다.

## <a name="linking-user-flows"></a>사용자 흐름 연결

로컬 계정을 사용하는 **가입 또는 로그인** 사용자 흐름에서는 환경의 첫 페이지에 **암호 찾기** 링크가 포함됩니다. 이 링크를 클릭해도 암호 재설정 사용자 흐름이 자동으로 트리거되지는 않습니다.

대신 `AADB2C90118` 오류 코드가 애플리케이션에 반환됩니다. 애플리케이션은 암호를 재설정하는 특정 사용자 흐름을 실행하여 이 오류 코드를 처리해야 합니다. 예제를 보려면 사용자 흐름 연결을 보여 주는 [단순 ASP.NET 샘플](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)을 살펴보세요.

## <a name="email-address-storage"></a>메일 주소 스토리지

사용자 흐름의 일부로 메일 주소가 필요할 수 있습니다. 사용자가 소셜 ID 공급자로 인증하는 경우 메일 주소가 **otherMails** 속성에 저장됩니다. 로컬 계정이 사용자 이름을 기반으로 하는 경우 메일 주소가 강력한 인증 정보 속성에 저장됩니다. 로컬 계정이 메일 주소를 기반으로 하는 경우 메일 주소가 **signInNames** 속성에 저장됩니다.

이러한 경우에는 메일 주소가 확인되지 않습니다. 테넌트 관리자는 로컬 계정에 대한 기본 정책에서 메일 확인을 사용하지 않도록 설정할 수 있습니다. 메일 주소 확인이 사용되더라도 소셜 ID 공급자가 제공한 것이며 변경되지 않은 경우에는 주소가 확인되지 않습니다.

Microsoft Graph API를 통해 **Othermails** 및 **signInNames** 속성만 노출 됩니다. 강력한 인증 정보 속성의 메일 주소를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

권장 사용자 흐름을 만들려면 [자습서: 사용자 흐름 만들기](tutorial-create-user-flows.md)의 지침을 따르세요.
