---
title: '자습서: Atlassian Cloud와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Atlassian Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3356d7425e692f248a3850e8bef7b80d4daba276
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179946"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>자습서: Atlassian Cloud와 Azure Active Directory 통합

이 자습서에서는 Atlassian Cloud와 Azure AD(Azure Active Directory)를 통합하는 방법에 대해 알아봅니다.
Atlassian Cloud를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서는 Atlassian Cloud에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Atlassian Cloud에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Atlassian Cloud와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Atlassian Cloud Single Sign-On을 사용하도록 설정된 구독
* Atlassian Cloud 제품에 SAML(Security Assertion Markup Language) Single Sign-On을 사용하도록 설정하려면 Atlassian Access를 설정해야 합니다. [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)에 대해 자세히 알아보세요.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Atlassian Cloud에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-atlassian-cloud-from-the-gallery"></a>갤러리에서 Atlassian Cloud 추가

Azure AD에 Atlassian Cloud와 Azure AD를 통합하도록 구성하려면 갤러리의 Atlassian Cloud를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Atlassian Cloud를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Atlassian Cloud**를 입력하고 결과 패널에서 **Atlassian Cloud**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Atlassian Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Atlassian Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Atlassian Cloud의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Atlassian Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Atlassian Cloud Single Sign-On 구성](#configure-atlassian-cloud-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Atlassian Cloud 테스트 사용자 만들기](#create-atlassian-cloud-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Atlassian Cloud에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Atlassian Cloud에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Atlassian Cloud** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![[애플리케이션 이름] 도메인 및 URL Single Sign-On 정보](common/idp-relay.png)

    a. **식별자** 텍스트 상자에서 `https://auth.atlassian.com/saml/<unique ID>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>` 패턴을 사용하여 URL을 입력합니다.

    다. **추가 URL 설정**을 클릭합니다.

    d. **릴레이 상태** 텍스트 상자에서 `https://<instancename>.atlassian.net` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 위의 값은 실제가 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값은 이 자습서의 뒷부분에 설명된 Atlassian Cloud SAML 구성 화면에서 얻습니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![[애플리케이션 이름] 도메인 및 URL Single Sign-On 정보](common/both-signonurl.png)

    **로그인 URL** 텍스트 상자에서 `https://<instancename>.atlassian.net` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 위의 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Atlassian Cloud 클라이언트 지원 팀](https://support.atlassian.com/)에 문의하세요.

6. Atlassian Cloud 애플리케이션은 특정 형식의 SAML 어설션을 예상하며, 따라서 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 **nameidentifier**가 **user.userprincipalname**과 매핑되는 기본 특성 목록을 보여줍니다. Atlassian Cloud 애플리케이션에서는 **nameidentifier**가 **user.mail**과 매핑되므로 특성 매핑을 변경하려면 **편집** 아이콘을 클릭하고 특성 매핑을 편집해야 합니다.

    ![이미지](common/edit-attribute.png)

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **Atlassian Cloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-atlassian-cloud-single-sign-on"></a>Atlassian Cloud Single Sign-On 구성

1. 애플리케이션에 SSO를 구성하려면 관리자 자격 증명을 사용하여 Atlassian Portal에 로그인합니다.

2. Single Sign-On을 구성하기 전에 도메인을 확인해야 합니다. 자세한 내용은 [Atlassian 도메인 확인](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) 문서를 참조하세요.

3. 왼쪽 창에서 **SAML Single Sign-On**을 선택합니다. 아직 하지 않는 경우 Atlassian Identity Manager를 구독합니다.

    ![Single Sign-On 구성](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. **Add SAML configuration**(SAML 구성 추가) 창에서 다음을 수행합니다.

    ![Single Sign-On 구성](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. **Identity provider Entity ID**(ID 공급자 엔터티 ID) 상자에 Azure Portal에서 복사한 SAML 엔터티 ID를 붙여넣습니다.

    b. **ID 공급자 SSO URL** 상자에 Azure Portal에서 복사한 SAML Single Sign-On 서비스 URL을 붙여넣습니다.

    다. Azure Portal에서 .txt 파일로 다운로드한 인증서를 열고 값(*시작 인증서* 및 *끝 인증서* 행 제외)을 복사하여 **공용 X509 인증서** 상자에 붙여넣습니다.

    d. **구성 저장**을 클릭하십시오.

5. 올바른 URL을 설정했는지 확인하려면 다음을 수행하여 Azure AD 설정을 업데이트합니다.

    ![Single Sign-On 구성](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. SAML 창에서 **SP Identity ID**(SP ID)를 복사한 후 Azure Portal의 Atlassian Cloud **도메인 및 URL** 아래에서 **식별자** 상자에 붙여넣습니다.

    b. SAML 창에서 **SP Assertion Consumer Service URL**을 복사한 후 Azure Portal의 Atlassian Cloud **도메인 및 URL** 아래에서 **회신 URL** 상자에 붙여넣습니다. [로그인 URL]은 Atlassian Cloud의 테넌트 URL입니다.

    > [!NOTE]
    > 기존 고객인 경우 Azure Portal에서 **SP Identity ID**(SP ID) 및 **SP Assertion Consumer Service URL** 값을 업데이트한 후 **Yes, update configuration**(예, 구성 업데이트)을 선택합니다. 새 고객인 경우 이 단계를 건너뛸 수 있습니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Atlassian Cloud에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Atlassian Cloud**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Atlassian Cloud**를 입력하고 선택합니다.

    ![애플리케이션 목록의 Atlassian Cloud 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud 테스트 사용자 만들기

Azure AD 사용자가 Atlassian Cloud에 로그인하도록 하려면 Atlassian Cloud에서 다음을 수행하여 사용자 계정을 수동으로 프로비전합니다.

1. **관리** 창에서 **사용자**를 선택합니다.

    ![Atlassian Cloud 사용자 링크](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Atlassian Cloud에서 사용자를 만들려면 **사용자 초대**를 선택합니다.

    ![Atlassian Cloud 사용자 만들기](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. **메일 주소** 상자에 사용자의 메일 주소를 입력한 다음 애플리케이션 액세스 권한을 할당합니다.

    ![Atlassian Cloud 사용자 만들기](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. 사용자에게 메일 초대를 보내려면 **사용자 초대**를 선택합니다. 사용자에게 메일 초대가 전송되고 사용자가 초대를 수락하면 시스템에서 활성화됩니다.

> [!NOTE]
> **사용자** 섹션에서 **대량 만들기** 단추를 선택하여 사용자를 대량으로 만들 수도 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Atlassian Cloud 타일을 클릭하면 SSO를 설정한 Atlassian Cloud에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   
