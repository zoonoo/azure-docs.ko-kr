---
title: Azure Active Directory를 사용하여 개발자 계정에 권한 부여 - Azure API Management | Microsoft Docs
description: API Management에서 Azure Active Directory를 사용하여 사용자에게 권한을 부여하는 방법을 알아봅니다.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d267ff3a43438d9fe6e4e21f0ac023cfa6675f19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956311"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한 부여

이 아티클에서는 Azure AD(Azure Active Directory)의 사용자에게 개발자 포털에 액세스할 수 있도록 하는 방법을 보여줍니다. 또한 이 가이드에서는 사용자를 포함하는 외부 그룹을 추가하여 Azure AD 사용자 그룹을 관리하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

- 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
- Azure API Management 인스턴스를 가져오고 게시합니다. 자세한 내용은 [가져오기 및 게시](import-and-publish.md)를 참조하세요.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD를 사용하여 개발자 계정에 권한 부여

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 선택 ![화살표](./media/api-management-howto-aad/arrow.png).
3. 검색 상자에 **api**를 입력합니다.
4. **API Management 서비스**를 선택합니다.
5. API Management 서비스 인스턴스를 선택합니다.
6. 아래 **Security**를 선택 **Identities**합니다.
7. 위에서 **+추가**를 선택합니다.

    **ID 공급자 추가** 창이 오른쪽에 나타납니다.
8. **공급자 형식** 아래에서 **Azure Active Directory**를 선택합니다.

    기타 필요한 정보를 입력할 수 있는 컨트롤이 창에 나타납니다. 제어에는 **클라이언트 ID** 및 **클라이언트 암호**가 포함됩니다. (이러한 컨트롤에 대한 정보는 이 아티클의 뒷부분에 다룹니다.)
9. 내용을 기록해 **리디렉션 URL**합니다.
    
   ![Azure Portal에서 ID 공급자를 추가하는 단계](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. 브라우저에서 다른 탭을 엽니다. 
11. 로 이동 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) Active Directory에서 앱을 등록 합니다.
12. 아래 **관리**를 선택 **앱 등록**합니다.
13. **새 등록**을 선택합니다. 에 **응용 프로그램을 등록** 페이지에서 값을 다음과 같이 설정 합니다.
    
* 설정할 **이름을** 의미 있는 이름입니다. e.g., *developer-portal*
* 설정 **지원 되는 계정 유형** 하 **만이 조직 디렉터리의 계정**합니다. 
* 설정할 **리디렉션 URI** 9 단계에서 가져온 값입니다. 
* 선택할 **등록**합니다. 

14.  응용 프로그램을 등록 한 후 복사 합니다 **(클라이언트) 응용 프로그램 ID** 에서 합니다 **개요** 페이지입니다. 
15. API Management 인스턴스를 다시 돌아갑니다. 에 **id 공급자 추가** 창에 붙여 넣습니다를 **(클라이언트) 응용 프로그램 ID** 값을 **클라이언트 ID** 상자.
16. Azure AD 구성에서는 선택으로 전환 **인증서 및 비밀** 아래에서 **관리**합니다. 선택 된 **새 클라이언트 암호** 단추입니다. 에 값을 입력 **설명을**에 대 한 옵션을 선택 **Expires** 선택한 **추가**합니다. 페이지를 나가기 전에 클라이언트 비밀 값을 복사 합니다. 이는 다음 단계에서 필요합니다. 
17. 아래 **관리**를 선택 **인증** 선택한 후 **ID 토큰** 아래 **암시적 권한 부여**
18. API Management 인스턴스로 돌아가서, 암호를 붙여 넣고 합니다 **클라이언트 암호** 상자입니다.

    > [!IMPORTANT]
    > 키가 만료되기 전에 **클라이언트 암호**를 업데이트해야 합니다. 
    >  
    >

19. **ID 공급자 추가** 창에는 **허용된 테넌트** 텍스트 상자도 포함됩니다. 여기에서 API Management 서비스 인스턴스의 API에 대한 액세스 권한을 부여하려는 Azure AD 인스턴스의 도메인을 지정합니다. 줄바꿈, 공백 또는 쉼표로 여러 도메인을 구분할 수 있습니다.

> [!NOTE]
> **허용된 테넌트** 섹션에서 여러 도메인을 지정할 수 있습니다. 사용자가 애플리케이션이 등록되었던 원래 도메인이 아닌 다른 도메인에서 로그인하려면, 다른 도메인의 전역 관리자가 디렉터리 데이터에 액세스할 수 있도록 애플리케이션에 권한을 부여해야 합니다. 전역 관리자는 권한을 부여 하려면:를 합니다. `https://<URL of your developer portal>/aadadminconsent`(예: https://contoso.portal.azure-api.net/aadadminconsent) 로 이동합니다.
> b. 액세스 권한을 부여하려는 Azure AD 테넌트의 도메인 이름을 입력합니다.
> c. **제출**을 선택합니다. 

20.  원하는 구성을 지정한 후에 **추가**를 선택합니다.

변경 내용이 저장되면 지정된 Azure AD 인스턴스의 사용자는 [Azure AD 계정을 사용하여 개발자 포털에 로그인](#log_in_to_dev_portal)의 단계를 수행하여 개발자 포털에 로그인할 수 있습니다.

## <a name="add-an-external-azure-ad-group"></a>외부 Azure AD 그룹 추가

Azure AD 인스턴스에서 사용자에 대한 액세스를 활성화한 후에 API Management에서 Azure AD 그룹을 추가할 수 있습니다. 그런 다음, 원하는 제품과 그룹 개발자의 연결을 보다 쉽게 관리할 수 있습니다.

 > [!IMPORTANT]
 > 외부를 추가 하려면 Azure AD 그룹을 먼저 구성 해야 Azure AD 인스턴스로에 **Identities** 이전 섹션의 절차를 수행 하 여 탭 합니다. 또한 응용 프로그램 권한을 부여 해야 사용 하 여 Azure AD Graph api `Directory.Read.All` 권한. 

API Management 인스턴스의 **그룹** 탭에 외부 Azure AD 그룹을 추가합니다.

1. **그룹** 탭을 선택합니다.
2. **AAD 그룹 추가** 단추를 선택합니다.
   !["AAD 그룹 추가" 단추](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 추가하려는 그룹을 선택합니다.
4. **선택** 단추를 누릅니다.

외부 Azure AD 그룹을 추가한 후에 해당 속성을 검토하고 구성할 수 있습니다. 그룹의 이름을 **그룹** 탭에서 선택합니다. 이 탭에서 그룹에 대한 **이름** 및 **설명** 정보를 편집할 수 있습니다.
 
이제 구성된 Azure AD 인스턴스의 사용자는 개발자 포털에 로그인할 수 있습니다. 표시 유형을 갖고 있는 모든 그룹을 확인하고 여기에 가입할 수 있습니다.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Azure AD 계정을 사용하여 개발자 포털에 로그인

이전 섹션에서 구성된 Azure AD 계정을 사용하여 개발자 포털에 로그인하려면:

1. Active Directory 애플리케이션 구성의 로그인 URL을 사용하여 새 브라우저 창을 열고, **Azure Active Directory**를 선택합니다.

   ![로그인 페이지][api-management-dev-portal-signin]

1. Azure AD에서 사용자의 자격 증명을 입력하고 **로그인**을 선택합니다.

   ![사용자 이름 및 암호로 로그인][api-management-aad-signin]

1. 추가 정보가 필요한 경우 등록 양식과 함께 메시지가 표시될 수 있습니다. 등록 양식을 완성하고 **등록**을 선택합니다.

   ![등록 양식의 "등록" 단추][api-management-complete-registration]

이제 사용자는 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

![등록 완료 이후 개발자 포털][api-management-registration-complete]

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
