---
title: "Azure Active Directory와 응용 프로그램 통합 | Microsoft Docs"
description: "Azure Active Directory(Azure AD)에서 응용 프로그램을 추가, 업데이트 또는 제거하는 방법을 보여줍니다."
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: lenalepa
ms.custom: aaddev
ms.reviewer: luleon
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3be341bcb897a1481f145825429a1a94dfaae3b0
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="integrating-applications-with-azure-active-directory"></a>Azure Active Directory와 응용 프로그램 통합
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

엔터프라이즈 개발자 및 소프트웨어 SaaS(software-as-a-service) 공급자는 Azure AD(Azure Active directory)와 함께 통합되어 보안 로그인 및 해당 서비스에 대한 인증을 제공하는 상용 클라우드 서비스 또는 업무용 응용 프로그램 제품군을 개발할 수 있습니다. 응용프로그램 또는 서비스를 Azure AD와 통합하려면 개발자가 먼저 Azure 클래식 포털을 통해 Azure AD와 응용프로그램에 대한 자세한 내용을 등록해야 합니다.

이 문서에서는 Azure AD에서 응용 프로그램을 추가, 업데이트 또는 제거하는 방법을 보여줍니다. Azure AD와 통합할 수 있는 다양한 유형의 응용 프로그램, 웹 API 등과 같은 기타 리소스에 액세스하도록 응용 프로그램을 구성하는 방법에 대해 알아봅니다.

등록된 응용 프로그램을 나타내는 두 Azure AD 개체와 그 관계에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](active-directory-application-objects.md)를 참조하고, Azure Active Directory로 응용 프로그램을 개발할 때 사용해야 하는 브랜딩 지침에 대해 자세히 알아보려면 [통합 앱에 대한 브랜딩 지침](active-directory-branding-guidelines.md)을 참조하세요.

## <a name="adding-an-application"></a>응용 프로그램 추가
Azure AD의 기능을 사용하려는 모든 응용프로그램이 먼저 Azure AD 테넌트에 등록되어야 합니다. 이 등록 프로세스는 응용 프로그램이 위치한 URL, 사용자가 인증된 후 회신을 보낼 URL, 앱을 식별하는 URI 등과 같이 응용 프로그램에 대한 Azure AD 세부 정보의 제공이 포함됩니다.

Azure AD에서 사용자를 위한 로그인만 지원해야 하는 웹 응용 프로그램을 만드는 경우 아래 지침을 따르기만 하면 됩니다. 웹 API에 액세스하기 위한 자격 증명 또는 권한이 응용 프로그램에 필요하거나 다른 Azure AD 테넌트의 사용자가 이 API에 액세스할 수 있도록 하려는 경우 [ 응용 프로그램 업데이트 ](#updating-an-application) 섹션을 참조하면서 응용 프로그램을 계속 구성합니다.

### <a name="to-register-a-new-application-in-the-azure-portal"></a>Azure Portal에서 새 응용 프로그램을 등록하려면
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **추가 서비스**를 선택하고 **앱 등록**을 클릭한 다음 **추가**를 클릭합니다.
4. 프롬프트에 따라 새 응용 프로그램을 만듭니다. 웹 응용 프로그램 또는 네이티브 응용 프로그램에 대한 구체적인 예제를 원하는 경우 [빠른 시작](active-directory-developers-guide.md)을 확인합니다.
  * 웹 응용 프로그램의 경우 사용자가 로그인할 수 있는 앱의 기본 URL인 **로그인 URL**을 제공합니다(예: `http://localhost:12345`).
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 네이티브 응용 프로그램의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 **리디렉션 URI**를 제공합니다. 응용 프로그램에 고유하게 해당되는 값을 입력합니다(예: `http://MyFirstAADApp`
5. 등록을 완료하면 Azure AD에서 응용 프로그램에 고유한 클라이언트 식별자인 응용 프로그램 ID를 할당합니다. 그러면 응용 프로그램이 추가되고 응용 프로그램의 빠른 시작 페이지로 이동합니다. 응용 프로그램이 웹 또는 네이티브 응용 프로그램인지에 따라 응용 프로그램에 기능을 추가하는 옵션이 다릅니다. 일단 응용 프로그램이 추가되면 사용자가 다른 응용 프로그램에서 로그인하고 웹 API에 액세스하거나 다중 테넌트 응용 프로그램을 구성할 수 있도록(다른 조직이 자신의 응용 프로그램에 액세스할 수 있도록 허용) 응용 프로그램의 업데이트를 시작할 수 있습니다.

> [!NOTE]
> 기본적으로 새로 만든 응용 프로그램의 등록은 여러분의 디렉터리로부터 사용자가 여러분의 응용 프로그램에 로그인할 수 있도록 구성됩니다.
> 
> 

## <a name="updating-an-application"></a>응용프로그램 업데이트
응용 프로그램이 Azure AD와 등록되면 웹 API에 액세스를 제공하도록 업데이트하고 다른 조직에서 사용할 수 있게 만드는 등, 기타 작업이 필요할 수도 있습니다. 이 섹션은 응용 프로그램을 추가로 구성하는 데 필요한 다양한 방법을 설명합니다. 먼저, 동의 프레임워크에 대해 설명하겠습니다. 자신의 조직이나 다른 조직에서 개발한 클라이언트 응용 프로그램에서 사용할 리소스/API 응용 프로그램을 빌드하고 있는 분은 동의 프레임워크를 이해하는 것이 중요합니다.

Azure AD에서 인증이 작동하는 방식에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](active-directory-authentication-scenarios.md)를 참조하세요.

### <a name="overview-of-the-consent-framework"></a>동의 프레임워크의 개요
Azure AD의 동의 프레임워크는 클라이언트 응용 프로그램이 등록된 것과 다른 Azure AD 테넌트에서 보호하는 웹 API에 액세스해야 하는 다중 테넌트 웹 및 네이티브 클라이언트 응용 프로그램을 쉽게 개발할 수 있게 해줍니다. 이러한 웹 API에는 사용자 고유의 웹 API 외에도 Microsoft Graph API(Azure Active Directory, Intune 및 Office 365의 서비스에 액세스하기 위해) 및 기타 Microsoft 서비스 API가 포함됩니다. 이 프레임워크는 응용프로그램을 자신의 디렉토리에 등록하는 것에 동의하는 사용자나 관리자를 기반으로 합니다. 이 때 디렉토리 데이터 액세스가 필요할 수도 있습니다.

예를 들어, 웹 클라이언트 응용 프로그램이 Office 365에서 사용자에 대한 일정 정보를 읽어야 하는 경우 해당 사용자가 클라이언트 응용 프로그램에 동의해야 합니다. 사용자가 동의해 주면 해당 클라이언트 응용 프로그램에서 사용자를 대신하여 Microsoft Graph API를 호출하고 필요한 대로 일정 정보를 사용할 수 있습니다. [Microsoft Graph API](https://graph.microsoft.io)는 Azure AD의 사용자와 그룹 및 더 많은 Microsoft 클라우드 서비스의 기타 데이터 개체뿐만 아니라 Office 365(예: Exchange의 일정 및 메시지, SharePoint의 사이트 및 목록, OneDrive의 문서, OneNote의 전자 필기장, Planner의 작업, Excel의 통합 문서 등)의 데이터에 대한 액세스를 제공합니다. 

동의 프레임워크는 공용 또는 기밀 클라이언트를 사용하여 인증 코드 부여 및 클라이언트 자격 증명 부여와 같은 다양한 흐름 및 OAuth 2.0을 기반으로 작성됩니다. OAuth 2.0을 사용하여 Azure AD는 전화기, 태블릿, 서버 또는 웹 응용 프로그램과 같은 다양한 유형의 클라이언트 응용 프로그램을 작성하고 필요한 리소스에 액세스할 수 잇습니다.

동의 프레임워크에 대한 자세한 정보는 [Azure AD의 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Azure AD에 대한 인증 시나리오](active-directory-authentication-scenarios.md)를 참조하고, Microsoft Graph를 통해 Office 365에 대한 권한이 부여된 액세스 가져오기에 대한 정보는 [Microsoft Graph로 앱 인증](https://graph.microsoft.io/docs/authorization/auth_overview)을 참조하세요.

#### <a name="example-of-the-consent-experience"></a>승인 환경 예
다음 단계는 응용 프로그램 개발자와 사용자 모두에 대해 동의 경험이 어떻게 작동하는지를 보여줍니다.

1. Azure Portal에 있는 웹 클라이언트 응용 프로그램의 구성 페이지에서 필요한 사용 권한 섹션에 있는 메뉴를 사용하여 응용 프로그램에 필요한 권한을 설정합니다.
   
    ![다른 응용 프로그램에 대한 권한](./media/active-directory-integrating-applications/requiredpermissions.png)
2. 응용 프로그램의 사용 권한이 업데이트되었고 응용 프로그램이 실행 중이며 사용자가 처음으로 사용하는 것임을 고려하세요. 응용 프로그램이 아직 액세스나 새로고침 토큰을 입수하지 않은 경우 응용 프로그램이 Azure AD의 권한 부여 끝점으로 이동하여 새로운 액세스와 새로고침 토큰을 얻는 데 사용할 수 있는 인증 코드를 입수해야 합니다.
3. 사용자가 아직 인증되지 않았다면 Azure AD에 로그인하라는 메시지가 나타납니다.
   
    ![사용자 또는 관리자가 Azure AD에 로그인](./media/active-directory-integrating-applications/usersignin.png)
4. 사용자가 로그인한 후 Azure AD는 사용자를 동의 페이지에 표시해야 하는지 여부를 결정합니다. 이 결정은 사용자(또는 해당 조직의 관리자)가 응용 프로그램 동의를 부여했는지 여부에 따라 다릅니다. 아직 동의가 부여되지 않았다면 Azure AD는 사용자에게 동의 여부를 묻는 메시지를 표시하며 작동에 필요한 사용 권한을 표시합니다. 동의 대화 상자에 표시되는 사용 권한 집합은 Azure Portal에서 위임된 권한에 선택된 것과 동일합니다.
   
    ![사용자 동의 경험](./media/active-directory-integrating-applications/consent.png)
5. 사용자가 동의를 부여하면 인증 코드가 응용 프로그램에 반환되며, 이것을 교환하여 액세스 토큰 및 새로고침 토큰을 획득할 수 있습니다. 이 흐름에 대한 자세한 내용은 [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)의 [웹 응용 프로그램-Web API 섹션](active-directory-authentication-scenarios.md#web-application-to-web-api)을 참조하세요.

6. 관리자로 테넌트의 모든 사용자를 대신하여 응용 프로그램의 위임된 권한에 동의할 수도 있습니다. 이렇게 하면 테넌트의 모든 사용자에게 동의 대화 상자가 표시되지 않습니다. 이 작업은 응용 프로그램 페이지의 [Azure Portal](https://portal.azure.com)에서 수행할 수 있습니다. 응용 프로그램에 대한 **설정** 블레이드에서 **필요한 사용 권한**을 클릭하고 **권한 부여** 단추를 클릭합니다. 

    ![명시적 관리자 동의를 위한 권한 부여](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> ADAL.js를 사용하는 SPA(단일 페이지 응용 프로그램)에는 **권한 부여** 단추를 사용하여 명시적인 동의를 제공해야 합니다. 동의 프롬프트 없이 액세스 토큰이 요청될 경우 아직 동의하지 않은 경우 실패하기 때문입니다.   

### <a name="configuring-a-client-application-to-access-web-apis"></a>웹 API에 액세스하는 클라이언트 응용 프로그램 구성
웹/기밀 클라이언트 응용 프로그램이 인증을 요구하고 액세스 토큰을 얻는 권한 부여 흐름에 참여하려면 보안 자격 증명을 설정해야 합니다. Azure Portal에서 지원하는 기본 인증 방법은 클라이언트 ID + 대칭 키입니다. 이 섹션에서는 비밀 키로 클라이언트의 자격 증명을 제공하는 데 필요한 구성 단계에 대해 설명합니다.

또한 클라이언트가 리소스 응용 프로그램(예: Microsoft Graph API)에서 공개한 웹 API에 액세스하기 전에 동의 프레임워크는 클라이언트에서 요청된 권한에 기반하여 필요한 권한 부여를 얻도록 합니다. 기본적으로 모든 응용 프로그램은 Azure AD "로그인 사용 및 사용자 프로필 읽기" 권한이 기본적으로 선택된 상태로 Azure Active Directory(그래프 API) 및 Azure 서비스 관리 API에서 사용 권한을 선택할 수 있습니다. 클라이언트 응용 프로그램을 Office 365 Azure AD 테넌트에 등록하는 경우 SharePoint 및 Exchange Online의 웹 API 및 권한도 선택할 수 있습니다. 원하는 웹 API 옆에 있는 드롭다운 메뉴에서 다음 [두 가지 유형의 권한](active-directory-dev-glossary.md#permissions) 중에서 선택할 수 있습니다.

* 응용 프로그램 권한: 클라이언트 응용 프로그램이 직접 웹 API에 액세스해야 합니다(사용자 컨텍스트 없음). 이 유형의 권한은 관리자의 동의가 필요하며 네이티브 클라이언트 응용 프로그램에 대해 사용할 수 없습니다.
* 위임된 권한: 클라이언트 응용 프로그램이 로그인된 사용자로 웹 API에 액세스해야 하지만 이 액세스는 선택한 권한에 따라 제한됩니다. 이 유형의 사용 권한은 관리자의 동의를 필요로 하지 않는 한 사용자가 부여할 수 있습니다. 

> [!NOTE]
> 위임된 권한을 응용 프로그램에 추가할 경우 Azure 클래식 포털에서 수행한 것처럼 테넌트 내의 사용자에게 자동으로 동의를 부여하지 않습니다. 관리자가 Azure Portal의 응용 프로그램 페이지에 있는 **필요한 사용 권한** 섹션에서 **권한 부여** 단추를 클릭하지 않는다면, 사용자는 런타임에 추가된 위임 권한에 직접 동의해야 합니다. 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>웹 API에 액세스할 수 있는 자격 증명 또는 권한을 추가하려면
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 위쪽 메뉴에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 후 구성하려는 응용 프로그램을 클릭합니다. 그러면 응용 프로그램의 빠른 시작 페이지로 이동되고 응용 프로그램에 대한 설정 블레이드가 열립니다.
4. 웹 응용 프로그램의 자격 증명에 대한 비밀 키를 추가하려면 설정 블레이드에서 [키] 섹션을 클릭합니다.  
   
   * 키에 대한 설명을 추가하고 기간으로 1년 또는 2년을 선택합니다. 
   * 구성 변경 사항을 저장하면 맨 오른쪽 열에 키 값이 포함됩니다. 저장을 클릭하면 이 섹션으로 돌아와 복사해야 합니다. 그러면 런타임에서 인증하는 중에 클라이언트 응용 프로그램에서 이 구성을 사용할 수 있습니다.
5. 클라이언트에서 리소스 API에 액세스하기 위한 권한을 추가하려면 설정 블레이드에서 [필요한 사용 권한] 섹션을 클릭합니다. 
   
   * 먼저 [추가] 단추를 클릭합니다.
   * [API 선택]을 클릭하고 선택할 리소스 유형을 선택합니다.
   * 사용 가능한 API 목록을 탐색하거나 검색 상자를 사용하여 Web API를 공개하는 디렉터리에서 사용 가능한 리소스 응용 프로그램 중에서 선택할 수 있습니다. 관심 있는 리소스를 클릭한 후 **선택**을 클릭합니다.
   * 선택했으면 **사용 권한 선택** 메뉴로 이동하여 응용 프로그램에 대해 [응용 프로그램 권한] 및 [위임된 권한]을 선택할 수 있습니다.
   
6. 완료되면 **완료** 단추를 클릭합니다.

> [!NOTE]
> **완료** 단추를 클릭해도 구성한 다른 응용 프로그램에 대한 권한을 기반으로 디렉토리 내 응용 프로그램에 대한 사용 권한이 자동으로 설정됩니다.  응용 프로그램 **설정** 블레이드에서 이러한 응용 프로그램 사용 권한을 확인할 수 있습니다.
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>웹 API를 공개하는 리소스 응용 프로그램 구성
액세스 [범위](active-directory-dev-glossary.md#scopes) 및 [역할](active-directory-dev-glossary.md#roles)을 공개하면 웹 API를 개발하고 클라이언트 응용 프로그램에서 사용할 수 있게 만들 수 있습니다. 올바르게 구성된 웹 API는 Graph API 및 Office 365 API와 같은 다른 Microsoft 웹 API와 마찬가지로 사용 가능합니다. 액세스 범위와 역할은 [응용 프로그램의 매니페스트](active-directory-dev-glossary.md#application-manifest)를 통해 공개되며, 이 매니페스트는 응용 프로그램의 ID 구성을 나타내는 JSON 파일입니다.  

다음 섹션에서는 리소스 응용 프로그램의 매니페스트를 수정하여 액세스 범위를 공개하는 방법을 보여 줍니다.

#### <a name="adding-access-scopes-to-your-resource-application"></a>리소스 응용 프로그램에 액세스 범위 추가
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 위쪽 메뉴에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 후 구성하려는 응용 프로그램을 클릭합니다. 그러면 응용 프로그램의 빠른 시작 페이지로 이동되고 응용 프로그램에 대한 설정 블레이드가 열립니다.
4. 응용 프로그램 페이지에서 **매니페스트**를 클릭하여 인라인 매니페스트 편집기를 엽니다. 
5. "oauth2Permissions" 노드를 다음 JSON 코드 조각으로 바꿉니다. 이 코드 조각은 “사용자 가장"이란 범위를 노출하는 방법의 예시입니다. “사용자 가장"을 사용하면 리소스 소유자가 클라이언트 응용 프로그램에 리소스에 대한 일종의 위임된 액세스 권한을 부여할 수 있습니다. 응용프로그램의 텍스트와 값을 변경했는지 확인합니다.
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    ID 값은 [GUID 생성 도구](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) 를 사용하거나 프로그래밍 방식으로 만드는 새롭게 생성된 GUID여야 합니다. 이것은 웹 API에 의해 노출되는 사용 권한을 위한 고유한 식별자를 나타냅니다. 클라이언트가 웹 API에 대한 액세스를 요청하도록 적절하게 구성되고 웹 API를 호출하면 범위(scp) 클레임이 위 값으로 설정된 OAuth 2.0 JWT 토큰을 나타내며 이 경우에는 user_impersonation입니다.
   
   > [!NOTE]
   > 추가 범위를 나중에 필요한 대로 노출할 수 있습니다. 웹 API에서 다양한 기능과 관련된 여러 범위를 공개할 수도 있음을 고려하세요. 이제 수신된 OAuth 2.0 JWT 토큰에서 범위(scp) 클레임을 사용하여 웹 API에 대한 액세스를 제어할 수 있습니다.
   > 
   > 
6. **저장**을 클릭하여 매니페스트를 저장합니다. Web API가 이제 디렉토리 내 다른 응용 프로그램에 의해 사용되도록 구성됩니다.

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>웹 API가 디렉터리의 다른 응용 프로그램에 노출되었는지 확인하려면
1. 위쪽 메뉴에서 **앱 등록**을 클릭하고 원하는 클라이언트 응용 프로그램을 선택하여 Web API에 대한 액세스를 구성한 다음 설정 블레이드로 이동합니다.
2. **필요한 사용 권한** 섹션에서 사용 권한을 노출한 Web API를 선택합니다. 위임된 권한 드롭다운 메뉴에서 새 사용 권한을 선택합니다.

![해야 할 일 목록 권한이 표시됨](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>응용프로그램 매니페스트에 대한 추가 정보
응용 프로그램 매니페스트는 실제로 설명한 API 액세스 범위를 포함하여, Azure AD 응용 프로그램의 ID 구성의 모든 특성을 정의하는 응용 프로그램 엔터티를 업데이트하기 위한 메커니즘으로 사용됩니다. 응용 프로그램 엔터티에 대한 자세한 내용은 [Graph API 응용 프로그램 엔터티 설명서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)를 참조하세요. 여기에서 API에 대한 사용 권한을 지정하는 데 사용되는 응용프로그램 엔터티 멤버에 관한 전체 참조 정보를 찾아볼 수 있습니다.  

* 웹 API에 대한 [응용 프로그램 권한](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type)을 정의하는 데 사용할 수 있는 **AppRole** 엔터티의 컬렉션인 appRoles 멤버  
* 웹 API에 대한 [위임된 권한](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type)을 정의하는 데 사용할 수 있는 **OAuth2Permission** 엔터티의 컬렉션인 oauth2Permissions 멤버

일반적인 응용 프로그램 매니페스트 개념에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 매니페스트 이해](active-directory-application-manifest.md)를 참조하세요.

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Microsoft Graph API를 통해 Azure AD Graph 및 Office 365에 액세스  
앞서 설명한 대로 리소스 응용프로그램에서 API 노출/액세스 외에도 Microsoft 리소스에 의해 노출된 API에 액세스하기 위해 클라이언트 응용프로그램을 업데이트할 수도 있습니다.  다른 응용프로그램에 대한 사용 권한 목록 중에서 "Microsoft Graph"라고 하는 Microsoft Graph API는 Azure AD에 등록된 모든 응용프로그램에서 사용할 수 있습니다. Office 365에서 프로비전된 Azure AD 테넌트의 클라이언트 응용 프로그램을 등록하는 경우, Microsoft Graph API에 의해 다양한 Office 365 리소스에 노출된 모든 사용 권한을 액세스할 수 있습니다.

Microsoft Graph API에 의해 노출된 액세스 범위에 대한 자세한 내용은 [사용 권한 범위 | Microsoft Graph API 개념](https://graph.microsoft.io/docs/authorization/permission_scopes) 문서를 참조하세요.

> [!NOTE]
> 현재 제한으로 인해 네이티브 클라이언트 응용 프로그램이 “조직의 디렉터리 액세스" 권한을 사용하는 경우 Azure AD 그래프 API만 호출할 수 있습니다.  이 제한은 웹 응용 프로그램에는 적용되지 않습니다.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>다중 테넌트 응용 프로그램 구성
Azure AD에 응용 프로그램을 추가하는 경우 조직 내의 사용자만 해당 응용 프로그램에 액세스할 수 있도록 제한하는 것이 좋습니다. 또는 외부 조직의 사용자가 응용 프로그램에 액세스할 수 있게 만들 수도 있습니다. 이러한 두 응용 프로그램 종류를 단일 테넌트 및 다중 테넌트 응용 프로그램이라고 합니다. 단일 테넌트 응용 프로그램의 구성을 다중 테넌트 응용 프로그램으로 수정할 수 있습니다. 이 섹션에서 아래와 같이 설명합니다.

단일 테넌트와 다중 테넌트 응용프로그램 사이의 차이점을 인식하는 것이 중요합니다.  

* 단일 테넌트 응용 프로그램은 단일 조직에서 사용하기 위한 것입니다. 일반적으로 엔터프라이즈 개발자가 작성한 LoB(기간 업무) 응용 프로그램이 이에 해당합니다. 단일 테넌트 응용 프로그램은 하나의 디렉터리에 있는 사용자들만 액세스해야 하므로 하나의 디렉터리에서만 프로비전해야 합니다.
* 다중 테넌트 응용 프로그램은 여러 조직에서 사용하기 위한 것입니다. 일반적으로 ISV(Independent Software Vendor)에서 작성한SaaS(Software-as-a-Service) 웹 응용 프로그램이 이에 해당합니다. 다중 테넌트 응용프로그램은 사용될 각 디렉토리에서 프로비저닝해야 하며, 그러려면 사용자나 관리자가 등록에 동의해야 하며, Azure AD 동의 프레임워크를 통해 지원되어야 합니다. 모든 네이티브 클라이언트 응용 프로그램은 기본적으로 리소스 소유자의 장치에 설치된 다중 테넌트입니다. 동의 프레임워크에 대한 자세한 내용은 위의 동의 프레임워크 개요 섹션을 참조하십시오.

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>외부 사용자가 리소스에 응용 프로그램 액세스 권한을 부여하도록 허용
고객이나 조직 외부의 파트너가 사용할 수 있는 응용 프로그램을 작성 중인 경우 Azure Portal에서 응용프로그램의 정의를 업데이트해야 합니다.

> [!NOTE]
> 다중 테넌트를 사용하도록 설정하면 응용프로그램의 앱 ID URI가 확인된 도메인에 속하도록 해야 합니다. 또한 반환 URL이 https://로 시작해야 합니다. 자세한 내용은 [응용 프로그램 개체 및 서비스 사용자 개체](active-directory-application-objects.md)를 참조하세요.
> 
> 

외부 사용자의 앱 액세스를 허용하려면: 

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 위쪽 메뉴에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 후 구성하려는 응용 프로그램을 클릭합니다. 그러면 응용 프로그램의 빠른 시작 페이지로 이동되고 응용 프로그램에 대한 설정 블레이드가 열립니다.
4. 설정 블레이드에서 **속성**을 클릭하고 **다중 테넌트** 스위치를 **예**로 누릅니다.

여기까지 변경했다면 다른 조직 내의 사용자와 관리자가 자신의 디렉터리 및 기타 데이터에 응용 프로그램 액세스를 부여할 수 있습니다.

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>런타임 시 Azure AD 동의 프레임워크 트리거
동의 프레임워크를 사용하려면 다중 테넌트 클라이언트 응용프로그램이 OAuth 2.0을 사용하여 권한 부여를 요청해야 합니다. [코드 샘플](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)은 웹 응용 프로그램, 네이티브 응용 프로그램 또는 서버/디먼 응용 프로그램에서 인증 코드와 액세스 토큰을 요청하여 웹 API 호출하는 방식을 보여 줍니다.

또한 웹 응용 프로그램에서 사용자를 위한 등록 환경을 제공할 수 있습니다. 등록 경험을 제공하는 경우 사용자가 등록 단추를 클릭하면 브라우저가 Azure AD OAuth2.0 권한 부여 끝점 또는 OpenID Connect userinfo 끝점으로 리디렉션됩니다. 이러한 끝점은 id_token을 검사하여 응용 프로그램이 새 사용자에 대한 정보를 얻을 수 있도록 지원합니다. 등록 단계를 따라 동의 프레임워크 개요 섹션에 위에 표시된 것과 유사한 동의 프롬프트가 표시됩니다.

또는 관리자가 "우리 회사에 등록"할 수 있도록 지원하는 경험을 제공할 수도 있습니다. 이러한 경험은 또한 사용자를 Azure AD OAuth 2.0 권한 부여 끝점으로 리디렉션합니다. 이 경우 prompt=admin_consent 매개 변수를 관리자가 자신의 조직을 대신해서 동의를 허용할 관리자 동의 경험에 적용할 인증 끝점에 전달합니다. 전역 관리자 역할에 속해 있는 계정으로 인증하는 사용자만이 동의를 제공할 수 있으며, 다른 사용자에게는 오류가 발생합니다. 동의에 성공하면 응답에 admin_consent=true가 포함됩니다. 액세스 토큰을 교환하는 경우 해당 응용 프로그램에 등록한 조직 및 관리자에 대한 정보를 제공하는 id_token도 받게 됩니다.

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>단일 페이지 응용 프로그램에 OAuth 2.0 암시적 허용 사용
SPA(단일 페이지 응용 프로그램)는 일반적으로 브라우저에서 실행되는 JavaScript 기반 프런트 엔드로 구성됩니다. 이러한 프런트 엔드는 응용 프로그램의 웹 API를 다시 호출하여 비즈니스 논리를 수행합니다. Azure AD에서 호스트되는 SPA의 경우, OAuth 2.0 암시적 허용을 사용하여 Azure AD에서 사용자를 인증하고, 응용 프로그램의 JavaScript 클라이언트에서 해당 백 엔드 웹 API로의 보안 호출을 사용할 수 있는 토큰을 가져옵니다. 사용자가 승인하면 이 동일한 인증 프로토콜을 사용하여 클라이언트와 응용 프로그램에 대해 구성된 다른 웹 API 리소스 간의 보안 호출을 위해 토큰을 가져올 수 있습니다. 암시적 권한 부여에 대해 자세히 파악하고 자신의 응용 프로그램 시나리오에 적절한지 판단하려면 [Azure Active Directory에서 OAuth2 암시적 권한 부여 흐름 이해](active-directory-dev-understanding-oauth2-implicit-grant.md)를 참조하세요.

기본적으로 응용 프로그램에 대해 OAuth 2.0 암시적 허용이 사용되지 않도록 설정됩니다. 응용 프로그램의 ID 구성을 나타내는 JSON 파일인 [응용 프로그램 매니페스트](active-directory-application-manifest.md)에 `oauth2AllowImplicitFlow` 값을 설정하면 응용 프로그램에 OAuth 2.0 암시적 권한 부여를 사용할 수 있습니다.

#### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 암시적 허용을 사용하도록 설정하려면
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 위쪽 메뉴에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 후 구성하려는 응용 프로그램을 클릭합니다. 그러면 응용 프로그램의 빠른 시작 페이지로 이동되고 응용 프로그램에 대한 설정 블레이드가 열립니다.
4. 응용 프로그램 페이지에서 **매니페스트**를 클릭하여 인라인 매니페스트 편집기를 엽니다.
   "oauth2AllowImplicitFlow" 값을 찾아 "true"로 설정합니다. 기본적으로 “false”입니다.
   
    `"oauth2AllowImplicitFlow": true,`
5. 업데이트된 매니페스트를 저장합니다. 저장되면 웹 API는 이제 OAuth 2.0 암시적 허용을 사용하여 사용자를 인증하도록 구성된 것입니다.


## <a name="removing-an-application"></a>응용 프로그램 제거
이 섹션에서는 Azure AD 테넌트에서 응용프로그램을 제거하는 방법을 설명합니다.

### <a name="removing-an-application-authored-by-your-organization"></a>조직이 작성한 응용프로그램 제거
이들은 Azure AD 테넌트에 대 한 기본 "응용프로그램" 페이지에서 "회사가 보유한 응용프로그램" 필터에서 표시되는 응용프로그램입니다. 기술적 측면에서 이들은 Azure 클래식 포털을 통해 수동으로 또는 PowerShell 또는 Graph API를 통해 프로그래밍 방식으로 등록한 응용 프로그램입니다. 구체적으로 말하면, 테넌트의 응용프로그램 및 서비스 주체 개체 모두에 의해 제공됩니다. 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](active-directory-application-objects.md) 를 참조하세요.

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>디렉터리에서 단일 테넌트 응용 프로그램을 제거하려면
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 위쪽 메뉴에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 후 구성하려는 응용 프로그램을 클릭합니다. 그러면 응용 프로그램의 빠른 시작 페이지로 이동되고 응용 프로그램에 대한 설정 블레이드가 열립니다.
4. 응용 프로그램 페이지에서 **삭제**를 클릭합니다.
5. 확인 메시지에서 **예** 를 클릭합니다.

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>디렉터리에서 다중 테넌트 응용 프로그램을 제거하려면
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 위쪽 메뉴에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 후 구성하려는 응용 프로그램을 클릭합니다. 그러면 응용 프로그램의 빠른 시작 페이지로 이동되고 응용 프로그램에 대한 설정 블레이드가 열립니다.
4. 설정 블레이드에서 **속성**을 클릭하고 **다중 테넌트** 스위치를 **아니요**로 누릅니다. 그러면 응용 프로그램이 단일 테넌트로 전환되지만 계속해서 이미 동의를 표시한 조직에 남게 됩니다.
5. 응용 프로그램 페이지에서 **삭제** 단추를 클릭합니다.
6. 확인 메시지에서 **예** 를 클릭합니다.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>다른 조직이 권한을 부여한 다중 테넌트 응용프로그램 제거
이들은 특히 "회사가 보유한 응용프로그램" 목록 아래에 나열되지 않은 Azure AD 테넌트에 대해 기본 "응용프로그램" 페이지에서 "회사가 보유한 응용프로그램" 필터 아래에 표시되는 응용프로그램의 하위 집합입니다. 기술적 측면에서 이들은 동의 프로세스 중에 등록되는 다중 테넌트 응용프로그램입니다. 구체적으로 말하면, 테넌트의 서비스 주체 개체에 의해서만 제공됩니다. 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](active-directory-application-objects.md) 를 참조하세요.

자사의 디렉토리에 대해 다중 테넌트 응용프로그램 액세스 권한을 제거하려면(동의를 표시한 후에) 회사 관리자는 Azure 구독이 있어야 Azure Portal을 통해 액세스 권한을 제거할 수 있습니다. 또는 회사 관리자는 [Azure AD PowerShell Cmdlet](http://go.microsoft.com/fwlink/?LinkId=294151) 을 사용하여 액세스를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 앱의 시각적 지침에 대한 팁은 [통합 앱에 대한 브랜딩 지침](active-directory-branding-guidelines.md)을 참조하세요.
* 응용 프로그램의 응용 프로그램 개체와 서비스 주체 개체 간의 관계에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](active-directory-application-objects.md)를 참조하세요.
* 앱 매니페스트에서 수행하는 역할에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 매니페스트 이해](active-directory-application-manifest.md)를 참조하세요.
* 핵심 Azure AD(Active Directory) 개발자 개념 중 일부에 대한 정의는 [Azure Active Directory 개발자 용어집](active-directory-dev-glossary.md)을 참조하세요.
* 모든 개발자 관련 콘텐츠에 대한 개요는 [Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.


