---
title: 웹 API를 호출하는 데몬 앱 등록 - Microsoft ID 플랫폼 | Azure
description: 웹 API - 앱 등록을 호출하는 데몬 앱을 빌드하는 방법 알아보기
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773387"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>웹 API를 호출하는 데몬 앱 - 앱 등록

데몬 응용 프로그램의 경우 앱을 등록할 때 알아야 할 내용은 다음과 같습니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

Daemon 응용 프로그램은 Azure AD 테넌경우에만 의미가 있습니다. 따라서 응용 프로그램을 만들 때 다음 옵션 중 하나를 선택해야 합니다.

- **이 조직 디렉터리에 있는 계정만**. 이 선택은 데몬 응용 프로그램이 일반적으로 LOB(업무 용) 개발자가 작성하기 때문에 가장 일반적인 선택입니다.
- **모든 조직 디렉터리내 의 계정**. 고객에게 유틸리티 도구를 제공하는 ISV인 경우 이 선택을 할 수 있습니다. 이를 승인하려면 고객의 테넌트 관리자가 필요합니다.

## <a name="authentication---no-reply-uri-needed"></a>인증 - 회신 URI가 필요하지 않습니다.

기밀 클라이언트 응용 프로그램이 클라이언트 자격 증명 *흐름만* 사용하는 경우 회신 URI를 등록할 필요가 없습니다. 응용 프로그램 구성 이나 구성에 대 한 필요 하지 않습니다. 클라이언트 자격 증명 흐름은 사용하지 않습니다.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 권한 - 앱 권한 및 관리자 동의

데몬 응용 프로그램은 API에 대한 응용 프로그램 권한만 요청할 수 있습니다(위임된 권한이 아님). 응용 프로그램 등록에 대한 **API 권한** 페이지에서 **권한 추가를** 선택하고 API 패밀리를 선택한 후 **응용 프로그램 사용 권한을**선택한 다음 권한을 선택합니다.

![앱 권한 및 관리자 동의](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 호출하려는 웹 API는 위임된 권한이 아닌 *응용 프로그램 권한(앱 역할)을*정의해야 합니다. 이러한 API를 노출하는 방법에 대한 자세한 내용은 [보호된 웹 API: 앱 등록 - 웹 API가 데몬 앱에서 호출되는 경우를](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)참조하십시오.

Daemon 응용 프로그램은 테넌트 관리자가 웹 API를 호출하는 응용 프로그램에 대한 사전 동의를 요구합니다. 테넌트 관리자는 조직에 대한 권한 부여 **관리자 동의를 *our organization* ** 선택하여 동일한 **API 권한** 페이지에서 이 동의를 제공합니다.

ISV가 다중 테넌트 응용 프로그램을 빌드하는 경우 [다중 테넌트 데몬 앱의 경우 배포](scenario-daemon-production.md#deployment---multitenant-daemon-apps)섹션을 읽어야 합니다.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데몬 앱 - 앱 코드 구성](./scenario-daemon-app-configuration.md)
