---
title: "자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory에서 AWS(Amazon Web Services)를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8ea8de916a36a1b5274f42513b0446f99707c179
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합
이 자습서에서는 AWS(Amazon Web Services)와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.  

AWS(Amazon Web Services)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다. 

* Azure AD에서는 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다. 
* 사용자가 Azure AD 계정으로 AWS(Amazon Web Services) SSO(Single Sign-On)에 자동으로 로그인될 수 있도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
AWS(Amazon Web Services)와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* AWS(Amazon Web Services) SSO 사용이 설정된 구독

>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다. 

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD SSO를 테스트하는 데 도움을 주기 위해 제공되었습니다.  

이 자습서에 설명된 시나리오는 다음 세 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 AWS(Amazon Web Services) 추가 
2. Azure AD SSO 구성 및 테스트

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>갤러리에서 AWS(Amazon Web Services) 추가
Azure AD에 AWS(Amazon Web Services)를 통합하도록 구성하려면 갤러리의 AWS(Amazon Web Services)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 AWS(Amazon Web Services)를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1] 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다. 
   
    ![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다. 
   
    ![응용 프로그램][3]

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다. 
   
    ![응용 프로그램][4]

6. 검색 상자에서 **AWS(Amazon Web Services)**를 입력합니다.
   
    ![응용 프로그램][5]

7. 결과 창에서 **AWS(Amazon Web Services)**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![응용 프로그램][6]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 AWS(Amazon Web Services)에서 Azure AD SSO를 구성하고 테스트하는 방법을 보여 줍니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 AWS(Amazon Web Services) 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.  

이 연결 관계는 Azure AD의 **사용자 이름** 값을 AWS(Amazon Web Services)의 **Username** 값으로 할당하여 설정합니다.

AWS(Amazon Web Services)에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[AWS(Amazon Web Services) 테스트 사용자 만들기](#creating-a-halogen-software-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 AWS(Amazon Web Services)에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션에서는 Azure 클래식 포털에서 Azure AD SSO를 사용하도록 설정하고 AWS(Amazon Web Services) 응용 프로그램에서 Single Sign-On을 구성합니다.  

AWS(Amazon Web Services) 응용 프로그램에는 특정 형식의 SAML 어설션이 필요하므로 사용자 지정 특성 매핑을 **SAML 토큰 특성** 구성에 추가해야 합니다. 

다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![Single Sign-on 구성][27]

**AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **AWS(Amazon Web Services)** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성][7]

2. **사용자가 AWS(Amazon Web Services)에 로그인하는 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-on 구성][8]

3. **앱 설정 구성** 대화 상자 페이지에서 **다음**을 클릭합니다. 
   
    ![앱 설정 구성][9]

4. **AWS(Amazon Web Services)에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 컴퓨터에 로컬로 저장합니다.
   
    ![Single Sign-On 구성][10]

5. 다른 브라우저 창에서 AWS(Amazon Web Services) 회사 사이트에 관리자로 로그인합니다.

6. **콘솔 홈**을 클릭합니다.
   
    ![Single Sign-On 구성][11]

7. **ID 및 액세스 관리**를 클릭합니다. 
   
    ![Single Sign-on 구성][12]

8. **ID 공급자**를 클릭한 다음 **공급자 만들기**를 클릭합니다. 
   
    ![Single Sign-On 구성][13]

9. **공급자 구성** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Single Sign-On 구성][14]   
  1. **공급자 유형**으로 **SAML**을 선택합니다.
  2. **공급자 이름** 텍스트 상자에 공급자 이름(예: *WAAD*)을 입력합니다.
  3. 다운로드한 메타데이터 파일을 업로드하려면 **파일 선택**을 클릭합니다.
  4. **다음 단계**를 클릭합니다.

10. **공급자 정보 확인** 대화 상자 페이지에서 **만들기**를 클릭합니다. 
    
    ![Single Sign-On 구성][15]

11. **역할**을 클릭하고 **새 역할 만들기**를 클릭합니다. 
    
    ![Single Sign-on 구성][16]

12. **역할 이름 설정** 대화 상자에서 다음 단계를 수행합니다. 
    
    ![Single Sign-on 구성][17] 
  1. **역할 이름** 텍스트 상자에 역할 이름(예: *TestUser*)을 입력합니다. 
  2. **다음 단계**를 클릭합니다.

13. **역할 유형 선택** 대화 상자에서 다음 단계를 수행합니다. 
    
    ![Single Sign-on 구성][18] 
  1. **ID 공급자 액세스에 대한 역할**을 선택합니다. 
  2. **SAML 공급자에게 WebSSO(웹 Single Sign-On) 액세스 권한 부여** 섹션에서 **선택**을 클릭합니다.

14. **트러스트 설정** 대화 상자에서 다음 단계를 수행합니다.  
    
    ![Single Sign-on 구성][19] 
  1. 이전에 만든 SAML 공급자(예: *WAAD*)를 SAML 공급자로 선택합니다.   
  2. **다음 단계**를 클릭합니다.

15. **역할 트러스트 확인** 대화 상자에서 **다음 단계**를 클릭합니다. 
    
    ![Single Sign-on 구성][32]

16. **정책 연결** 대화 상자에서 **다음 단계**를 클릭합니다.  
    
    ![Single Sign-on 구성][33]

17. **검토** 대화 상자에서 다음 단계를 수행합니다.   
    
    ![Single Sign-on 구성][34] 
  1. **역할 ARN** 값을 복사합니다.  
  2. **신뢰할 수 있는 엔터티** ARN 값을 복사합니다. 
  3. **역할 만들기**를 클릭합니다. 

18. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
    
    ![Azure AD Connect의 정의][20]

19. **Single Sign-On 확인** 페이지에서 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
    
    ![Azure AD Connect의 정의][22]

20. 위쪽 메뉴에서 **특성** to open the **SAML Token 특성** 대화 상자를 엽니다. 
    
    ![Single Sign-on 구성][21]

21. **사용자 특성 추가**를 클릭합니다. 
    
    ![Single Sign-On 구성][23]

22. 사용자 특성 추가 대화 상자에서 다음 단계를 수행합니다. 
    
    ![Single Sign-On 구성][24]  
  1. **특성 이름** 텍스트 상자에서 **https://aws.amazon.com/SAML/Attributes/Role**을 입력합니다.  
  2. **특성 값** 텍스트 상자에서 **[역할 ARN 값],[신뢰할 수 있는 엔터티 ARN 값]**을 입력합니다.
    
     >[!TIP]
     >이들은 역할을 만들었을 때 검토 대화 상자에서 복사한 값입니다. 
     > 
     
  3. **완료**를 클릭하여 **사용자 특성 추가** 대화 상자를 닫습니다.

23. **사용자 특성 추가**를 클릭합니다. 
    
    ![Single Sign-on 구성][23]

24. 사용자 특성 추가 대화 상자에서 다음 단계를 수행한 다음 **변경 사항 적용**을 클릭합니다. 
    
    ![Single Sign-on 구성][25]
 1. **특성 이름** 텍스트 상자에서 **https://aws.amazon.com/SAML/Attributes/RoleSessionName**을 입력합니다.
 2. **특성 값** 텍스트 상자의 드롭다운 목록에서 **user.userprincipalname**을 입력하거나 선택합니다.

     ![Single Sign-On 구성][35]
 3. **완료**를 클릭하여 **사용자 특성 추가** 대화 상자를 닫습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png) 
 1. 사용자 유형에서 조직의 새 사용자를 선택합니다.
 2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
 3. 다음을 클릭합니다.

6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png)  
 1. **이름** 텍스트 상자에 **Britta**를 입력합니다.   
 2. **성** 텍스트 상자에 **Simon**을 입력합니다. 
 3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다. 
 4. **역할** 목록에서 **사용자**를 선택합니다. 
 5. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png) 

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png)  
 1. **새 암호**값을 적어둡니다.  
 2. **완료**를 클릭합니다.   

### <a name="create-a-amazon-web-services-aws-test-user"></a>AWS(Amazon Web Services) 테스트 사용자 만들기
이 섹션에서는 AWS(Amazon Web Services)에서 Britta Simon이라는 사용자를 만듭니다.

**AWS(Amazon Web Services)에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. **AWS(Amazon Web Services)** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **콘솔 홈** 아이콘을 클릭합니다. 
   
    ![Single Sign-On 구성][11]

3. ID 및 액세스 관리를 클릭합니다. 
   
    ![Single Sign-On 구성][28]

4. 대시보드에서 사용자를 클릭한 다음 새 사용자 만들기를 클릭합니다. 
   
    ![Single Sign-On 구성][29]

5. 사용자 만들기 대화 상자에서 다음 단계를 수행합니다. 
   
    ![Single Sign-On 구성][30]   
 1. **사용자 이름 입력** 텍스트 상자에 Azure AD의 Brita Simon 사용자 이름(userprincipalname)을 입력합니다. 
 2. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Azure SSO를 사용할 수 있도록 Britta Simon에게 AWS(Amazon Web Services)에 대한 액세스 권한을 부여합니다.

![사용자 할당][31]

**Britta Simon을 AWS(Amazon Web Services)에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][26]

2. 응용 프로그램 목록에서 **AWS(Amazon Web Services)**를 선택합니다.
   
    ![사용자 할당][27]

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][25]

4. 사용자 목록에서 **Britta Simon**을 선택합니다.

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][29]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트하기 위한 것입니다.  

[액세스 패널]에서 [AWS(Amazon Web Services)] 타일을 클릭하면 AWS(Amazon Web Services) 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service-tutorial/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service-tutorial/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/user_attributes_01.png























