---
title: '자습서: Box와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory에서 Box를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2016
ms.author: jeedes

---
# 자습서: Box와 Azure Active Directory 통합
이 자습서는 Azure 및 Box의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* Box의 테스트 테넌트

이 자습서를 완료한 후 Box에 할당한 Azure AD 사용자가 Box 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. Box에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 및 그룹 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-box-tutorial/IC769537.png "시나리오")

## Box에 응용 프로그램 통합 사용
이 섹션은 Box에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### Box에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-box-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-box-tutorial/IC749321.png "응용 프로그램 추가")
5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-box-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **Box**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-box-tutorial/IC701023.png "응용 프로그램 갤러리")
7. 결과 창에서 **Box**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")

## Single Sign-On 구성
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Box에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 Box.com에 메타데이터를 업로드해야 합니다.

### Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **Box** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-box-tutorial/IC769538.png "Single Sign-On 구성")
2. **Box에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-box-tutorial/IC769539.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지에 있는 **Box 테넌트 URL** 텍스트 상자에 Box 테넌트 URL(예: https://<mydomainname>.box.com)을 입력하고 **다음**을 클릭합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-box-tutorial/IC669826.png "앱 URL 구성")
4. **Box에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드하려면 **메타데이터 다운로드**를 클릭한 다음 데이터 파일을 컴퓨터에 로컬로 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-box-tutorial/IC669824.png "Single Sign-On 구성")
5. Box 지원 팀에 해당 메타데이터 파일을 전달합니다. 지원팀은 Single Sign-On을 구성합니다.
6. Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-box-tutorial/IC769540.png "Single Sign-On 구성")
   
   ## 사용자 프로비전 구성

이 섹션은 Box에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **Box** 응용 프로그램 통합 페이지에서 **사용자 프로비전 구성**을 클릭하여 **사용자 프로비전 구성** 대화 상자를 엽니다.
   
    ![자동 사용자 프로비전 사용](./media/active-directory-saas-box-tutorial/IC769541.png "자동 사용자 프로비전 사용")
2. **Box에 사용자 프로비전을 사용하도록 설정** 대화 상자 페이지에서 **사용자 프로비전 사용**을 클릭합니다.
   
    ![자동 사용자 프로비전 사용](./media/active-directory-saas-box-tutorial/IC769544.png "자동 사용자 프로비전 사용")
3. **Box에 대한 액세스 권한 부여를 위한 로그인** 페이지에서 필요한 자격 증명을 제공한 다음 **권한 부여**를 클릭합니다.
   
    ![자동 사용자 프로비전 사용](./media/active-directory-saas-box-tutorial/IC769546.png "자동 사용자 프로비전 사용")
4. **Box에 액세스 허용**을 클릭하여 이 작업에 권한을 부여하고 Azure 클래식 포털로 돌아옵니다.
   
    ![자동 사용자 프로비전 사용](./media/active-directory-saas-box-tutorial/IC769549.png "자동 사용자 프로비전 사용")
5. **프로비전 옵션** 페이지에서 **프로비전할 개체 유형** 확인란을 사용하여 사용자 개체 외에 그룹 개체를 Box에 프로비전할지 여부를 선택할 수 있습니다. 자세한 내용은 아래의 "사용자 및 그룹 할당 섹션"을 참조하세요.
6. 구성을 마치려면 완료 단추를 클릭합니다.
   
    ![자동 사용자 프로비전 사용](./media/active-directory-saas-box-tutorial/IC769551.png "자동 사용자 프로비전 사용")

## 테스트 사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### Box에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **Box** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-box-tutorial/IC769552.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-box-tutorial/IC767830.png "예")

이제 10분 동안 기다린 후 계정이 Box에 동기화되었는지 확인해야 합니다.

첫 번째 확인 단계로 Azure 클래식 포털의 Box 응용 프로그램 통합 페이지에서 D의 대시보드를 클릭하여 프로비전 상태를 확인할 수 있습니다.

![대시보드](./media/active-directory-saas-box-tutorial/IC769553.png "대시보드")

주기를 프로비전하는 성공적으로 완료된 사용자는 관련된 상태에서 표시됩니다.

![통합 상태](./media/active-directory-saas-box-tutorial/IC769555.png "통합 상태")

Box 테넌트에서 동기화된 사용자가 **관리 콘솔**의 **관리된 사용자**에 나열됩니다.

![통합 상태](./media/active-directory-saas-box-tutorial/IC769556.png "통합 상태")

## 사용자 및 그룹 할당
Azure 클래식 포털의 **Box > 사용자 및 그룹** 탭에서 Box에 대한 액세스 권한을 부여해야 하는 사용자 및 그룹을 지정할 수 있습니다. 사용자 또는 그룹을 할당하면 다음과 같은 상황이 발생합니다.

* Azure AD에서 할당된 사용자에게 Box에 인증하도록 허용합니다(직접 할당 또는 그룹 멤버 자격을 통해). 사용자가 할당되지 않은 경우에는 Azure AD에서 Box에 로그인하도록 허용하지 않으며 Azure AD 로그인 페이지에서 오류를 반환합니다.
* Box의 앱 타일이 사용자의 [응용 프로그램 시작 관리자](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)에 추가됩니다.
* 자동 프로비전이 설정된 경우 할당된 사용자 및/또는 그룹이 자동으로 프로비전되도록 프로비전 큐에 추가됩니다.
  
  * 사용자 개체만 프로비전되도록 구성된 경우 직접 할당된 모든 사용자가 프로비전 큐에 배치되며, 할당된 그룹의 멤버인 모든 사용자가 프로비전 큐에 배치됩니다.
  * 그룹 개체가 프로비전되도록 구성된 경우 할당된 모든 그룹 개체뿐만 아니라 해당 그룹의 멤버인 모든 사용자가 Box에 프로비전됩니다. 그룹 및 사용자 멤버 자격은 Box에 기록될 때 유지됩니다.

**특성 > Single Sign-On** 탭을 사용하여 SAML 기반 인증 중에 Box에 표시되는 사용자 특성(또는 클레임)을 구성하고, **특성 > 프로비전** 탭을 사용하여 프로비전 작업 중에 사용자 및 그룹 특성이 Azure AD에서 Box로 이동하는 방식을 구성할 수 있습니다. 자세한 내용은 아래 참고 자료를 참조하세요.

## 추가 리소스
* [SAML 토큰에 발급된 클레임 사용자 지정](active-directory-saml-claims-customization.md)
* [프로비전: 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0720_2016-->