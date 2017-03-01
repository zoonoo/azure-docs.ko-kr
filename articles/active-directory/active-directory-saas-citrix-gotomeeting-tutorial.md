---
title: "자습서: Citrix GoToMeeting과 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory에서 Citrix GoToMeeting를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7d7897f6-b88e-4dd5-8f3a-e612337b1413
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 400793331aa2d56358a83a51ce64c67f59bbf3b7
ms.openlocfilehash: d81de30a46f7b849e70b99e7f7423cbc90f25326
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>자습서: Citrix GoToMeeting과 Azure Active Directory 통합

이 자습서는 Azure 및 Citrix GoToMeeting의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* Citrix GoToMeeting의 테넌트

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. Citrix GoToMeeting에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![구성](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "구성")

## <a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Citrix GoToMeeting에 응용 프로그램 통합 사용
이 섹션은 Citrix GoToMeeting에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Citrix GoToMeeting에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "응용 프로그램")

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램 추가](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "응용 프로그램 추가")

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6. **검색 상자**에서 **Citrix GoToMeeting**을 입력합니다.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7. 결과 창에서 **Citrix GoToMeeting**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
   
## <a name="configuring-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Citrix GoToMeeting에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  
이 절차의 일부로 base-64로 인코딩된 인증서 파일을 Citrix GoToMeeting 테넌트에 업로드해야 합니다.  
이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. **Citrix GoToMeeting** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 사용](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Single Sign-On 사용")

2. **사용자가 Citrix GoToMeeting에 로그인하는 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Single Sign-On 구성")

3. **응용 프로그램 설정 구성** 페이지에서 **다음**을 클릭합니다. 
   
    ![Single Sign-On 사용](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Single Sign-On 사용")

4. **Citrix GoToMeeting에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Single Sign-On 구성")

5. 다른 브라우저 창에서 [Citrix 조직 센터](https://account.citrixonline.com/organization/administration/)에 로그인합니다.

6. **ID 공급자** 탭을 클릭한 후에 다음 단계를 수행합니다.  
   
    ![SAML 설정](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML 설정")
   
    a. **수동**

    b. Azure 클래식 포털의 **Citrix GoToMeeting에서 Single Sign-On 구성** 대화 상자 페이지에서 **로그인 페이지 URL** 값을 복사한 다음 **로그인 페이지 URL** 텍스트 상자에 붙여넣습니다. 

    c. Azure 클래식 포털의 **Citrix GoToMeeting에서 Single Sign-On 구성** 대화 상자 페이지에서 **로그아웃 페이지 URL** 값을 복사한 다음 **로그아웃 페이지 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure 클래식 포털의 **Citrix GoToMeeting에서 Single Sign-On 구성** 대화 상자 페이지에서 **엔터티 ID** 값을 복사한 다음 **ID 공급자 엔터티 ID** 텍스트 상자에 붙여넣습니다.

    e. 다운로드한 인증서를 업로드하려면 **인증서 업로드**를 클릭합니다.

    f. **Save**를 클릭합니다.


1. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Single Sign-On 구성")

2. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
   
    ![SAML 설정](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML 설정")

## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성
이 섹션은 Citrix GoToMeeting에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비저닝을 구성하려면
1. Azure 클래식 포털의 **Citrix GoToMeeting** 응용 프로그램 통합 페이지에서 **사용자 프로비전 구성**을 클릭하여 **사용자 프로비전 구성** 대화 상자를 엽니다.
   
    ![사용자 프로비저닝 구성](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "사용자 프로비저닝 구성")

2. **설정 및 관리자 자격 증명** 페이지에서 다음 단계를 수행합니다.
   
    ![사용자 프로비저닝 구성](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "사용자 프로비저닝 구성")
   
    a. **Citrix GoToMeeting 관리자 사용자 이름** 텍스트 상자에서 관리자의 사용자 이름을 입력합니다.

    b. **Citrix GoToMeeting 관리자 암호** 텍스트 상자에서 관리자의 암호입니다.

    c. **Next**를 클릭합니다.

1. **확인** 페이지에서 확인 표시를 클릭하여 구성을 저장합니다.
2. **유효성 검사** 단추를 클릭하여 구성을 확인합니다.

## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### <a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Citrix GoToMeeting에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **Citrix GoToMeeting** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "사용자 할당")

3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
    ![예](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "예")

이제 10분 동안 기다린 후 계정이 비즈니스용 Dropbox에 동기화되었는지 확인해야 합니다.

첫 번째 확인 단계로 Azure 클래식 포털의 **Citrix GoToMeeting** 응용 프로그램 통합 페이지에서 D의 대시보드를 클릭하여 프로비전 상태를 확인할 수 있습니다.

![대시보드](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "대시보드")

주기를 프로비전하는 성공적으로 완료된 사용자는 관련된 상태에서 표시됩니다.

![통합 상태](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "통합 상태")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하세요.


