---
title: "자습서: New Relic과 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 New Relic을 사용하여 Single sign-on, 자동화 된 프로비저닝 등에 사용할 수 있는 방법에 대해 알아봅니다!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 84f9c9745cc0c95fc5134dcc7e659e7ace11b188
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>자습서: New Relic과 Azure Active Directory 통합
이 자습서는 Azure Active Directory와 New Relic 사이에 SSO(Single Sign-On)를 설정하는 방법을 보여주기 위한 것입니다.

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* New Relic SSO(Single Sign-On)가 설정된 구독

이 자습서를 완료한 후 New Relic에 할당한 Azure Active Directory 사용자가 AAD 액세스 패널에서 SSO를 수행할 수 있습니다.

1. New Relic에 응용 프로그램 통합 사용
2. SSO(Single Sign-On) 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-new-relic-tutorial/IC797030.png "시나리오")

## <a name="enabling-the-application-integration-for-new-relic"></a>New Relic에 응용 프로그램 통합 사용
이 섹션은 New Relic에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

**New Relic에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-new-relic-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-new-relic-tutorial/IC749321.png "응용 프로그램 추가")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-new-relic-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **New Relic**을 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-new-relic-tutorial/IC797031.png "응용 프로그램 갤러리")
7. 결과 창에서 **New Relic**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
   
## <a name="configure-single-sign-on"></a>Single Sign-On 구성

이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure Active Directory의 계정으로 New Relic에 인증할 수 있게 하는 방법을 간략하게 설명합니다.

**SSO를 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **New Relic** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Single Sign-On 구성")
2. **New Relic에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지의 **New Relic 로그온 URL** 텍스트 상자에 New Relic 응용 프로그램에 로그온하기 위해 사용자가 사용한 URL을 입력한 후 **다음**을 클릭합니다. 
   
   앱 URL은 New Relic 테넌트 URL입니다(예: *https://rpm.newrelic.com*).
   
   ![앱 URL 구성](./media/active-directory-saas-new-relic-tutorial/IC797034.png "앱 URL 구성")
4. **New Relic에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Single Sign-On 구성")
5. 다른 웹 브라우저 창에서 **New Relic** 회사 사이트에 관리자로 로그인합니다.
6. 위쪽의 메뉴에서 **계정 설정**을 클릭합니다.
   
   ![계정 설정](./media/active-directory-saas-new-relic-tutorial/IC797036.png "계정 설정")
7. **보안 및 인증** 탭을 클릭한 후 **Single sign on** 탭을 클릭합니다.
   
   ![Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")
8. SAML 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")
   
   1. **파일 선택** 을 클릭하여 다운로드한 Azure Active Directory 인증서를 업로드합니다.
   2. Azure 클래식 포털의 **New Relic에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **원격 로그인 URL** 텍스트 상자에 붙여넣습니다.
   3. Azure 클래식 포털의 **New Relic에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **로그아웃 방문 URL** 텍스트 상자에 붙여넣습니다.
   4. **내 변경 내용 저장**을 클릭합니다.
9. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Single Sign-On 구성")
   
## <a name="configure-user-provisioning"></a>사용자 프로비전 구성

Azure Active Directory 사용자가 New Relic에 로그인할 수 있도록 하려면 New Relic으로 프로비전되어야 합니다. New Relic의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 New Relic에 프로비전하려면 다음 단계를 수행합니다.**

1. **New Relic** 회사 사이트에 관리자 권한으로 로그인합니다.
2. 위쪽의 메뉴에서 **계정 설정**을 클릭합니다.
   
   ![계정 설정](./media/active-directory-saas-new-relic-tutorial/IC797040.png "계정 설정")
3. 왼쪽의 **계정** 창에서 **요약**을 클릭한 다음 **사용자 추가**를 클릭합니다.
   
   ![계정 설정](./media/active-directory-saas-new-relic-tutorial/IC797041.png "계정 설정")
4. **활성 사용자** 대화 상자에서 다음 단계를 수행합니다.
   
   ![활성 사용자](./media/active-directory-saas-new-relic-tutorial/IC797042.png "활성 사용자")
   
   1. **이메일** 텍스트 상자에 프로비전하려는 유효한 Azure Active Directory 사용자의 이메일 주소를 입력합니다.
   2. **역할**로 **사용자**를 선택합니다.
   3. **이 사용자 추가**를 클릭합니다.

>[!NOTE]
>다른 New Relic 사용자 계정 생성 도구 또는 New Relic이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

## <a name="assign-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

**New Relic에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **New Relic** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-new-relic-tutorial/IC797043.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-new-relic-tutorial/IC767830.png "예")

SSO 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)
