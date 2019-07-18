---
title: Single sign-on을 구성 하는 방법 | Microsoft Docs
description: Azure AD로 개발 및 등록 중인 사용자 지정 애플리케이션에 대해 Single Sign-On을 구성하는 방법.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d151d4d7f32c479f2cfb4d71a8ed667f42e03b49
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465682"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>응용 프로그램에서 single sign-on을 구성 하는 방법

앱에서 페더레이션된 SSO(Single Sign-On) 사용은 OpenID Connect, SAML 2.0 또는 WS-Fed에 대해 Azure AD를 통해 페더레이션할 때 자동으로 설정됩니다. Azure AD에 기존 세션이 이미 있음에도 불구하고 최종 사용자가 로그인해야 하는 경우 앱이 잘못 구성되었을 수 있습니다.

* ADAL/MSAL을 사용하는 경우 **PromptBehavior**를 **항상**이 아니라 **자동**으로 설정했는지 확인하세요.

* 모바일 앱을 빌드하는 경우 조정되었거나 조정되지 않은 SSO를 사용하려면 추가 구성이 필요할 수 있습니다.

Android의 경우 [Android에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)을 참조하세요.<br>

iOS의 경우 [iOS에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Android에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[iOS에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[AzureAD와 앱 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[AzureAD v2.0 수렴형 앱에 대한 동의 및 권한 부여](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
