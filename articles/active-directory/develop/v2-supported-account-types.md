---
title: 지원 되는 계정 유형-Microsoft identity platform | Microsoft
description: 애플리케이션의 대상 그룹 및 지원되는 계정 유형에 대한 개념 설명서
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 50a0859b58e2db0d3f644db01cfcde8c533b6871
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518133"
---
# <a name="supported-account-types"></a>지원되는 계정 유형

이 문서에서는 Microsoft id 플랫폼 응용 프로그램에서 지원 되는 계정 유형 ( *대상*그룹이 라고도 함)을 설명 합니다.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>공용 클라우드의 계정 유형

Microsoft Azure 공용 클라우드에서, 대부분의 앱 유형은 사용자를 대상으로 하 여 사용자를 로그인 할 수 있습니다.

- LOB (기간 업무) 응용 프로그램을 작성 하는 경우 사용자의 조직에서 사용자에 게 로그인 할 수 있습니다. 이러한 응용 프로그램은 *단일 테 넌 트*라고도 합니다.
- ISV 인 경우 사용자에 게 로그인 하는 응용 프로그램을 작성할 수 있습니다.

  - 모든 조직. 이러한 응용 프로그램을 *다중 테 넌 트* 웹 응용 프로그램 이라고 합니다. 회사 또는 학교 계정을 사용 하 여 사용자가 로그인 하는 경우를 종종 읽습니다.
  - 회사 또는 학교 또는 개인 Microsoft 계정을 사용 합니다.
  - 개인 Microsoft 계정만 사용 합니다.
    
- B2b 응용 프로그램을 작성 하는 경우 Azure Active Directory B2C (Azure AD B2C)를 사용 하 여 소셜 id로 사용자를 로그인 할 수도 있습니다.

## <a name="account-type-support-in-authentication-flows"></a>인증 흐름의 계정 유형 지원

일부 계정 유형은 특정 인증 흐름에 사용할 수 없습니다. 예: 데스크톱, UWP 또는 디먼 응용 프로그램:

- 디먼 응용 프로그램은 Azure AD 조직 에서만 사용할 수 있습니다. Microsoft 개인 계정을 조작 하기 위해 디먼 응용 프로그램을 사용 하는 것은 의미가 없습니다. 관리자 동의가 부여 되지 않습니다.
- Windows 통합 인증 흐름은 회사 또는 학교 계정 (조직 또는 조직) 에서만 사용할 수 있습니다. Windows 통합 인증은 도메인 계정과 함께 작동 하며 도메인에 가입 되어 있거나 Azure AD에 가입 되어 있어야 합니다. 이 흐름은 개인 Microsoft 계정에는 적합 하지 않습니다.
- 개인 Microsoft 계정에는 [리소스 소유자 암호 자격 증명 부여](./v2-oauth-ropc.md) (사용자 이름/암호)를 사용할 수 없습니다. 개인 Microsoft 계정을 사용 하려면 각 로그인 세션에서 개인 리소스에 액세스 하는 사용자가 동의 해야 합니다. 이러한 이유로이 동작은 비 대화형 흐름과 호환 되지 않습니다.

## <a name="account-types-in-national-clouds"></a>국가별 클라우드의 계정 유형

앱에서 사용자를 [국가별 클라우드](authentication-national-cloud.md)에 로그인할 수도 있습니다. 그러나 Microsoft 개인 계정은 이러한 클라우드에서 지원 되지 않습니다. 이러한 이유로, 이러한 클라우드의 클라우드, 조직 (단일 테 넌 트) 또는 조직 (다중 테 넌 트 응용 프로그램)에 대해 지원 되는 계정 유형이 줄어듭니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory의 테 넌 트](./single-and-multi-tenant-apps.md)에 대해 자세히 알아보세요.
- [국가별 클라우드](./authentication-national-cloud.md)에 대해 자세히 알아보세요.
