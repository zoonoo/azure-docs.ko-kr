---
title: 쿠키 정의-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 사용 하는 쿠키에 대 한 정의 제공 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.component: B2C
ms.openlocfilehash: 1de1734d791608f3262d2af70becc2e082c9f317
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511142"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대 한 쿠키 정의

다음 표에서 Azure Active Directory B2C에서 사용 하는 쿠키를 나열 합니다.

| 이름 | Domain | 만료 | 목적 |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | 테 넌 트에서 사용자 구성원 자격 데이터를 보유합니다. 테 넌 트 사용자를 멤버의 수준 (관리자 또는 사용자)의 멤버 자격의 경우 |
| x-ms-cpim-slice | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | 적절 한 프로덕션 인스턴스에 요청을 라우팅하는 데 있습니다. |
| x-ms-cpim-trans | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | 트랜잭션 (Azure AD B2C로 인증 요청 수) 및 현재 트랜잭션을 추적에 사용 합니다. |
| x-ms-cpim-sso:{Id} | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | SSO 세션을 유지 관리에 사용 합니다. |
| x-ms-cpim-cache:{id}_n | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md), 인증 성공 | 요청 상태를 유지 관리에 사용 합니다. |
| x-ms-cpim-csrf | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | CRSF 보호에 사용 되는 교차 사이트 요청 위조 토큰입니다. |
| x-ms-cpim-dc | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | Azure AD B2C 네트워크 라우팅에 사용 합니다. |
| x-ms-cpim-ctx | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | Context |
| x-ms-cpim-rp | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | 리소스 공급자 테 넌 트에 대 한 구성원 자격 데이터를 저장 하는 데 사용 합니다. |
| x-ms-cpim-rc | b2clogin.com login.microsoftonline.com, 브랜드 도메인 | 끝 [브라우저 세션](active-directory-b2c-token-session-sso.md) | 릴레이 쿠키를 저장 하는 데 사용 합니다. |

