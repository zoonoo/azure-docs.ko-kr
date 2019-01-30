---
title: '자습서: ISM(Ivanti Service Manager)과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 ISM(Ivanti Service Manager) 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeedes
ms.openlocfilehash: 3b394ff8e3638a9663e756fd6db866b0c3e5d2ef
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447664"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>자습서: ISM(Ivanti Service Manager)과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ISM(Ivanti Service Manager)을 통합하는 방법을 알아봅니다.

ISM(Ivanti Service Manager)을 Azure AD와 통합하면 다음과 같은 혜택이 있습니다.

- Azure AD에서 ISM(Ivanti Service Manager)에 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 ISM(Ivanti Service Manager)에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

ISM(Ivanti Service Manager)과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- ISM(Ivanti Service Manager) Single Sign-On 지원 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ISM(Ivanti Service Manager) 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>갤러리에서 ISM(Ivanti Service Manager) 추가

Azure AD로 ISM(Ivanti Service Manager) 통합을 구성하려면 갤러리의 ISM(Ivanti Service Manager)을 관리형 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ISM(Ivanti Service Manager)을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **ISM(Ivanti Service Manager)** 을 입력하고 결과 창에서 **ISM(Ivanti Service Manager)** 을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 ISM(Ivanti Service Manager)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 한 ISM(Ivanti Service Manager)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서는 Azure AD 사용자에 해당하는 ISM(Ivanti Service Manager) 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 ISM(Ivanti Service Manager)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

ISM(Ivanti Service Manager)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[ISM(Ivanti Service Manager) 테스트 사용자 만들기](#creating-an-ivanti-service-manager-ism-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ISM(Ivanti Service Manager)에서 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 ISM(Ivanti Service Manager) 애플리케이션에서 Single Sign-On을 구성합니다.

**ISM(Ivanti Service Manager)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **ISM(Ivanti Service Manager)** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial-general-301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![ISM(Ivanti Service Manager) 도메인 및 URL Single Sign-On 정보](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url.png)

    a. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![ISM(Ivanti Service Manager) 도메인 및 URL Single Sign-On 정보](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url1.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<customer>.saasit.com/`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [ISM(Ivanti Service Manager) 클라이언트 지원 팀](https://www.ivanti.com/support/contact)에 문의하세요.

6. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고, **인증서(원시)** 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-certificate.png) 

7. **ISM(Ivanti Service Manager) 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![ISM(Ivanti Service Manager) 구성](common/configuresection.png)

8. **ISM(Ivanti Service Manager)** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **인증서(Raw)** 및 복사한 **로그인 URL**, **Azure AD 식별자**, **로그아웃 URL**을 [ISM(Ivanti Service Manager) 지원 팀](https://www.ivanti.com/support/contact)으로 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](common/create-aaduser-01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](common/create-aaduser-02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="creating-an-ivanti-service-manager-ism-test-user"></a>ISM(Ivanti Service Manager) 테스트 사용자 만들기

이 섹션의 목적은 ISM(Ivanti Service Manager)에서 Britta Simon이라는 사용자를 만들기 위함입니다. ISM(Ivanti Service Manager)은 Just-In-Time 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다. 이 섹션에 작업 항목이 없습니다. 아직 존재하지 않는 경우 ISM(Ivanti Service Manager)에 액세스하려 하는 동안 새 사용자를 만듭니다.
>[!Note]
>사용자를 수동으로 만들어야 하는 경우  [ISM(Ivanti Service Manager) 지원 팀](https://www.ivanti.com/support/contact)에 문의하세요.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ISM(Ivanti Service Manager)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **ISM(Ivanti Service Manager)** 을 선택합니다.

    ![Configure Single Sign-On](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ISM(Ivanti Service Manager) 타일을 클릭하면 ISM(Ivanti Service Manager) 애플리케이션에 자동으로 로그온돼야 합니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
