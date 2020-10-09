---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87311547"
---
> [!IMPORTANT]
> 현재 MSAL.js 2.0은 Azure AD B2C를 PKCE 권한 부여 코드 흐름에 사용하는 것을 지원하지 않습니다. 지금은 Azure AD B2C에서 [자습서: 애플리케이션 등록][implicit-flow]에 설명된 대로 암시적 흐름을 사용하는 것이 좋습니다. 이 문제에 대한 진행률을 추적하려면 [MSAL.js wiki][msal-wiki]를 참조하세요.

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
