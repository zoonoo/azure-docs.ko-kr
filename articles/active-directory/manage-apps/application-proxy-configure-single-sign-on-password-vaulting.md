---
title: Azure AD 애플리케이션 프록시를 사용하여 앱에 SSO(Single sign-on) | Microsoft Docs
description: Azure Portal에서 Azure AD 애플리케이션을 사용하여 게시된 온-프레미스 애플리케이션에 대한 SSO(Single sign-on)를 켭니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18510bd7ace6ca87278b5bf68f79b372251ac0e1
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807818"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>애플리케이션 프록시를 사용하여 Single Sign-On에 대한 암호 자격 증명 모음 설정

Azure Active Directory 애플리케이션 프록시는 원격 직원들이 안전하게 액세스할 수 있도록 온-프레미스 애플리케이션을 게시하여 생산성을 개선하는 데 도움을 줍니다. Azure Portal에서는 이러한 앱에 대한 SSO(Single Sign-On)을 설정할 수도 있습니다. 사용자는 Azure AD만으로 인증해야 하며, 다시 로그인할 필요 없이 엔터프라이즈 애플리케이션에 액세스할 수 있습니다.

애플리케이션 프록시에서는 여러 [Single Sign-On 모드](what-is-single-sign-on.md#choosing-a-single-sign-on-method)를 지원합니다. 암호 기반 로그인은 인증에 사용자 이름/암호 조합을 사용하는 애플리케이션을 위한 것입니다. 애플리케이션에 대한 암호 기반 로그인을 구성하는 경우 사용자는 온-프레미스 애플리케이션에 한 번 로그인해야 합니다. 그런 다음 Azure Active Directory에서 로그인 정보를 저장하고 사용자가 원격으로 액세스할 때 애플리케이션에 자동으로 제공합니다.

이미 애플리케이션 프록시를 사용하여 앱을 게시하고 테스트했을 것입니다. 그렇지 않은 경우 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)의 단계를 수행한 다음 여기로 다시 돌아옵니다.

## <a name="set-up-password-vaulting-for-your-application"></a>애플리케이션에 대한 암호 보관 설정

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **Enterprise 애플리케이션** > **모든 애플리케이션**을 선택합니다.
1. 목록에서 SSO로 설정할 앱을 선택합니다.  
1. **Single Sign-On**을 선택합니다.

   ![앱의 개요 페이지에서 Single sign-on 선택](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. SSO 모드에 대해 **암호 기반 로그온**을 선택합니다.
1. 로그온 URL에 대해 사용자가 회사 네트워크 외부에서 앱에 로그인하기 위해 사용자 이름과 암호를 입력하는 페이지에 대한 URL을 입력합니다. 이 URL은 애플리케이션 프록시를 통해 앱을 게시할 때 만든 외부 URL일 수 있습니다.

   ![암호 기반 로그온 선택 및 URL 입력](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1.           **저장**을 선택합니다.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>앱 테스트

애플리케이션에 원격으로 액세스하도록 구성된 외부 URL로 이동합니다. 해당 앱에 대한 자격 증명(또는 액세스 권한으로 설정한 테스트 계정에 대한 자격 증명)으로 로그인합니다. 로그인한 후에는 자격 증명을 다시 입력하지 않아도 앱에서 나갔다가 다시 돌아올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Single Sign-On](what-is-single-sign-on.md)을 구현하는 다른 방법에 대해 읽기
- [Azure AD 애플리케이션 프록시를 사용하여 앱에 원격으로 액세스하는 경우 보안 고려 사항](application-proxy-security.md)을 알아봅니다
