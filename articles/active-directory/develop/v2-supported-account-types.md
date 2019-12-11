---
title: Microsoft id 플랫폼에서 지원 되는 계정 유형 | Microsoft
description: 응용 프로그램에서 대상 및 지원 되는 계정 유형에 대 한 개념 설명서
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
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04aac02623fe4918db671e2385c181653bdc344a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966557"
---
# <a name="supported-account-types"></a>Támogatott fióktípusok

이 문서에서는 응용 프로그램에서 지원 되는 계정 유형 (경우에 따라 이름이 지정 된 대상)을 설명 합니다.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Microsoft Id 플랫폼 응용 프로그램에서 지원 되는 계정 유형

Microsoft Azure 공용 클라우드에서, 대부분의 앱 유형은 사용자를 대상으로 하 여 사용자를 로그인 할 수 있습니다.

- LOB (기간 업무) 응용 프로그램을 작성 하는 경우 사용자의 조직에서 사용자에 게 로그인 할 수 있습니다. 이러한 응용 프로그램은 종종 **단일 테 넌 트**라고 합니다.
- ISV 인 경우 로그인 사용자를 로그인 하는 응용 프로그램을 작성할 수 있습니다.

  - 모든 조직에서 이러한 응용 프로그램은 **다중 테 넌 트** 웹 응용 프로그램 이라고 합니다. 회사 또는 학교 계정을 사용 하 여 사용자가 로그인 하는 경우를 종종 읽습니다.
  - 회사 또는 학교 또는 개인 Microsoft 계정를 사용 합니다.
  - 개인 Microsoft 계정만 사용 합니다.
    > [!NOTE]
    > 현재 Microsoft id 플랫폼은 **회사 또는 학교 또는 Microsoft 개인 계정**에 앱을 등록 하는 경우에만 개인 microsoft 계정을 지원 하며, `https://login.microsoftonline.com/consumers`와 같은 응용 프로그램을 빌드할 때 Azure AD 기관을 지정 하 여 응용 프로그램 코드에서 로그인을 제한 합니다.

- 소비자에 게 비즈니스 응용 프로그램을 작성 하는 경우 Azure AD B2C를 사용 하 여 소셜 id로 사용자를 로그인 할 수도 있습니다.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>특정 인증 흐름은 모든 계정 유형을 지원 하지 않습니다.

일부 계정 유형은 특정 인증 흐름과 함께 사용할 수 없습니다. 예를 들어 데스크톱, UWP 응용 프로그램 또는 디먼 응용 프로그램에서 다음을 수행 합니다.

- 디먼 응용 프로그램은 Azure Active Directory 조직 에서만 사용할 수 있습니다. 디먼 응용 프로그램을 사용 하 여 Microsoft 개인 계정을 조작 하는 것은 의미가 없습니다. 관리자 동의가 부여 되지 않습니다.  
- 회사 또는 학교 계정 (조직 또는 조직)에는 Windows 통합 인증 흐름을 사용할 수 있습니다. 실제로 Windows 통합 인증은 도메인 계정에서 작동 하며, 컴퓨터가 도메인에 가입 되어 있거나 Azure AD에 가입 되어 있어야 합니다. 이 흐름은 개인 Microsoft 계정에는 적합 하지 않습니다.
- 개인 Microsoft 계정에는 [리소스 소유자 암호 부여](./v2-oauth-ropc.md) (사용자 이름/암호)를 사용할 수 없습니다. 실제로 개인 Microsoft 계정에는 사용자가 각 로그인 세션에서 개인 리소스에 대 한 액세스를 동의 해야 합니다. 이러한 이유로이 동작은 비 대화형 흐름과 호환 되지 않습니다.
- 장치 코드 흐름은 아직 개인 Microsoft 계정으로 작동 하지 않습니다.

## <a name="supported-account-types-in-national-clouds"></a>국가별 클라우드에서 지원 되는 계정 유형

 앱은 [국가별 클라우드에서](authentication-national-cloud.md)사용자를 로그인 할 수도 있습니다. 그러나 Microsoft 개인 계정은 이러한 클라우드에서 지원 되지 않습니다 (이러한 클라우드의 정의). 이러한 이유로 클라우드, 클라우드, 조직 (단일 테 넌 트) 또는 조직 (다중 테 넌 트 응용 프로그램)에 대해 지원 되는 계정 유형이 줄어듭니다.

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory의 테 넌 트](./single-and-multi-tenant-apps.md) 에 대해 자세히 알아보기
- [국가별 클라우드](./authentication-national-cloud.md) 에 대 한 자세한 정보
