---
title: 앱에서 지원되는 계정(대상 그룹) - Microsoft ID 플랫폼
description: 애플리케이션의 대상 그룹 및 지원되는 계정 유형에 대한 개념 설명서
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ba764dfee7c164c3e6f0ff24d2bbf6a0df287
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544836"
---
# <a name="supported-account-types"></a>지원되는 계정 유형

이 문서에서는 애플리케이션에서 지원하는 계정 유형(경우에 따라 대상 그룹이라고도 함)을 설명합니다.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Microsoft ID 플랫폼 애플리케이션에서 지원하는 계정 유형

Microsoft Azure 공용 클라우드에서는 대부분의 앱 유형이 모든 대상 그룹을 사용하여 사용자를 로그인할 수 있습니다.

- LOB(기간 업무) 애플리케이션을 작성하는 경우 사용자를 조직 내에 로그인할 수 있습니다. 이러한 애플리케이션을 **단일 테넌트**라고도 합니다.
- ISV는 사용자를 다음 항목에 로그인하는 애플리케이션을 작성할 수 있습니다.

  - 모든 조직. 이러한 애플리케이션을 **다중 테넌트** 웹 애플리케이션이라고 합니다. 회사 또는 학교 계정으로 사용자를 로그인한다는 내용을 종종 보게 될 것입니다.
  - 회사 또는 학교 계정이나 Microsoft 개인 계정
  - Microsoft 개인 계정만
    > [!NOTE]
    > 현재 Microsoft ID 플랫폼은 **회사 또는 학교 계정이나 Microsoft 개인 계정**에 대한 앱을 등록하여 Microsoft 개인 계정만 지원하며, 애플리케이션을 빌드할 때 `https://login.onmicrosoftonline.com/consumers`처럼 Azure AD 기관을 지정하여 애플리케이션의 코드에서 로그인을 제한합니다.

- B2C 애플리케이션을 작성 중인 경우 Azure AD B2C를 사용하여 사용자의 소셜 ID로 사용자를 로그인할 수 있습니다.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>특정 인증 흐름은 일부 계정 유형을 지원하지 않습니다.

일부 계정 유형은 특정 인증 흐름에 사용할 수 없습니다. 예를 들어 데스크톱, UWP 애플리케이션 또는 디먼 애플리케이션은 다음과 같습니다.

- 디먼 애플리케이션은 Azure Active Directory 조직에만 사용할 수 있습니다. 디먼 애플리케이션을 사용하여 Microsoft 개인 계정을 조작할 수 없습니다(관리자 동의를 절대 얻을 수 없음).  
- 소속 조직 또는 타 조직의 회사 또는 학교 계정에 Windows 통합 인증 흐름만 사용할 수 있습니다. 실제로 Windows 통합 인증은 도메인 계정과 호환되며, 머신을 도메인 또는 Azure AD에 조인해야 합니다. 이 흐름에는 Microsoft 개인 계정이 적합하지 않습니다.
- [리소스 소유자 암호 권한 부여](./v2-oauth-ropc.md)(사용자 이름/암호)는 Microsoft 개인 계정에 사용할 수 없습니다. 실제로 Microsoft 개인 계정을 사용하려면 사용자가 각 로그인 세션에서 개인 리소스 액세스에 동의해야 합니다. 그렇기 때문에 이 동작은 비 대화형 흐름과 호환되지 않습니다.
- 디바이스 코드 흐름은 아직 Microsoft 개인 계정과 호환되지 않습니다.

## <a name="supported-account-types-in-national-clouds"></a>국가별 클라우드에서 지원되는 계정 유형

 앱에서 사용자를 [국가별 클라우드](authentication-national-cloud.md)에 로그인할 수도 있습니다. 하지만 Microsoft 개인 계정은 국가별 클라우드에서 지원되지 않습니다(국가별 클라우드의 정의에 따라). 이러한 이유로 국가별 클라우드에 지원되는 계정 유형이 소속 조직(단일 테넌트) 또는 모든 조직(다중 테넌트 애플리케이션)으로 축소됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory의 테넌시](./single-and-multi-tenant-apps.md)에 대한 자세한 정보
- [국가별 클라우드](./authentication-national-cloud.md)에 대한 자세한 정보