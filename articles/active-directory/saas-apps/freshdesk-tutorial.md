---
title: '자습서: FreshDesk와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 FreshDesk 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010816"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>자습서: FreshDesk와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 FreshDesk를 통합하는 방법에 대해 알아봅니다.

FreshDesk를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- FreshDesk에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 FreshDesk에 자동으로 SSO(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

FreshDesk와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- FreshDesk Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 FreshDesk 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-freshdesk-from-the-gallery"></a>갤러리에서 FreshDesk 추가

FreshDesk의 Azure AD 통합을 구성하려면 갤러리의 FreshDesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 FreshDesk를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]

3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **FreshDesk**를 입력하고 결과 패널에서 **FreshDesk**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 FreshDesk](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 FreshDesk에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 FreshDesk 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 FreshDesk의 관련 사용자 간에 연결이 형성되어야 합니다.

FreshDesk에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[FreshDesk 테스트 사용자 만들기](#creating-a-freshdesk-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 FreshDesk에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 FreshDesk 응용 프로그램에서 Single Sign-On을 구성합니다.

**FreshDesk에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **FreshDesk** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![FreshDesk 도메인 및 URL Single Sign-On 정보](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<tenant-name>.freshdesk.com` 또는 Freshdesk에서 제안한 다른 값을 사용하여 URL을 입력합니다.

    b. **ID(엔터티 ID)** 텍스트 상자에서 다음 패턴: `https://<tenant-name>.freshdesk.com` 또는 Freshdesk에서 제안한 다른 값을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이 값을 얻으려면 [FreshDesk Client 지원 팀](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)에 문의하세요.

5. FreshDesk 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. **사용자 ID**의 기본값은 **user.userprincipalname**이지만 **FreshDesk**에는 이것이 사용자의 이메일 주소와 매핑되어야 합니다. 목록에서 **user.mail** 특성을 사용하거나 조직 구성을 기반으로 적절한 특성 값을 사용할 수 있기 위해서입니다.

    ![이미지](./media/freshdesk-tutorial/i4-attribute.png)

6. **사용자 특성 및 클레임** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    a. **편집 아이콘**을 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/freshdesk-tutorial/i2-attribute.png)

    ![이미지](./media/freshdesk-tutorial/i3-attribute.png)

    b. **원본 특성** 목록에서 **user.mail**을 선택합니다.

    다. **저장**을 클릭합니다.

7. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **인증서(Base64)** 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. **명령 프롬프트**를 열고 다음 명령을 실행합니다.

    a. 명령 프롬프트에서 `certutil.exe -dump FreshDesk.cer` 값을 입력합니다.

    > [!NOTE]
    > 여기에서 **FreshDesk.cer**은 Azure Portal에서 다운로드한 인증서입니다.

    b. **Cert Hash(sha256)** 값을 복사하여 메모장에 붙여 넣습니다. 

9. **FreshDesk 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![FreshDesk 구성](common/configuresection.png)

10. 다른 웹 브라우저 창에서 Freshdesk 회사 사이트에 관리자로 로그인합니다.

11. **설정 아이콘**을 선택하고 **보안** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign On](./media/freshdesk-tutorial/IC776770.png "Single Sign On")
  
    a. **SSO(Single Sign On)** 의 경우 **On**을 선택합니다.

    b. **SAML SSO**를 선택합니다.

    다. **SAML 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    d. **로그아웃 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    e. **보안 인증서 지문** 텍스트 상자에 앞서 얻은 **Cert Hash(sha256)** 값을 붙여넣습니다.
  
    f. **저장**을 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="creating-a-freshdesk-test-user"></a>FreshDesk 테스트 사용자 만들기

Azure AD 사용자가 FreshDesk에 로그인할 수 있도록 하려면 FreshDesk로 프로비전되어야 합니다.  
FreshDesk의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Freshdesk** 테넌트에 로그인합니다.

2. 위쪽의 메뉴에서 **관리자**를 클릭합니다.

    ![관리자](./media/freshdesk-tutorial/IC776772.png "관리자")

3. **일반 설정** 탭에서 **에이전트**를 클릭합니다.
  
    ![에이전트](./media/freshdesk-tutorial/IC776773.png "에이전트")

4. **새 에이전트**를 클릭합니다.

    ![새 에이전트](./media/freshdesk-tutorial/IC776774.png "새 에이전트")

5. 에이전트 정보 대화 상자에서 다음 단계를 수행합니다.

    ![에이전트 정보](./media/freshdesk-tutorial/IC776775.png "에이전트 정보")

    a. **전자 메일** 텍스트 상자에 프로비전하려는 Azure AD 계정의 Azure AD 전자 메일 주소를 입력합니다.

    b. **전체 이름** 텍스트 상자에 프로비전하려는 Azure AD 계정의 이름을 입력합니다.

    다. **제목** 텍스트 상자에 프로비전하려는 Azure AD 계정의 제목을 입력합니다.

    d. **저장**을 클릭합니다.

    >[!NOTE]
    >Azure AD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다.
    >
    >[!NOTE]
    >다른 Freshdesk 사용자 계정 생성 도구 또는 Freshdesk가 제공한 API를 사용하여AAD 사용자 계정을 Freshdesk에 프로비전할 수 있습니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 FreshDesk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **FreshDesk**를 선택합니다.

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 FreshDesk 타일을 클릭하면 FreshDesk 애플리케이션에 자동으로 로그인됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
