---
title: "자습서: Picturepark와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 Picturepark를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e24bd626cd950f6e6d9474d1bd5f97c3ea4fb925
ms.openlocfilehash: 626bdc99702b38c127beba54979add9b1cbd633f


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>자습서: Picturepark와 Azure Active Directory 통합
이 자습서는 Azure 및 Picturepark의 통합을 보여 주기 위한 것입니다.  

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* Picturepark 테넌트

이 자습서를 완료한 후 Picturepark에 할당한 Azure AD 사용자가 Picturepark 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. Picturepark에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-picturepark-tutorial/IC795055.png "시나리오")

## <a name="enabling-the-application-integration-for-picturepark"></a>Picturepark에 응용 프로그램 통합 사용
이 섹션은 Picturepark에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

**Picturepark에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-picturepark-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-picturepark-tutorial/IC749321.png "응용 프로그램 추가")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-picturepark-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **Picturepark**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-picturepark-tutorial/IC795056.png "응용 프로그램 갤러리")
7. 결과 창에서 **Picturepark**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")


## <a name="configuring-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Picturepark에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  

Picturepark에 대한 Single Sign-On을 구성하려면 인증서의 지문(thumbprint) 값을 검색해야 합니다.  

이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

**Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **Picturepark** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Single Sign-On 구성")
2. **Picturepark에 대한 사용자 로그온 방법 선택** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지에서 **Picturepark 로그온 URL** 텍스트 상자에 다음 패턴 "*http://company.picturepark.com*"을 사용하여 URL을 입력하고 **다음**을 클릭합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-picturepark-tutorial/IC795060.png "앱 URL 구성")
4. **Picturepark의 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면, **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Single Sign-On 구성")
5. 다른 웹 브라우저 창에서 Picturepark 회사 사이트에 관리자로 로그인합니다.
6. 위쪽의 도구 모음에서 **관리 도구**를 클릭한 다음 **관리 콘솔**을 클릭합니다.
   
   ![관리 콘솔](./media/active-directory-saas-picturepark-tutorial/IC795062.png "관리 콘솔")
7. **인증**을 클릭한 다음 **ID 공급자**를 클릭합니다.
   
   ![인증](./media/active-directory-saas-picturepark-tutorial/IC795063.png "인증")
8. **ID 공급자 구성** 섹션에서 다음 단계를 수행합니다.
   
   ![ID 공급자 구성](./media/active-directory-saas-picturepark-tutorial/IC795064.png "ID 공급자 구성")
   
   1. **추가**를 클릭합니다.
   2. 구성 이름을 입력합니다.
   3. **기본값으로 설정**을 선택합니다.
   4. Azure 클래식 포털의 **Picturepark에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **SAML SSO URL** 값을 복사한 다음 **발급자 URI** 텍스트 상자에 붙여넣습니다.
   5. 내보낸 인증서에서 **지문** 값을 복사한 다음 **신뢰할 수 있는 발급자 지**문 텍스트 상자에 붙여넣습니다.  
      
      > [!TIP]
      > 자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)
      > 

9. **JoinDefaultUsersGroup**을 클릭합니다.
10. **클레임** 텍스트 상자에 **Emailaddress** 속성을 설정하려면 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**를 입력합니다.

      ![구성](./media/active-directory-saas-picturepark-tutorial/IC795065.png "구성") a. **Save**를 클릭합니다.
11. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Single Sign-On 구성")

## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성
Azure AD 사용자가 Picturepark에 로그인할 수 있도록 하려면 Picturepark로 프로비전되어야 합니다.  
Picturepark의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Picturepark** 테넌트에 로그인합니다.
2. 위쪽의 도구 모음에서 **관리 도구**를 클릭한 다음 **사용자**를 클릭합니다.
   
   ![사용자](./media/active-directory-saas-picturepark-tutorial/IC795067.png "사용자")
3. **사용자 개요** 탭에서 **새로 만들기**를 클릭합니다.
   
   ![사용자 관리](./media/active-directory-saas-picturepark-tutorial/IC795068.png "사용자 관리")
4. **사용자 만들기** 대화 상자에서 다음 단계를 수행합니다.
   
   ![사용자 만들기](./media/active-directory-saas-picturepark-tutorial/IC795069.png "사용자 만들기")
   
  1. 프로비전할 유효한 Azure Active Directory 사용자의 **이메일 주소**, **암호**, **암호 확인**, **이름**, **성**, **회사**, **국가**, **ZIP**, **도시**를 관련된 텍스트 상자에 입력합니다.
  2. **언어**를 선택합니다.
  3. **만들기**를 클릭합니다.

> [!NOTE]
> 다른 Picturepark 사용자 계정 생성 도구 또는 Picturepark가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

**Picturepark에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **Picturepark **응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-picturepark-tutorial/IC795070.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-picturepark-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.




<!--HONumber=Feb17_HO1-->


