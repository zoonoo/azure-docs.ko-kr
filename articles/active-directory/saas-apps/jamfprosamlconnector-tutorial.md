---
title: '자습서: Azure Active Directory와 Jamf Pro 통합 | Microsoft Docs'
description: Azure Active Directory와 Jamf Pro 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2018
ms.author: jeedes
ms.openlocfilehash: 94b8b935728110cd5dd07b2066e8320274e3b082
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428420"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>자습서: Azure Active Directory와 Jamf Pro 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Jamf Pro를 통합하는 방법에 대해 알아봅니다.

Jamf Pro를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Jamf Pro에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 본인 Azure AD 계정으로 Jamf Pro에 자동으로 로그온(Single Sign-On)하도록 할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure AD와 Jamf Pro의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Jamf Pro Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Jamf Pro 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-jamf-pro-from-the-gallery"></a>갤러리에서 Jamf Pro 추가
Azure AD에 Jamf Pro의 통합을 구성하려면 갤러리의 Jamf Pro를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Jamf Pro를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
1. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

1. 검색 상자에 **Jamf Pro**를 입력하고 결과 패널에서 **Jamf Pro**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Jamf Pro에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Jamf Pro 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Jamf Pro의 관련 사용자 간에 연결 관계가 설정되어 있어야 합니다.

Jamf Pro에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Jamf Pro 테스트 사용자 만들기](#create-a-jamf-pro-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Jamf Pro에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Jamf Pro 응용 프로그램에서 Single Sign-On을 구성합니다.

**Jamf Pro에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Jamf Pro** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

1. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **Jamf Pro 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Jamf Pro 도메인 및 URL Single Sign-On 정보](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다. `https://<subdomain>.jamfcloud.com/saml/metadata`

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<subdomain>.jamfcloud.com/saml/SSO`

1. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![Jamf Pro 도메인 및 URL Single Sign-On 정보](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 자습서의 뒷부분에 설명하는 실제 식별자 값을 Jamf Pro 포털의 **Single Sign On** 섹션에서 가져옵니다. 실제 **하위 도메인** 값을 식별자 값에서 추출하여 해당 **하위 도메인** 정보를 로그온 URL 및 회신 URL에서 사용할 수 있습니다.

1. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.

    ![인증서 다운로드 링크](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
1. 다른 웹 브라우저 창에서 Jamf Pro 회사 사이트에 관리자로 로그인합니다.

1. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘**을 클릭합니다.

    ![Jamf Pro 구성](./media/jamfprosamlconnector-tutorial/configure1.png)

1. **Single Sign-On**을 클릭합니다.

    ![Jamf Pro 구성](./media/jamfprosamlconnector-tutorial/configure2.png)

1. **Single Sign-On** 페이지에서 다음 단계를 수행합니다.

    ![Jamf Pro 단일](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. **Jamf Pro 서버**를 선택하여 Single Sign-On 액세스를 사용하도록 설정합니다.

    나. **모든 사용자에 바이패스 허용**을 선택하면 사용자가 인증을 위한 ID 공급자 로그인 페이지로 리디렉션되지 않지만, 대신 Jamf Pro에 직접 로그인할 수 있습니다. 사용자가 ID 공급자를 통해 Jamf Pro에 액세스하려고 시도하는 경우 IdP에서 시작한 SSO 인증 및 권한 부여가 발생합니다.

    다. **사용자 매핑: SAML**에 대해 **NameID** 옵션을 선택합니다. 기본적으로 이 설정은 **NameID**로 설정되어 있지만 사용자 지정 특성을 정의할 수도 있습니다.

    d. **사용자 매핑: JAMF PRO**에 대해 **이메일**을 선택합니다. Jamf Pro는 사용자 및 그룹에 의한 방법으로 IdP에서 보낸 SAML 특성을 매핑합니다. 사용자가 Jamf Pro에 액세스하려고 시도할 때 Jamf Pro는 기본적으로 ID 공급자로부터 사용자에 대한 정보를 가져와서 Jamf Pro 사용자 계정과 대조해 봅니다. 들어오는 사용자 계정이 Jamf Pro에 존재하지 않으면 그룹 이름 일치가 발생합니다.

    e. `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 값을 **그룹 특성 이름** 텍스트 상자에 붙여넣습니다.
 
1. 같은 페이지에서 **Single Sign-On** 섹션의 **ID 공급자**까지 아래로 스크롤하여 다음 단계를 수행합니다.

    ![Jamf Pro 구성](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **ID 공급자** 드롭다운 메뉴에서 **기타**를 옵션으로 선택합니다.

    나. **다른 공급자** 텍스트 상자에 **Azure AD**를 입력합니다.

    다. **ID 공급자 메타데이터 소스** 드롭다운에서 **메타데이터 URL**을 옵션으로 선택하고, 다음 텍스트 상자에 Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 붙여넣습니다.

    d. **엔터티 ID** 값을 복사하고 Azure Portal의 **Jamf Pro 도메인 및 URL** 섹션에 있는 **식별자(엔터티 ID)** 텍스트 상자에 붙여넣습니다.

    >[!NOTE]
    > 여기서 흐릿한 값은 하위 도메인 부분입니다. 이 값을 사용하여 Azure Portal의 **Jamf Pro 도메인 및 URL** 섹션에서 로그온 URL 및 회신 URL을 완료합니다.

    e. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-jamf-pro-test-user"></a>Jamf Pro 테스트 사용자 만들기

Azure AD 사용자가 Jamf Pro에 로그인할 수 있도록 하려면 Jamf Pro로 프로비전되어야 합니다. Jamf Pro의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Jamf Pro 회사 사이트에 관리자 권한으로 로그인합니다.

1. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘**을 클릭합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/configure1.png)

1. **Jamf Pro 사용자 계정 및 그룹**을 클릭합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user1.png)

1. **새로 만들기**를 클릭합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user2.png)

1. **Standard 계정 만들기**를 선택합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user3.png)

1. **새 계정** 대화 상자에서 다음 단계를 수행합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **사용자 이름** 텍스트 상자에 BrittaSimon의 전체 이름을 입력합니다.

    나. 사용자 조직별로 **액세스 수준**, **권한 설정** 및 **액세스 상태**의 적절한 옵션을 선택합니다.
    
    다. **전체 이름** 텍스트 상자에 Britta Simon의 전체 이름을 입력합니다.

    d. **이메일 주소** 텍스트 상자에 Britta Simon 계정의 이메일 주소를 입력합니다.

    e. **암호** 텍스트 상자에 사용자의 암호를 입력합니다.

    f. **암호 확인** 텍스트 상자에 사용자의 암호를 입력합니다.

    g. **저장**을 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Jamf Pro에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Jamf Pro에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

1. 응용 프로그램 목록에서 **Jamf Pro**를 선택합니다.

    ![응용 프로그램 목록의 Jamf Pro 링크](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Jamf Pro 타일을 클릭하면 Jamf Pro 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

