---
title: 보호 된 Web API-개요 | Microsoft
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02bd4b84cc7542714f6db45c12c4b5b13a7fb449
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852591"
---
# <a name="scenario-protected-web-api"></a>시나리오: 보호된 웹 API

이 시나리오에서는 인증 된 사용자만 API에 액세스할 수 있도록 웹 API를 노출 하는 방법과이를 보호 하는 방법을 보여 줍니다. 회사 및 학교 계정이 나 개인 Microsoft 개인 계정으로 인증 된 사용자가 web API를 사용 하도록 할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>자세히

웹 Api를 보호 하기 위해 알아야 할 몇 가지 사항은 다음과 같습니다.

- 앱 등록에서 하나 이상의 범위를 노출 해야 합니다. Web API에서 허용 하는 토큰 버전은 로그인 대상 그룹에 따라 달라 집니다.
- Web api에 대 한 코드를 구성 하는 경우 web API를 호출할 때 사용 되는 토큰의 유효성을 검사 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-protected-web-api-app-registration.md)
