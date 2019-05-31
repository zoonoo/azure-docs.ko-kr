---
title: 호출 웹 Microsoft id 플랫폼 Api (프로덕션으로 이동)-되는 모바일 앱
description: 모바일 앱을 빌드하는 방법을 알아봅니다 호출 웹 Api (프로덕션으로 이동) 되는
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8b6a5c2a29228de806088ea93e197d42bf1ab47
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962345"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>웹을 호출 하는 모바일 앱 Api-프로덕션 환경으로 이동

이 문서에서는 프로덕션으로 이동 하기 전에 품질 및 앱의 안정성을 개선 하는 방법에 대 한 세부 정보를 제공 합니다.

## <a name="handling-errors-in-mobile-applications"></a>모바일 응용 프로그램에서 오류 처리

다양 한 오류 조건이 시점에서 앱에서 발생할 수 있습니다. 주요 시나리오를 처리 하는 자동 오류 및 상호 작용에 대체 됩니다. 프로덕션에 대해 고려해 야 하는 다른 조건에는 아니요 네트워크 상황, 서비스 중단, 관리자 동의 대 한 요구 사항 및 시나리오 별로 경우도 포함 됩니다.

각 MSAL 라이브러리에 이러한 상황을 처리 하는 방법에 설명 하는 샘플 코드 및 wiki 콘텐츠:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>문제를 조사 및 완화

앱의 문제를 진단 하려면 데이터를 수집할 수 있습니다. 수집 수는 데이터에 대 한 정보 MSAL 플랫폼 wiki를 참조 하십시오.

- 사용자는 문제에 도달할 때 도움말을 요청할 수 있습니다. 캡처 및 일시적으로 로그를 저장 하 고 사용자가 업로드할 수 있는 위치를 제공 하는 것이 좋습니다. MSAL 인증에 대 한 자세한 정보를 캡처하려면 로깅 확장을 제공 합니다.
- 사용 가능한 경우에 앱에 사용자가 로그인 하는 방법에 대 한 데이터를 수집 하 여 MSAL 통해 원격 분석을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
