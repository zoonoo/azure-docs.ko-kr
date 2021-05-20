---
title: 웹 API를 호출하는 디먼 앱 등록 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 디먼 앱을 빌드하는 방법 알아보기 - 앱 등록
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 938a19276839d5e0d2bd3e0244510fc068cb029c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582910"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>웹 API를 호출하는 디먼 앱 - 앱 등록

디먼 애플리케이션의 경우 앱을 등록할 때 알아야 할 사항은 다음과 같습니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

디먼 애플리케이션은 Azure AD 테넌트에서만 사용할 수 있습니다. 따라서 애플리케이션을 만들 때 다음 옵션 중 하나를 선택합니다.

- **이 조직 디렉터리의 계정만 해당**. 디먼 애플리케이션은 일반적으로 LOB(기간 업무) 개발자가 작성하기 때문에 이 선택은 가장 일반적입니다.
- **모든 조직 디렉터리의 계정**. 유틸리티 도구를 고객에게 제공하는 ISV인 경우 이 옵션을 선택할 수 있습니다. 승인하려면 고객의 테넌트 관리자가 필요합니다.

## <a name="authentication---no-reply-uri-needed"></a>인증 - 회신 URI가 필요하지 않음

기밀 클라이언트 애플리케이션이 클라이언트 자격 증명 흐름 *만* 사용하는 경우에는 회신 URI를 등록할 필요가 없습니다. 애플리케이션 구성 또는 생성에 필요하지 않습니다. 클라이언트 자격 증명 흐름은 이를 사용하지 않습니다.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 권한 - 앱 권한 및 관리자 동의

디먼 애플리케이션은 API에 대한 애플리케이션 권한만 요청할 수 있습니다(위임된 권한 아님). 애플리케이션 등록을 위한 **API 권한** 페이지에서 **권한 추가** 를 선택하고 API 제품군을 선택한 후 **애플리케이션 권한** 을 선택한 다음, 권한을 선택합니다.

![앱 권한 및 관리자 동의](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 호출하려는 웹 API는 위임된 권한이 아닌 *애플리케이션 권한(앱 역할)* 을 정의해야 합니다. 이러한 API를 노출하는 방법에 대한 자세한 내용은 [보호된 웹 API: 앱 등록 - 디먼 앱에서 웹 API를 호출하는 경우](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)를 참조하세요.

디먼 애플리케이션을 사용하려면 테넌트 관리자가 웹 API를 호출하는 애플리케이션에 사전 동의해야 합니다. 테넌트 관리자는 ***조직* 에 관리자 동의 부여** 를 선택하여 동일한 **API 권한** 페이지에서 이 동의를 제공합니다.

다중 테넌트 애플리케이션을 빌드하는 ISV인 경우 [다중 테넌트 디먼 앱의 배포 - 사례](scenario-daemon-production.md#deployment---multitenant-daemon-apps) 섹션을 참조하세요.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 코드 구성](./scenario-daemon-app-configuration.md)으로 이동합니다.
