---
title: '자습서: RingCentral과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 RingCentral 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 7cd20397c122834a23586bf51af6467f58531094
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997243"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>자습서: RingCentral과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Active Directory)와 RingCentral을 통합하는 방법에 대해 알아봅니다.
RingCentral을 Azure AD와 통합하면 다음과 같은 이점을 얻을 수 있습니다.

* RingCentral 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 RingCentral에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

RingCentral과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* RingCentral Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* RingCentral은 **SP** 시작 SSO를 지원합니다.

## <a name="adding-ringcentral-from-the-gallery"></a>갤러리에서 RingCentral 추가

Azure AD에 대한 RingCentral 통합을 구성하려면 갤러리의 RingCentral을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 RingCentral을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **RingCentral**을 입력하고 결과 패널에서 **RingCentral**을 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 RingCentral](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 RingCentral에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 RingCentral의 관련 사용자 간에 연결 관계를 설정해야 합니다.

RingCentral에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[RingCentral Single Sign-On 구성](#configure-ringcentral-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[RingCentral 테스트 사용자 만들기](#create-ringcentral-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 RingCentral에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

RingCentral에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **RingCentral** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **서비스 공급자 메타데이터 파일**이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 **기본 SAML 구성** 섹션에 자동으로 채워집니다.

    ![RingCentral 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    **로그온 URL** 텍스트 상자에 다음 URL을 입력합니다.
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > **서비스 공급자 메타데이터 파일**은 RingCentral SSO 구성 페이지에서 제공됩니다. 이 페이지에 대해서는 자습서 뒷부분에서 설명합니다.

5. **서비스 공급자 메타데이터 파일**이 없는 경우 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 URL을 입력합니다.

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. **식별자** 텍스트 상자에 다음 URL을 입력합니다.

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    다. **회신 URL** 텍스트 상자에서 URL을 입력합니다.

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![RingCentral 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>RingCentral Single Sign-On 구성

1. 다른 웹 브라우저 창에서 RingCentral에 보안 관리자로 로그인합니다.

1. 브라우저 위쪽의 **도구**를 클릭합니다.

    ![이미지](./media/ringcentral-tutorial/ringcentral1.png)

1. **Single Sign-On**으로 이동합니다.

    ![이미지](./media/ringcentral-tutorial/ringcentral2.png)

1. **Single Sign-On** 페이지의 **SSO 구성** 섹션 아래 **1단계**에서 **편집**을 클릭하고 다음 단계를 수행합니다.

    ![이미지](./media/ringcentral-tutorial/ringcentral3.png)

1. **Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    ![이미지](./media/ringcentral-tutorial/ringcentral4.png)

    a. **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    b. 메타데이터를 업로드하면 **SSO 일반 정보** 섹션에 값이 자동으로 입력됩니다.

    다. **특성 매핑** 섹션에서 **전자 메일 특성 매핑 대상**을 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`로 선택합니다.

    d. **저장**을 클릭합니다.

    e. **2단계**에서 **다운로드**를 클릭하여 **서비스 공급자 메타데이터 파일**을 다운로드한 후 **기본 SAML 구성** 섹션에서 업로드합니다. 그러면 Azure Portal에서 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

    ![이미지](./media/ringcentral-tutorial/ringcentral6.png) 

    f. 같은 페이지의 **SSO 사용** 섹션으로 이동하여 다음 단계를 수행합니다.

    ![이미지](./media/ringcentral-tutorial/ringcentral5.png)

    * **SSO 서비스 사용**을 선택합니다.

    * **사용자가 SSO 또는 RingCentral 자격 증명으로 로그인할 수 있도록 허용**을 선택합니다.

    * **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon@yourcompanydomain.extension`을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 RingCentral 액세스 권한을 부여하여 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **RingCentral**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **RingCentral**을 선택합니다.

    ![애플리케이션 목록의 RingCentral 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-ringcentral-test-user"></a>RingCentral 테스트 사용자 만들기

이 섹션에서는 RingCentral에서 Britta Simon 사용자를 만듭니다. RingCentral 플랫폼에서 사용자를 추가하려면  [RingCentral 클라이언트 지원 팀](https://success.ringcentral.com/RCContactSupp) 에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 RingCentral 타일을 클릭하면 SSO를 설정한 RingCentral에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
