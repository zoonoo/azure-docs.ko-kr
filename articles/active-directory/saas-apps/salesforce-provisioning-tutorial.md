---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Salesforce 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Salesforce로 자동 프로비저닝 및 프로비저닝 해제하려면 Salesforce 및 Azure AD에서 어떤 단계를 수행해야 하는지 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: c5df0a5fc054a12e3fa2ef1e352645c57c357b01
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798734"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Salesforce 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Salesforce로 자동 프로비전 및 프로비전 해제하도록 Salesforce 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure Active Directory 테넌트
* Salesforce.com 테넌트

> [!Note]
> 역할을 가져올 때 Azure Active Directory에서 역할을 수동으로 편집하면 안 됩니다.

> [!IMPORTANT]
> Salesforce.com 평가판을 사용하는 경우 자동화된 사용자 프로비저닝을 구성할 수 없습니다. 평가판 계정은 구입할 때까지 필요한 API 액세스가 활성화되지 않습니다. 이 자습서를 완료하기 위해 [무료 개발자 계정](https://developer.salesforce.com/signup)을 사용하여 이러한 제한을 해결할 수 있습니다.

Salesforce 샌드박스 환경을 사용하는 경우 [Salesforce 샌드박스 통합 자습서](./salesforce-sandbox-tutorial.md)를 참조하세요.

## <a name="assigning-users-to-salesforce"></a>Salesforce에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하려면 먼저 Salesforce 앱에 대한 액세스가 필요한 Azure AD의 사용자 또는 그룹을 결정해야 합니다. 결정했으면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 지침에 따라 사용자를 Salesforce 앱에 할당할 수 있습니다.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Salesforce에 사용자를 할당하기 위한 주요 팁

* 프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 Salesforce에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* Salesforce에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

    > [!NOTE]
    > 이 앱은 프로비저닝 프로세스에서 Salesforce의 프로필을 가져오며, 고객이 사용자를 Azure AD에 할당할 때 선택할 수 있습니다. Salesforce에서 가져온 프로필은 Azure AD에서 역할로 표시됩니다.

## <a name="enable-automated-user-provisioning"></a>자동 사용자 프로비전 사용

이 섹션에서는 사용자의 Azure AD를 [Salesforce의 사용자 계정 프로비전 API - v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Salesforce에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비저닝 서비스를 구성하는 방법을 안내합니다.

> [!Tip]
> [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Salesforce에 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

이 섹션은 Salesforce에 Active Directory 사용자 계정을 사용자 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

2. 이미 Salesforce에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 Salesforce의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가** 를 선택하고 애플리케이션 갤러리에서 **Salesforce** 를 검색합니다. 검색 결과에서 Salesforce를 선택하고 애플리케이션 목록에 추가합니다.

3. Salesforce의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![이 스크린샷은 프로비전 모드가 자동으로 설정되었으며 설정할 수 있는 다른 값으로 설정된 Salesforce 프로비저닝 페이지를 보여 줍니다.](./media/salesforce-provisioning-tutorial/provisioning.png)

5. **관리자 자격 증명** 섹션에서 다음 구성 설정을 제공합니다.

    a. **관리 사용자 이름** 텍스트 상자에 Salesforce.com에서 **시스템 관리자** 프로필이 할당된 Salesforce 계정 이름을 입력합니다.

    b. **관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.

6. Salesforce 보안 토큰을 얻으려면 새 탭을 열고 동일한 Salesforce 관리자 계정에 로그인합니다. 페이지의 오른쪽 위 모서리에 있는 사용자 이름을 클릭하고 **설정** 을 클릭합니다.

    ![스크린샷은 선택된 설정 링크를 보여 줍니다.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "자동 사용자 프로비전 사용")

7. 왼쪽 탐색 패널에서 **내 개인 정보** 를 클릭하여 관련 섹션을 확장하고 **내 보안 토큰 재설정** 을 클릭합니다.
  
    ![스크린샷은 내 개인 정보에서 선택한 내 보안 토큰 재설정을 보여 줍니다.](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "자동 사용자 프로비전 사용")

8. **보안 토큰 재설정** 페이지에서 **보안 토큰 재설정** 단추를 클릭합니다.

    ![스크린샷은 설명 텍스트와 보안 토큰을 다시 설정하는 옵션을 포함하는 Rest 보안 토큰 페이지를 보여 줍니다.](./media/salesforce-provisioning-tutorial/sf-reset-token.png "자동 사용자 프로비전 사용")

9. 이 관리자 계정과 연결된 전자 메일 받은 편지함을 확인합니다. Salesforce.com에서 새 보안 토큰이 포함된 전자 메일을 찾습니다.

10. 토큰을 복사하고 Azure AD 창으로 이동하여 **비밀 토큰** 필드에 붙여넣습니다.

11. Salesforce의 인스턴스가 Salesforce 정부 클라우드에 있는 경우 **테넌트 URL** 을 입력해야 합니다. 그렇지 않은 경우 선택 사항입니다. "https://\<your-instance\>.my.salesforce.com" 형식으로 테넌트 URL을 입력하고 \<your-instance\>를 Salesforce 인스턴스의 이름으로 바꿉니다.

12. Azure Portal에서 **연결 테스트** 를 클릭하여 Azure AD가 Salesforce 앱에 연결할 수 있는지 확인합니다.

13. 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 **알림 메일** 필드에 입력하고 아래의 확인란을 선택합니다.

14. **저장** 을 클릭합니다.  

15. 매핑 섹션에서 **Azure Active Directory 사용자를 Salesforce에 동기화** 를 선택합니다.

16. **특성 매핑** 섹션에서 Azure AD에서 Salesforce로 동기화할 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Salesforce의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

17. Salesforce에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 설정 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

18. **저장** 을 클릭합니다.

> [!NOTE]
> Salesforce 애플리케이션에서 사용자가 프로비저닝되면 관리자는 해당 사용자의 언어 관련 설정을 구성해야 합니다. 언어 구성에 대한 자세한 내용은 [이 문서](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)를 참조하세요.

사용자 및 그룹 섹션에서 Salesforce에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 Salesforce 앱의 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 로그에 연결된 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="common-issues"></a>일반적인 문제
* Salesforce에 대한 액세스 권한을 부여하는 데 문제가 있는 경우 다음 사항을 확인합니다.
    * 사용된 자격 증명에는 Salesforce에 대한 관리자 액세스 권한이 있습니다.
    * 사용 중인 Salesforce 버전이 웹 액세스(예: Salesforce의 개발자, 엔터프라이즈, 샌드박스 및 무제한 버전)를 지원합니다.
    * 사용자가 웹 API 액세스를 사용할 수 있습니다.
* Azure AD 프로비저닝 서비스는 사용자에 대한 언어, 로캘 및 timeZone 프로비저닝을 지원합니다. 이러한 특성은 기본 특성 매핑에 있지만 기본 원본 특성은 없습니다. 기본 원본 특성을 선택하고 원본 특성이 SalesForce에 필요한 형식인지 확인합니다. 예를 들어 영어(미국)의 localeSidKey는 en_US입니다. [여기](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)에 제공된 지침을 검토하여 적절한 localeSidKey 형식을 확인하세요. languageLocaleKey 형식은 [여기](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)서 찾을 수 있습니다. 형식이 올바른지 확인하는 것 외에도 [여기](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)에 설명된 대로 사용자가 언어를 사용하도록 설정했는지 확인해야 할 수도 있습니다. 
* **SalesforceLicenseLimitExceeded:** 사용자에게 사용할 수 있는 라이선스가 없어서 대상 애플리케이션에서 이 사용자를 만들 수 없습니다. 대상 애플리케이션에 사용할 추가 라이선스를 구입하거나, 사용자 할당 및 특성 매핑 구성을 검토하여 올바른 사용자가 올바른 특성으로 할당되었는지 확인해야 합니다.
* **SalesforceDuplicateUserName:** Salesforce.com 'Username'이 다른 Salesforce.com 테넌트와 중복되므로 사용자를 프로비저닝할 수 없습니다.  Salesforce.com에서 'Username' 특성의 값은 모든 Salesforce.com 테넌트에서 고유해야 합니다.  기본적으로 Azure Active Directory 사용자의 userPrincipalName은 Salesforce.com에서 'Username'이 됩니다.다음과 같은 두 가지 옵션이 있습니다.  한 가지 옵션은 다른 테넌트도 관리하는 경우 해당 Salesforce.com 테넌트의 중복 'Username'을 찾아서 이름을 바꾸는 것입니다.  다른 옵션은 디렉터리가 통합된 Salesforce.com 테넌트에 대한 Azure Active Directory 사용자의 액세스 권한을 제거하는 것입니다. 다음 동기화 때 이 작업을 다시 시도하겠습니다. 
* **SalesforceRequiredFieldMissing:** 사용자를 성공적으로 만들거나 업데이트하려면 Salesforce에서 특정 특성이 있어야 합니다. 이 사용자는 필요한 특성 중 하나가 없습니다. Salesforce로 프로비저닝하려는 모든 사용자가 이메일 및 별칭과 같은 특성이 채워져 있는지 확인하세요. [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 이러한 특성이 없는 사용자의 범위를 지정할 수 있습니다. 
* Salesforce로 프로비저닝하기 위한 기본 특성 매핑으로는 Azure AD의 appRoleAssignments를 Salesforce의 ProfileName에 매핑하는 SingleAppRoleAssignments 식이 있습니다. 특성 매핑은 한 가지 역할을 프로비저닝하는 것만 지원하므로 Azure AD에서 사용자에게 여러 앱 역할이 할당되지 않도록 주의합니다. 
* Salesforce는 이메일 변경 전에 이메일 업데이트를 수동으로 승인해야 합니다. 따라서 이메일 변경이 승인될 때까지 프로비저닝 로그에 사용자의 이메일을 업데이트하는 항목이 여러 개 보일 수 있습니다.


## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Single Sign-On 구성](./salesforce-tutorial.md)
