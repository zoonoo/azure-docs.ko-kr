<properties pageTitle="자습서: IdeaScale과 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 IdeaScale을 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: IdeaScale과 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=529830)를 클릭하십시오.
  
이 자습서는 Azure와 IdeaScale의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   IdeaScale Single Sign-On이 가능하도록 설정된 구독
  
이 자습서를 완료하면 IdeaScale에 할당한 Azure AD 사용자가 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On으로 로그인할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  IdeaScale에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-ideascale-tutorial/IC790838.png "시나리오")
##IdeaScale에 응용 프로그램 통합 사용
  
이 섹션에서는 IdeaScale에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

###IdeaScale에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-ideascale-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-ideascale-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하십시오.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-ideascale-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **IdeaScale**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-ideascale-tutorial/IC790841.png "응용 프로그램 갤러리")

7.  결과 창에서 **IdeaScale**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##Single Sign-On 구성
  
이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 IdeaScale에 인증할 수 있게 하는 방법을 간략하게 설명합니다. IdeaScale에 대한 Single Sign-On을 구성하려면 인증서의 손도장(thumbprint) 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 손도장 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하세요.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **IdeaScale** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Single Sign-On 구성")

2.  **IdeaScale에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **IdeaScale Sign On URL** 텍스트 상자에 IdeaScale 응용 프로그램에 로그온하기 위해 사용자가 사용하는 URL(예: "**https://company.IdeaScale.com*"))을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-ideascale-tutorial/IC790845.png "앱 URL 구성")

4.  **IdeaScale에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 컴퓨터에 로컬로 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 IdeaScale 회사 사이트에 관리자로 로그인합니다.

6.  **커뮤니티 설정**으로 이동합니다.

    ![커뮤니티 설정](./media/active-directory-saas-ideascale-tutorial/IC790847.png "커뮤니티 설정")

7.  **보안 > Single Sign On 설정**으로 이동합니다.

    ![Single SignOn 설정](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single SignOn 설정")

8.  **Single Sign On 유형**으로 **SAML 2.0**을 선택합니다.

    ![Single SignOn 유형](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single SignOn 유형")

9.  **Single Sign On 설정** 대화 상자에서 다음 단계를 수행합니다.

    ![Single SignOn 설정](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single SignOn 설정")

    1.  Azure 포털의 **IdeaScale에서 Single Sign-on 구성** 대화 상자 페이지에서 **엔터티 ID** 값을 복사한 다음 **SAML IdP 엔터티 ID** 텍스트 상자에 붙여넣습니다.
    2.  다운로드한 메타데이터 파일의 내용을 복사한 다음 **SAML IdP 메타데이터** 텍스트 상자에 붙여넣습니다.
    3.  Azure 포털의 **IdeaScale에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **로그아웃 성공 URL** 텍스트 상자에 붙여넣습니다.
    4.  **변경 내용 저장**을 클릭합니다.

10. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Single Sign-On 구성")
##사용자 프로비저닝 구성
  
Azure AD 사용자가 IdeaScale에 로그인할 수 있도록 하려면 IdeaScale로 프로비저닝되어야 합니다. IdeaScale의 경우 프로비저닝은 수동 작업입니다.

###사용자 프로비저닝을 구성하려면 다음 단계를 수행합니다.

1.  **IdeaScale** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **커뮤니티 설정**으로 이동합니다.

    ![커뮤니티 설정](./media/active-directory-saas-ideascale-tutorial/IC790847.png "커뮤니티 설정")

3.  **기본 설정 > 멤버 관리**로 이동합니다.

4.  **멤버 추가**를 클릭합니다.

    ![멤버 관리](./media/active-directory-saas-ideascale-tutorial/IC790852.png "멤버 관리")

5.  새 멤버 추가 섹션에서 다음 단계를 수행합니다.

    ![새 멤버 추가](./media/active-directory-saas-ideascale-tutorial/IC790853.png "새 멤버 추가")

    1.  **전자 메일 주소** 텍스트 상자에 프로비저닝할 유효한 AAD 계정의 전자 메일 주소를 입력합니다.
    2.  **변경 내용 저장**을 클릭합니다.

    >[AZURE.NOTE]Azure Active Directory 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크가 포함된 전자 메일을 받습니다.

>[AZURE.NOTE]다른 IdeaScale 사용자 계정 생성 도구 또는 IdeaScale이 제공한 API를 사용하여 AAD 사용자 계정을 프로비저닝할 수도 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###IdeaScale에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **IdeaScale** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-ideascale-tutorial/IC790854.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-ideascale-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하세요.

<!---HONumber=August15_HO7-->