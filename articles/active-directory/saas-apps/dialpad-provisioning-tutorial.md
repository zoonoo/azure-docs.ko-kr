---
title: '자습서: Azure Active Directory로 자동 사용자 프로 비전에 대 한 다이얼 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 다이얼으로 사용자 계정을 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611771"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>자습서: 다이얼 자동 사용자 프로 비전 구성

이 자습서의 목적은 자동으로 프로 비전 하 고 사용자 및/또는 그룹 다이얼을 프로 비전 해제 하도록 Azure AD를 구성 하려면 다이얼 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 주기 위해입니다.

> [!NOTE]
>  이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.

> 이 커넥터는 현재 미리 보기로 제공에서 됩니다. 일반적인 Microsoft Azure 사용 약관 미리 보기 기능에 대 한 자세한 내용은 참조 하세요. [사용 특약 조건의 Microsoft Azure 미리 보기에 대 한](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트.
* [다이얼 테 넌 트](https://www.dialpad.com/pricing/)합니다.
* 관리자 권한이 있는 다이얼의 사용자 계정.

## <a name="assign-users-to-dialpad"></a>다이얼에 사용자 할당
Azure Active Directory 할당 이라는 개념을 사용 하 여 어떤 사용자가 선택한 앱에 대 한 액세스를 받아야 하는지를 판단 합니다. 자동 사용자 프로 비전의 컨텍스트에서의 사용자 및/또는 Azure AD에서 응용 프로그램에 할당 된 그룹 동기화 됩니다.

를 구성 하 고 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 다이얼에 대 한 액세스를 해야 하는 사용자 및/또는 Azure AD의 그룹 결정 해야 합니다. 결정 했으면 할당할 수 있습니다 이러한 사용자 및/또는 그룹 다이얼을 여기의 지침에 따라.
 
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>다이얼에 사용자를 할당 하기 위한 주요 팁

 * 것이 좋습니다 단일 다이얼에 자동 사용자 프로 비전 구성을 테스트 하려면 Azure AD 사용자를 할당 합니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 다이얼에 사용자를 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)를 선택 해야 합니다. 기본 액세스 역할을 사용 하 여 사용자 프로 비전에서 제외 됩니다.

## <a name="setup-dialpad-for-provisioning"></a>프로 비전을 위해 다이얼을 설정 합니다.

다이얼 자동 사용자를 Azure AD를 사용 하 여 프로 비전을 구성 하기 전에 다이얼에서 일부 프로 비전 정보를 검색 해야 합니다.

1. 에 로그인 하 여 [다이얼 관리 콘솔](https://dialpadbeta.com/login) 선택한 **관리 설정**합니다. 했는지 **우리 회사** 드롭다운 목록에서 선택 합니다. 이동할 **인증 > API 키**합니다.

    ![다이얼 SCIM 추가](media/dialpad-provisioning-tutorial/dialpad01.png)

2. 클릭 하 여 새 키를 생성할 **키를 추가** 비밀 토큰의 속성을 구성 합니다.

    ![다이얼 SCIM 추가](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![다이얼 SCIM 추가](media/dialpad-provisioning-tutorial/dialpad03.png)

3. 클릭 합니다 **값을 표시 하려면 클릭** 최근에 생성된 된 API 키 단추 및 표시 되는 값을 복사 합니다. 이 값에 입력 됩니다 합니다 **비밀 토큰** 필드에 Azure portal에서 다이얼 응용 프로그램의 프로 비전 탭 합니다. 

    ![다이얼 토큰 만들기](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>갤러리에서 다이얼을 추가 합니다.

다이얼 자동 사용자를 Azure AD를 사용 하 여 프로 비전을 구성 하려면 다이얼을 Azure AD 응용 프로그램 갤러리에서 관리 되는 SaaS 응용 프로그램의 목록에 추가 해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 다이얼을 추가 하려면 다음 단계를 수행 합니다.**

1. 에  **[Azure portal](https://portal.azure.com)** , 왼쪽된 탐색 패널에서 선택 **Azure Active Directory**합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 선택 합니다 **새 응용 프로그램** 창의 맨 위에 있는 단추입니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 입력 **다이얼**를 선택 **다이얼** 결과 패널에서 합니다.
    ![결과 목록의 다이얼](common/search-new-app.png)

5. 로 이동 합니다 **URL** 별도 브라우저에서 아래 강조 표시 합니다. 

    ![다이얼 SCIM 추가](media/dialpad-provisioning-tutorial/dialpad05.png)

6. 오른쪽 위 모서리에서 선택 **로그에 > 사용 하 여 다이얼 온라인**합니다.

    ![다이얼 SCIM 추가](media/dialpad-provisioning-tutorial/dialpad06.png)

7. 다이얼 OpenIDConnect 앱 이므로 Microsoft 회사 계정을 사용 하 여 다이얼에 로그인을 선택 합니다.

    ![다이얼 SCIM 추가](media/dialpad-provisioning-tutorial/loginpage.png)

8. 인증에 성공 하면 동의 페이지에 대 한 동의 확인 프롬프트를 수락 합니다. 응용 프로그램이 자동으로 추가 됩니다 테 넌 트 및 다이얼 계정으로 이동 합니다.

    ![다이얼 SCIM 추가](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>다이얼에 자동 사용자 프로비저닝 구성

이 섹션에서는 생성, 업데이트 및 사용자를 사용 하지 않도록 설정 하려면 Azure AD 프로 비전 서비스를 구성 하는 단계 및/또는 안내 그룹 다이얼에서 Azure AD에서 사용자 및/또는 그룹 할당에 기반 합니다.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Azure AD에서 다이얼에 대 한 자동 사용자 프로 비전을 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 선택 **엔터프라이즈 응용 프로그램**을 선택한 후 **모든 응용 프로그램**합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 선택 **다이얼**합니다.

    ![응용 프로그램 목록의 다이얼 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

5. 아래는 **관리자 자격 증명** 섹션에서 입력 `https://dialpad.com/scim` 에서 **테 넌 트 URL**합니다. 검색 및에서 다이얼에서 이전에 저장 하는 값 입력 **비밀 토큰**합니다. 클릭 **연결 테스트** Azure를 확인 하려면 AD 다이얼에 연결할 수 있습니다. 연결이 실패 다이얼 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![테 넌 트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 전자 메일](common/provisioning-notification-email.png)

7. **Save**을 클릭합니다.

8. 아래는 **매핑을** 섹션에서 **Azure Active Directory 사용자를 동기화 다이얼**합니다.

    ![다이얼 사용자 매핑](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. 다이얼에서 Azure AD에서 동기화 된 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. 으로 선택한 특성 **일치** 속성은 다이얼의 업데이트 작업에 대 한 사용자 계정을 일치 하는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![다이얼 사용자 특성](media/dialpad-provisioning-tutorial/dialpad07.png)

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. 프로 비전 서비스 다이얼을 Azure AD를 사용 하도록 설정 하려면 변경를 **프로 비전 상태** 에 **에** 에 **설정** 섹션입니다.

    ![프로 비전 상태에서 전환](common/provisioning-toggle-on.png)

12. 원하는 값을 선택 하 여 다이얼에 프로 비전 할 사용자 및/또는 하려는 그룹을 정의할 **범위** 에 **설정** 섹션입니다.

    ![프로 비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로 비전 구성을 저장 합니다.](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. 사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 링크를 프로 비전 활동 보고서를 프로 비전 서비스 다이얼에서 Azure AD에서 수행한 모든 작업을 설명 하는 섹션입니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대 한 자세한 내용은 참조 하세요. [자동 사용자 계정 프로 비전에 대 한 보고](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>커넥터 제한 사항
* 다이얼 그룹 이름 바꾸기를 현재 지원 하지 않습니다. 즉, 변경 된 **displayName** Azure에서 그룹의 AD 업데이트 및 다이얼에 반영 하지 것입니다.

## <a name="additional-resources"></a>추가 자료

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
