---
title: 쿠키 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에 사용 되는 쿠키에 대 한 정의를 제공 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e44e2e1eb37e808e60134a6fba5051552e84029
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389346"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C에 대 한 쿠키 정의

다음 섹션에서는 Azure Active Directory B2C (Azure AD B2C)에 사용 되는 쿠키에 대 한 정보를 제공 합니다.

## <a name="samesite"></a>SameSite

Microsoft Azure AD B2C 서비스는 특성에 대 한 지원을 포함 하 여 SameSite 브라우저 구성과 호환 됩니다 `SameSite=None` `Secure` .

사이트에 대 한 액세스를 보호 하기 위해 웹 브라우저에서는 별도로 지정 하지 않는 한 모든 쿠키를 외부 액세스 로부터 보호 해야 한다고 가정 하는 새로운 보안 기본 모델을 도입 합니다. Chrome 브라우저는이 변경 내용을 구현 하기 위한 첫 번째 방법으로, [2020 년 2 월에 chrome 80](https://www.chromium.org/updates/same-site)부터 시작 합니다. Chrome에서 변경을 준비 하는 방법에 대 한 자세한 내용은 [개발자: Get Ready For New SameSite = None을 참조 하세요. Chromium 블로그의 보안 쿠키 설정](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)

개발자는 새 쿠키 설정인 `SameSite=None` 를 사용 하 여 사이트 간 액세스를 위한 쿠키를 지정 해야 합니다. 특성이 있는 경우에는 `SameSite=None` `Secure` HTTPS 연결을 통해서만 사이트 간 쿠키에 액세스할 수 있도록 추가 특성을 사용 해야 합니다. Azure AD B2C를 사용 하는 응용 프로그램을 포함 하 여 모든 응용 프로그램의 유효성을 검사 하 고 테스트 합니다.

자세한 내용은 다음을 참조하세요.

* [Chrome 브라우저에서 SameSite 쿠키 변경 내용 처리](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Chrome 버전 80 이상에서 고객 웹 사이트 및 Microsoft 서비스 및 제품에 미치는 영향](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>쿠키

다음 표에서는 Azure AD B2C에 사용 되는 쿠키를 나열 합니다.

| 속성 | 도메인 | 만료 | 목적 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | [브라우저 세션](session-behavior.md) 의 끝 | 테 넌 트 간에 사용자 멤버 자격 데이터를 저장 합니다. 사용자가 구성원 인 테 넌 트 (관리자 또는 사용자)입니다. |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 요청을 적절 한 프로덕션 인스턴스로 라우팅하는 데 사용 됩니다. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 트랜잭션을 추적 하는 데 사용 됩니다 (Azure AD B2C 인증 요청 수) 및 현재 트랜잭션. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | SSO 세션을 유지 관리 하는 데 사용 됩니다. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md)의 끝, 인증 성공 | 요청 상태를 유지 관리 하는 데 사용 됩니다. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | CRSF 보호에 사용 되는 사이트 간 요청 위조 토큰입니다. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | Azure AD B2C 네트워크 라우팅에 사용 됩니다. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 리소스 공급자 테 넌 트에 대 한 멤버 자격 데이터를 저장 하는 데 사용 됩니다. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 릴레이 쿠키를 저장 하는 데 사용 됩니다. |
