---
title: "Azure Active Directory를 사용하여 개발자 계정에 권한 부여 - Azure API Management | Microsoft Docs"
description: "API Management에서 Azure Active Directory를 사용하여 권한을 부여하는 방법"
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법

이 가이드에서는 Azure Active Directory의 사용자에게 개발자 포털에 액세스할 수 있도록 하는 방법을 보여 줍니다. 또한 이 가이드에서는 Azure Active Directory의 사용자를 포함하는 외부 그룹을 추가하여 Azure Active Directory 사용자 그룹을 관리하는 방법을 보여줍니다.

> [!WARNING]
> Azure Active Directory 통합은 [개발자, 표준 및 프리미엄](https://azure.microsoft.com/pricing/details/api-management/) 계층에서만 사용 가능합니다.

## <a name="prerequisites"></a>필수 조건

- 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
- API Management 인스턴스를 가져오고 게시합니다. 자세한 내용은 [가져오기 및 게시](import-and-publish.md)를 참조하세요.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 여기서 ![화살표](./media/api-management-howto-aad/arrow.png)에서도 확인할 수 있습니다.
3. 검색 상자에 "api"를 입력합니다.
4. **API Management 서비스**를 클릭합니다.
5. APIM 서비스 인스턴스를 선택합니다.
6. **보안** 아래에서 **ID**를 선택합니다.

    ![외부 ID](./media/api-management-howto-aad/api-management-with-aad001.png)
7. 위에서 **+추가**를 클릭합니다.

    **ID 공급자 추가** 창이 오른쪽에 나타납니다.
8. **공급자 형식** 아래에서 **Azure Active Directory**를 선택합니다.

    기타 필요한 정보를 입력할 수 있는 컨트롤이 창에 나타납니다. 제어에는 **클라이언트 ID**, **클라이언트 암호**가 포함됩니다(자습서의 뒷부분에서 정보를 가져옴).
9. **리디렉션 URL**을 기록해 둡니다.  
10. 브라우저에서 다른 탭을 엽니다. 
11. [Azure Portal](https://portal.azure.com)을 엽니다.
12. 여기서 ![화살표](./media/api-management-howto-aad/arrow.png)에서도 확인할 수 있습니다.
13. "활성"을 입력하면 **Azure Active Directory**가 나타납니다.
14. **Azure Active Directory**를 선택합니다.
15. **관리** 아래에서 **앱 등록**을 선택합니다.

    ![앱 등록](./media/api-management-howto-aad/api-management-with-aad002.png)
16. **새 응용 프로그램 등록**을 클릭합니다.

    **만들기** 창이 오른쪽에 나타납니다. 여기에 AAD 앱 관련 정보를 입력합니다.
17. 응용 프로그램의 이름을 입력합니다.
18. 응용 프로그램 형식에서 **웹앱/API**를 선택합니다.
19. 로그온 URL에서 개발자 포털의 로그온 URL을 입력합니다. 이 예제에서 로그온 URL은 https://apimwithaad.portal.azure-api.net/signin입니다.
20. **만들기**를 클릭하여 응용 프로그램을 만듭니다.
21. 앱을 찾으려면 **앱 등록**을 선택하고 이름으로 검색합니다.

    ![앱 등록](./media/api-management-howto-aad/find-your-app.png)
22. 응용 프로그램을 등록한 후에 **회신 URL**로 이동하고 "리디렉션 URL"이 9단계에서 가져온 값으로 설정되어 있는지 확인합니다. 
23. 응용 프로그램을 구성하려는 경우(예: **앱 ID URL** 변경) **속성**을 선택합니다.

    ![앱 등록](./media/api-management-howto-aad/api-management-with-aad004.png)

    이 응용 프로그램에 여러 Azure Active Directory를 사용하려는 경우 **응용 프로그램이 다중 테넌트임**에 **예**를 클릭합니다. 기본값은 **아니요**입니다.
24. **필수 권한**을 선택하여 응용 프로그램 사용 권한을 설정합니다.
25. 응용 프로그램을 선택하고 **디렉터리 데이터 읽기** 및 **로그인 및 사용자 프로필 읽기**를 선택합니다.

    ![앱 등록](./media/api-management-howto-aad/api-management-with-aad005.png)

    응용 프로그램 및 위임된 권한에 대한 자세한 내용은 [Graph API 액세스][Accessing the Graph API]를 참조하세요.
26. 왼쪽 창에서 **응용 프로그램 ID** 값을 복사합니다.

    ![앱 등록](./media/api-management-howto-aad/application-id.png)
27. API Management 응용 프로그램으로 다시 전환합니다. **ID 공급자 추가** 창이 표시됩니다. <br/>**클라이언트 ID** 상자에 **응용 프로그램 ID** 값을 붙여넣습니다.
28. Azure Active Directory 구성으로 다시 전환하고 **키**를 클릭합니다.
29. 이름과 기간을 지정하여 새 키를 만듭니다. 
30. **저장**을 클릭합니다. 키가 생성됩니다.

    키를 클립보드에 복사합니다.

    ![앱 등록](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > 이 키를 기록해 둡니다. Azure Active Directory 구성 창을 닫으면 키를 다시 표시할 수 없습니다.
    > 
    > 
31. API Management 응용 프로그램으로 다시 전환합니다. <br/>**ID 공급자 추가** 창의 **클라이언트 암호** 텍스트 상자에 키를 붙여넣습니다.
32. **ID 공급자 추가** 창에는 **허용되는 테넌트** 텍스트 상자가 포함됩니다. 여기에서 API Management 서비스 인스턴스의 API에 액세스할 수 있는 디렉터리를 지정합니다. <br/>액세스 권한을 부여하려는 Azure Active Directory 인스턴스의 도메인을 지정합니다. 줄바꿈, 공백 또는 쉼표로 여러 도메인을 구분할 수 있습니다.

    여러 도메인은 **허용된 테넌트** 섹션에서 지정할 수 있습니다. 사용자가 응용 프로그램이 등록되었던 원래 도메인이 아닌 다른 도메인에서 로그인하려면, 다른 도메인의 전역 관리자가 디렉터리 데이터에 액세스할 수 있도록 응용 프로그램에 권한을 부여해야 합니다. 권한을 부여하려면 전역 관리자는 `https://<URL of your developer portal>/aadadminconsent`로 이동하고(예: https://contoso.portal.azure-api.net/aadadminconsent) 액세스를 지정하려는 Active Directory 테넌트의 도메인 이름을 입력하여 제출을 클릭합니다. 다음 예제에서 `miaoaad.onmicrosoft.com`의 전역 관리자는 이 특정 개발자 포털에 있는 사용 권한을 부여하려고 합니다. 

33. 원하는 구성이 지정되면 **추가**를 클릭합니다.

    ![앱 등록](./media/api-management-howto-aad/api-management-with-aad007.png)

변경 내용이 저장되면 지정된 Azure Active Directory의 사용자는 [Azure Active Directory 계정을 사용하여 개발자 포털에 로그인](#log_in_to_dev_portal)의 단계를 수행하여 개발자 포털에 로그인할 수 있습니다.

![권한](./media/api-management-howto-aad/api-management-aad-consent.png)

다음 화면에서 전역 관리자에게는 사용 권한의 부여를 확인하는 메시지가 표시됩니다. 

![권한](./media/api-management-howto-aad/api-management-permissions-form.png)

전역 관리자에게 권한이 부여되기 전에 비 전역 관리자가 로그인을 시도하는 경우, 로그인 시도에 실패하며 오류 화면이 표시됩니다.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>외부 Azure Active Directory 그룹을 추가하는 방법

Azure Active Directory의 사용자가 액세스할 수 있게 되면 Azure Active Directory 그룹을 API Management에 추가하여 원하는 제품이 있는 그룹에서 개발자와의 연계를 보다 쉽게 관리할 수 있습니다.

외부 Azure Active Directory 그룹을 구성하려면 이전 섹션의 과정을 수행하여 ID 탭에서 먼저 Azure Active Directory가 구성되어야 합니다. 

외부 Azure Active Directory 그룹을 API Management 인스턴스의 **그룹** 탭에 추가합니다.

![그룹](./media/api-management-howto-aad/api-management-with-aad008.png)

1. **그룹** 탭을 선택합니다.
2. **AAD 그룹 추가** 단추를 클릭합니다.
3. 추가하려는 그룹을 선택합니다.
4. **선택** 단추를 누릅니다.

Azure Active Directory 그룹이 생성되면 추가된 외부 그룹의 속성을 검토 및 구성하려면 **그룹** 탭에서 그룹 이름을 클릭합니다.

여기서 그룹의 **이름** 및 **설명**을 편집할 수 있습니다.
 
구성된 Azure Active Directory의 사용자는 개발자 포털에 로그인할 수 있으며 다음 섹션의 지침을 수행하여 표시 여부가 있는 그룹을 보고 구독할 수 있습니다.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Azure Active Directory 계정을 사용하여 개발자 포털에 로그인하는 방법

이전 섹션에서 구성된 Azure Active Directory 계정을 사용하여 개발자 포털에 로그인하려면 Active Directory 응용 프로그램 구성에서 **로그온 URL**을 사용하여 새 브라우저 창을 열고 **Azure Active Directory**를 클릭합니다.

![개발자 포털][api-management-dev-portal-signin]

Azure Active Directory에서 사용자 중 하나의 자격 증명을 입력하고 **로그인**을 클릭합니다.

![로그인][api-management-aad-signin]

추가 정보가 필요한 경우 등록 양식과 함께 메시지가 표시될 수 있습니다. 등록 양식을 완성하고 **등록**을 클릭합니다.

![등록][api-management-complete-registration]

사용자는 이제 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

![등록 완료][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
