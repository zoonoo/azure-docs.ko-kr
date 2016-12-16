---
title: "자습서: ArcGIS와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 ArcGIS를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4b6ad270d7aded7e4ea7343e53ffd8fc3a59d1c6


---
# <a name="tutorial-azure-active-directory-integration-with-arcgis"></a>자습서: ArcGIS와 Azure Active Directory 통합
이 자습서는 Azure 및 ArcGIS의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* ArcGIS Single Sign-on이 설정된 구독

이 자습서를 완료한 후 ArcGIS에 할당한 Azure AD 사용자가 ArcGIS 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. ArcGIS에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario")

## <a name="enabling-the-application-integration-for-arcgis"></a>ArcGIS에 응용 프로그램 통합 사용
이 섹션은 ArcGIS에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>ArcGIS에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Add application")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Add an application from gallerry")
6. **검색 상자**에 **ArcGIS**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Applcation Gallery")
7. 결과 창에서 **ArcGIS**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
   
   ## <a name="configuring-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ArcGIS에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **ArcGIS** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configure Single Sign-On")
2. **ArcGIS에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configure Single Sign-On")
3. **앱 URL 구성** 페이지의 **ArcGIS 로그인 URL** 텍스트 상자에 "*https://company.maps.arcgis.com*" 패턴을 사용하여 로그인하기 위해 사용자가 사용한 URL을 입력하고 **다음**을 클릭합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configure App URL")
4. **ArcGIS에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 로컬로 메타데이터 파일을 저장합니다.
   
   ![Single Sign-on 구성](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configure Single Sign-On")
5. 다른 웹 브라우저 창에서 ArcGIS 회사 사이트에 관리자로 로그인합니다.
6. **설정 편집**을 클릭합니다.
   
   ![설정 편집](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")
7. **보안**을 클릭합니다.
   
   ![보안](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Security")
8. **회사 로그인**에서 **ID 공급자 설정**을 클릭합니다.
   
   ![회사 로그인](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")
9. **ID 공급자 설정** 구성 페이지에서 다음 단계를 수행합니다.
   
   ![ID 공급자 설정](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")
   
   1. 이름 텍스트 상자에 조직의 이름을 입력합니다.
   2. **회사 ID 공급자에 대한 메타데이터를 다음을 사용하여 제공합니다**에서 **파일**을 선택합니다.
   3. 다운로드한 메타데이터 파일을 업로드하려면 **파일 선택**을 클릭합니다.
   4. **ID 공급자 설정**을 클릭합니다.
10. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 ArcGIS에 로그인할 수 있도록 하려면 ArcGIS로 프로비전되어야 합니다.  
ArcGIS의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면
1. **ArcGIS** 테넌트에 로그인합니다.
2. **멤버 초대**를 클릭합니다.
   
   ![멤버 초대](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")
3. **전자 메일을 보내지 않고 멤버를 자동으로 추가**를 선택하고 **다음**을 클릭합니다.
   
   ![멤버를 자동으로 추가](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")
4. **멤버** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![추가 및 검토](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")
   
   1. 프로비전하려는 유효한 AAD 계정의 **이름**, **성** 및 **전자 메일**을 입력합니다.
   2. **추가 및 검토**를 클릭합니다.
5. 입력한 데이터를 검토한 다음 **멤버 추가**를 클릭합니다.
   
   ![멤버 추가](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

> [!NOTE]
> 다른 ArcGIS 사용자 계정 생성 도구 또는 ArcGIS가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### <a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>ArcGIS에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **ArcGIS** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assign Users")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Yes")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.




<!--HONumber=Nov16_HO3-->


