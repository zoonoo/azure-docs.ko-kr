---
title: "자습서: ITRP와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 ITRP을 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 3d1f24cb23a58b4478a30c5e4a0858b474d5d90e
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>자습서: ITRP와 Azure Active Directory 통합
이 자습서는 Azure 및 ITRP의 통합을 보여주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* ITRP 테넌트

이 자습서를 완료하면 ITRP에 할당한 Azure AD 사용자가 ITRP 회사 사이트(서비스 공급자가 제공한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On으로 로그인할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. ITRP에 응용 프로그램 통합 사용
2. SSO(Single Sign-On) 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-itrp-tutorial/IC775551.png "시나리오")

## <a name="enable-the-application-integration-for-itrp"></a>ITRP에 응용 프로그램 통합 사용
이 섹션에서는 ITRP에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

**ITRP에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램](./media/active-directory-saas-itrp-tutorial/IC700994.png "응용 프로그램")

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램 추가](./media/active-directory-saas-itrp-tutorial/IC749321.png "응용 프로그램 추가")

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-itrp-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6. **검색 상자**에 **ITRP**를 입력합니다.
   
    ![응용 프로그램 갤러리](./media/active-directory-saas-itrp-tutorial/IC775565.png "응용 프로그램 갤러리")

7. 결과 창에서 **ITRP**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
   
## <a name="configure-single-sign-on"></a>Single Sign-On 구성

이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ITRP에 인증할 수 있게 하는 방법을 간략하게 설명합니다.  

ITRP에 대한 Single Sign-on을 구성하려면 인증서의 손도장(thumbprint) 값을 검색해야 합니다.  

이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

**Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **ITRP** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-itrp-tutorial/IC771709.png "Single Sign-On 구성")

2. **ITRP에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-itrp-tutorial/IC775567.png "Single Sign-On 구성")

3. **앱 URL 구성** 페이지의 **ITRP 로그인 URL** 텍스트 상자에 "*https://\<tenant-name\>.ITRP.com*" 패턴을 사용하여 URL을 입력하고 **다음**을 클릭합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-itrp-tutorial/IC775568.png "앱 URL 구성")

4. **ITRP에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 **c:\\ITRP.cer**에 로컬로 인증서 파일을 저장합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-itrp-tutorial/IC775569.png "Single Sign-On 구성")

5. 다른 웹 브라우저 창에서 ITRP 회사 사이트에 관리자로 로그인합니다.

6. 위쪽에 도구 모음에서 **설정**을 클릭합니다.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7. 왼쪽 탐색 창에서 **Single Sign-On**을 선택합니다.
   
    ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8. Single Sign-On 구성 섹션에서 다음 단계를 수행합니다.
   
    ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")
    
    ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")   
  1. **사용**을 클릭합니다.
  2. Azure 클래식 포털의 **ITRP에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **원격 로그아웃 URL** 텍스트 상자에 붙여 넣습니다.
  3. Azure 클래식 포털의 **ITRP에서 Single Sign-On 구성** 대화 상자 페이지에서 **SAML SSO URL** 값을 복사한 다음 **SAML SSO URL** 텍스트 상자에 붙여 넣습니다.
  4. 내보낸 인증서에서 **지문** 값을 복사한 다음 **인증서 지문** 텍스트 상자에 붙여넣습니다.
      
     >[!TIP]
     >자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하세요.
     >
    
  5. **Save**를 클릭합니다.

9. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-itrp-tutorial/IC775574.png "Single Sign-On 구성")
   
## <a name="configure-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 ITRP에 로그인할 수 있도록 하려면 ITRP로 프로비저닝되어야 합니다.  

ITRP의 경우 프로비저닝 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **ITRP** 테넌트에 로그인합니다.

2. 위쪽의 도구 모음에서 **레코드**를 클릭합니다.
   
    ![관리자](./media/active-directory-saas-itrp-tutorial/IC775575.png "관리자")

3. 팝업 메뉴에서 **사람**을 선택합니다.
   
    ![사람](./media/active-directory-saas-itrp-tutorial/IC775587.png "사람")

4. **새 사람 추가** (“+”)를 클릭합니다.
   
    ![관리자](./media/active-directory-saas-itrp-tutorial/IC775576.png "관리자")

5. 새 사람 추가 대화 상자에서 다음 단계를 수행합니다.
   
    ![사용자](./media/active-directory-saas-itrp-tutorial/IC775577.png "사용자")   
  1. 프로비전할 유효한 AAD 계정의 **이름**과 **전자 메일**을 입력합니다.
  2. **Save**를 클릭합니다.

>[!NOTE]
>다른 ITRP 사용자 계정 생성 도구 또는 ITRP가 제공한 API를 사용하여 AAD 사용자 계정을 프로비저닝할 수 있습니다. 
> 

## <a name="assign-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

**ITRP에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure AD 포털에서 테스트 계정을 만듭니다.

2. **ITRP** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-itrp-tutorial/IC775588.png "사용자 할당")

3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
    ![예](./media/active-directory-saas-itrp-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.


