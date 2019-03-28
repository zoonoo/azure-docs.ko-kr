---
title: '자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Amazon Web Services(AWS) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f58c8c3ea76cf44730a54897e6b77ef6e648a49
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901664"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합

이 자습서에서는 Amazon Web Services(AWS)를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.
AWS(Amazon Web Services)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서는 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 Azure AD 계정으로 AWS(Amazon Web Services)에 자동으로 로그인(Single Sign-On)할 수 있도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

![AWS(Amazon Web Services)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

아래와 같이 여러 인스턴스의 여러 식별자를 구성할 수 있습니다.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

이러한 값을 사용하면 Azure AD에서 **#** 값을 제거하고 올바른 값 `https://signin.aws.amazon.com/saml`을 SAML 토큰의 대상 URL로 전송합니다.

**다음과 같은 이유로 이 방법을 사용하는 것이 좋습니다.**

a. 각 응용 프로그램에는 고유한 X509 인증서가 제공되므로 각 인스턴스의 인증서 만료 날짜가 서로 다를 수 있습니다. 또한, 사용자가 개별 AWS 계정 기준에 따라 인증서를 관리할 수 있습니다. 이 경우 전체 인증서 롤오버가 쉽게 진행됩니다.

b. Azure AD에서 AWS 앱을 사용하여 사용자 프로비저닝을 사용하도록 설정하면 Azure 서비스에서 해당 AWS 계정의 모든 역할을 가져옵니다. 따라서 앱에서 AWS 역할을 수동으로 추가하거나 업데이트할 필요가 없습니다.

다. Azure AD에서 직접 앱을 관리할 수 있는 앱 소유자를 개별적으로 앱에 할당할 수 있습니다.

> [!Note]
> 갤러리 앱만 사용하는지 확인합니다.

## <a name="prerequisites"></a>필수 조건

AWS(Amazon Web Services)와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* AWS(Amazon Web Services) Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

> [!Note]
> 여러 AWS 계정을 Single Sign-On에 대한 한 Azure 계정에 통합하려는 경우 [이](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) 문서를 참조하세요.

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

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[AWS(Amazon Web Services) Single Sign-On 구성](#configure-amazon-web-services-aws-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[AWS(Amazon Web Services) 테스트 사용자 만들기](#create-amazon-web-services-aws-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 AWS(Amazon Web Services)에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **AWS(Amazon Web Services)** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 애플리케이션은 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 **저장** 단추를 클릭하여 구성을 저장해야 합니다.

    ![이미지](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. 둘 이상의 인스턴스를 구성하는 경우 식별자 값을 제공합니다. 두 번째 인스턴스에서 다음 형식으로 식별자 값을 제공합니다. **#** 부호를 사용하여 고유한 SPN 값을 지정하세요.

    `https://signin.aws.amazon.com/saml#2`

    ![AWS(Amazon Web Services) 도메인 및 URL Single Sign-On 정보](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. AWS(Amazon Web Services) 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

7. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | 이름  | 원본 특성  | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 역할            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | “900초(15분)에서 43200초(12시간) 사이의 값을 제공합니다.” |  https://aws.amazon.com/SAML/Attributes |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 텍스트 상자에 해당 행에 대해 표시되는 네임스페이스 값을 입력합니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

9. **AWS(Amazon Web Services) 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

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

    다. Azure Portal에서 다운로드한 **메타데이터 파일**을 업로드하려면 **파일 선택**을 클릭합니다.

    d. **다음 단계**를 클릭합니다.

6. **공급자 정보 확인** 대화 상자 페이지에서 **만들기**를 클릭합니다.

    ![Single Sign-On 확인 구성][15]

7. **역할**을 클릭하고 **역할 만들기**를 클릭합니다.

    ![Single Sign-On 역할 구성][16]

8. **역할 만들기** 페이지에서 다음 단계를 수행합니다.  

    ![Single Sign-On 트러스트 구성][19]

    a. **신뢰할 수 있는 엔터티 유형 선택**에서 **SAML 2.0 페더레이션**을 선택합니다.

    b. **SAML 2.0 공급 기업 선택**에서 이전에 만든 **SAML 공급 기업**을 선택합니다(예: *WAAD*).

    다. **프로그래밍 및 AWS 관리 콘솔 액세스 허용**을 선택합니다.
  
    d. **다음: 사용 권한**을 클릭합니다.

9. **권한 정책 연결** 대화 상자에서 조직에 따라 적절한 정책을 연결하세요. **다음: 검토**를 클릭합니다.  

    ![Single Sign-On 정책 구성][33]

10. **검토** 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 검토 구성][34]

    a. **역할 이름** 텍스트 상자에 역할 이름을 입력합니다.

    b. **역할 설명** 텍스트 상자에 설명을 입력합니다.

    다. **역할 만들기**를 클릭합니다.

    d. 필요한 만큼 역할을 만들어서 ID 공급자에 매핑합니다.

11. Azure AD 사용자 프로비전의 AWS 계정에서 역할을 페치하기 위해 AWS 서비스 계정 자격 증명을 사용합니다. 이 경우에 AWS 콘솔 홈을 엽니다.

12. **서비스** -> **보안, ID 및 규정 준수** -> **IAM**을 클릭합니다.

    ![AWS 계정에서 역할 페치](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. IAM 섹션에서 **정책** 탭을 선택합니다.

    ![AWS 계정에서 역할 페치](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Azure AD 사용자 프로비전의 AWS 계정에서 역할을 가져올 수 있도록 **정책 만들기**를 클릭하여 새 정책을 만듭니다.

    ![새 정책 만들기](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. 다음 단계를 수행하여 AWS 계정에서 모든 역할을 가져오는 고유한 정책을 만듭니다.

    ![새 정책 만들기](./media/amazon-web-service-tutorial/policy1.png)

    a. **"정책 만들기"** 섹션에서 **"JSON"** 탭을 클릭합니다.

    b. 정책 문서에서 아래 JSON을 추가합니다.

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    다. **정책 검토 단추**를 클릭하여 정책의 유효성을 검사합니다.

    ![새 정책 정의](./media/amazon-web-service-tutorial/policy5.png)

16. 다음 단계를 수행하여 **새 정책**을 정의합니다.

    ![새 정책 정의](./media/amazon-web-service-tutorial/policy2.png)

    a. **정책 이름**을 **AzureAD_SSOUserRole_Policy**로 제공합니다.

    b. 정책에 **이 정책을 사용하면 AWS 계정에서 역할을 페치할 수 있습니다.** 라는 **설명**을 제공할 수 있습니다.

    다. **"정책 만들기"** 단추를 클릭합니다.

17. 다음 단계를 수행하여 AWS IAM 서비스에서 새 사용자 계정을 만듭니다.

    a. AWS IAM 콘솔에서 **사용자** 탐색을 클릭합니다.

    ![새 정책 정의](./media/amazon-web-service-tutorial/policy3.png)

    b. **사용자 추가** 단추를 클릭하여 새 사용자를 만듭니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/policy4.png)

    다. **사용자 추가** 섹션에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/adduser1.png)

    * 사용자 이름을 **AzureADRoleManager**로 입력합니다.

    * 액세스 형식에서 **프로그래밍 방식 액세스** 옵션을 선택합니다. 이러한 방식으로 사용자는 API를 호출하고 AWS 계정에서 역할을 가져올 수 있습니다.

    * 오른쪽 아래 모서리에서 **다음 사용 권한** 단추를 클릭합니다.

18. 이제 다음 단계를 수행하여 이 사용자에 대한 새 정책을 만듭니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/adduser2.png)

    a. **기존 정책 직접 연결** 단추를 클릭합니다.

    b. **AzureAD_SSOUserRole_Policy** 필터 섹션에서 새로 만든 정책을 검색합니다.

    다. **정책**을 선택하고 **다음: 검토** 단추를 클릭합니다.

19. 다음 단계를 수행하여 연결된 사용자에 대한 정책을 검토합니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/adduser3.png)

    a. 사용자 이름, 액세스 형식 및 사용자에 매핑된 정책을 검토합니다.

    b. 오른쪽 아래 모서리에 있는 **사용자 만들기** 단추를 클릭하여 사용자를 만듭니다.

20. 다음 단계를 수행하여 사용자의 사용자 자격 증명을 다운로드합니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/adduser4.png)

    a. 사용자 **액세스 키 ID** 및 **암호 액세스 키**를 복사합니다.

    b. Azure AD 사용자 프로비전 섹션에 이러한 자격 증명을 입력하여 AWS 콘솔에서 역할을 페치합니다.

    다. 아래쪽에 있는 **닫기** 단추를 클릭합니다.

21. Azure AD 관리 포털에 있는 Amazon Web Services 앱의 **사용자 프로비전** 섹션으로 이동합니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/provisioning.png)

22. **클라이언트 암호** 및 **암호 토큰** 필드에 각각 **액세스 키** 및 **암호**를 입력합니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/provisioning1.png)

    a. **clientsecret** 필드에 AWS 사용자 액세스 키를 입력합니다.

    b. **암호 토큰** 필드에 AWS 사용자 암호를 입력합니다.

    다. **연결 테스트** 단추를 클릭하고 이 연결을 성공적으로 테스트할 수 있어야 합니다.

    d. 위쪽에 있는 **저장** 단추를 클릭하여 설정을 저장합니다.

23. 스위치를 켜고 상단에서 **저장** 단추를 클릭하여 설정 섹션에서 프로비전 상태 **켜기**를 사용할 수 있어야 합니다.

    ![사용자 추가](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 AWS(Amazon Web Services)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **AWS(Amazon Web Services)** 를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **AWS(Amazon Web Services)** 를 입력하고 선택합니다.

    ![애플리케이션 목록의 AWS(Amazon Web Services) 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-amazon-web-services-aws-test-user"></a>AWS(Amazon Web Services) 테스트 사용자 만들기

이 섹션에서는 AWS(Amazon Web Services)에서 Britta Simon이라는 사용자를 만듭니다. AWS(Amazon Web Services)에서는 SSO용으로 시스템에서 사용자를 만들 필요가 없으므로 여기에서 작업을 수행할 필요가 없습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 AWS(Amazon Web Services) 타일을 클릭하면 SSO를 설정하기 위한 AWS(Amazon Web Services)에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="known-issues"></a>알려진 문제

 * **프로비전** 섹션에서 **매핑** 하위 섹션에 "로드 중..." 메시지가 표시되고 특성 매핑이 표시되지 않습니다. 현재 지원되는 유일한 프로비전 워크플로는 사용자/그룹 할당 중에 선택을 위해 AWS에서 Azure AD로 역할을 가져오는 것입니다. 이와 관련된 특성 매핑은 사전 지정되어 있으며 구성할 수 없습니다.
 
 * **프로비전** 섹션에서는 한 번에 하나의 AWS 테넌트에 대한 하나의 자격 증명 집합만 입력할 수 있습니다. 가져온 모든 역할은 AWS 테넌트에 대한 Azure AD [servicePrincipal 개체](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)의 appRoles 속성에 기록됩니다. 갤러리에서 Azure AD로 프로비전에 사용할 여러 AWS 테넌트(servicePrincipals로 표현됨)를 추가할 수 있지만 프로비전에 사용되는 여러 AWS servicePrincipal에서 가져온 모든 역할을 Single Sign-On에 사용되는 단일 servicePrincipal에 자동으로 기록할 수 없다는 알려진 문제가 있습니다. 이 문제를 해결하기 위해 [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)를 사용하여 가져온 모든 appRoles를 프로비전이 구성된 각 AWS servicePrincipal에 추출할 수 있습니다. 나중에 Single Sign-On이 구성된 AWS servicePrincipal에 이러한 역할 문자열을 추가할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
