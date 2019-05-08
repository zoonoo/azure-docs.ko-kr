---
title: 클라이언트 응용 프로그램 (Microsoft 인증 라이브러리) | Azure
description: 응용 프로그램에서 인증 라이브러리 (MSAL (Microsoft) 공용 클라이언트 및 기밀 클라이언트에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077142"
---
# <a name="public-client-and-confidential-client-applications"></a>공용 클라이언트 및 기밀 클라이언트 응용 프로그램
Microsoft 인증 라이브러리 (MSAL) 두 가지 유형의 클라이언트를 정의 합니다: 공용 클라이언트 및 기밀 클라이언트입니다. 두 클라이언트 유형은 권한 부여 서버를 사용 하 여 안전 하 게 인증 하 고 클라이언트 자격 증명의 기밀을 유지 하는 기능으로 구분 됩니다.  반면, Azure AD 인증 라이브러리 (ADAL)에 인증 컨텍스트 (즉, Azure AD에 대 한 연결) 개념이 있습니다.

- **기밀 클라이언트 응용 프로그램** 응용 프로그램 (웹 앱, 웹 API 또는 서비스/디먼 응용 프로그램도) 서버에서 실행 됩니다. 액세스 하기 어려우며 응용 프로그램 비밀을 유지 하므로 가능 간주 됩니다. 비밀 클라이언트는 구성 시간 비밀을 저장할 수 있습니다. 클라이언트의 각 인스턴스에 고유한 구성 (clientId 및 암호 포함)에 이러한 값은 최종 사용자가 추출에 대 한 어렵습니다. 웹 앱은 가장 일반적인 기밀 클라이언트입니다. 클라이언트 ID는 웹 브라우저를 통해 노출 되지만 암호 백 채널에만 전달 되며 직접 노출 되지 않습니다.

    기밀 클라이언트 앱: <BR>
    ![웹 앱](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![데몬/서비스](media/msal-client-applications/daemon-service.png)

- **공용 클라이언트 응용 프로그램** 장치나 데스크톱 컴퓨터 또는 웹 브라우저에서 실행 하는 응용 프로그램입니다. 응용 프로그램 비밀을 안전 하 게 유지 하 고 따라서 (만 지 공용 클라이언트 흐름) 사용자를 대신 하 여 웹 Api에만 액세스 하도록 신뢰 하지 않습니다. 공용 클라이언트는 구성 시간 비밀을 저장할 수 있고 결과적으로 클라이언트 비밀이 아닙니다 할 수 없습니다.

    공용 클라이언트 응용 프로그램: <BR>
    ![데스크톱 앱](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![모바일 앱](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js를에서 공용 및 기밀 클라이언트 앱의 분리 되지 않습니다.  MSAL.js는 클라이언트 앱 사용자 에이전트 기반 응용 프로그램으로 클라이언트를 나타내는 공용 웹 브라우저와 같은 사용자 에이전트에서 실행 되는 클라이언트 코드입니다.  이러한 클라이언트는 브라우저 컨텍스트가 공개적으로 액세스할 수 있으므로 암호를 저장 하지 마십시오.

## <a name="comparing-the-client-types"></a>클라이언트 유형 비교
일부의 공통점과 차이점 공용 클라이언트 및 기밀 클라이언트 응용 프로그램:

- 두 종류의 응용 프로그램 사용자 토큰 캐시를 유지 관리 하 고 자동으로 (경우에는 토큰은 토큰 캐시를 이미 있는) 토큰을 획득할 수 있습니다. 기밀 클라이언트 응용 프로그램에는 앱 자체는 토큰에 대 한 앱 토큰 캐시를 수도 있습니다.
- 둘 다 사용자 계정 관리 및 수 계정을 사용자 토큰 캐시에서 해당 식별자에서 계정 가져오기 가져오거나 계정을 제거 합니다.
- 공용 클라이언트 응용 프로그램에는 기밀 클라이언트 응용 프로그램 3 개 (및 권한 부여 끝점 id 공급자의 URL을 계산 하는 하나의 방법) 하는 반면 (네 가지 인증 흐름) 토큰을 획득 하기 위해 다음 네 가지입니다. 자세한 내용은 참조 시나리오 및 토큰을 획득 합니다.

이전에 ADAL을 사용 하는 경우에 ADAL의 인증 컨텍스트를 달리 MSAL의 응용 프로그램 및 더 이상 생성 시 ID (응용 프로그램 ID 또는 앱 ID 라고도 함)가 한 번에 전달 하는 클라이언트를 반복 해야 토큰을 확보 하는 경우를 확인할 수 없습니다. 이 경우 두 공용 및 기밀 클라이언트 응용 프로그램에 대 한 합니다. 기밀 클라이언트 응용 프로그램의 생성자에 클라이언트 자격 증명 전달 될 수도 있습니다: id 공급자를 사용 하 여 공유 암호.

## <a name="next-steps"></a>다음 단계
자세한 정보:
- [클라이언트 응용 프로그램 구성 옵션](msal-client-application-configuration.md)
- [MSAL.NET을 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-net-initializing-client-applications.md)합니다.
- [MSAL.js를 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-js-initializing-client-applications.md)합니다.
