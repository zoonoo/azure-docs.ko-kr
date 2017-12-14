---
title: "자습서: 사용자 프로비전에 대한 Workplace by Facebook 구성 | Microsoft Docs"
description: "사용자 계정을 Azure AD에서 Workplace by Facebook으로 자동으로 프로비전 및 프로비전 해제하도록 구성하는 방법을 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 77f5f33044b1915fbda7b86c6b07882c0e1e7554
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>자습서: 사용자 프로비전에 대한 Workplace by Facebook 구성

이 자습서에서는 Azure AD(Azure Active Directory)에서 Workplace by Facebook으로 사용자 계정을 자동으로 프로비전 및 프로비전 해제하는 데 필요한 단계를 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

Workplace by Facebook과 Azure AD 통합을 구성하려면 다음이 필요합니다.

- Azure AD 구독
- Workplace by Facebook SSO(Single Sign-On)가 설정된 구독

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.

## <a name="assign-users-to-workplace-by-facebook"></a>Workplace by Facebook에 사용자 할당

Azure AD는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Workplace by Facebook 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자 및 그룹을 결정합니다. 그런 다음 [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)의 지침에 따라 이러한 사용자를 Workplace by Facebook에 할당할 수 있습니다.

>[!IMPORTANT]
>*   단일 Azure AD 사용자를 Workplace by Facebook에 할당하여 프로비전 구성을 테스트합니다. 추가 사용자 및 그룹을 나중에 할당합니다.
>*   Workplace by Facebook에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. 기본 액세스 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-automated-user-provisioning"></a>자동 사용자 프로비전 사용

이 섹션은 Azure AD를 Workplace by Facebook의 사용자 계정 프로비전 API에 연결하는 단계를 안내합니다. 또한 프로비전 서비스를 Workplace by Facebook에서 할당된 사용자 계정을 생성, 업데이트 및 비활성화하도록 구성하는 방법을 배웁니다. 이는 Azure AD에서 사용자 및 그룹 할당에 기반합니다.

>[!Tip]
>[Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Workplace by Facebook에 SAML 기반 SSO를 사용하도록 설정할 수도 있습니다. SSO는 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Azure AD에서 Workplace by Facebook에 사용자 계정 프로비전 구성

Azure AD는 할당된 사용자의 계정 세부 정보를 Workplace by Facebook에 자동으로 동기화하는 기능을 지원합니다. 이 자동 동기화를 사용하면 Workplace by Facebook에서 사용자가 처음으로 로그인하기 전에 사용자에게 액세스 권한을 부여하는 데 필요한 데이터를 가져올 수 있습니다. 또한 Azure AD에서 액세스가 취소되면 Workplace by Facebook에서 사용자 프로비전을 취소합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** > **엔터프라이즈 앱** > **모든 응용 프로그램**을 선택합니다.

2. 이미 Workplace by Facebook에 SSO를 구성한 경우 검색 필드를 사용하여 Workplace by Facebook의 인스턴스를 검색합니다. 그러지 않은 경우 **추가**를 선택하고 응용 프로그램 갤러리에서 **Workplace by Facebook**을 검색합니다. 검색 결과에서 **Workplace by Facebook**을 선택하고 응용 프로그램 목록에 추가합니다.

3. Workplace by Facebook의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다. 

    ![Workplace by Facebook 프로비전 옵션의 스크린샷](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. **관리자 자격 증명** 섹션 아래에 Workplace by Facebook 관리자의 **비밀 토큰**과 **테넌트 URL**을 입력합니다.

6. Azure Portal에서 **연결 테스트**를 선택하여 Azure AD가 Workplace by Facebook 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 Workplace by Facebook 계정에 팀 관리자 권한이 있는지 확인합니다.

7. 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

8. **저장**을 선택합니다.

9. 매핑 섹션에서 **Azure Active Directory 사용자를 Workplace by Facebook에 동기화**를 선택합니다.

10. **특성 매핑** 섹션에서 Azure AD에서 Workplace by Facebook으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Workplace by Facebook의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

11. Workplace by Facebook에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

12. **저장**을 선택합니다.

자동 프로비전을 구성하는 방법에 대한 자세한 내용은 [Facebook 설명서](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)를 참조하세요.

이제 테스트 계정을 만들 수 있습니다. 이제 최대 20분 동안 기다린 후 계정이 Workplace by Facebook에 동기화되었는지 확인합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)
* [Single Sign-on 구성](active-directory-saas-facebook-at-work-tutorial.md)

