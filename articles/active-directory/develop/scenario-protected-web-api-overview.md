---
title: 보호된 웹 API - 개요
titleSuffix: Microsoft identity platform
description: 보호된 웹 API를 빌드하는 방법(개요)을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537205"
---
# <a name="scenario-protected-web-api"></a>시나리오: 보호된 웹 API

이 시나리오에서는 웹 API를 노출 하는 방법을 배웁니다. 또한 인증된 사용자만 액세스할 수 있도록 웹 API를 보호하는 방법에 대해서도 알아봅니다.

웹 API를 사용하려면 인증된 사용자가 직장 및 학교 계정을 모두 사용하도록 설정하거나 Microsoft 개인 계정을 사용하도록 설정해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>구체적인

다음은 웹 API를 보호하기 위해 알아야 할 특정 정보입니다.

- 앱 등록은 하나 이상의 범위를 노출해야 합니다. 웹 API에서 허용하는 토큰 버전은 로그인 대상에 따라 다릅니다.
- 웹 API에 대한 코드 구성은 웹 API가 호출될 때 사용되는 토큰의 유효성을 검사해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-protected-web-api-app-registration.md)
