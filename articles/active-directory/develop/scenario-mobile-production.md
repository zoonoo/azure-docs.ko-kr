---
title: 호출 웹 Microsoft id 플랫폼 Api (프로덕션으로 이동)-되는 모바일 앱
description: Web Api (프로덕션으로 이동)를 호출 하는 모바일 앱을 빌드하는 방법을 알아봅니다
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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074952"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>웹을 호출 하는 모바일 앱 Api-프로덕션 환경으로 이동

이 문서에서는 품질 및 프로덕션으로 이동 하려면 앱의 안정성 향상에 세부 정보를 제공 합니다.

## <a name="handling-errors-in-mobile-applications"></a>모바일 응용 프로그램에서 오류 처리

했으므로 지금 강조 표시 된 것을 다른 흐름에서 발생할 수 있는 오류 조건에는 여러 가지가 있습니다. 기본 시나리오를 처리 하는 자동 오류 및 상호 작용에 대체 됩니다. 추가 조건이 없는 네트워크 상황, 서비스 중단, 필요한 관리자 동 및 시나리오 별로 경우도 포함 하 여 프로덕션에 대해 고려해 야 합니다.

각 MSAL 라이브러리에 이러한 조건을 처리 자세히 설명 하는 샘플 코드 및 wiki 콘텐츠입니다.

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>문제를 조사 및 완화

데이터 수집 앱 문제를 진단 하는 데 도움이 됩니다. 수집 수 데이터 형식에 대 한 자세한 내용이 각 MSAL 플랫폼 wiki를 참조 하십시오.

- 문제가 발생 하는 경우 사용자에 대 한 도움말 요청할 수 있습니다. 캡처 로그를 일시적으로 저장 하 고 어딘가에 업로드할 수 있도록 하는 것이 좋습니다. 인증에 대 한 자세한 정보를 캡처하려면 로깅 확장을 제공 하는 MSAL
- 사용 가능한 경우 사용자는 앱에 로그인 하는 방법에 대 한 데이터를 수집 하 여 MSAL 통해 원격 분석을 사용 하도록 설정 합니다.

## <a name="testing-your-app"></a>앱 테스트

에 대해 앱을 테스트 해야 합니다 [통합 검사 목록](identity-platform-integration-checklist.md)합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
