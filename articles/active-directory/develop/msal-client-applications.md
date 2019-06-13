---
title: 클라이언트 응용 프로그램 (Microsoft 인증 라이브러리) | Azure
description: 응용 프로그램에서 인증 라이브러리 (MSAL (Microsoft) 공용 클라이언트 및 기밀 클라이언트에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430820"
---
# <a name="public-client-and-confidential-client-applications"></a>공용 클라이언트 및 기밀 클라이언트 응용 프로그램
Microsoft 인증 라이브러리 (MSAL) 두 가지 유형의 클라이언트를 정의 합니다: 공용 클라이언트 및 기밀 클라이언트입니다. 두 클라이언트 유형은 권한 부여 서버를 사용 하 여 안전 하 게 인증 하 고 클라이언트 자격 증명의 기밀을 유지 하는 기능으로 구분 됩니다. Azure AD 인증 라이브러리 (ADAL)을 사용 하는 반면 *인증 컨텍스트* (하는 Azure AD에 연결 된).

- **기밀 클라이언트 응용 프로그램** (web apps, Web API 앱 또는 서비스/디먼 앱도) 서버에서 실행 되는 앱입니다. 간주 어렵고 액세스, 이런 이유로 응용 프로그램 비밀을 유지 하는 지원 합니다. 비밀 클라이언트는 구성 시 암호를 포함할 수 있습니다. 클라이언트의 각 인스턴스에 고유한 구성 (클라이언트 ID 및 클라이언트 암호 포함)에 이러한 값은 최종 사용자가 추출에 대 한 어렵습니다. 웹 앱은 가장 일반적인 기밀 클라이언트입니다. 클라이언트 ID는 웹 브라우저를 통해 노출 되지만 암호 백 채널에만 전달 되며 직접 노출 되지 않습니다.

    기밀 클라이언트 앱: <BR>
    ![웹 앱](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![데몬/서비스](media/msal-client-applications/daemon-service.png)

- **공용 클라이언트 응용 프로그램** 장치나 데스크톱 컴퓨터 또는 웹 브라우저에서 실행 되는 앱입니다. 이러한 사용자 대신 Web Api에만 액세스 하도록 응용 프로그램 비밀을 안전 하 게 유지 하려면 신뢰할 수 있는 아닙니다. (공용 클라이언트 흐름만 지원합니다.) 공용 클라이언트는 클라이언트 비밀 있으므로 구성 시 암호를 포함할 수 없습니다.

    공용 클라이언트 앱: <BR>
    ![데스크톱 앱](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![모바일 앱](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js를에서 공용 및 기밀 클라이언트 앱의 분리 되지 않습니다.  MSAL.js는 사용자 에이전트 기반 앱으로 공용 클라이언트 웹 브라우저 사용자 에이전트에서 실행 되는 클라이언트 코드 클라이언트 앱을 나타냅니다. 브라우저 컨텍스트를 공개적으로 액세스할 수 없으므로 이러한 클라이언트 비밀을 저장 하지 않습니다.

## <a name="comparing-the-client-types"></a>클라이언트 유형 비교
다음은 몇 가지 간의 유사점 및 차이점 공용 클라이언트 및 기밀 클라이언트 앱:

- 두 종류의 앱 사용자 토큰 캐시를 유지 관리 하 고 자동으로 (토큰 인 경우 이미 토큰 캐시에서) 토큰을 획득할 수 있습니다. 기밀 클라이언트 앱에는 앱 자체에 대 한 토큰에 대 한 앱 토큰 캐시를 수도 있습니다.
- 두 가지 유형의 앱 사용자 계정 관리 및 수 계정을 사용자 토큰 캐시에서 해당 식별자에서 계정 가져오기 가져오거나 계정을 제거 합니다.
- 공용 클라이언트 앱 (네 가지 인증 흐름) 토큰을 획득 하는 네 가지 방법에 있습니다. 기밀 클라이언트 앱에 토큰을 획득 하는 세 가지 방법 (및 권한 부여 끝점 id 공급자의 URL을 계산 하는 한 가지 방법은). 자세한 내용은 [토큰을 얻기](msal-acquire-cache-tokens.md)합니다.

ADAL을 사용한 경우 나타날 수 있습니다는 ADAL의 인증 컨텍스트를 MSAL 클라이언트 ID와 달리 (라고도 합니다 *응용 프로그램 ID* 또는 *앱 ID*) 응용 프로그램의 생성 시 한 번 전달 됩니다. 앱 토큰을 획득할 때 다시 전달할 필요가 없습니다. 이 공용 및 기밀 클라이언트 앱에 대 한 둘 다 마찬가지입니다. 기밀 클라이언트 앱의 생성자에 클라이언트 자격 증명 전달 될 수도 있습니다: id 공급자를 사용 하 여 공유 암호.

## <a name="next-steps"></a>다음 단계
자세한 정보:
- [클라이언트 응용 프로그램 구성 옵션](msal-client-application-configuration.md)
- [MSAL.NET을 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-net-initializing-client-applications.md)
- [MSAL.js를 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-js-initializing-client-applications.md)
