---
title: "자습서: Netsuite와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory에서 Netsuite를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 74ef08108a5ff27a50f928781f906b6d769f1085


---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>자습서: Netsuite와 Azure Active Directory 통합
이 자습서에서는 Netsuite 환경을 Azure AD(Azure Active Directory)에 연결하는 방법을 보여줍니다. Single Sign-On을 Netsuite에 구성하는 방법, 자동화된 사용자 프로비전을 사용하도록 설정하는 방법 및 Netsuite에 대한 액세스 권한을 사용자에게 할당하는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>필수 조건
1. [Azure 클래식 포털](https://manage.windowsazure.com)을 통해 Azure Active Directory에 액세스하려면 먼저 유효한 Azure 구독이 있어야 합니다.
2. [Netsuite](http://www.netsuite.com/portal/home.shtml) 구독에 대한 관리자 액세스 권한이 있어야 합니다. 어느 서비스에나 무료 평가판 계정을 사용할 수 있습니다.

## <a name="step-1-add-netsuite-to-your-directory"></a>1단계: 디렉터리에 Netsuite 추가
1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![왼쪽 탐색 창에서 Active Directory를 선택합니다.][0]
2. **디렉터리** 목록에서 Netsuite를 추가할 디렉터리를 선택합니다.
3. 상단 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램을 클릭합니다.][1]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![추가 클릭하여 새 응용 프로그램을 추가합니다.][2]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![갤러리에서 응용 프로그램 추가를 클릭합니다.][3]
6. **검색 상자**에서 **Netsuite**를 입력합니다. 그런 다음 결과 창에서 **Netsuite**를 클릭하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Netsuite 추가][4]
7. 이제 Netsuite의 [빠른 시작] 페이지가 표시됩니다.
   
    ![Azure AD의 Netsuite 빠른 시작 페이지][5]

## <a name="step-2-enable-single-sign-on"></a>2단계: Single Sign-On 사용
1. Azure AD의 Netsuite [빠른 시작] 페이지에서 **Single Sign-On 구성** 단추를 클릭합니다.
   
    ![Single Sign-On 구성 단추][6]
2. 대화 상자가 열리고 "사용자가 Netsuite에 로그인하는 방법을 선택하십시오."라고 요청하는 화면이 표시됩니다. **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On 선택][7]
   
   > [!NOTE]
   > 다른 Single Sign-On 옵션에 대해 자세히 알아보려면 [여기를 클릭](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. **앱 설정 구성** 페이지의 **회신 URL** 필드에서 다음 형식 중 하나를 사용하여 Netsuite 테넌트 URL을 입력합니다.
   
   * `https://<tenant-name>.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na1.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na2.netsuite.com/saml2/acs`
   * `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`
     
     ![테넌트 URL 입력][8]
4. **NetSuite에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.
   
    ![메타데이터를 다운로드합니다.][9]
5. 브라우저에서 새 탭을 열고 관리자 권한으로 Netsuite 회사 사이트에 로그인합니다.
6. 페이지의 위쪽에 있는 도구 모음에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.
   
    ![설치 관리자 이동][10]
7. **설치 작업** 목록에서 **통합**을 선택합니다.
   
    ![통합으로 이동][11]
8. **인증 관리** 섹션에서 **SAML Single Sign-On**을 클릭합니다.
   
    ![SAML Single Sign-on으로 이동][12]
9. **SAML 설정** 페이지에서 다음 단계를 수행합니다.
   
   * Azure Active Directory에서 **원격 로그인 URL** 값을 복사하여 Netsuite의 **ID 공급자 로그인 페이지** 필드에 붙여넣습니다.
     
       ![SAML 설치 페이지입니다.][13]
   * [Netsuite 설치]에서 **기본 인증 방법**을 선택합니다.
   * **SAMLV2 ID 공급자 메타데이터** 필드에서 **IDP 메타 데이터 파일 업로드**를 선택합니다. 그런 다음 **찾아보기** 를 클릭하여 4단계에서 다운로드한 메타데이터 파일을 업로드합니다.
     
       ![메타데이터 업로드][16]
   * **제출**을 클릭합니다.
10. Azure AD에서 Netsuite에 업로드한 인증서를 사용하도록 설정하기 위해 [Single Sign-On 구성 확인] 확인란을 선택합니다. 그런 후 **Next**를 클릭합니다.
    
     ![확인 확인란 선택][14]
11. 대화 상자의 마지막 페이지에서 이 Single Sign-On 구성 유지 관리와 관련된 오류 및 경고에 대한 전자 메일 알림을 수신하려는 경우 전자 메일 주소에 입력합니다. 
    
    ![전자 메일 주소를 입력합니다.][15]
12. **완료** 를 클릭하여 대화 상자를 닫습니다. 그런 다음 페이지의 위쪽에 있는 **특성** 을 클릭합니다.
    
    ![특성을 클릭합니다.][17]
13. **사용자 특성 추가**를 클릭합니다.
    
    ![사용자 특성 추가를 클릭합니다.][18]
14. **특성 이름** 필드에 `account`을(를) 입력합니다. **특성 값** 필드에서 Netsuite 계정 ID를 입력합니다. 사용자의 계정 ID를 찾는 방법에 대한 지침은 아래에 포함되어 있습니다.
    
    ![Netsuite 계정 ID 추가][19]
    
    * Netsuite의 위쪽 탐색 메뉴에서 **설치** 를 클릭합니다.
    * 왼쪽 탐색 메뉴의 **설치 작업** 섹션 아래를 클릭하고 **통합** 섹션을 선택한 다음 **웹 서비스 기본 설정**을 클릭합니다.
    * Netsuite 계정 ID를 복사하여 Azure AD의 **특성 값** 필드에 붙여넣습니다.
      
        ![사용자의 계정 ID 가져오기][20]
15. Azure AD에서 **완료** 를 클릭하여 SAML 특성 추가 작업을 마칩니다. 그런 다음 아래쪽 메뉴에 있는 **변경 내용 적용** 을 클릭합니다.
    
    ![변경 내용을 저장합니다.][21]
16. 사용자가 Netsuite에서 Single Sign-On을 수행하려면 먼저 Netsuite에 적절한 권한을 할당해야 합니다. 이러한 사용 권한을 할당하려면 아래 지침을 따릅니다.
    
    * 위쪽 탐색 메뉴에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.
      
        ![설치 관리자 이동][10]
    * 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **역할 관리**를 클릭합니다.
      
        ![역할 관리로 이동][22]
    * **새 역할**을 클릭합니다.
    * 새 역할에 대한 **이름**을 입력하고 **Single Sign-On 전용** 확인란을 선택합니다.
      
        ![새 역할에 이름을 지정합니다.][23]
    * **Save**를 클릭합니다.
    * 위쪽에 있는 메뉴에서 **사용 권한**을 클릭합니다. **설치**를 클릭합니다.
      
        ![사용 권한으로 이동][24]
    * **SAM Single Sign-On 설치**를 선택한 다음 **추가**를 클릭합니다.
    * **Save**를 클릭합니다.
    * 위쪽 탐색 메뉴에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.
      
        ![설치 관리자 이동][10]
    * 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **사용자 관리**를 클릭합니다.
      
        ![사용자 관리로 이동][25]
    * 테스트 사용자를 선택합니다. **편집**을 클릭합니다.
      
        ![사용자 관리로 이동][26]
    * 역할 대화 상자에서 만든 역할을 선택하거 **추가**를 클릭합니다.
      
        ![사용자 관리로 이동][27]
    * **Save**를 클릭합니다.
17. 구성을 테스트하려면 [Netsuite에 사용자 할당](#step-4-assign-users-to-netsuite)섹션을 참조하세요.

## <a name="step-3-enable-automated-user-provisioning"></a>3단계: 자동화된 사용자 프로비저닝 사용
> [!NOTE]
> 기본적으로 프로비전된 사용자를 Netsuite 환경의 루트 자회사에 추가합니다.
> 
> 

1. Azure Active Directory에서 Netsuite의 [빠른 시작] 에서**사용자 프로비전 구성**을 클릭합니다.
   
    ![사용자 프로비저닝 구성][28]
2. 열린 대화 상자에서 Netsuite에 대한 관리자 자격 증명을 입력한 후 **다음**을 클릭합니다.
   
    ![Netsuite 관리자 자격 증명을 입력합니다.][29]
3. 확인 페이지에서 프로비저닝 오류 알림을 받을 전자 메일 주소를 입력합니다.
   
    ![확인합니다.][30]
4. **완료** 를 클릭하여 대화 상자를 닫습니다.

## <a name="step-4-assign-users-to-netsuite"></a>4단계: Netsuite에 사용자 할당
1. 구성을 테스트하려면 디렉터리에 새 테스트 계정 만들기를 시작합니다.
2. [Netsuite 빠른 시작] 페이지에서 **사용자 할당** 단추를 클릭합니다.
   
    ![사용자 할당을 클릭합니다.][31]
3. 테스트 사용자를 선택하고 화면 아래쪽에 있는 **할당** 단추를 클릭합니다.
   
   * 자동화된 사용자 프로비저닝 사용하지 않도록 설정하면 다음을 확인하는 메시지가 표시됩니다.
     
        ![할당을 확인합니다.][32]
   * 자동화된 사용자 프로비전을 사용하도록 설정하면 Netsuite에 있어야 하는 사용자 역할 유형을 정의하라는 메시지가 표시됩니다. 잠시 후에 새로 프로비전된 사용자가 Netsuite 환경에 표시됩니다.
4. Single Sign-On 설정을 테스트하려면 [https://myapps.microsoft.com](https://myapps.microsoft.com/)에서 [액세스 패널]을 연 다음 테스트 계정에 로그인하고 **Netsuite**를 클릭합니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png



<!--HONumber=Dec16_HO2-->


