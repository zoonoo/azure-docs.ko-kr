---
title: "Azure AD | Microsoft Docs에서 페더레이션 인증서를 관리하는 방법"
description: "페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법 및 곧 만료되는 인증서를 갱신하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: fb33a0b01b7d9806c92dfc66303b0dd2ca1044d9
ms.openlocfilehash: 04276fc2da32b27dc9e0a4601ab45b9f1e95959a


---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리
이 문서에서는 Azure Active Directory에서 페더레이션된 SSO(Single Sign-On)를 설정하기 위해 만드는 인증서와 관련된 일반적인 질문을 다룹니다.

이 문서는 아래 예에 표시된 것처럼 **Azure AD Single Sign-On**을 사용하도록 구성된 앱에 대한 것입니다.

![Azure AD Single Sign-On](./media/active-directory-sso-certs/fed-sso.PNG)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate"></a>페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법
기본적으로 인증서는&2;년 후에 만료되도록 설정됩니다. 다음 단계를 수행하여 인증서에 대한 다른 만료 날짜를 선택할 수 있습니다. 포함된 스크린샷에서는 예제를 위해 Salesforce를 사용하지만 이러한 단계는 페더레이션된 모든 SaaS 앱에 적용할 수 있습니다.

1. Azure Active Directory에서 응용 프로그램에 대한 빠른 시작 페이지의 **Single Sign-On 구성**을 클릭합니다.
   
    ![SSO 구성 마법사를 엽니다.](./media/active-directory-sso-certs/config-sso.png)
2. **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
3. 응용 프로그램의 **로그온 URL**을 입력하고 **페더레이션된 Single Sign-On에 사용되는 인증서를 구성**합니다. 그런 후 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On](./media/active-directory-sso-certs/new-app-config-sso.PNG)
4. 다음 페이지에서 **새 인증서 생성**을 선택하고 인증서에 대해 유효한 기간을 선택합니다. 그런 후 **다음**을 클릭합니다.
   
    ![새 인증서 생성](./media/active-directory-sso-certs/new-app-config-cert.PNG)
5. 그런 다음 **인증서 다운로드**를 클릭합니다. 특정 SaaS 앱에 인증서를 업로드하는 방법을 알아보려면 **구성 지침 보기**를 클릭합니다.
   
    ![다운로드한 다음 인증서 업로드](./media/active-directory-sso-certs/new-app-config-app.PNG)
6. 대화 상자 맨아래에 있는 확인 확인란을 선택하고 제출을 클릭할 때까지 인증서를 사용할 수 없습니다.

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>곧 만료되는 인증서를 갱신하는 방법
아래에 표시된 갱신 단계를 수행하면 가동 중지 시간이 발생하지 않습니다. 이 섹션에 사용된 스크린샷은 Salesforce를 예로 사용하지만, 이러한 단계를 페더레이션된 모든 SaaS 앱에 적용할 수 있습니다.

1. Azure Active Directory에서 응용 프로그램에 대한 빠른 시작 페이지의 **Single Sign-On 구성**을 클릭합니다.
   
    ![SSO 구성 마법사 열기](./media/active-directory-sso-certs/renew-sso-button.PNG)
2. 대화 상자에서의 첫 페이지에서 **Azure AD Single Sign-on**이 이미 선택되어 있으므로 **다음**을 클릭합니다.
3. 두 번째 페이지에서 **페더레이션된 Single Sign-On에 사용되는 인증서를 구성합니다.**확인란을 선택합니다. 그런 후 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On](./media/active-directory-sso-certs/renew-config-sso.PNG)
4. 다음 페이지에서 **새 인증서 생성**을 선택하고 새 인증서에 대해 유효한 기간을 선택합니다. 그런 후 **다음**을 클릭합니다.
   
    ![새 인증서 생성](./media/active-directory-sso-certs/new-app-config-cert.PNG)
5. 그런 다음 **인증서 다운로드**를 클릭합니다. 인증서를 성공적으로 갱신하려면 다음 두 단계를 수행해야 합니다.
   
   * SaaS 앱의 Single Sign-On 구성 화면으로 새 인증서를 업로드합니다. 특정 SaaS 앱에 인증서를 업로드하는 방법을 알아보려면 **구성 지침 보기**를 클릭합니다.
   * Azure AD에서 새 인증서를 사용하도록 설정하는 대화 상자의 맨 아래에 있는 확인 확인란을 선택하고 **다음** 을 클릭하여 전송합니다.
     
     > [!IMPORTANT]
     > 앱에 Single Sign-On은 이러한 두 단계 중 한 단계를 완료하는 순간 비활성화되지만, 두 번째 단계를 완료하면 다시 활성화됩니다. 따라서 가동 중지 시간을 최소화하려면 각각 짧은 시간 내에 두 단계를 완료할 수 잇도록 준비하세요.
     > 
     > 
     
     ![다운로드한 다음 인증서 업로드](./media/active-directory-sso-certs/renew-config-app.PNG)

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)
* [SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md)




<!--HONumber=Jan17_HO3-->


