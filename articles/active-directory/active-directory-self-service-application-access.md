---
title: "Azure Active Directory를 사용하는 셀프 서비스 응용 프로그램 액세스 및 위임된 관리 | Microsoft Docs"
description: "이 문서는 Azure Active Directory를 사용하는 셀프 서비스 응용 프로그램 액세스 및 위임된 관리를 사용하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 448a7fe8-a162-475e-9ba2-2e3ab59302bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: asmalser
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 57093159c0b9c63b6e2dc0971e3aea645a33aa55
ms.contentlocale: ko-kr
ms.lasthandoff: 02/16/2017


---
# <a name="self-service-application-access-and-delegated-management-with-azure-active-directory"></a>Azure Active Directory를 사용하는 셀프 서비스 응용 프로그램 액세스 및 위임된 관리
최종 사용자에 대한 셀프 서비스 기능을 사용하는 작업은 엔터프라이즈 IT에 대한 일반적인 시나리오입니다. 사용자가 많고 응용 프로그램의 많으며 액세스 권한 부여 결정을 내리는 가장 많은 정보를 가진 사람은 디렉터리 관리자가 아닐 수도 있습니다. 종종 응용 프로그램에 액세스할 수 있는 사용자를 결정하는 최상의 사용자는 팀 리더는 또는 다른 위임된 관리자입니다. 하루가 끝날 때 앱을 사용하는 사용자이며 이 사용자는 작업을 수행하는 데 필요한 사항을 압니다.

셀프 서비스 응용 프로그램 액세스는 디렉터리 관리자가 다음을 수행할 수 있도록 하는 [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/)의 기능입니다.

*  [Azure AD 액세스 패널](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* 액세스를 요청할 수 있는 응용 프로그램 사용자 설정
* 사용자가 응용 프로그램에 대한 액세스를 자체 할당할 수 있도록 승인이 필요한지 여부 설정
* 각 응용 프로그램에 대한 요청을 승인하고 액세스를 관리해야 하는 사용자 설정

오늘날 이 기능은 [Azure Active Directory 응용 프로그램 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/)에서 페더레이션된 또는 암호 기반 Single Sign-On을 지원하는 Salesforce, Dropbox, Google Apps과 같은 앱을 포함하여 미리 통합된 사용자 지정 앱을 지원합니다.
이 문서에서는 다음과 같이 방법을 설명합니다.

* 최종 사용자에게 선택적 승인 워크플로의 구성을 비롯한 셀프 서비스 응용 프로그램 액세스를 구성합니다. 
* 조직에서 가장 적합한 사람에게 특정 응용 프로그램에 대한 액세스 관리를 위임하고, Azure AD 액세스 패널을 사용하여 액세스 요청을 승인하며, 선택된 사용자에 대한 액세스를 직접 할당하거나 또는 (선택적으로) 암호 기반 Single Sign-On이 구성된 경우 응용 프로그램 액세스에 대한 자격 증명을 설정합니다.

## <a name="configuring-self-service-application-access"></a>셀프 서비스 응용 프로그램 액세스 구성
셀프 서비스 응용 프로그램 액세스를 사용하고 추가하거나 최종 사용자가 요청한 응용 프로그램을 구성하려면 아래 지침에 따릅니다.

**1:**[Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.

**2:** **Active Directory** 섹션에서 디렉터리를 선택한 다음 **응용 프로그램** 탭을 선택합니다. 

**3:** **추가** 단추를 선택하고 갤러리 옵션을 사용하여 응용 프로그램을 선택하고 추가합니다.

**4:** 앱이 추가된 후에 앱 빠른 시작 페이지를 가져오게 됩니다. **Single Sign-On 구성**을 클릭하고 원하는 Single Sign-On 모드를 선택한 다음 구성을 저장합니다. 

**5:** 다음으로 **구성** 탭을 선택합니다. Azure AD 액세스 패널에서 사용자가 이 앱에 대한 액세스를 요청하도록 설정하려면 **셀프 서비스 응용 프로그램 액세스 허용**을 **예**로 설정합니다.

![][1]

**6:** 필요에 따라 액세스 요청에 대한 승인 워크플로를 구성하려면 **액세스 권한을 부여하기 전에 승인 필요**를 **예**로 설정합니다. **승인자** 단추를 사용하여 하나 이상의 승인자를 선택할 수 있습니다.

승인자는 Azure AD 계정이 있는 조직의 사용자일 수 있고 앱에 대한 액세스 권한을 부여하기 전에 조직이 요구하는 프로비전, 라이선스 또는 비즈니스 프로세스 중인 계정에 대한 책임을 집니다. 승인자는 하나 이상의 공유 계정 그룹 소유자일 수 있고 사용자를 이러한 그룹 중 하나를 할당하여 공유 계정을 통해 액세스 권한을 부여할 수 있습니다. 

승인이 필요하지 않은 경우 사용자는 Azure AD 액세스 패널에 추가된 응용 프로그램을 즉시 가져옵니다. 응용 프로그램이 [사용자 자동 프로비전](active-directory-saas-app-provisioning.md)에 설정된 경우 또는 사용자가 이미 사용자 계정을 보유하고 암호를 알고 있는 ["사용자 관리"](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) 암호 SSO 모드를 설정한 경우에 적절합니다.

**7:** 암호 기반 Single Sign-On을 사용하도록 응용 프로그램을 구성한 경우 승인자가 각 사용자를 대신하여 SSO 자격 증명을 설정하도록 허용하는 옵션을 사용할 수 있습니다. 자세한 내용은 대리자 액세스 관리에 대한 다음 섹션을 참조하세요.

**8:** 마지막으로 **자체 할당된 사용자에 대한 그룹**은 응용 프로그램에 대한 액세스가 부여되거나 할당된 사용자를 저장하는 데 사용되는 그룹의 이름을 표시합니다. 액세스 승인자는 이 그룹의 소유자가 됩니다. 표시된 그룹 이름이 없는 경우 자동으로 생성됩니다. 필요에 따라 그룹 이름은 기존 그룹의 이름으로 설정할 수 있습니다.

**9:** 화면 맨 아래에 있는 저장을 클릭하여 구성을 **저장**합니다. 이제 사용자가 액세스 패널에서 이 앱에 대한 액세스를 요청할 수 있습니다.

**10:** 최종 사용자 환경을 실행하려면 앱 승인자가 아닌 다른 계정을 사용하여 https://myapps.microsoft.com에서 조직의 Azure AD 액세스 패널에 로그인합니다. 

**11:** **응용 프로그램** 탭에서 **응용 프로그램 더 가져오기** 타일을 클릭합니다. 왼쪽의 앱 항목에서 검색하고 필터링하는 기능을 통해 디렉터리에 셀프 서비스 응용 프로그램 액세스에 사용된 모든 응용 프로그램의 갤러리를 표시합니다. 

**12:** 앱을 클릭하면 요청 프로세스를 시작합니다. 승인 프로세스가 필요하지 않은 경우 짧게 확인한 후에 **응용 프로그램** 탭에 응용 프로그램을 즉시 추가합니다. 승인이 필요한 경우 이를 나타내는 대화 상자가 표시되고 승인자에게 전자 메일을 보냅니다. (빠른 노트: 이 요청 프로세스를 확인하려면 비-승인자로 액세스 패널에 서명해야 합니다.)

**13:** 전자 메일은 승인자가 Azure AD 액세스 패널에 로그인하고 요청을 승인하도록 지시합니다. 요청이 승인되면(그리고 정의한 특별한 프로세스를 승인자가 수행하면) 사용자는 로그인할 수 있는 **응용 프로그램** 탭에 나타나는 응용 프로그램을 확인하게 됩니다.

## <a name="delegated-application-access-management"></a>위임된 응용 프로그램 액세스 관리
응용 프로그램 액세스 승인자는 조직에서 문제의 응용 프로그램에 대한 액세스를 승인하거나 거부하는 가장 적절한 사용자일 수 있습니다. 이 사용자는 앱에 대한 액세스 권한을 부여하기 전에 조직이 요구하는 프로비전, 라이선스 또는 비즈니스 프로세스 중인 계정에 대한 책임을 집니다.

위에서 설명한 셀프 서비스 응용 프로그램 액세스를 구성할 때 할당된 응용 프로그램 승인자는 Azure AD 액세스 패널에서 추가 **응용 프로그램 관리** 타일을 확인하게 되며 이는 액세스 관리자가 동의하는 응용 프로그램을 보여줍니다. 앱을 클릭하면 몇 가지 옵션이 있는 화면을 보여줍니다.

![][2]

### <a name="approve-requests"></a>요청 승인
**승인 요청** 타일을 사용하면 승인자가 해당 앱에 특정한 보류 중인 승인을 볼 수 있고 요청이 승인되거나 거부될 수 있는 승인 탭으로 리디렉션합니다. 또한 수행할 사항을 지시하는 요청이 만들어질 때마다 승인자가 자동화된 전자 메일을 수신합니다.

### <a name="add-users"></a>사용자 추가
**사용자 추가** 타일을 사용하면 승인자가 선택된 사용자에게 직접 응용 프로그램에 대한 액세스 권한을 부여하도록 할 수 있습니다. 이 타일을 클릭하면 승인자가 대화 상자를 확인하여 디렉터리에서 사용자를 보고 검색할 수 있습니다. 사용자를 추가하면 해당 사용자의 Azure AD 액세스 패널 또는 Office 365에 표시되는 응용 프로그램에서 결과가 발생합니다. 사용자가 로그인할 수 있기 전에 수동 사용자 프로비전 프로세스가 앱에 필요한 경우 승인자는 액세스를 할당하기 전에 이 프로세스를 수행해야 합니다.  

### <a name="manage-users"></a>사용자 관리
**사용자 관리** 타일을 사용하면 승인자가 직접 응용 프로그램에 액세스할 수 있는 사용자를 업데이트하거나 제거할 수 있습니다. 

### <a name="configure-password-sso-credentials-if-applicable"></a>암호 SSO 자격 증명 구성(적용 가능한 경우)
**구성** 타일은 응용 프로그램이 암호 기반 Single Sign-On을 사용하는 IT 관리자에 의해 구성되는 경우에만 표시되고 관리자는 앞에서 설명한 대로 암호 SSO 자격 증명을 설정하는 기능을 승인자에게 부여합니다. 선택하면 자격 증명이 사용자를 할당하도록 전파하는 방법에 대한 몇 가지 옵션을 통해 승인자를 표시합니다.

![][3]

* **사용자가 고유한 암호를 사용하여 로그인합니다** - 이 모드에서 할당된 사용자는 응용 프로그램에 대한 해당 사용자 이름 및 암호를 알고 첫 번째 로그인 시 해당 사항을 응용 프로그램에 입력하라는 메시지가 표시됩니다. [사용자가 자격 증명을 관리](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)하는 암호 SSO 사례에 해당합니다.
* **사용자는 관리하는 별도 계정을 사용하여 자동으로 로그인합니다** -이 모드에서 할당된 사용자는 응용 프로그램에 로그인할 때 앱 특정 자격 증명을 입력하거나 알지 않아도 됩니다. 대신 승인자는 **사용자 추가** 타일을 사용하여 액세스를 할당한 후에 각 사용자에 대한 자격 증명을 설정합니다. 사용자가 액세스 패널 또는 Office 365에서 응용 프로그램을 클릭할 때 승인자가 설정한 자격 증명을 사용하여 자동으로 로그인됩니다. [관리자가 자격 증명을 관리](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)하는 암호 SSO 사례에 해당합니다.
* **사용자가 관리한 단일 계정을 사용하여 자동으로 로그인됩니다.** - 이는 특수한 경우이며 모든 할당된 사용자가 단일 공유 계정을 사용하여 액세스 권한을 부여받아야 할 때 사용하는 것이 적절합니다. 이에 대한 가장 일반적인 사용 사례는 소셜 미디어 응용 프로그램이며 여기서 조직에는 단일 "회사" 계정이 있고 여러 사용자는 해당 계정을 업데이트해야 합니다. 이 또한 [관리자가 자격 증명을 관리](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)하는 암호 SSO 사례에 해당합니다. 그러나 이 옵션을 선택한 후에 승인자는 단일 공유 계정에 대한 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다. 완료되면 해당 Azure AD 액세스 패널 또는 Office 365에서 응용 프로그램을 클릭할 때 할당된 모든 사용자가 이 계정을 사용하여 로그인합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG

