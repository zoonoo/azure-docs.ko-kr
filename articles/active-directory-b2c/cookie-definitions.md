---
title: 쿠키 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에 사용되는 쿠키에 대한 정의를 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189517"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C에 대한 쿠키 정의

다음 섹션에서는 Azure Active Directory B2C(Azure AD B2C)에서 사용되는 쿠키에 대한 정보를 제공합니다.

## <a name="samesite"></a>동일 사이트

Microsoft Azure AD B2C 서비스는 `SameSite=None` `Secure` 특성에 대한 지원을 포함하여 SameSite 브라우저 구성과 호환됩니다.

웹 브라우저는 사이트에 대한 액세스를 보호하기 위해 달리 명시되지 않는 한 모든 쿠키를 외부 액세스로부터 보호해야 한다고 가정하는 새로운 보안 기본 모델을 도입합니다. 크롬 브라우저는 [2020 년 2 월에 크롬 80을](https://www.chromium.org/updates/same-site)시작으로이 변화를 구현하는 첫 번째입니다. Chrome의 변경 준비에 대한 자세한 내용은 [개발자: 새 동일사이트 준비=없음을 참조하십시오. ](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)크롬 블로그에서 쿠키 설정을 보호합니다.

개발자는 사이트 간 액세스를 `SameSite=None`위해 쿠키를 지정하기 위해 새 쿠키 설정을 사용해야 합니다. 특성이 `SameSite=None` 있는 경우 HTTPS `Secure` 연결을 통해서만 사이트 간 쿠키에 액세스할 수 있도록 추가 특성을 사용해야 합니다. Azure AD B2C를 사용하는 응용 프로그램을 포함하여 모든 응용 프로그램의 유효성을 검사하고 테스트합니다.

자세한 내용은 다음을 참조하세요.

* [Chrome 브라우저에서 SameSite 쿠키 변경 내용 처리](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [크롬 버전 80 이상에서 고객 웹 사이트 및 Microsoft 서비스 및 제품에 미치는 영향](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>쿠키

다음 표에는 Azure AD B2C에 사용되는 쿠키가 나열되어 있습니다.

| 이름 | 도메인 | 만료 | 목적 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | 브라우저 [세션](session-behavior.md) 종료 | 테넌자 간에 사용자 구성원 데이터를 보유합니다. 사용자가 구성원 및 구성원 수준(관리자 또는 사용자)인 테넌트와의 테넌트와 |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | 요청을 적절한 프로덕션 인스턴스로 라우팅하는 데 사용됩니다. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | 트랜잭션(Azure AD B2C에 대한 인증 요청 수) 및 현재 트랜잭션을 추적하는 데 사용됩니다. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | SSO 세션을 유지하는 데 사용됩니다. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md)종료, 인증 성공 | 요청 상태를 유지하는 데 사용됩니다. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | CRSF 보호에 사용되는 사이트 간 요청 위조 토큰입니다. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | Azure AD B2C 네트워크 라우팅에 사용됩니다. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | 리소스 공급자 테넌트의 멤버 자격 데이터를 저장하는 데 사용됩니다. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | 브라우저 [세션](session-behavior.md) 종료 | 릴레이 쿠키를 저장하는 데 사용됩니다. |
