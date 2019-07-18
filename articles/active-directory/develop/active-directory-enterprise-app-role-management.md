---
title: Azure AD의 엔터프라이즈 응용 프로그램에 대해 SAML 토큰에서 발급된 역할 클레임 구성 | Microsoft Docs
description: Azure Active Directory의 엔터프라이즈 애플리케이션에 SAML 토큰에서 발급된 역할 클레임을 구성하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: jeevansd
manager: CelesteDG
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2019
ms.author: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15165bce70a9bc2fbf3eb840ca8bce4fd5073280
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544625"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 역할 클레임 구성

Azure AD(Azure Active Directory)를 사용하여 앱을 승인 후에 나타나는 응답 토큰에서 역할 클레임에 대한 클레임 유형을 지정할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 디렉터리가 설치된 Azure AD 구독
- SSO(Single Sign-On)를 사용하도록 설정된 구독 애플리케이션에 SSO를 구성해야 함

## <a name="when-to-use-this-feature"></a>이 기능을 사용하는 시기

애플리케이션에서 사용자 지정 역할이 SAML 응답에 전달될 것으로 예상하는 경우 이 기능을 사용해야 합니다. Azure AD에서 다시 애플리케이션으로 전달하는 데 필요한 만큼 역할을 만들 수 있습니다.

## <a name="create-roles-for-an-application"></a>애플리케이션에 대한 역할 만들기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다.

    ![Azure Active Directory 아이콘][1]

2. **Enterprise 애플리케이션**을 선택합니다. 그런 다음, **모든 애플리케이션**을 선택합니다.

    ![Enterprise 애플리케이션 창][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 선택합니다.

    !["새 애플리케이션" 단추][3]

4. 검색 상자에 애플리케이션 이름을 입력한 다음, 결과 패널에서 애플리케이션을 선택합니다. **추가** 단추를 선택하여 애플리케이션을 추가합니다.

    ![결과 목록의 애플리케이션](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. 애플리케이션이 추가되면 **속성** 페이지로 이동하여 개체 ID를 복사합니다.

    ![속성 페이지](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 또 다른 창에서 [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 열고 다음 단계를 수행합니다.

    a. 테넌트의 전역 관리자 또는 공동 관리자 자격 증명을 사용하여 Graph Explorer 사이트에 로그인합니다.

    b. 역할을 만들 수 있는 충분한 권한이 필요합니다. **권한 수정**을 선택하여 권한을 얻을 수 있습니다.

      !["권한 수정" 단추](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. 목록에서 다음 권한을 선택하고(아직 선택하지 않은 경우) **권한 수정**을 선택합니다.

      ![권한 목록 및 "권한 수정" 단추](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > 클라우드 앱 관리자 및 앱 관리자 역할은 디렉터리 읽기 및 쓰기에 대한 글로벌 관리자 권한이 필요하므로 이 시나리오에서는 작동하지 않습니다.

    d. 동의 확인에 동의합니다. 시스템에 다시 로그인됩니다.

    e. 버전을 **베타**로 변경하고 다음 쿼리를 사용하여 테넌트에서 서비스 사용자 목록을 가져옵니다.

     `https://graph.microsoft.com/beta/servicePrincipals`

      여러 디렉터리를 사용하는 경우 이 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` 패턴을 따릅니다.

      ![서비스 사용자 가져오기에 대한 쿼리가 있는 Graph Explorer 대화 상자](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > 이미 API를 업그레이드하는 중이므로 고객은 서비스 중단을 확인할 수 있습니다.

    f. 가져온 서비스 사용자 목록에서 수정해야 하는 서비스 사용자를 가져옵니다. Ctrl+F를 사용하여 나열된 모든 서비스 사용자에서 애플리케이션을 검색할 수도 있습니다. **속성** 페이지에서 복사한 개체 ID를 검색하고, 다음 쿼리를 사용하여 각 서비스 사용자로 이동합니다.

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![수정해야 하는 서비스 사용자 가져오기에 대한 쿼리](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. 서비스 사용자 개체에서 **appRoles** 속성을 추출합니다.

      ![appRoles 속성의 세부 정보](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > 사용자 지정 앱(Azure Marketplace 앱 제외)을 사용할 경우 사용자와 msiam_access 등의 두 기본 역할이 표시됩니다. Marketplace 앱의 경우 기본 역할은 msiam_access뿐입니다. 기본 역할은 변경할 필요가 없습니다.

    h. 애플리케이션에 대한 새 역할을 생성합니다.

      다음 JSON은 **appRoles** 개체의 예제입니다. 애플리케이션에 역할을 추가할 유사한 개체를 만듭니다.

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > 패치 작업에 대한 msiam_access 다음에만 새 역할을 추가할 수 있습니다. 또한 조직 요구에 따라 원하는 만큼의 역할을 추가할 수 있습니다. Azure AD는 SAML 응답의 클레임 값으로 이 역할의 값을 보냅니다. 새 역할의 ID에 대한 GUID 값을 생성하려면 [이](https://www.guidgenerator.com/)와 같은 웹 도구를 사용합니다.

    i. Graph Explorer로 돌아가 메서드를 **GET**에서 **PATCH**로 변경합니다. 이전 예제의 것과 같이 **appRoles** 속성을 업데이트하여 원하는 역할을 갖도록 서비스 사용자 개체를 패치합니다. **쿼리 실행**을 선택하여 패치 작업을 실행합니다. 성공 메시지가 나타나 역할 생성을 확인합니다.

      ![성공 메시지가 있는 패치 작업](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. 서비스 사용자에 더 많은 역할이 패치되면 각 역할에 사용자를 할당할 수 있습니다. 포털로 이동하고 애플리케이션을 검색하여 사용자를 할당할 수 있습니다. **사용자 및 그룹** 탭을 선택합니다. 이 탭은 앱에 이미 할당된 모든 사용자 및 그룹을 나열합니다. 새 역할에 새 사용자를 추가할 수 있습니다. 또한 기존 사용자를 선택하고 **편집**을 선택하여 역할을 변경할 수도 있습니다.

    !["사용자 및 그룹" 탭](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    사용자에게 역할을 할당하려면 새 역할을 선택하고 페이지 아래의 **할당** 단추를 선택합니다.

    !["할당 편집" 및 "역할 선택" 창](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > 새 역할을 확인하려면 Azure Portal에서 세션을 새로 고쳐야 합니다.

8. **특성** 테이블을 업데이트하여 역할 클레임의 사용자 지정된 매핑을 정의합니다.

9. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | 특성 이름 | 특성 값 |
    | -------------- | ----------------|
    | 역할 이름  | user.assignedroles |

    >[!NOTE]
    >역할 클레임 값이 null 인 경우 다음 Azure AD는 보내지이 값이 토큰에 및 디자인에 따라 기본입니다.

    a. 클릭 **편집할** 열려면 아이콘 **사용자 특성 및 클레임** 대화 합니다.

      !["특성 추가" 단추](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. 에 **사용자 클레임을 관리할** 대화 상자에서 클릭 하 여 SAML 토큰 특성을 추가할 **추가 새 클레임**.

      !["특성 추가" 단추](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      !["특성 추가" 창](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. **이름** 상자에서 필요에 따라 특성 이름을 입력합니다. 이 예에서는 클레임 이름으로 **역할 이름**을 사용합니다.

    d. **네임스페이스** 상자를 비워 둡니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **저장**을 선택합니다.

10. ID 공급자로 시작한 Single Sign-On에서 애플리케이션을 테스트하려면 [액세스 패널](https://myapps.microsoft.com)에 로그인하고 애플리케이션 타일을 선택합니다. SAML 토큰에서 지정한 클레임 이름의 사용자에게 할당된 모든 역할이 표시됩니다.

## <a name="update-an-existing-role"></a>기존 역할 업데이트

기존 역할을 업데이트하려면 다음 단계를 수행합니다.

1. [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 엽니다.

2. 테넌트의 전역 관리자 또는 공동 관리자 자격 증명을 사용하여 Graph Explorer 사이트에 로그인합니다.

3. 버전을 **베타**로 변경하고 다음 쿼리를 사용하여 테넌트에서 서비스 사용자 목록을 가져옵니다.

    `https://graph.microsoft.com/beta/servicePrincipals`

    여러 디렉터리를 사용하는 경우 이 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` 패턴을 따릅니다.

    ![서비스 사용자 가져오기에 대한 쿼리가 있는 Graph Explorer 대화 상자](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. 가져온 서비스 사용자 목록에서 수정해야 하는 서비스 사용자를 가져옵니다. Ctrl+F를 사용하여 나열된 모든 서비스 사용자에서 애플리케이션을 검색할 수도 있습니다. **속성** 페이지에서 복사한 개체 ID를 검색하고, 다음 쿼리를 사용하여 각 서비스 사용자로 이동합니다.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![수정해야 하는 서비스 사용자 가져오기에 대한 쿼리](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. 서비스 사용자 개체에서 **appRoles** 속성을 추출합니다.

    ![appRoles 속성의 세부 정보](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. 기존 역할을 업데이트하려면 다음 단계를 사용합니다.

    !["설명" 및 "표시 이름"이 강조 표시된 "패치"에 대한 요청 본문](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. 메서드를 **GET**에서 **PATCH**로 변경합니다.

    b. 기존 역할을 복사하여 **요청 본문** 아래에 붙여넣습니다.

    c. 필요에 따라 역할 설명, 역할 값 또는 역할 표시 이름을 업데이트하여 역할 값을 업데이트합니다.

    d. 모든 필요한 역할을 업데이트 한 후 **쿼리 실행**을 선택합니다.

## <a name="delete-an-existing-role"></a>기존 역할 삭제

기존 역할을 삭제하려면 다음 단계를 수행합니다.

1. 또 다른 창에서 [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 엽니다.

2. 테넌트의 전역 관리자 또는 공동 관리자 자격 증명을 사용하여 Graph Explorer 사이트에 로그인합니다.

3. 버전을 **베타**로 변경하고 다음 쿼리를 사용하여 테넌트에서 서비스 사용자 목록을 가져옵니다.

    `https://graph.microsoft.com/beta/servicePrincipals`

    여러 디렉터리를 사용하는 경우 이 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` 패턴을 따릅니다.

    ![서비스 사용자 목록 가져오기에 대한 쿼리가 있는 Graph Explorer 대화 상자](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. 가져온 서비스 사용자 목록에서 수정해야 하는 서비스 사용자를 가져옵니다. Ctrl+F를 사용하여 나열된 모든 서비스 사용자에서 애플리케이션을 검색할 수도 있습니다. **속성** 페이지에서 복사한 개체 ID를 검색하고, 다음 쿼리를 사용하여 각 서비스 사용자로 이동합니다.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![수정해야 하는 서비스 사용자 가져오기에 대한 쿼리](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. 서비스 사용자 개체에서 **appRoles** 속성을 추출합니다.

    ![서비스 사용자 개체에서 appRoles 속성의 세부 정보](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. 기존 역할을 삭제하려면 다음 단계를 사용합니다.

    ![IsEnabled가 false로 설정된 "패치"에 대한 요청 본문](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. 메서드를 **GET**에서 **PATCH**로 변경합니다.

    b. 애플리케이션에서 기존 역할을 복사하여 **요청 본문** 아래에 붙여넣습니다.

    c. 삭제하려는 역할의 **IsEnabled** 값을 **false**로 설정합니다.

    d. **쿼리 실행**을 선택합니다.

    > [!NOTE]
    > 생성된 역할에서 msiam_access 역할과 ID가 일치하는지 확인합니다.

7. 역할이 비활성화되면 **appRoles** 섹션에서 해당 역할 블록을 삭제합니다. 메서드를 **패치**로 유지하고, **쿼리 실행**을 선택합니다.

8. 쿼리를 실행하면 역할이 삭제됩니다.

    > [!NOTE]
    > 역할을 제거하려면 먼저 비활성화해야 합니다.

## <a name="next-steps"></a>다음 단계

추가 단계는 [앱 설명서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)를 참조하세요.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
