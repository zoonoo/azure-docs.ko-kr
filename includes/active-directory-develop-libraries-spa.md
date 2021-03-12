---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010707"
---
| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                                                    | 패키지                                                                      | 도달<br/>시작됨                             | 사용자 로그인                                         | 웹 Api 액세스                                                 | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [Msal 각도 v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
| Angular              | [Msal 각도](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[자습서](../articles/active-directory/develop/tutorial-v2-angular.md)| ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| AngularJS            | [Msal AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [자습서](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [자습서](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| React                | [Msal 반응](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

<sup>2</sup> pcke만 사용 하는 [인증 코드 흐름][auth-code-flow] (권장) 

<sup>3</sup> [암시적 부여 흐름][implicit-flow] 에만 해당 합니다.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md