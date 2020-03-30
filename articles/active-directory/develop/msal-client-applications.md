---
title: 공개 및 기밀 클라이언트 앱(MSAL) | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)에서 공용 클라이언트 및 기밀 클라이언트 응용 프로그램에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d59819c0ab614b0f6cc102c7ebe8c760fb851599
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084128"
---
# <a name="public-client-and-confidential-client-applications"></a>공용 클라이언트 및 기밀 클라이언트 응용 프로그램
MSAL(Microsoft 인증 라이브러리)은 공용 클라이언트와 기밀 클라이언트의 두 가지 유형의 클라이언트를 정의합니다. 두 클라이언트 유형은 권한 부여 서버를 사용하여 안전하게 인증하고 클라이언트 자격 증명의 기밀성을 유지하는 능력에 의해 구별됩니다. 반면, Azure AD 인증 라이브러리(ADAL)는 *인증 컨텍스트(Azure* AD에 대한 연결)를 사용합니다.

- **기밀 클라이언트 응용 프로그램은** 서버(웹 앱, 웹 API 앱 또는 서비스/데몬 앱)에서 실행되는 앱입니다. 액세스하기 어려운 것으로 간주되며 이러한 이유로 응용 프로그램을 비밀로 유지할 수 있습니다. 기밀 클라이언트는 구성 시간 비밀을 보유할 수 있습니다. 클라이언트의 각 인스턴스에는 고유한 구성(클라이언트 ID 및 클라이언트 보안 포함)이 있습니다. 이러한 값은 최종 사용자가 추출하기 어렵습니다. 웹 앱은 가장 일반적인 기밀 클라이언트입니다. 클라이언트 ID는 웹 브라우저를 통해 노출되지만 비밀은 백 채널에서만 전달되며 직접 노출되지 않습니다.

    기밀 클라이언트 앱: <BR>
    ![웹](media/msal-client-applications/web-app.png) ![앱](media/msal-client-applications/web-api.png) ![웹 API 데몬/서비스](media/msal-client-applications/daemon-service.png)

- **공용 클라이언트 응용 프로그램은** 장치 또는 데스크톱 컴퓨터 또는 웹 브라우저에서 실행되는 앱입니다. 응용 프로그램 비밀을 안전하게 유지할 수 있는 신뢰할 수 없으므로 사용자를 대신하여 웹 API에만 액세스할 수 있습니다. 공용 클라이언트 흐름만 지원합니다. 공용 클라이언트는 구성 시간 비밀을 보유할 수 없으므로 클라이언트 암호가 없습니다.

    공용 클라이언트 앱: <BR>
    ![데스크톱](media/msal-client-applications/desktop-app.png) ![앱 브라우저리스 API](media/msal-client-applications/browserless-app.png) ![모바일 앱](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js에서는 공용 및 기밀 클라이언트 앱이 분리되지 않습니다.  MSAL.js는 클라이언트 앱을 사용자 에이전트 기반 앱으로 나타내며, 웹 브라우저와 같은 사용자 에이전트에서 클라이언트 코드가 실행되는 공용 클라이언트입니다. 이러한 클라이언트는 브라우저 컨텍스트에 공개적으로 액세스할 수 있으므로 비밀을 저장하지 않습니다.

## <a name="comparing-the-client-types"></a>클라이언트 유형 비교
다음은 공용 클라이언트와 기밀 클라이언트 앱 간의 몇 가지 유사점과 차이점입니다.

- 두 종류의 앱 모두 사용자 토큰 캐시를 유지 관리하며 토큰이 이미 토큰 캐시에 있는 경우 자동으로 토큰을 획득할 수 있습니다. 또한 기밀 클라이언트 앱에는 앱 자체에 대한 토큰에 대한 앱 토큰 캐시가 있습니다.
- 두 유형의 앱 모두 사용자 계정을 관리하며 사용자 토큰 캐시에서 계정을 얻거나 식별자에서 계정을 얻거나 계정을 제거할 수 있습니다.
- 공용 클라이언트 앱에는 토큰을 획득하는 네 가지 방법(4개의 인증 흐름)이 있습니다. 기밀 클라이언트 앱에는 토큰을 획득하는 세 가지 방법이 있습니다(ID 공급자의 URL을 계산하는 한 가지 방법은 엔드포인트에 권한을 부여합니다). 자세한 내용은 [토큰 획득 을 참조하십시오.](msal-acquire-cache-tokens.md)

ADAL을 사용한 경우 ADAL의 인증 컨텍스트와 달리 MSAL에서 클라이언트 ID(응용 프로그램 *ID* 또는 *앱 ID라고도*함)가 응용 프로그램 구성 시 한 번 전달된다는 것을 알 수 있습니다. 앱이 토큰을 획득할 때 다시 전달할 필요가 없습니다. 이는 공용 및 기밀 클라이언트 앱 모두에 해당됩니다. 기밀 클라이언트 앱의 생성자는 ID 공급자와 공유하는 비밀인 클라이언트 자격 증명도 전달됩니다.

## <a name="next-steps"></a>다음 단계
자세한 정보:
- [클라이언트 응용 프로그램 구성 옵션](msal-client-application-configuration.md)
- [MSAL.NET 사용하여 클라이언트 응용 프로그램 인스턴스화](msal-net-initializing-client-applications.md)
- [MSAL.js를 사용하여 클라이언트 응용 프로그램 인스턴스화](msal-js-initializing-client-applications.md)
