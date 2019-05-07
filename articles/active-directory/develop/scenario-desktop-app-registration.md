---
title: 데스크톱 앱 호출 웹 Api (응용 프로그램 등록)-되는 Microsoft id 플랫폼
description: 데스크톱 앱을 빌드하는 방법을 알아봅니다 호출 웹 Api (응용 프로그램 등록) 되는
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5da934709274d90668d94dfea3a9c223e191d032
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076062"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>웹 앱 등록 Api를 호출 되는 데스크톱 앱

이 문서에서는 데스크톱 응용 프로그램에 대 한 앱 등록 specificities 포함 되어 있습니다.

## <a name="supported-accounts-types"></a>지원 되는 계정 유형

강화 하려는 환경에 데스크톱 응용 프로그램에서 지원 되는 계정 유형에 따라 달라 집니다, 따라서 흐름에서 사용 하려는 및 합니다.

### <a name="audience-for-interactive-token-acquisition"></a>대화형 토큰 획득에 대 한 대상 그룹

데스크톱 응용 프로그램에서 대화형 인증을 사용 하는 경우에 모든 사용자가 로그인 할 수 있습니다 [계정 유형](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>데스크톱 앱 자동 흐름에 대 한 대상 그룹

- 통합 Windows 인증 또는 사용자 이름/암호를 사용 하려는 경우 응용 프로그램에서는 사용자 고유의 테 넌 트 (LOB 개발자) 또는 Azure Active directory 조직 (ISV 시나리오)에 로그인 해야 합니다. 이러한 인증 흐름은 Microsoft 개인 계정에 대 한 지원 되지 않습니다.
- 장치 코드 흐름을 사용 하려는 경우 로그인 할 수 없는 Microsoft 개인 계정으로 사용자를 아직
- B2C 기관 및 정책에 전달 하는 소셜 id를 사용 하 여 사용자를 로그인 하는 경우에 대화형 및 사용자 이름 / 암호 인증을 사용할 수 있습니다.

## <a name="redirect-uris"></a>리디렉션 URI

다시 리디렉션 Uri를 데스크톱 응용 프로그램에서 사용 하 여 사용 하려는 흐름에 따라 달라 집니다.

- 사용 하려는 대화형 인증을 사용 하는 경우 `https://login.microsoftonline.com/common/oauth2/nativeclient`합니다. 이 구성에서 해당 URL을 클릭 하 여 달성할 수 있습니다 합니다 **인증** 응용 프로그램에 대 한 섹션
  
  > [!IMPORTANT]
  > 지금 MSAL.NET가 다른 리디렉션 URI를 사용 하 여 Windows에서 실행 되는 데스크톱 응용 프로그램에서 기본적으로 (`urn:ietf:wg:oauth:2.0:oob`). 이 기본값을 변경 하고자 하는 나중에 및를 사용 하는 권장 하므로 `https://login.microsoftonline.com/common/oauth2/nativeclient`

- 앱에는 통합 Windows 인증, 사용자 이름/암호 또는 장치 코드 흐름을 사용 하는 경우에 응용 프로그램에 대 한 리디렉션 URI를 등록할 필요가 없습니다. 실제로 이러한 흐름 Microsoft id 플랫폼 v2.0 끝점에 왕복을 수행 하 고 모든 특정 URI에서 응용 프로그램을 다시 호출 되지 않습니다. 구분할 수 있도록 없는 기밀 클라이언트 응용 프로그램 흐름을에서 리디렉션 Uri 중 하나 (클라이언트 자격 증명 흐름 디먼 응용 프로그램에서 사용)에서는 응용 프로그램을 공용 클라이언트 응용 프로그램을 나타내는 데 필요 합니다. 이 구성으로 이동 하 여 이루어집니다 합니다 **인증** 및 응용 프로그램에 대 한 섹션을 **고급 설정** 하위 섹션을 선택 **예**, 질문에 대 한 **응용 프로그램 공용 클라이언트로 간주** (에 **기본 클라이언트 형식** 단락)

  ![공용 클라이언트를 허용 합니다.](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 사용 권한

데스크톱 응용 프로그램에서 로그인 한 사용자를 대신 하 여 Api를 호출합니다. 위임 된 권한을 요청 해야 합니다. 응용 프로그램 사용 권한을 요청할 수 없습니다 (내 에서만 처리 되는 [디먼 응용 프로그램](scenario-daemon-overview.md))

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱-앱 구성](scenario-desktop-app-configuration.md)
