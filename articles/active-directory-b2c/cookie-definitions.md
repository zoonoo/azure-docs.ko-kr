---
title: 쿠키 정의-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에 사용 되는 쿠키에 대 한 정의를 제공 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930901"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대 한 쿠키 정의

다음 표에서는 Azure Active Directory B2C에 사용 되는 쿠키를 나열 합니다.

| 이름 | 도메인 | 만료 | 용도 |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | [브라우저 세션](session-behavior.md) 의 끝 | 테 넌 트 간에 사용자 멤버 자격 데이터를 저장 합니다. 사용자가 구성원 인 테 넌 트 (관리자 또는 사용자)입니다. |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 요청을 적절 한 프로덕션 인스턴스로 라우팅하는 데 사용 됩니다. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 트랜잭션을 추적 하는 데 사용 됩니다 (Azure AD B2C 인증 요청 수) 및 현재 트랜잭션. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | SSO 세션을 유지 관리 하는 데 사용 됩니다. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md)의 끝, 인증 성공 | 요청 상태를 유지 관리 하는 데 사용 됩니다. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | CRSF 보호에 사용 되는 사이트 간 요청 위조 토큰입니다. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | Azure AD B2C 네트워크 라우팅에 사용 됩니다. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | Context |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 리소스 공급자 테 넌 트에 대 한 멤버 자격 데이터를 저장 하는 데 사용 됩니다. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 의 끝 | 릴레이 쿠키를 저장 하는 데 사용 됩니다. |
