---
title: 웹 Api를 호출 하는 데스크톱 앱 등록-Microsoft identity platform | Microsoft
description: 웹 Api (앱 등록)를 호출 하는 데스크톱 앱을 빌드하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f7f2dfdbf404a092773857a0f7727618cd429a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965537"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>웹 Api를 호출 하는 데스크톱 앱-앱 등록

이 문서에서는 데스크톱 응용 프로그램에 대 한 앱 등록 세부 사항을 설명 합니다.

## <a name="supported-accounts-types"></a>지원 되는 계정 유형

데스크톱 응용 프로그램에서 지원 되는 계정 유형은 강화 하려는 환경에 따라 달라 집니다. 이 관계로 인해 지원 되는 계정 유형은 사용 하려는 흐름에 따라 달라 집니다.

### <a name="audience-for-interactive-token-acquisition"></a>대화형 토큰 획득을 위한 대상 그룹

데스크톱 응용 프로그램에서 대화형 인증을 사용 하는 경우 모든 [계정 유형에](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)서 사용자에 게 로그인 할 수 있습니다.

### <a name="audience-for-desktop-app-silent-flows"></a>데스크톱 앱 자동 흐름의 대상

- Windows 통합 인증 또는 사용자 이름/암호를 사용 하려면 응용 프로그램에서 사용자 고유의 테 넌 트 (LOB 개발자) 또는 Azure Active directory 조직 (ISV 시나리오)에 로그인 해야 합니다. 이러한 인증 흐름은 Microsoft 개인 계정에 대해 지원 되지 않습니다.
- 장치 코드 흐름을 사용 하려는 경우에는 Microsoft 개인 계정으로 사용자를 로그인 할 수 없습니다.
- B2C 인증 기관 및 정책을 전달 하는 소셜 id를 사용 하 여 사용자를 로그인 하는 경우에는 대화형 및 사용자 이름 암호만 사용할 수 있습니다.

## <a name="redirect-uris"></a>Átirányítási URI azonosítók

데스크톱 응용 프로그램에서 사용할 리디렉션 Uri는 사용 하려는 흐름에 따라 달라 집니다.

- **대화형 인증** 또는 **장치 코드 흐름**을 사용 하는 경우 `https://login.microsoftonline.com/common/oauth2/nativeclient`를 사용 하는 것이 좋습니다. 응용 프로그램에 대 한 **인증** 섹션에서 해당 URL을 클릭 하 여이 구성을 달성할 수 있습니다.
  
  > [!IMPORTANT]
  > Today MSAL.NET는 Windows에서 실행 되는 데스크톱 응용 프로그램 (`urn:ietf:wg:oauth:2.0:oob`)에서 기본적으로 다른 리디렉션 URI를 사용 합니다. 나중에이 기본값을 변경 하는 것이 좋습니다. 따라서를 사용 하는 것이 좋습니다 `https://login.microsoftonline.com/common/oauth2/nativeclient`

- MacOS에 대 한 기본 목표-C 또는 Swift 앱을 빌드하는 경우 응용 프로그램의 번들 식별자를 기반으로 하는 redirectUri를 **msauth auth** 형식으로 등록할 수 있습니다. > > <:/.. s u s. i s. i s.
- 앱이 Windows 통합 인증 또는 사용자 이름/암호를 사용 하는 경우에는 응용 프로그램에 대 한 리디렉션 URI를 등록할 필요가 없습니다. 이러한 흐름은 Microsoft identity platform v2.0 끝점으로의 왕복을 수행 하 고 특정 URI에서 응용 프로그램이 다시 호출 되지 않습니다.
- 리디렉션 Uri가 없는 기밀 클라이언트 응용 프로그램 흐름 (디먼 응용 프로그램에서 사용 되는 클라이언트 자격 증명 흐름)에서 장치 코드 흐름, Windows 통합 인증 및 사용자 이름/암호를 구분 하려면 응용 프로그램이 공용 클라이언트 응용 프로그램 임을 표시 해야 합니다. 이 구성을 얻으려면 응용 프로그램에 대 한 **인증** 섹션으로 이동 합니다. 그런 다음 **고급 설정** 하위 섹션의 **기본 클라이언트 형식** 단락에서 **예** 를 선택 하 여 질문 **처리 응용 프로그램을 공용 클라이언트로**설정 합니다.

  ![공용 클라이언트 허용](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-engedélyek

데스크톱 응용 프로그램은 로그인 한 사용자에 대 한 Api를 호출 합니다. 위임 된 권한을 요청 해야 합니다. 그러나 [디먼 응용 프로그램](scenario-daemon-overview.md)에서만 처리 되는 응용 프로그램 권한은 요청할 수 없습니다.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [데스크톱 앱-앱 구성](scenario-desktop-app-configuration.md)
