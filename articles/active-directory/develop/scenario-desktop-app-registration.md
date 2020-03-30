---
title: 웹 API를 호출하는 데스크톱 앱 등록 - Microsoft ID 플랫폼 | Azure
description: 웹 API(앱 등록)를 호출하는 데스크톱 앱을 빌드하는 방법 알아보기
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
ms.openlocfilehash: c55fc9eb94a88dba1ab9fc915fe84bc2dd7d4d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702184"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>웹 API를 호출하는 데스크톱 앱: 앱 등록

이 문서에서는 데스크톱 응용 프로그램에 대한 앱 등록 세부 사항을 다룹니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

데스크톱 응용 프로그램에서 지원되는 계정 유형은 점등하려는 경험에 따라 다릅니다. 이 관계 때문에 지원되는 계정 형식은 사용하려는 흐름에 따라 달라집니다.

### <a name="audience-for-interactive-token-acquisition"></a>대화형 토큰 수집을 위한 청중

데스크톱 응용 프로그램에서 대화형 인증을 사용하는 경우 모든 [계정 유형의](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)사용자를 로그인할 수 있습니다.

### <a name="audience-for-desktop-app-silent-flows"></a>데스크톱 앱 무음 흐름에 대한 잠재고객

- 통합 Windows 인증 또는 사용자 이름 및 암호를 사용하려면 LOB(기간 업무) 개발자인 경우 응용 프로그램이 사용자 고유의 테넌트의 사용자를 로그인해야 합니다. 또는 Azure Active Directory 조직에서는 ISV 시나리오인 경우 응용 프로그램이 사용자 고유의 테넌트에 로그인해야 합니다. 이러한 인증 흐름은 Microsoft 개인 계정에서 지원되지 않습니다.
- 장치 코드 흐름을 사용하려는 경우 Microsoft 개인 계정으로 사용자를 로그인할 수 없습니다.
- B2C(비즈니스-상거래) 권한 및 정책을 통과하는 소셜 ID를 사용하여 사용자를 로그인하는 경우 대화형 및 사용자 이름 암호 인증만 사용할 수 있습니다.

## <a name="redirect-uris"></a>리디렉션 URI

데스크톱 응용 프로그램에서 사용할 리디렉션 URI는 사용하려는 흐름에 따라 다릅니다.

- 대화형 인증 또는 장치 코드 흐름을 `https://login.microsoftonline.com/common/oauth2/nativeclient`사용하는 경우 을 사용합니다. 이 구성을 달성하려면 응용 프로그램의 **인증** 섹션에서 해당 URL을 선택합니다.
  
  > [!IMPORTANT]
  > 오늘날 MSAL.NET Windows()에서`urn:ietf:wg:oauth:2.0:oob`실행되는 데스크톱 응용 프로그램에서 기본적으로 다른 리디렉션 URI를 사용합니다. 나중에 이 기본값을 변경하려는 것이 좋습니다. `https://login.microsoftonline.com/common/oauth2/nativeclient`

- macOS용 기본 Objective-C 또는 Swift 앱을 빌드하는 경우 응용 프로그램의 번들 식별자를 기반으로 리디렉션 URI를 다음 형식으로 등록합니다: msauth.<your.app.bundle.id>://auth. <your.app.bundle.id> 응용 프로그램의 번들 식별자로 대체합니다.
- 앱에서 통합 Windows 인증 또는 사용자 이름과 암호만 사용하는 경우 응용 프로그램에 대한 리디렉션 URI를 등록할 필요가 없습니다. 이러한 흐름은 Microsoft ID 플랫폼 v2.0 끝점으로 왕복합니다. 응용 프로그램은 특정 URI에서 다시 호출되지 않습니다.
- 장치 코드 흐름, 통합 Windows 인증 및 사용자 이름과 암호를 리디렉션URI가 없는 기밀 클라이언트 응용 프로그램 흐름(데몬 응용 프로그램에 사용되는 클라이언트 자격 증명 흐름)을 구분하려면 응용 프로그램은 공용 클라이언트 응용 프로그램입니다. 이 구성을 달성하려면 응용 프로그램의 **인증** 섹션으로 이동하십시오. 고급 **설정** 하위 섹션에서 **기본 클라이언트 유형** 단락에서 응용 프로그램 처리에 대해 **예가** **공용 클라이언트로**선택합니다.

  ![공용 클라이언트 허용](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 사용 권한

데스크톱 응용 프로그램은 로그인한 사용자에 대해 API를 호출합니다. 위임된 권한을 요청해야 합니다. [데몬 응용](scenario-daemon-overview.md)프로그램에서만 처리되는 응용 프로그램 권한을 요청할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱: 앱 구성](scenario-desktop-app-configuration.md)
