---
title: "자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory와 Amazon Web Services(AWS) 간에 Single Sign-On을 구성하는 방법을 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 61999ebea05371c53e5ff27644a68039db1aef96
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합

이 자습서에서는 Amazon Web Services(AWS)를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

AWS(Amazon Web Services)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 AWS(Amazon Web Services)에 자동으로 로그인(Single Sign-On)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>필수 조건

AWS(Amazon Web Services)와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- AWS(Amazon Web Services) Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 AWS(Amazon Web Services) 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>갤러리에서 AWS(Amazon Web Services) 추가
Azure AD에 AWS(Amazon Web Services)를 통합하도록 구성하려면 갤러리의 AWS(Amazon Web Services)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 AWS(Amazon Web Services)를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에서 **AWS(Amazon Web Services)**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. 결과 창에서 **AWS(Amazon Web Services)**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 AWS(Amazon Web Services) 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 AWS(Amazon Web Services)의 **Username** 값으로 할당하여 설정합니다.

AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[AWS(Amazon Web Services) 테스트 사용자 만들기](#creating-an-amazon-web-services-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 AWS(Amazon Web Services)에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 AWS(Amazon Web Services) 응용 프로그램에서 Single Sign-On을 구성합니다.

**AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **AWS(Amazon Web Services)** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single Sign-On** 대화 상자에서 **모드**로 **SAML 기반 로그인**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. **AWS(Amazon Web Services) 도메인 및 URL** 섹션에서 앱이 Azure와 이미 사전 통합되었으므로 사용자는 아무 단계도 수행할 필요가 없습니다.

    ![Single Sign-on 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. AWS(Amazon Web Services) 소프트웨어 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대한 다음 클레임을 구성하세요. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

    ![Single Sign-on 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름  | 특성 값 | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 역할               | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >AWS 콘솔에서 모든 역할을 가져오려면 Azure AD에서 사용자 프로비전을 구성해야 합니다. 프로비전 단계는 아래에서 참조하세요.

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다. 네임스페이스 값을 위에 지정된 대로 추가합니다.
    
    d. **확인**을 클릭합니다.

7. **저장** 단추를 클릭하여 Azure에 설정을 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 다른 브라우저 창에서 AWS(Amazon Web Services) 회사 사이트에 관리자로 로그인합니다.

9. **콘솔 홈**을 클릭합니다.
   
    ![Single Sign-On 구성][11]

10. **Security, Identity & Compliance(보안, ID 및 규정 준수)** 서비스에서 **IAM**을 클릭합니다.
   
    ![Single Sign-on 구성][12]

11. **ID 공급자**를 클릭한 다음 **공급자 만들기**를 클릭합니다.
   
    ![Single Sign-On 구성][13]

12. **공급자 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성][14]
 
      a. **공급자 유형**으로 **SAML**을 선택합니다.

      b. **공급자 이름** 텍스트 상자에 공급자 이름(예: *WAAD*)을 입력합니다.

      c. 다운로드한 메타데이터 파일을 업로드하려면 **파일 선택**을 클릭합니다.

      d. **다음 단계**를 클릭합니다.

13. **공급자 정보 확인** 대화 상자 페이지에서 **만들기**를 클릭합니다. 
    
    ![Single Sign-On 구성][15]

14. **역할**을 클릭하고 **새 역할 만들기**를 클릭합니다. 
    
    ![Single Sign-on 구성][16]

15. **역할 이름 설정** 대화 상자에서 다음 단계를 수행합니다. 
    
    ![Single Sign-On 구성][17] 

      a. **역할 이름** 텍스트 상자에 역할 이름(예: *TestUser*)을 입력합니다. 

      b. **다음 단계**를 클릭합니다.

16. **역할 유형 선택** 대화 상자에서 다음 단계를 수행합니다. 
    
    ![Single Sign-On 구성][18] 

      a. **ID 공급자 액세스에 대한 역할**을 선택합니다. 

      b. **SAML 공급자에게 WebSSO(웹 Single Sign-On) 액세스 권한 부여** 섹션에서 **선택**을 클릭합니다.

17. **트러스트 설정** 대화 상자에서 다음 단계를 수행합니다.  
    
    ![Single Sign-On 구성][19] 

      a. 이전에 만든 SAML 공급자(예: *WAAD*)를 SAML 공급자로 선택합니다.
  
      b. **다음 단계**를 클릭합니다.

18. **역할 트러스트 확인** 대화 상자에서 **다음 단계**를 클릭합니다.
    
    ![Single Sign-on 구성][32]

19. **정책 연결** 대화 상자에서 **다음 단계**를 클릭합니다.
    
    ![Single Sign-on 구성][33]

20. **검토** 대화 상자에서 다음 단계를 수행합니다.
    
    ![Single Sign-On 구성][34]
 
      a. **역할 만들기**를 클릭합니다.

    b. 필요한 만큼 역할을 만들어서 ID 공급자에 매핑합니다.

21. 이제 AWS에서 모든 역할을 가져오도록 사용자 프로비전을 구성합니다.

    a. AWS 콘솔에서 루트 계정으로 로그인합니다.

    b. 오른쪽 위 모서리에서 이름을 클릭한 다음 **My Security Credentials(내 보안 자격 증명)** 옵션을 클릭합니다. 그러면 화면이 경고 메시지로 열립니다. **Security Credentials(보안 자격 증명)** 단추를 클릭하여 화면을 이동합니다.
        
       ![Single Sign-On 구성][36]

       ![Single Sign-On 구성][37]

    c. 액세스 키 섹션에서 **Create New Access Key(새 액세스 키 만들기)** 단추를 클릭합니다. 그러면 액세스 키 ID와 토큰 값이 생성됩니다.
    
       ![Single Sign-on 구성][38]

    d. 이 값을 읽어버리지 않도록 모두 복사하고 다운로드합니다.

    e. Azure Portal의 AWS(Amazon Web Services) 응용 프로그램 통합 페이지에서 **프로비전**을 클릭합니다.
        
       ![Single Sign-on 구성][35]

    f. 프로비전 모드를 **자동**으로 설정합니다.
        
       ![Single Sign-on 구성][39]

    g. 이제 **clientsecret** 및 **비밀 토큰**에 AWS 콘솔에서 복사한 해당 값을 붙여 넣습니다.
    
    h. **연결 테스트** 단추를 클릭하여 연결을 테스트할 수 있습니다. 성공적으로 수행되면 프로비전 커넥터를 시작할 수 있습니다.
       
       ![Single Sign-on 구성][40]

    i. 이제 프로비전 상태를 **설정**으로 선택합니다. 그러면 응용 프로그램에서 역할을 가져오기 시작합니다.

       ![Single Sign-on 구성][41]

    > [!NOTE]
    > Azure AD 프로비전 서비스는 일정 시간 후마다 실행되어 AWS의 역할을 동기화합니다. 모든 ID 공급자가 AWS 역할을 Azure AD에 연결한 것을 볼 수 있으며 사용자 또는 그룹에 응용 프로그램을 할당하는 동안 이것을 볼 수 있습니다.

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-an-amazon-web-services-test-user"></a>AWS(Amazon Web Services) 테스트 사용자 만들기

Azure AD 사용자가 AWS(Amazon Web Services)에 로그인할 수 있도록 하려면 사용자가 AWS(Amazon Web Services)에 프로비전되어야 합니다. AWS(Amazon Web Services)의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **AWS(Amazon Web Services)** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **콘솔 홈** 아이콘을 클릭합니다. 
   
    ![Single Sign-On 구성][11]

3. ID 및 액세스 관리를 클릭합니다. 
   
    ![Single Sign-on 구성][28]

4. 대시보드에서 **Users(사용자)**를 클릭한 다음 **Create New Users(새 사용자 만들기)**를 클릭합니다. 
   
    ![Single Sign-On 구성][29]

5. 사용자 만들기 대화 상자에서 다음 단계를 수행합니다. 
   
    ![Single Sign-On 구성][30]   
    
    a. **사용자 이름 입력** 텍스트 상자에 Azure AD의 Brita Simon 사용자 이름(userprincipalname)을 입력합니다.

    b. **만들기**를 클릭합니다.
        
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 AWS(Amazon Web Services)에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 AWS(Amazon Web Services)에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **AWS(Amazon Web Services)**를 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **역할 선택** 탭에서 사용자에게 적합한 역할을 선택합니다. 모든 역할은 역할 이름 및 ID 공급자 이름으로 표시됩니다. 이러한 방식으로 AWS에서 역할을 손쉽게 식별할 수 있습니다.

8. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

[액세스 패널]에서 [AWS(Amazon Web Services)] 타일을 클릭하면 AWS(Amazon Web Services) 응용 프로그램에 자동으로 로그온됩니다. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
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

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

