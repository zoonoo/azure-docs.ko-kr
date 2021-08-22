---
title: OIDC 기반 Single Sign On 이해
titleSuffix: Azure AD
description: Azure Active Directory의 앱에서 사용되는 OIDC 기반 SSO(Single Sign-On)를 이해합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/19/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4cca31c844d4955df69a5fbdca450b67fd0f8bc9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528812"
---
# <a name="understand-oidc-based-single-sign-on"></a>OIDC 기반 Single Sign On 이해

애플리케이션 관리에 대한 [빠른 시작 시리즈](view-applications-portal.md)에서는 애플리케이션용 IdP(ID 공급자)로 Azure AD를 사용하는 방법을 알아보았습니다. 이 문서에서는 OpenID Connect 표준을 사용하여 Single Sign-On을 구현하는 앱에 대해 자세히 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure Active Directory 테넌트에 앱을 추가하는 프로세스는 구현된 애플리케이션의 Single Sign-On의 유형에 따라 달라집니다. ID 관리에 Azure AD를 사용할 수 있는 앱에 사용 가능한 Single Sign-On 옵션에 대한 자세한 내용은 [Single Sign-On 옵션](sso-options.md)을 참조하세요. 이 문서에서는 OIDC 기반 앱을 설명합니다.

## <a name="basic-oidc-configuration"></a>기본 OIDC 구성

[빠른 시작 시리즈](add-application-portal-setup-oidc-sso.md)에는 Single Sign-On 구성에 관한 문서가 있습니다. 여기서는 Azure 테넌트에 OIDC 기반 앱을 추가하는 방법에 대해 알아봅니다.

Single Sign-On에 OIDC 표준을 사용하는 앱을 추가하면 구성이 최소화됩니다. 다음은 테넌트에 OIDC 기반 앱을 추가하는 방법을 보여주는 짧은 비디오입니다.

Azure Active Directory에서 OIDC 기반 앱 추가

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

사용자 및 관리자 동의에 대한 자세한 내용은 [사용자 및 관리자 동의 이해](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 관리에 대한 빠른 시작 시리즈](add-application-portal-setup-oidc-sso.md)
- [Single Sign-On 옵션](sso-options.md)
- [방법: 다중 테넌트 애플리케이션 패턴을 사용하여 Azure Active Directory 사용자 로그인](../develop/howto-convert-app-to-be-multi-tenant.md)
