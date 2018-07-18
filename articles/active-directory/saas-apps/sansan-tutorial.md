---
title: '자습서: Sansan과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Sansan 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: c8b5a84c853a974ede77e716b77f0a5007775ef7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231215"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>자습서: Sansan과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Sansan을 통합하는 방법에 대해 알아봅니다.

Sansan을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Sansan에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Sansan에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Sansan과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Sansan Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Sansan 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-sansan-from-the-gallery"></a>갤러리에서 Sansan 추가
Sansan이 Azure AD에 통합되도록 구성하려면 갤러리의 Sansan을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Sansan을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Sansan**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/sansan-tutorial/tutorial_sansan_search.png)

5. 결과 패널에서 **Sansan**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 Sansan에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Sansan 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Sansan의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Sansan에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Sansan에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Sansan 테스트 사용자 만들기](#creating-a-sansan-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Sansan에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Sansan 응용 프로그램에서 Single Sign-On을 구성합니다.

**Sansan에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Sansan** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. **Sansan 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_url.png)

    **로그온 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다. 
    
    | Environment | URL |
    |:--- |:--- |
    | PC 웹 |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | 네이티브 모바일 앱 |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | 모바일 브라우저 설정 |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Sansan 클라이언트 지원 팀](https://www.sansan.com/form/contact)에 문의하세요. 
     
4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_general_400.png)

6. Sansan 응용 프로그램에서는 PowerShell 스크립트를 사용하여 구성할 수 있는 여러 환경(PC 웹, 네이티브 모바일 앱, 모바일 브라우저 설정)을 지원하기 위해 여러 **식별자** 및 **회신 URL**이 필요합니다. 자세한 단계는 아래에 설명되어 있습니다.

7. PowerShell 스크립트를 사용하여 Sansan 응용 프로그램에 대해 여러 **식별자** 및 **회신 URL**을 구성하려면 다음 단계를 수행합니다.

    ![Single Sign On 개체 구성](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. **Sansan** 응용 프로그램의 **속성** 페이지로 이동하고, **복사** 단추를 사용하여 **개체 ID**를 복사한 다음, 메모장에 붙여넣습니다.

    나. Azure Portal에서 복사한 **개체 ID**는 이 자습서의 뒷부분에서 사용할 PowerShell 스크립트에서 **ServicePrincipalObjectId**로 사용됩니다. 

    다. 이제 승격된 Windows PowerShell 명령 프롬프트를 엽니다.
    
    >[!NOTE] 
    > Azure AD 모듈을 설치해야 합니다(`Install-Module -Name AzureAD` 명령 사용). NuGet 모듈 또는 새로운 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 Enter 키를 누릅니다.

    d. `Connect-AzureAD`를 실행하고 전역 관리자 사용자 계정으로 로그인합니다.

    e. 다음 스크립트를 사용하여 응용 프로그램에서 여러 URL을 업데이트합니다.

    ```poweshell
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

8. PowerShell 스크립트가 완료되면 스크립트 결과가 아래와 같이 표시되며 URL 값이 업데이트되지만 Azure Portal에는 반영되지 않습니다. 

    ![Single Sign On 스크립트 구성](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. **Sansan 구성** 섹션에서 **Sansan 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. **Sansan** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **인증서**, **로그아웃 URL**, **SAML 엔터티 ID** 및 **SAML Single Sign-On 서비스 URL**을 [Sansan 지원 팀](https://www.sansan.com/form/contact)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

>[!NOTE]
>PC 브라우저 설정은 또한 웹 PC와 함께 모바일 앱과 모바일 브라우저에 대해서도 작동합니다. 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/sansan-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/sansan-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/sansan-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/sansan-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-sansan-test-user"></a>Sansan 테스트 사용자 만들기

이 섹션에서는 Sansan에서 Britta Simon이라는 사용자를 만듭니다. Sansan 응용 프로그램에서 SSO를 수행하기 전에 해당 사용자를 응용 프로그램에 프로비전해야 합니다. 

>[!NOTE]
>한 사용자 또는 여러 사용자를 수동으로 만들려면 [Sansan 지원 팀](https://www.sansan.com/form/contact)에 문의해야 합니다. 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Sansan에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Sansan에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Sansan**을 선택합니다.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Sansan 타일을 클릭하면 Sansan 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

