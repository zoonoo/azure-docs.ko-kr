---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Federated Directory 구성 | Microsoft Docs'
description: 사용자 계정을 Federated Directory로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359802"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 Federated Directory 구성

이 자습서에서는 사용자 및/또는 그룹을 Federated Directory로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Federated Directory 및 Azure AD에서 수행하는 단계를 보여 줍니다.

> [!NOTE]
>  이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트.
* [Federated Directory](https://www.federated.directory/pricing)
* 관리자 권한이 있는 Federated Directory의 사용자 계정

## <a name="assign-users-to-federated-directory"></a>Federated Directory에 사용자 할당
Azure Active Directory는 '할당'이라는 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 애플리케이션에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Federated Directory에 액세스해야 하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Federated Directory에 할당할 수 있습니다.

 * [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>사용자를 Federated Directory에 할당하기 위한 주요 팁
 * 단일 Azure AD 사용자를 Federated Directory에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Federated Directory에 할당할 때 할당 대화 상자에서 유효한 애플리케이션 특정 역할(사용 가능한 경우)을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비전에서 제외됩니다.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>프로비저닝을 수행하도록 Federated Directory 설정

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Federated Directory를 구성하려면 먼저 Federated Directory에서 SCIM 프로비저닝을 사용하도록 설정해야 합니다.

1. [Federated Directory 관리 콘솔](https://federated.directory/of)에 로그인합니다.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="회사 이름을 입력하기 위한 필드를 보여 주는 Federated Directory 관리 콘솔의 스크린샷. 로그인 단추도 표시되어 있습니다." border="false":::

2. **디렉터리 > 사용자 디렉터리** 로 차례로 이동하고, 테넌트를 선택합니다. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Directories 및 Federated Directory Azure AD Test가 강조 표시된 Federated Directory 관리 콘솔의 스크린샷" border="false":::

3.  영구 전달자 토큰을 생성하려면 **Directory Keys(디렉터리 키) > Create New Key(새 키 만들기)** 로 차례로 이동합니다. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Federated Directory 관리 콘솔의 Directory keys 페이지에 대한 스크린샷. Create new key 단추가 강조 표시되어 있습니다." border="false":::

4. 디렉터리 키를 만듭니다. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Name(이름) 및 Description(설명) 필드와 Create key(키 만들기) 단추가 있는 Federated Directory 관리 콘솔의 Create directory key(디렉터리 키 만들기) 페이지에 대한 스크린샷" border="false":::
    

5. **액세스 토큰** 값을 복사합니다. 이 값은 Azure Portal에 있는 Federated Directory 애플리케이션에 대한 [프로비저닝] 탭의 **비밀 토큰** 필드에 입력됩니다. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Federated Directory 관리 콘솔의 페이지에 대한 스크린샷. 액세스 토큰 자리 표시자, 키 이름, 설명 및 발급자가 표시되어 있습니다." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>갤러리에서 Federated Directory 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Federated Directory를 구성하려면 Azure AD 애플리케이션 갤러리에서 Federated Directory를 관리형 SaaS 애플리케이션 목록에 추가해야 합니다.

**Azure AD 애플리케이션 갤러리에서 Federated Directory를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 창 위쪽에서 **새 애플리케이션** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Federated Directory** 를 입력하고, 결과 패널에서 **Federated Directory** 를 선택합니다.

    ![결과 목록의 Federated Directory](common/search-new-app.png)

5. 별도의 브라우저에서 아래에 강조 표시된 **URL** 로 이동합니다. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Federated Directory에 대한 정보를 표시하는 Azure Portal의 페이지에 대한 스크린샷. URL 값이 강조 표시되어 있습니다." border="false":::

6. **LOG IN(로그인)** 을 클릭합니다.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Federated Directory 사이트의 주 메뉴에 대한 스크린샷. Log in(로그인) 단추가 강조 표시되어 있습니다." border="false":::

7.  Federated Directory는 OpenIDConnect 앱이므로 Microsoft 회사 계정을 사용하여 Federated Directory에 로그인하도록 선택합니다.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Federated Directory 사이트의 SCIM AD Test(SCIM AD 테스트) 페이지에 대한 스크린샷. Log in with your Microsoft account(Microsoft 회사 계정으로 로그인)가 강조 표시되어 있습니다." border="false":::
 
8. 인증에 성공하면 동의 페이지에 대한 동의 확인 프롬프트를 수락합니다. 그러면 애플리케이션이 테넌트에 자동으로 추가되고 Federated Directory 계정으로 리디렉션됩니다.

    ![Federated Director SCIM 추가](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Federated Directory에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Federated Directory에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Azure AD에서 Federated Directory에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Federated Directory** 를 선택합니다.

    ![애플리케이션 목록의 Federated Directory 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래의 테넌트 URL에 `https://api.federated.directory/v2/`를 입력합니다. 이전에 Federated Directory에서 검색하고 저장한 값을 **비밀 토큰** 에 입력합니다. **연결 테스트** 를 클릭하여 Azure AD에서 Federated Directory에 연결할 수 있는지 확인합니다. 연결이 실패하면 Federated Directory 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

8. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

9. **저장** 을 클릭합니다.

10. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Federated Directory에 동기화** 를 선택합니다.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="매핑 섹션의 스크린샷. 이름 아래에 Azure Active Directory 사용자를 Federated Directory에 동기화가 강조 표시되어 있습니다." border="false":::
    
    
11. **특성 매핑** 섹션에서 Azure AD에서 Federated Directory로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Federated Directory의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="특성 매핑 페이지의 스크린샷. 테이블에 Azure Active Directory 특성, Federated Directory 특성 및 일치하는 상태가 나열되어 있습니다." border="false":::
    

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Azure AD 프로비저닝 서비스를 Federated Directory에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Federated Directory에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상황을 모니터링하고, Federated Directory의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 작업 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.
## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
