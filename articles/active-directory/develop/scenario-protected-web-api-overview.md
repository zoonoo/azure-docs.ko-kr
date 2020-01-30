---
title: 보호 된 Web API-개요
titleSuffix: Microsoft identity platform
description: 보호 된 web API를 빌드하는 방법에 대해 알아봅니다 (개요).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773347"
---
# <a name="scenario-protected-web-api"></a>시나리오: 보호 된 web API

이 시나리오에서는 web API를 노출 하는 방법에 대해 알아봅니다. 또한 인증 된 사용자만 액세스할 수 있도록 web API를 보호 하는 방법을 알아봅니다.

Web API를 사용 하려면 회사 및 학교 계정으로 인증 된 사용자를 사용 하도록 설정 하거나 Microsoft 개인 계정을 사용 하도록 설정 해야 합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>자세히

웹 Api를 보호 하기 위해 알아야 하는 구체적인 정보는 다음과 같습니다.

- 앱 등록에서 하나 이상의 범위를 노출 해야 합니다. 웹 API에서 허용 하는 토큰 버전은 로그인 대상 그룹에 따라 달라 집니다.
- Web api에 대 한 코드 구성에서는 web API를 호출할 때 사용 되는 토큰의 유효성을 검사 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-protected-web-api-app-registration.md)
