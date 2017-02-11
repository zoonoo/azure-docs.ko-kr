---
title: "Azure API 관리에서 Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법"
description: "API 관리에서 Azure Active Directory를 사용하여 권한을 부여하는 방법"
services: api-management
documentationcenter: API Management
author: steved0x
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d1b2f7b798e4cabfaa604358c2a380e8ed04fc6


---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Azure API 관리에서 Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법
## <a name="overview"></a>개요
이 가이드에서는 하나 이상의 Azure Active Directory의 모든 사용자에게 개발자 포털에 액세스할 수 있도록 하는 방법을 보여줍니다. 또한 이 가이드에서는 Azure Active Directory의 사용자를 포함하는 외부 그룹을 추가하여 Azure Active Directory 사용자 그룹을 관리하는 방법을 보여줍니다.

> 이 가이드의 단계를 완료하려면 먼저 응용 프로그램을 만들 Azure Active Directory가 있어야 합니다.
> 
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법
시작하려면 Azure Portal에서 API Management 서비스에 대한 **게시자 포털**을 클릭합니다. API 관리 게시자 포털로 이동됩니다.

![게시자 포털][api-management-management-console]

> 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [Azure API Management 시작][API 관리 서비스 인스턴스 만들기] 자습서에서 [API Management 서비스 인스턴스 만들기][API Management 서비스 인스턴스 만들기]를 참조하세요.
> 
> 

왼쪽의 **API Management** 메뉴에서 **보안**을 클릭하고 **외부 ID**를 클릭합니다.

![외부 ID][api-management-security-external-identities]

**Azure Active Directory**를 클릭합니다. **리디렉션 URL** 을 기록해 두고 Azure 클래식 포털에서 Azure Active Directory로 전환합니다.

![외부 ID][api-management-security-aad-new]

**추가** 단추를 클릭하여 새 Azure Active Directory 응용 프로그램을 만들고 **내 조직에서 개발 중인 응용 프로그램 추가**를 선택합니다.

![새 Azure Active Directory 응용 프로그램 추가][api-management-new-aad-application-menu]

응용 프로그램의 이름을 입력하고 **웹 응용 프로그램 및/또는 Web API**를 선택한 후 다음 단추를 클릭합니다.

![새 Azure Active Directory 응용 프로그램][api-management-new-aad-application-1]

**로그온 URL**에는 개발자 포털의 로그온 URL을 입력합니다. 이 예제에서 **로그온 URL**은 `https://aad03.portal.current.int-azure-api.net/signin`입니다. 

**앱 ID URL**의 경우 Azure Active Directory에서 기본 도메인 또는 사용자 지정 도메인을 입력하고 고유 문자열을 추가합니다. 이 예제에서 **https://contoso5api.onmicrosoft.com**의 기본 도메인은 지정된 **/api** 접미사와 함께 사용됩니다.

![새 Azure Active Directory 응용 프로그램 속성][api-management-new-aad-application-2]

확인 단추를 클릭하여 새 응용 프로그램을 저장하여 만들고 **구성** 탭으로 전환하여 새 응용 프로그램을 구성합니다.

![새 Azure Active Directory 응용 프로그램 만들어짐][api-management-new-aad-app-created]

이 응용 프로그램에 여러 Azure Active Directory를 사용하려는 경우 **응용 프로그램이 다중 테넌트임**에 **예**를 클릭합니다. 기본값은 **아니요**입니다.

![예][api-management-aad-app-multi-tenant]

게시자 포털의 **외부 ID** 탭에 있는 **Azure Active Directory** 섹션에서 **리디렉션 URL**을 복사하여 **회신 URL** 텍스트 상자에 붙여 넣습니다. 

![회신 URL][api-management-aad-reply-url]

구성 탭의 아래쪽으로 스크롤하고 **응용 프로그램 권한** 드롭다운을 선택하고 **디렉터리 데이터 읽기**를 선택합니다.

![응용 프로그램 권한][api-management-aad-app-permissions]

**권한 위임** 드롭다운을 선택하고 **로그온 사용 및 사용자 프로필 읽기**를 선택합니다.

![위임된 권한][api-management-aad-delegated-permissions]

> 응용 프로그램 및 위임된 권한에 대한 자세한 내용은 [Graph API 액세스][그래프 API 액세스]를 참조하세요.
> 
> 

**클라이언트 ID** 를 클립보드에 복사합니다.

![클라이언트 ID][api-management-aad-app-client-id]

게시자 포털로 다시 전환하고 Azure Active Directory 응용 프로그램 구성에서 복사한 **클라이언트 ID** 를 붙여넣습니다.

![클라이언트 ID][api-management-client-id]

Azure Active Directory 구성으로 다시 전환하고 **키** 섹션에서 **기간 선택** 드롭다운 목록을 클릭하여 간격을 지정합니다. 이 예제에서는 **1년** 을 사용합니다.

![키][api-management-aad-key-before-save]

**저장** 을 클릭하여 구성을 저장하고 키를 표시합니다. 키를 클립보드에 복사합니다.

> 이 키를 기록해 둡니다. Azure Active Directory 구성 창을 닫으면 키를 다시 표시할 수 없습니다.
> 
> 

![키][api-management-aad-key-after-save]

게시자 포털로 다시 전환하고 키를 **클라이언트 암호** 텍스트 상자에 붙여 넣습니다.

![클라이언트 암호][api-management-client-secret]

**허용된 테넌트** 는 어느 디렉터리를 API 관리 서비스 인스턴스의 API에 액세스할지 지정합니다. 액세스 권한을 부여하려는 Azure Active Directory 인스턴스의 도메인을 지정합니다. 줄바꿈, 공백 또는 쉼표로 여러 도메인을 구분할 수 있습니다.

![허용된 테넌트][api-management-client-allowed-tenants]

여러 도메인은 **허용된 테넌트** 섹션에서 지정할 수 있습니다. 사용자가 응용 프로그램이 등록되었던 원래 도메인이 아닌 다른 도메인에서 로그인하려면, 다른 도메인의 전역 관리자가 디렉터리 데이터에 액세스할 수 있도록 응용 프로그램에 권한을 부여해야 합니다. 권한을 부여하려면 전역 관리자가 응용 프로그램에 로그인하여 **수락**을 클릭해야 합니다. 다음 예제에서는 `miaoaad.onmicrosoft.com` 이(가) **허용된 테넌트** 에 추가되었으며 해당 도메인의 전역 관리자가 처음으로 로그인하고 있습니다.

![권한][api-management-permissions-form]

> 전역 관리자에게 권한이 부여되기 전에 비 전역 관리자가 로그인을 시도하는 경우, 로그인 시도에 실패하며 오류 화면이 표시됩니다.
> 
> 

원하는 구성이 지정되면 **저장**을 클릭합니다.

![저장][api-management-client-allowed-tenants-save]

변경 내용이 저장되면 [Azure Active Directory 계정을 사용하여 개발자 포털에 로그인][Azure Active Directory 계정을 사용하여 개발자 포털에 로그인]의 단계를 수행하여 지정된 Azure Active Directory의 사용자가 개발자 포털에 로그인할 수 있습니다.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>외부 Azure Active Directory 그룹을 추가하는 방법
Azure Active Directory의 사용자가 액세스할 수 있게 되면 Azure Active Directory 그룹을 API 관리에 추가하여 원하는 제품이 있는 그룹에서 개발자와의 연계를 보다 쉽게 관리할 수 있습니다.

> 외부 Azure Active Directory 그룹을 구성하려면 이전 섹션의 과정을 수행하여 ID 탭에서 먼저 Azure Active Directory가 구성되어야 합니다. 
> 
> 

그룹에 대한 액세스 권한을 부여하려는 제품의 **표시 여부** 탭에 외부 Azure Active Directory 그룹이 추가됩니다. **제품**을 클릭한 다음 원하는 제품의 이름을 클릭합니다.

![제품 구성][api-management-configure-product]

**표시 여부** 탭으로 전환하고 **Azure Active Directory에서 그룹 추가**를 클릭합니다.

![그룹 추가][api-management-add-groups]

드롭다운 목록에서 **Azure Active Directory 테넌트**를 선택한 다음 **그룹**에 원하는 그룹 이름을 입력하여 텍스트 상자에 추가합니다.

![그룹 선택][api-management-select-group]

이 그룹 이름은 다음 예제에서 보이는 대로 Azure Active Directory의 **그룹** 목록에서 찾을 수 있습니다.

![Azure Active Directory 그룹 목록][api-management-aad-groups-list]

**추가** 를 클릭하여 그룹 이름의 유효성을 검사하고 그룹을 추가합니다. 이 예제에서는 **Contoso 5 개발자** 외부 그룹이 추가됩니다. 

![그룹 추가됨][api-management-aad-group-added]

**저장** 을 클릭하여 새 그룹 선택 내용을 저장합니다.

Azure Active Directory 그룹이 한 제품에서 구성되면 API 관리 서비스 인스턴스에 있는 다른 제품의 **표시 여부** 탭에서 확인할 수 있습니다.

추가된 외부 그룹의 속성을 검토 및 구성하려면 **그룹** 탭에서 그룹 이름을 클릭합니다.

![그룹 관리][api-management-groups]

여기서 그룹의 **이름** 및 **설명**을 편집할 수 있습니다.

![그룹 편집][api-management-edit-group]

구성된 Azure Active Directory의 사용자는 개발자 포털에 로그인할 수 있으며 다음 섹션의 지침을 수행하여 표시 여부가 있는 그룹을 보고 구독할 수 있습니다.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Azure Active Directory 계정을 사용하여 개발자 포털에 로그인하는 방법
이전 섹션에서 구성된 Azure Active Directory 계정을 사용하여 개발자 포털에 로그인하려면 Active Directory 응용 프로그램 구성에서 **로그온 URL**을 사용하여 새 브라우저 창을 열고 **Azure Active Directory**를 클릭합니다.

![개발자 포털][api-management-dev-portal-signin]

Azure Active Directory에서 사용자 중 하나의 자격 증명을 입력하고 **로그인**을 클릭합니다.

![로그인][api-management-aad-signin]

추가 정보가 필요한 경우 등록 양식과 함께 메시지가 표시될 수 있습니다. 등록 양식을 완성하고 **등록**을 클릭합니다.

![등록][api-management-complete-registration]

사용자는 이제 API 관리 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

![등록 완료][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[API에 작업을 추가하는 방법]: api-management-howto-add-operations.md
[제품을 추가하고 게시하는 방법]: api-management-howto-add-products.md
[모니터링 및 분석]: api-management-monitoring.md
[제품에 API 추가]: api-management-howto-add-products.md#add-apis
[제품 게시]: api-management-howto-add-products.md#publish-product
[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[API Management 정책 참조]: api-management-policy-reference.md
[캐싱 정책]: api-management-policy-reference.md#caching-policies
[API Management 서비스 인스턴스 만들기]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[그래프 API 액세스]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[필수 구성 요소]: #prerequisites
[API 관리에서 OAuth 2.0 권한 부여 서버 구성]: #step1
[OAuth 2.0 사용자 권한 부여를 사용하도록 API 구성]: #step2
[개발자 포털에서 OAuth 2.0 사용자 권한 부여 테스트]: #step3
[다음 단계]: #next-steps

[Azure Active Directory 계정을 사용하여 개발자 포털에 로그인]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account




<!--HONumber=Nov16_HO3-->


