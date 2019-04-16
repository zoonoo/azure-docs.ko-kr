---
title: '자습서: Sansan과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Sansan 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 59cfdfaae597feb8f1cab212b407a8879d78f9f9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265579"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>자습서: Sansan과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Sansan을 통합하는 방법에 대해 알아봅니다.
Sansan을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서 Sansan에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Sansan에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Sansan과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Sansan Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Sansan에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-sansan-from-the-gallery"></a>갤러리에서 Sansan 추가

Sansan이 Azure AD에 통합되도록 구성하려면 갤러리의 Sansan을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Sansan을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Sansan**을 입력하고, 결과 패널에서 **Sansan**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Sansan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Sansan에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Sansan의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Sansan에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Sansan Single Sign-On 구성](#configure-sansan-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Sansan 테스트 사용자 만들기](#create-sansan-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Sansan에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Sansan에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Sansan** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Sansan 도메인 및 URL Single Sign-On 정보](common/sp-signonurl.png)

    **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.
    | Environment | URL |
    |:--- |:--- |
    | PC 웹 |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | 네이티브 모바일 앱 |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | 모바일 브라우저 설정 |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 해당 값을 얻으려면 [Sansan 클라이언트 지원 팀](https://www.sansan.com/form/contact)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. Sansan 애플리케이션에서는 PowerShell 스크립트를 사용하여 구성할 수 있는 여러 환경(PC 웹, 네이티브 모바일 앱, 모바일 브라우저 설정)을 지원하기 위해 여러 **식별자** 및 **회신 URL**이 필요합니다. 자세한 단계는 아래에 설명되어 있습니다.

7. PowerShell 스크립트를 사용하여 Sansan 애플리케이션에 대해 여러 **식별자** 및 **회신 URL**을 구성하려면 다음 단계를 수행합니다.

    ![Single Sign On 개체 구성](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. **Sansan** 애플리케이션의 **속성** 페이지로 이동하고, **복사** 단추를 사용하여 **개체 ID**를 복사한 다음, 메모장에 붙여넣습니다.

    b. Azure Portal에서 복사한 **개체 ID**는 이 자습서의 뒷부분에서 사용할 PowerShell 스크립트에서 **ServicePrincipalObjectId**로 사용됩니다.

    다. 이제 승격된 Windows PowerShell 명령 프롬프트를 엽니다.

    >[!NOTE]
    > Azure AD 모듈을 설치해야 합니다(`Install-Module -Name AzureAD` 명령 사용). NuGet 모듈 또는 새로운 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 Enter 키를 누릅니다.

    d. `Connect-AzureAD`를 실행하고 전역 관리자 사용자 계정으로 로그인합니다.

    e. 다음 스크립트를 사용하여 애플리케이션에서 여러 URL을 업데이트합니다.

    ```powershell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. PowerShell 스크립트가 성공적으로 완료되면 스크립트 결과가 아래와 같이 표시되며 URL 값이 업데이트되지만 Azure Portal에는 반영되지 않습니다.

    ![Single Sign On 스크립트 구성](./media/sansan-tutorial/tutorial_sansan_powershell.png)

9. **Sansan 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-sansan-single-sign-on"></a>Sansan Single Sign-On 구성

**Sansan** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **인증서(Base64)** 와 적절히 복사한 URL을 [Sansan 클라이언트 지원 팀](https://www.sansan.com/form/contact)으로 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

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

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Sansan에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Sansan**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Sansan**을 선택합니다.

    ![애플리케이션 목록의 Sansan 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-sansan-test-user"></a>Sansan 테스트 사용자 만들기

이 섹션에서는 Sansan에서 Britta Simon이라는 사용자를 만듭니다. Sansan 애플리케이션에서 SSO를 수행하기 전에 해당 사용자를 애플리케이션에 프로비전해야 합니다.

> [!NOTE]
> 한 사용자 또는 여러 사용자를 수동으로 만들려면 [Sansan 지원 팀](https://www.sansan.com/form/contact)에 문의해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Sansan 타일을 클릭하면 SSO를 설정한 Sansan에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)