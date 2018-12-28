---
title: '자습서: Dropbox for Business와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 Dropbox for Business 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: jeedes
ms.openlocfilehash: eadf6724891d348c2ea3654bcf19ef0d74078049
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42145882"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>자습서: Dropbox for Business와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Dropbox for Business를 통합하는 방법에 대해 알아봅니다.

Dropbox for Business를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- Dropbox for Business에 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Dropbox for Business에 자동으로 로그온(Single Sign-On) 되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Dropbox for Business와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Dropbox for Business Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Dropbox for Business 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-dropbox-for-business-from-the-gallery"></a>갤러리에서 Dropbox for Business 추가

Dropbox for Business가 Azure AD에 통합되도록 구성하려면 갤러리에서 Dropbox for Business를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Dropbox for Business를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Dropbox for Business**를 입력하고 결과 패널에서 **Dropbox for Business**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Dropbox for Business](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Dropbox for Business에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD의 사용자에 해당하는 Dropbox for Business의 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 Dropbox for Business의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

Dropbox for Business에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Dropbox for Business에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Dropbox for Business 테스트 사용자 만들기](#create-a-dropbox-for-business-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 Dropbox for Business에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Dropbox for Business 응용 프로그램에서 Single Sign-On을 구성합니다.

**Dropbox for Business에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Dropbox for Business** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. **Dropbox for Business 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Dropbox for Business 도메인 및 URL Single Sign-On 정보](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://www.dropbox.com/sso/<id>`

    b. **식별자** 텍스트 상자에 값 `Dropbox`를 입력합니다.

    > [!NOTE]
    > 위의 로그온 URL 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 로그온 URL로 값을 업데이트하게 됩니다.

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

6. **Dropbox for Business 구성** 섹션에서 **Dropbox for Business 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Dropbox for Business 구성](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. **Dropbox for Business** 쪽에서 Single Sign-On을 구성하려면 Dropbox for Business 테넌트에서 계속 진행하고 DropBox for Business 테넌트에 로그온합니다.

    ![Single Sign-On 구성](./media/dropboxforbusiness-tutorial/ic769509.png "Single Sign-On 구성")

8. **사용자 아이콘**을 클릭하고 **설정** 탭을 선택합니다.

    ![Single Sign-On 구성](./media/dropboxforbusiness-tutorial/configure1.png "Single Sign-On 구성")

9. 왼쪽의 탐색 창에서 **관리 콘솔**을 클릭합니다.

    ![Single Sign-On 구성](./media/dropboxforbusiness-tutorial/configure2.png "Single Sign-On 구성")

10. **관리 콘솔**의 왼쪽 탐색 창에서 **설정**을 클릭합니다.

    ![Single Sign-On 구성](./media/dropboxforbusiness-tutorial/configure3.png "Single Sign-On 구성")

11. **인증** 섹션에서 **Single Sign-On** 옵션을 선택합니다.

    ![Single Sign-On 구성](./media/dropboxforbusiness-tutorial/configure4.png "Single Sign-On 구성")

12. **Single Sign-On** 섹션에서 다음 단계를 수행합니다.  

    ![Single Sign-On 구성](./media/dropboxforbusiness-tutorial/configure5.png "Single Sign-On 구성")

    a. **Single Sign-On**에 대한 드롭다운에서 옵션으로 **필수**를 선택합니다.

    b. **로그인 URL 추가**를 클릭하고 **ID 공급자 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **SAML Single Sign-on 서비스 URL** 값을 붙여 넣고 **완료**를 선택합니다.

    ![Single Sign-On 구성](./media/dropboxforbusiness-tutorial/configure6.png "Single Sign-On 구성")

    다. **인증서 업로드**를 클릭하고 Azure Portal에서 다운로드한 **Base64로 인코딩된 인증서 파일**로 이동합니다.

    d. **링크 복사**를 클릭하고 복사한 값을 Azure Portal의 **Dropbox for Business 도메인 및 URL** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여 넣습니다.

    e. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Dropbox for Business 테스트 사용자 만들기

이 섹션에서는 Dropbox for Business에서 Britta Simon이라는 사용자를 만듭니다. Dropbox for Business는 기본적으로 사용하도록 설정된 Just-In-Time 프로비전을 지원합니다.

이 섹션에 작업 항목이 없습니다. Dropbox for Business에 아직 사용자가 없으면 Dropbox for Business에 액세스하려고 할 때 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Dropbox for Business 클라이언트 지원 팀](https://www.dropbox.com/business/contact)에 문의합니다. 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Dropbox for Business에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Dropbox for Business에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 응용 프로그램 목록에서 **Dropbox for Business**를 선택합니다.

    ![응용 프로그램 목록의 Dropbox for Business 링크](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Dropbox for Business 타일을 클릭하면 Dropbox for Business 응용 프로그램의 로그인 페이지가 표시됩니다.
 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

