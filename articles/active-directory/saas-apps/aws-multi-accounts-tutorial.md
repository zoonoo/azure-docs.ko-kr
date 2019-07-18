---
title: '자습서: 여러 계정 연결을 위해 AWS(Amazon Web Services)와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 여러 Amazon Web Services(AWS) 계정 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 322615203d188581dd04aadeff2a08307b733d06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65738360"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>자습서: AWS(Amazon Web Services)와 여러 Azure Active Directory 계정 통합

이 자습서에서는 여러 Amazon Web Services(AWS) 계정을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

AWS(Amazon Web Services)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 AWS(Amazon Web Services)에 자동으로 로그인(Single Sign-On)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

![결과 목록의 AWS(Amazon Web Services)](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>모든 AWS 계정에 하나의 AWS 앱을 연결하는 것은 권장되는 접근 방식이 아닙니다. 대신 [이](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) 방법을 사용하여 Azure AD에 있는 여러 AWS 앱 인스턴스에 대해 여러 AWS 계정 인스턴스를 구성하는 것이 좋습니다.

**다음과 같은 이유로 이 방법을 사용하지 않는 것이 좋습니다.**

* Graph 탐색기 접근 방식을 사용하여 모든 역할을 앱에 패치해야 합니다. 매니페스트 파일 방식은 권장되지 않습니다.

* 단일 AWS 앱에 대해 1,200가지 이내의 앱 역할을 추가한 후에 앱에서 작업을 수행하면 크기와 관련된 오류가 발생한다는 것을 보고하는 고객이 확인되었습니다. 애플리케이션 개체의 경우 하드 크기 제한이 있습니다.

* 역할은 추가가 아닌 바꾸기 방식으로 계정에서 추가되므로 역할을 수동으로 업데이트해야 합니다. 또한 계정이 커지는 경우 계정 및 역할과 n x n 관계가 됩니다.

* 모든 AWS 계정은 동일한 페더레이션 메타데이터 XML 파일을 사용하며, 인증서 롤오버 시 이러한 방대한 작업을 진행하여 모든 AWS 계정에서 인증서를 동시에 업데이트해야 합니다.

## <a name="prerequisites"></a>필수 조건

AWS(Amazon Web Services)와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* AWS(Amazon Web Services) Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* AWS(Amazon Web Services)에서 **SP 및 IDP** 시작 SSO 지원

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>갤러리에서 AWS(Amazon Web Services) 추가

Azure AD에 AWS(Amazon Web Services)를 통합하도록 구성하려면 갤러리의 AWS(Amazon Web Services)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 AWS(Amazon Web Services)를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **AWS(Amazon Web Services)** 를 입력하고, 결과 패널에서 **AWS(Amazon Web Services)** 를 선택하고, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 AWS(Amazon Web Services)](common/search-new-app.png)

5. 애플리케이션이 추가되면 **속성** 페이지로 이동하여 **개체 ID**를 복사합니다.

    ![결과 목록의 AWS(Amazon Web Services)](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 AWS(Amazon Web Services) 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

AWS(Amazon Web Services)에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[AWS(Amazon Web Services) Single Sign-On 구성](#configure-amazon-web-services-aws-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 AWS(Amazon Web Services) 애플리케이션에서 Single Sign-On을 구성합니다.

**AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **AWS(Amazon Web Services)** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. 앱이 Azure와 이미 사전 통합되었으므로 사용자는 **기본 SAML 구성** 섹션에서 아무 단계도 수행할 필요가 없습니다.

    ![image](common/preintegrated.png)

5. AWS(Amazon Web Services) 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성 및 클레임** 대화 상자를 엽니다.

    ![image](common/edit-attribute.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | 이름  | 원본 특성  | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 역할            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | “900초(15분)에서 43200초(12시간) 사이의 값을 제공합니다.” |  https://aws.amazon.com/SAML/Attributes |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    c. **네임스페이스** 텍스트 상자에 해당 행에 대해 표시되는 네임스페이스 값을 입력합니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **페더레이션 메타데이터 XML**을 컴퓨터에 다운로드하고 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>AWS(Amazon Web Services) Single Sign-On 구성

1. 다른 브라우저 창에서 AWS(Amazon Web Services) 회사 사이트에 관리자로 로그인합니다.

2. **AWS 홈**을 클릭합니다.

    ![Single Sign-On 홈 구성][11]

3. **ID 및 액세스 관리**를 클릭합니다.

    ![Single Sign-On ID 구성][12]

4. **ID 공급자**를 클릭한 다음 **공급자 만들기**를 클릭합니다.

    ![Single Sign-On 공급자 구성][13]

5. **공급자 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 대화 상자 구성][14]

    a. **공급자 유형**으로 **SAML**을 선택합니다.

    b. **공급 기업 이름** 텍스트 상자에 공급 기업 이름(예: *WAAD*)을 입력합니다.

    c. Azure Portal에서 다운로드한 **메타데이터 파일**을 업로드하려면 **파일 선택**을 클릭합니다.

    d. **다음 단계**를 클릭합니다.

6. **공급자 정보 확인** 대화 상자 페이지에서 **만들기**를 클릭합니다.

    ![Single Sign-On 확인 구성][15]

7. **역할**을 클릭하고 **역할 만들기**를 클릭합니다.

    ![Single Sign-On 역할 구성][16]

8. **역할 만들기** 페이지에서 다음 단계를 수행합니다.  

    ![Single Sign-On 트러스트 구성][19]

    a. **신뢰할 수 있는 엔터티 유형 선택**에서 **SAML 2.0 페더레이션**을 선택합니다.

    b. **SAML 2.0 공급 기업 선택**에서 이전에 만든 **SAML 공급 기업**을 선택합니다(예: *WAAD*).

    c. **프로그래밍 및 AWS 관리 콘솔 액세스 허용**을 선택합니다.
  
    d. **다음: 사용 권한**을 클릭합니다.

9. **권한 정책 연결** 대화 상자에서 조직에 따라 적절한 정책을 연결하세요. **다음: 검토**를 클릭합니다.  

    ![Single Sign-On 정책 구성][33]

10. **검토** 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 검토 구성][34]

    a. **역할 이름** 텍스트 상자에 역할 이름을 입력합니다.

    b. **역할 설명** 텍스트 상자에 설명을 입력합니다.

    c. **역할 만들기**를 클릭합니다.

    d. 필요한 만큼 역할을 만들어서 ID 공급자에 매핑합니다.

11. 현재 AWS 계정에서 로그아웃하고 Azure AD에 Single Sign-On을 구성할 다른 계정으로 로그인합니다.

12. 2~10단계를 수행하여 이 계정에 대해 설정하려는 여러 역할을 만듭니다. 계정이 3개 이상인 경우 모든 계정에 같은 단계를 수행하여 해당하는 역할을 만듭니다.

13. 계정에서 역할이 모두 만들어지면 해당 계정의 **역할** 목록에 나타납니다.

    ![역할 설정](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. 모든 계정에서 모든 역할에 대해 모든 역할 ARN 및 신뢰할 수 있는 엔터티를 캡처해야 하므로 Azure AD 애플리케이션에 수동으로 매핑해야 합니다.

15. 역할을 클릭하여 **역할 ARN** 및 **신뢰할 수 있는 엔터티** 값을 복사합니다. Azure AD에서 만들어야 하는 모든 역할에 대해 이 값이 필요합니다.

    ![역할 설정](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. 모든 계정의 모든 역할에 대해 위의 단계를 수행하고 메모장에 모든 항목을 **역할 ARN, 신뢰할 수 있는 엔터티** 형식으로 저장합니다.

17. 또 다른 창에서 [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 엽니다.

    a. 테넌트의 전역 관리자/공동 관리자 자격 증명을 사용하여 Graph Explorer 사이트에 로그인합니다.

    b. 역할을 만들 수 있는 권한이 필요합니다. **권한 수정**을 클릭하여 필요한 권한을 얻을 수 있습니다.

    ![Graph Explorer 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. 목록에서 다음 권한을 선택하고(아직 선택하지 않은 경우) "권한 수정"을 클릭합니다. 

    ![Graph Explorer 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. 여기서는 다시 로그인하고 동의할 것을 요청합니다. 동의 후에는 Graph Explorer에 다시 로그인됩니다.

    e. 버전 드롭다운을 **베타**로 변경합니다. 테넌트로부터 모든 서비스 사용자를 가져오려면 다음 쿼리를 사용합니다.

    `https://graph.microsoft.com/beta/servicePrincipals`

    여러 디렉터리를 사용하는 경우 다음 패턴을 사용할 수 있습니다. 여기서는 주 도메인이 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`에 있습니다.

    ![Graph Explorer 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. 가져온 서비스 사용자 목록에서 수정할 서비스 사용자를 가져옵니다. Ctrl+F를 사용하여 나열된 모든 ServicePrincipals에서 애플리케이션을 검색할 수도 있습니다. Azure AD 속성 페이지에서 복사한 **개체 ID**를 사용하여 다음 쿼리를 통해 해당하는 서비스 사용자로 이동할 수 있습니다.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Graph Explorer 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. 서비스 사용자 개체에서 appRoles 속성을 추출합니다.

    ![Graph Explorer 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. 이제 애플리케이션에 대한 새 역할을 생성해야 합니다. 

    i. 다음 JSON은 appRoles 개체의 예입니다. 애플리케이션에 역할을 추가할 유사한 개체를 만듭니다.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > 패치 작업에 대한 **msiam_access** 다음에만 새 역할을 추가할 수 있습니다. 또한 조직 요구에 따라 원하는 만큼의 역할을 추가할 수 있습니다. Azure AD는 SAML 응답의 클레임 값으로 이 역할의 **값**을 보냅니다.

    j. Graph Explorer로 돌아가 메서드를 **GET**에서 **PATCH**로 변경합니다. 위 예제의 것과 유사하게 appRoles 속성을 업데이트하여 원하는 역할을 갖도록 서비스 사용자 개체를 패치합니다. **쿼리 실행**을 클릭하여 패치 작업을 실행합니다. Amazon Web Services 애플리케이션에 대한 역할이 만들어졌음을 확인하는 성공 메시지가 표시됩니다.

    ![Graph Explorer 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. 서비스 사용자에 더 많은 역할이 패치되면 각 역할에 사용자/그룹을 할당할 수 있습니다. 이 작업을 수행하려면 포털로 이동한 후 Amazon Web Services 애플리케이션으로 이동합니다. 맨 위에서 **사용자 및 그룹** 탭을 클릭합니다.

19. 해당 그룹에 특정 역할을 할당할 수 있도록 모든 AWS 역할에 대해 새 그룹을 만드는 것이 좋습니다. 하나의 그룹과 하나의 역할에 대한 일대일 매핑입니다. 그런 다음, 해당 그룹에 속하는 멤버를 추가할 수 있습니다.

20. 그룹을 만든 후에는 그룹을 선택하여 애플리케이션에 할당합니다.

    ![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > 그룹을 할당할 때 중첩 그룹은 지원되지 않습니다.

21. 그룹에 역할을 할당하려면 역할을 선택하고 페이지 아래의 **할당** 단추를 클릭합니다.

    ![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > 새 역할을 확인하려면 Azure Portal에서 세션을 새로 고쳐야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 AWS(Amazon Web Services) 타일을 클릭하면 역할 선택 옵션이 없는 AWS(Amazon Web Services) 애플리케이션 페이지가 나타납니다.

![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

SAML 응답에서도 클레임으로 전달된 역할을 확인할 수 있습니다.

![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
