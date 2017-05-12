---
title: "Azure AD 응용 프로그램 프록시를 사용하여 앱에 SSO(Single sign-on) | Microsoft Docs"
description: "Azure Portal에서 Azure AD 응용 프로그램을 사용하여 게시된 온-프레미스 응용 프로그램에 대한 SSO(Single sign-on)를 켭니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b0454ad51a2d0edecb6f7e7748ec3b74eeefa1da
ms.contentlocale: ko-kr
ms.lasthandoff: 04/28/2017

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>Azure AD 응용 프로그램 프록시를 사용하여 SSO(Single sign-on) 제공 - 공개 미리 보기

Azure Active Directory 응용 프로그램 프록시는 원격 직원들이 안전하게 액세스할 수 있도록 온-프레미스 응용 프로그램을 게시하여 생산성을 개선하는 데 도움을 줍니다. Azure Portal에서는 이러한 앱에 대한 SSO(Single Sign-On)을 설정할 수도 있습니다. 이제 사용자는 Azure AD로 인증해야 하며 다시 로그인할 필요 없이 Enterprise 응용 프로그램에 액세스할 수 있습니다.

이 문서에서는 암호 기반 앱의 예제를 사용하여 암호 보관이 단순한 SSO 환경을 제공하는 방법을 보여 줍니다. 

이미 응용 프로그램 프록시를 사용하여 앱을 게시하고 테스트했을 것입니다. 그렇지 않은 경우 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시 - 공개 미리 보기](application-proxy-publish-azure-portal.md)의 단계를 따른 다음, 이곳으로 다시 오세요. 

또는 응용 프로그램 프록시가 처음인 경우 [온-프레미스 응용 프로그램에 안전한 원격 액세스를 제공하는 방법](active-directory-application-proxy-get-started.md) 문서를 사용하여 이 기능에 대해 자세히 알아 보세요.

## <a name="set-up-password-vaulting-for-your-application"></a>응용 프로그램에 대한 암호 보관 설정

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.
3. 목록에서 SSO로 설정할 앱을 선택합니다. 많은 앱이 있는 경우 검색 상자를 사용하여 적합한 앱을 필터링할 수 있습니다.  
4. 관리 섹션에서 **Single sign-on**을 클릭합니다.

   ![Single Sign-On 선택](./media/application-proxy-sso-azure-portal/select-sso.png)

5. SSO 모드에 대해 **암호 기반 로그온**을 선택합니다.
6. 로그온 URL에 대해 사용자가 앱에 로그인하기 위해 사용자 이름과 암호를 입력하는 페이지의 URL을 입력합니다. 이 URL은 응용 프로그램 프록시를 통해 앱을 게시할 때 만든 외부 URL이어야 합니다. 

   ![암호 기반 로그온 선택 및 URL 입력](./media/application-proxy-sso-azure-portal/password-sso.png)

7. **저장**을 선택합니다.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>앱 테스트

[MyApps 사이트](https://myapps.microsoft.com)로 이동한 후 방금 구성한 앱을 선택합니다. 해당 앱에 대한 자격 증명(또는 액세스 권한으로 설정한 테스트 계정에 대한 자격 증명)으로 로그인합니다. 로그인한 후에는 자격 증명을 다시 입력하지 않아도 앱에서 나갔다가 다시 돌아올 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[응용 프로그램 프록시를 사용하는 Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)을 구현하는 다른 방법에 대해 읽기

