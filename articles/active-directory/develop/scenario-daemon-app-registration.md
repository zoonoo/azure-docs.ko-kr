---
title: 디먼 앱 웹 Api 호출 (앱 등록)-Microsoft id 플랫폼
description: 앱 등록 웹 Api를 호출 하는 디먼 앱을 빌드하는 방법을 알아봅니다
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076242"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>웹 앱 등록 Api를 호출 되는 데몬 앱

디먼 응용 프로그램에 대 한 다음 앱을 등록 하는 경우 알아야 할 항목은입니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

디먼 응용 프로그램 에서만 의미가 Azure AD 테 넌 트에서 응용 프로그램을 만들 때 선택 해야 합니다.

- 어느 **만이 조직 디렉터리의 계정**합니다. 이 선택 가장 일반적인 경우는 디먼 응용 프로그램은 일반적으로 기간 업무 (LOB) 개발자가 작성 됩니다.
- 또는 **모든 조직 디렉터리의 계정**합니다. 고객에 게 유틸리티 도구를 제공 하는 ISV 인 경우이 옵션을 만들어야 합니다. 고객의 테 넌 트 관리자가 승인 해야 합니다.

## <a name="authentication---no-reply-uri-needed"></a>인증-없습니다 회신 URI 필요

기밀 클라이언트 응용 프로그램이 사용 하는 경우에서 **만** 클라이언트 자격 증명 흐름, 회신 URL을 등록할 필요가 없습니다. 가 필요 하지 않기 응용 프로그램 구성/생성에 대 한 중 하나입니다. 클라이언트 자격 증명 흐름 사용 하지 않습니다.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 사용 권한-앱 사용 권한 및 관리자 동의

디먼 응용 프로그램 (위임된 되지 않은 권한) Api에 응용 프로그램 사용 권한을 요청할 수 있습니다. 에 **API 사용 권한** 응용 프로그램 등록을 선택한 후 페이지 **권한을 추가** API 제품군을 선택 하면 선택한 **응용 프로그램 사용 권한**, 선택한 다음 사용자의 권한

![앱 사용 권한 및 관리자 동의](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

디먼 응용 프로그램 웹 API를 호출 응용 프로그램에 미리 동의 테 넌 트 관리자가 필요 합니다. 동일한이 동의 제공 됩니다 **API 사용 권한** 는 테 넌 트 관리자를 선택 하 여 페이지 **관리자 동의를 허용할 *조직***

확인 하려는 다중 테 넌 트 응용 프로그램을 빌드하는 ISV 인 경우는 [배포-다중 테 넌 트 디먼 앱의 경우](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) 단락입니다.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [디먼 앱-앱 코드 구성](./scenario-daemon-app-configuration.md)
