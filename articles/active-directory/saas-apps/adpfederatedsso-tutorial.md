---
title: '자습서: ADP와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 ADP 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: 487c9cb145000b92a4aa664ea2bd159026104b6b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065157"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>자습서: ADP와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ADP를 통합하는 방법에 대해 알아봅니다.
ADP를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* ADP에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 ADP에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

ADP와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* ADP Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* ADP에서 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-adp-from-the-gallery"></a>갤러리에서 ADP 추가

ADP의 Azure AD 통합을 구성하려면 갤러리의 ADP를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ADP를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **ADP**를 입력하고 결과 패널에서 **ADP**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 ADP](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 ADP에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 ADP의 관련 사용자 간에 연결 관계를 설정해야 합니다.

ADP에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[ADP Single Sign-On 구성](#configure-adp-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[ADP 테스트 사용자 만들기](#create-adp-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 ADP에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

ADP에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. Azure Portal의 **ADP** 애플리케이션 통합 페이지에서 **속성 탭**을 클릭하고 다음 단계를 수행합니다. 

    ![Single Sign-On 속성](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. **사용자가 로그인할 수 있습니까** 필드 값을 **예**로 설정합니다.

    b. **사용자 액세스 URL**을 복사하고 자습서의 뒷부분에서 설명되는 **로그온 URL 구성 섹션**에 붙여넣어야 합니다.

    다. **사용자 할당 필요** 필드 값을 **예**로 설정합니다.

    d. **사용자가 볼 수 있습니까** 필드 값을 **아니요**로 설정합니다.

2. [Azure Portal](https://portal.azure.com/)의 **ADP** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

3. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

5. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![ADP 도메인 및 URL Single Sign-On 정보](common/idp-identifier.png)

    **식별자(엔터티 ID)** 텍스트 상자에서 `https://fed.adp.com` URL을 입력합니다.

6. ADP 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다. 클레임 이름은 항상 **"PersonImmutableID"** 이며, **employeeid**와 매핑하기 위해 이를 표시한 값입니다. 

    Azure AD에서 ADP로의 사용자 매핑은 **employeeid**에서 수행되지만, 애플리케이션 설정에 따라 이를 다른 값으로 매핑할 수 있습니다. 따라서 사용자의 올바른 식별자를 사용하고 해당 값을 **"PersonImmutableID"** 클레임으로 매핑하려면 [ADP 지원팀](https://www.adp.com/contact-us/overview.aspx)과 먼저 작업해 보세요.

    ![이미지](common/edit-attribute.png)

7. 위의 이미지와 같이 SAML 토큰 특성을 구성하기 위해 **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 **편집 아이콘**을 사용하여 클레임을 편집하거나 **새 클레임 추가**를 사용하여 클레임을 추가하고, 다음 단계를 수행합니다.
    
    | 이름 | 원본 특성 | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

    > [!NOTE] 
    > SAML 어설션을 구성하려면 먼저 [ADP 지원팀](https://www.adp.com/contact-us/overview.aspx)에 문의하여 테넌트에 대한 고유 사용자 식별자의 특성 값을 요청해야 합니다. 애플리케이션에 대한 사용자 지정 클레임을 구성하려면 이 값이 필요합니다. 

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>ADP Single Sign-On 구성

**ADP** 측에서 Single Sign-On을 구성하려면 [ADP 웹 사이트](https://adpfedsso.adp.com/public/login/index.fcc)에서 다운로드된 **메타데이터 XML**을 업로드해야 합니다.

> [!NOTE]  
> 이 프로세스는 며칠이 걸릴 수 있습니다.

### <a name="configure-your-adp-services-for-federated-access"></a>페더레이션 액세스에 대한 ADP 서비스 구성

>[!Important]
> ADP 서비스에 대한 페더레이션 액세스를 필요로 하는 직원은 ADP 서비스 앱에 할당되어야 하며 이후에 사용자는 특정 ADP 서비스에 다시 할당되어야 합니다.
ADP 담당자로부터 확인을 받는 즉시 ADP 서비스를 구성하고 특정 ADP 서비스에 대한 사용자 액세스를 제어하도록 사용자를 할당/관리합니다.

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **ADP**를 입력하고 결과 패널에서 **ADP**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 ADP](common/search-new-app.png)

5. Azure Portal의 **ADP** 애플리케이션 통합 페이지에서 **속성 탭**을 클릭하고 다음 단계를 수행합니다.  

    ![Single Sign-On 연결된 속성](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  **사용자가 로그인할 수 있습니까** 필드 값을 **예**로 설정합니다.

    b.  **사용자 할당 필요** 필드 값을 **예**로 설정합니다.

    다.  **사용자가 볼 수 있습니까** 필드 값을 **예**로 설정합니다.

6. [Azure Portal](https://portal.azure.com/)의 **ADP** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

7. **Single Sign-On 방법 선택** 대화 상자에서 **모드**를 **연결됨**으로 선택합니다. 애플리케이션을 **ADP**에 연결하합니다.

    ![연결된 Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. **로그온 URL 구성** 섹션으로 이동하고 다음 단계를 수행합니다.

    ![Single Sign-On 속성](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. 위의 **속성 탭**(기본 ADP 앱)에서 복사한 **사용자 액세스 URL**을 붙여넣습니다.
                                                             
    b. 다음은 다른 **릴레이 상태 URL**을 지원하는 다섯 개의 앱입니다. 특정 애플리케이션에 대한 적절한 **릴레이 상태 URL** 값을 **사용자 액세스 URL**에 수동으로 추가해야 합니다.
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **저장** 합니다.

10. ADP 담당자로부터 확인을 받는 즉시 한 명 또는 두 명의 사용자로 테스트를 시작합니다.

    a. ADP 서비스 앱에 적은 수의 사용자를 할당하여 페더레이션 액세스를 테스트합니다.

    b. 사용자가 갤러리에서 ADP 서비스 앱에 액세스하고 해당 ADP 서비스에 액세스할 수 있는 경우 테스트는 성공합니다.
 
11. 성공적인 테스트의 확인 후 페더레이션된 ADP 서비스를 개별 사용자 또는 사용자 그룹에 할당하고(자습서의 뒷부분에서 설명됨) 직원에게 롤아웃합니다.
 
### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ADP에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **ADP**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **ADP**를 입력하고 선택합니다.

    ![애플리케이션 목록의 ADP 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-adp-test-user"></a>ADP 테스트 사용자 만들기

이 섹션은 ADP에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. ADP 계정에 사용자를 추가하려면 [ADP 지원팀](https://www.adp.com/contact-us/overview.aspx)에 문의하세요. 

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ADP 타일을 클릭하면 SSO를 설정한 ADP에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

