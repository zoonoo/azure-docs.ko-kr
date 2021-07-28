---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae9b749f62dc74a6cb0dfea3701fd4ebac11c188
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107931579"
---
## <a name="enable-logging"></a>로깅 사용

디버깅 및 인증 실패 문제 해결 시나리오를 지원하기 위해 Microsoft 인증 라이브러리는 기본 제공 로깅 지원을 제공합니다. 로깅은 다음 문서에서 설명합니다.

:::row:::
    :::column:::
        - [MSAL.NET의 로깅](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Android용 MSAL에서 로깅](../articles/active-directory/develop/msal-logging-android.md)
        - [MSAL.js의 로깅](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [iOS/macOS용 MSAL에서 로깅](../articles/active-directory/develop/msal-logging-ios.md)
        - [Java용 MSAL에서 로깅](../articles/active-directory/develop/msal-logging-java.md)
        - [Python용 MSAL에서 로깅](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

다음은 데이터 수집에 대한 몇 가지 제안 사항입니다.

- 사용자는 문제가 발생할 때 도움을 요청할 수 있습니다. 로그를 캡처하여 임시로 저장하는 것이 가장 좋습니다. 사용자가 로그를 업로드할 수 있는 위치를 제공합니다. MSAL은 인증에 대한 자세한 정보를 캡처하는 로깅 확장을 제공합니다.

- 원격 분석을 사용할 수 있는 경우 MSAL을 통해 활성화하여 사용자가 앱에 로그인하는 방법에 대한 데이터를 수집합니다.


## <a name="validate-your-integration"></a>통합 유효성 검사

[Microsoft ID 플랫폼 통합 검사 목록](../articles/active-directory/develop/identity-platform-integration-checklist.md)에 따라 통합을 테스트합니다.

## <a name="build-for-resilience"></a>복원력을 위해 빌드

앱에서 복원력을 높이는 방법에 대해 알아봅니다. 자세한 내용은 [개발하는 인증 및 권한 부여 애플리케이션의 복원력 향상](../articles/active-directory/fundamentals/resilience-app-development-overview.md)을 참조하세요.
