---
title: '자습서: Facebook at Work와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Facebook at Work 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: asmalser

---
# <a name="tutorial:-azure-active-directory-integration-with-facebook-at-work"></a>자습서: Facebook at Work와 Azure Active Directory 통합
이 자습서에서는 Facebook at Work와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.

Facebook at Work를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다. 

* Facebook at Work에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. 
* Facebook at Work에 액세스가 부여된 사용자에 대한 계정을 자동으로 프로비전할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Facebook at Work에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치에서 계정을 관리할 수 있습니다. 

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
CS Stars와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* Single Sign-On을 사용하여 사용하도록 설정된 Facebook at Work

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다. 

## <a name="adding-facebook-at-work-from-the-gallery"></a>갤러리에서 Facebook at Work 추가
Facebook at Work의 Azure AD 통합을 구성하려면 갤러리의 Facebook at Work를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Facebook at Work를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에 **Facebook at Work**를 입력합니다.
7. 결과 창에서 **Facebook at Work**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure Active Directory의 계정으로 Facebook at Work에 인증할 수 있게 하는 방법을 간략하게 설명합니다.

**Facebook at Work에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 Facebook at Work를 추가한 후에 **Single Sign-On 구성**을 클릭합니다.
2. **앱 URL 구성** 화면에서 사용자가 Facebook at Work 응용 프로그램에 로그인하려는 URL을 입력합니다. Facebook at Work 테넌트 URL입니다(예: https://example.facebook.com/). 완료되면 **다음**을 클릭합니다.
3. 다른 웹 브라우저 창에서 Facebook at Work 회사 사이트에 관리자로 로그인합니다.
4. 다음 URL에서 Facebook at Work를 구성하는 지침을 수행하여 Azure AD를 ID 공급자로 사용합니다. [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. 완료되면 Azure 클래식 포털을 보여주는 브라우저 창으로 돌아가서 절차를 완료했는지 확인하는 확인란을 클릭하고 차례로 **다음** 및 **완료**를 클릭합니다.

## <a name="automatically-provisioning-users-to-facebook-at-work"></a>사용자를 Facebook at Work에 자동으로 프로비전
Azure AD는 Facebook at Work에 할당된 사용자의 계정 세부 정보를 자동으로 동기화하는 기능을 지원합니다. 이 자동 동기화를 사용하면 Facebook at Work가 처음으로 로그인을 시도하는 것보다 먼저 액세스에 대한 사용자 권한을 부여하는 데 필요한 데이터를 가져올 수 있습니다. 또한 Azure AD에서 액세스 권한이 취소된 경우 Facebook at Work에서 사용자의 프로비전을 취소합니다.

Azure 클래식 포털 창에서 **계정 프로비전 구성** 을 클릭하여 자동 프로비전을 설정할 수 있습니다.

자동 프로비전을 구성하는 방법에 대한 추가적인 세부 내용은 [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-facebook-at-work"></a>Facebook at Work에 사용자 할당
프로비전된 AAD 사용자가 액세스 패널에서 Facebook at Work를 확인할 수 있기 위해 Azure 클래식 포털 내에서 액세스를 할당받아야 합니다.

**Facebook at Work에 사용자를 할당하려면**

1. Azure 클래식 포털의 Facebook at Work에 대한 시작 페이지에서 **계정 할당**을 클릭합니다.
2. **표시** 메뉴에서 Facebook at Work에 사용자 또는 그룹을 할당할지를 선택하고 확인 표시 단추를 클릭합니다.
3. 결과 목록에서 Facebook at Work를 할당하려는 사용자 또는 그룹을 선택합니다.
4. 페이지 바닥글에서 **할당** 단추를 클릭합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png







<!--HONumber=Oct16_HO2-->


