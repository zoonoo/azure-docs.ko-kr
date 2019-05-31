---
title: 보호 된 웹 API-앱 등록 | Azure
description: 보호 된 Web API 및 앱을 등록 하는 데 필요한 정보를 빌드하는 방법에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59af4e20c7fe838f7c725b47e45968941fa85cb7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254066"
---
# <a name="protected-web-api---app-registration"></a>보호 된 web API-앱 등록

이 문서는 보호 된 web API에 대 한 앱 등록 세부 정보를 설명합니다.

[빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록](quickstart-register-app.md) 응용 프로그램을 등록 하는 방법에는 일반적인 단계입니다.

## <a name="accepted-token-version"></a>허용 되는 토큰 버전

Microsoft id 플랫폼 끝점 두 가지 유형의 토큰을 발급할 수 있습니다: 토큰 v1.0 및 v2.0 토큰입니다. 이러한 토큰에 대 한 자세히 알아볼 수 있습니다 [액세스 토큰을](access-tokens.md)입니다. 허용 되는 토큰 버전에 따라 달라 집니다 합니다 **지원 되는 계정 유형** 응용 프로그램을 만들 때 선택한:

- 경우 값 **지원 되는 계정 유형** 됩니다 **모든 조직 디렉터리에 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com) 계정을**, 허용 되는 토큰 버전 v2.0 됩니다.
- 그렇지 않은 경우 허용 되는 토큰 버전 v1.0 됩니다.

응용 프로그램을 만든 후에 다음이 단계를 수행 하 여 허용 되는 토큰 버전을 변경할 수 있습니다.

1. Azure portal에서 앱을 선택 하 고 다음을 선택 합니다 **매니페스트** 앱에 대 한 합니다.
2. 검색할 매니페스트에서 **"accessTokenAcceptedVersion"** , 및 해당 값은 확인할 **2**합니다. 이 속성을 통해 Azure AD는 web API v2.0 토큰을 허용 하는지 알고 있어야 합니다. 있으면 **null**, 허용 되는 토큰 버전 v1.0 됩니다.
3. **저장**을 선택합니다.

> [!NOTE]
> 에 달려 허용 (v1.0 또는 v2.0) 토큰 버전을 결정 하는 웹 API입니다. 클라이언트 웹 Microsoft id 플랫폼 v2.0 끝점을 사용 하 여 API에 대 한 토큰을 요청 하면 웹 API에 의해 허용 되는 버전을 나타내는 토큰을 하 게 합니다.

## <a name="no-redirect-uri"></a>없는 리디렉션 URI

Web Api는 대화형으로 로그인 없는 사용자가 리디렉션 URI를 등록 하지 않아도 됩니다.

## <a name="expose-an-api"></a>API를 노출 합니다.

다른 설정은 웹 Api에 특정에 노출 된 API 및 노출 된 범위입니다.

### <a name="resource-uri-and-scopes"></a>리소스 URI 및 범위

범위는 일반적으로 폼의 `resourceURI/scopeName`합니다. Microsoft Graph에 대 한 범위 바로 가기가 같은 `User.Read`,이 문자열에 대 한 바로 가기 일 이지만 `https://graph.microsoft.com/user.read`합니다.

앱 등록 중 다음 매개 변수를 정의 해야 합니다.

- 응용 프로그램 등록 포털 기본적으로 하나의 리소스 URI를 권장 하는 사용 하 여 `api://{clientId}`입니다. 이 리소스 URI는 고유 하지만 사람이 아닙니다 읽을 수 있습니다. 변경할 수 있지만 고유한 지 확인 합니다.
- 하나 또는 여러 개의 범위

범위를 응용 프로그램을 사용 하는 최종 사용자에 게 표시 되는 동의 화면에 표시 됩니다. 따라서 범위를 설명 하는 해당 문자열을 제공 해야 합니다.

- 최종 사용자가 볼 수 있듯이
- 관리자 동의 부여할 수 있는 테 넌 트 관리자가 볼 수 있듯이

### <a name="how-to-expose-the-api"></a>API를 노출 하는 방법

1. 선택 된 **API를 노출** 응용 프로그램 등록에서 섹션 및:
   1. **범위 추가**를 선택합니다.
   1. 제안 된 응용 프로그램 ID URI를 수락 (api:// {clientId})을 선택 하 여 **저장 및 계속**합니다.
   1. 다음 매개 변수를 입력합니다.
      - 에 대 한 **범위 이름**를 사용 하 여 `access_as_user`입니다.
      - 에 대 한 **동의할 수 있는 사람**에 있는지 확인 합니다 **관리자 및 사용자** 옵션을 선택 합니다.
      - **관리자 동의 표시 이름**, 형식 `Access TodoListService as a user`합니다.
      - **관리자 동의 설명**, 형식 `Accesses the TodoListService Web API as a user`합니다.
      - **사용자 동의 표시 이름**, 형식 `Access TodoListService as a user`합니다.
      - **사용자 동의 설명**, 형식 `Accesses the TodoListService Web API as a user`합니다.
      - 유지할 **상태** 로 설정 **Enabled**합니다.
      - 선택 **범위 추가**합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-protected-web-api-app-configuration.md)
