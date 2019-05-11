---
title: 웹 API 보호 개요 | Azure
description: 보호 된 web API (개요)를 빌드하는 방법을 알아봅니다.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074892"
---
# <a name="scenario-protected-web-api"></a>시나리오: 보호 된 web API

이 시나리오에서는 보여드리겠습니다 web API를 노출 하는 방법 및 어떻게 보호할 수 있습니다 인증 된 사용자만 있도록 API에 액세스할 수 있습니다. Web API를 사용 하 여 회사 및 학교 계정 또는 개인 Microsoft 개인 계정으로 인증 된 사용자를 사용 하도록 설정 해야 합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>세부 정보

웹 Api를 보호 하기 위해 알아야 할 몇 가지 세부 정보는 다음과 같습니다.

- 앱 등록 하나 이상의 범위를 노출 해야 합니다. Web API에서 허용 하는 토큰 버전 대상 로그인에 따라 달라 집니다.
- Web API에 대 한 코드의 구성을 web API를 호출할 때 사용 되는 토큰을 확인 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-protected-web-api-app-registration.md)
