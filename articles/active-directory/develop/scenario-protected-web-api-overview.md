---
title: 보호 된 web API-개요
titleSuffix: Microsoft identity platform
description: 보호 된 web API를 빌드하는 방법에 대해 알아봅니다 (개요).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4e530f76c8301dc74f73b675befa6f0710aedab7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026631"
---
# <a name="scenario-protected-web-api"></a>시나리오: 보호 된 web API

이 시나리오에서는 web API를 노출 하는 방법에 대해 알아봅니다. 또한 인증 된 사용자만 액세스할 수 있도록 web API를 보호 하는 방법을 알아봅니다.

Web API를 사용 하려면 회사 및 학교 계정으로 인증 된 사용자를 사용 하도록 설정 하거나 Microsoft 개인 계정을 사용 하도록 설정 해야 합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>특수 적용 사항

웹 Api를 보호 하기 위해 알아야 하는 구체적인 정보는 다음과 같습니다.

- 앱 등록은 하나 이상의 *범위* 또는 하나의 *응용 프로그램 역할*을 노출 해야 합니다.
  - 범위는 사용자를 대신 하 여 호출 되는 웹 Api에 의해 노출 됩니다.
  - 응용 프로그램 역할은 디먼 응용 프로그램에서 호출 하는 웹 Api에 의해 노출 됩니다 (자체 사용자 대신 web API를 호출 함).
- 새 web API 앱 등록을 만드는 경우 웹 API에서 허용 하는 [액세스 토큰 버전](reference-app-manifest.md#accesstokenacceptedversion-attribute) 을로 선택 `2` 합니다. 레거시 웹 Api의 경우 허용 되는 토큰 버전은 일 수 `null` 있지만이 값은 조직 전용 로그인 사용자를 제한 하 고 개인 Microsoft 계정 (MSA)은 지원 되지 않습니다.
- Web api에 대 한 코드 구성에서는 web API를 호출할 때 사용 되는 토큰의 유효성을 검사 해야 합니다.
- 컨트롤러 작업의 코드는 토큰에서 역할이 나 범위의 유효성을 검사 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-protected-web-api-app-registration.md)
