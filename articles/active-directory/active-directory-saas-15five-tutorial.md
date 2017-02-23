---
title: "자습서: 15Five와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory에서 15Five를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: ce98338e6b21eb35a17f0183f409dd54d1123bb9


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>자습서: 15Five와 Azure Active Directory 통합
이 자습서는 Azure 및 15Five의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* 15Five Single Sign-on이 설정된 구독

이 자습서를 완료한 후 15Five에 할당한 Azure AD 사용자는 15Five 회사 사이트(로그인을 시작한 서비스 공급자)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. 15Five에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-15five-tutorial/IC784667.png "시나리오")

## <a name="enabling-the-application-integration-for-15five"></a>15Five에 응용 프로그램 통합 사용
이 섹션은 15Five에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>15Five에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-15five-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-15five-tutorial/IC749321.png "응용 프로그램 추가")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-15five-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에서 **15Five**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-15five-tutorial/IC784668.png "응용 프로그램 갤러리")
7. 결과 창에서 **15Five**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
## <a name="configuring-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 15Five에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **15Five** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-15five-tutorial/IC784670.png "Single Sign-On 구성")
2. **사용자가 15Five에 로그인하는 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-15five-tutorial/IC784671.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지의 **15Five 로그인 URL** 텍스트 상자에서 "*https://company.15Five.com*" 패턴을 사용하여 URL을 입력하고 **다음**을 클릭합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-15five-tutorial/IC784672.png "앱 URL 구성")
4. **15Five에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 15Five 지원 팀에 전달합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-15five-tutorial/IC784673.png "Single Sign-On 구성")
   
   > [!NOTE]
   > Single Sign-on은 15Five 지원팀에서 사용할 수 있어야 합니다.
   > 
   > 
5. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-15five-tutorial/IC784674.png "Single Sign-On 구성")
   

## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 15Five에 로그인할 수 있도록 하려면 15Five로 프로비전되어야 합니다.  
15Five의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면
1. **15Five** 회사 사이트에 관리자 권한으로 로그인합니다.
2. **회사 관리**로 이동합니다.
   
   ![회사 관리](./media/active-directory-saas-15five-tutorial/IC784675.png "회사 관리")
3. **사람\>사람 추가**로 이동합니다.
   
   ![사람](./media/active-directory-saas-15five-tutorial/IC784676.png "사람")
4. 새 사람 추가 섹션에서 다음 단계를 수행합니다.
   
   ![새 사람 추가](./media/active-directory-saas-15five-tutorial/IC784677.png "새 사람 추가")
   
   1. 관련 텍스트 상자에 프로비전할 유효한 Azure Active Directory 계정의 **이름**, **성**, **제목**, **전자 메일 주소**를 입력합니다.
   2. **Done**을 클릭합니다.
   
   > [!NOTE]
   > Azure AD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다.
   > 
   > 



## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### <a name="to-assign-users-to-15five-perform-the-following-steps"></a>15Five에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **15Five** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-15five-tutorial/IC784678.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-15five-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.




<!--HONumber=Dec16_HO5-->


