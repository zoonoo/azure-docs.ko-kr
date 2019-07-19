---
title: 웹 Api를 호출 하는 모바일 앱 (프로덕션으로 이동)-Microsoft identity platform
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법 알아보기 (프로덕션으로 이동)
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
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e778e55bd87e325b3dcf14a8c9f1616157a420b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320883"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>웹 Api를 호출 하는 모바일 앱-프로덕션으로 이동

이 문서에서는 프로덕션 환경으로 이동 하기 전에 앱의 품질 및 안정성을 개선 하는 방법에 대 한 세부 정보를 제공 합니다.

## <a name="handling-errors-in-mobile-applications"></a>모바일 응용 프로그램의 오류 처리

이 시점에서 많은 오류 조건이 앱에서 발생할 수 있습니다. 처리할 주요 시나리오는 자동 오류 이며 상호 작용에 대 한 대체입니다. 프로덕션에 고려해 야 할 기타 조건에는 네트워크 환경, 서비스 중단, 관리자 동의에 대 한 요구 사항 및 기타 시나리오 관련 사례가 포함 됩니다.

각 MSAL 라이브러리에는 다음 조건을 처리 하는 방법을 설명 하는 샘플 코드와 wiki 콘텐츠가 있습니다.

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>문제 완화 및 조사

앱에서 문제를 진단 하기 위해 데이터를 수집 하는 데 도움이 됩니다. 수집할 수 있는 데이터의 종류에 대 한 자세한 내용은 MSAL platform wiki를 참조 하세요.

- 사용자는 문제가 발생할 때 도움을 요청할 수 있습니다. 모범 사례는 로그를 캡처 및 임시로 저장 하 고 사용자가 업로드할 수 있는 위치를 제공 하는 것입니다. MSAL은 인증에 대 한 자세한 정보를 캡처하기 위해 로깅 확장을 제공 합니다.
- 사용할 수 있는 경우 MSAL을 통해 원격 분석을 사용 하도록 설정 하 여 사용자가 앱에 로그인 하는 방법에 대 한 데이터를 수집 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
