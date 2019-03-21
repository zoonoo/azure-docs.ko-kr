---
title: Azure Active Directory와 애플리케이션 통합
description: Azure AD(Azure Active Directory)에서 애플리케이션을 업데이트하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee16ed8205453546702946628c98c73b0f34b15
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103811"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>빠른 시작: Azure Active Directory에서 애플리케이션 업데이트

Azure AD(Azure Active Directory)를 통해 애플리케이션을 등록한 엔터프라이즈 개발자 및 SaaS(Software-as-a-Service) 웹 API와 같은 다른 리소스에 액세스하도록 애플리케이션을 구성하여 다른 조직 등에서 사용할 수 있도록 해야 할 수도 있습니다.

이 빠른 시작에서는 사용자와 다른 조직의 요구 사항이나 필요를 충족시키도록 응용 프로그램을 구성 또는 업데이트할 수 있는 다양한 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

시작하기 위해서는 다음 단계를 완료했는지 확인합니다.

* [Azure AD 동의 프레임워크](consent-framework.md)의 개요를 읽습니다. 여기서는 다른 사용자 또는 응용 프로그램에서 사용해야 하는 응용 프로그램을 빌드하는 경우를 이해하는 것이 중요합니다.
* 응용 프로그램이 등록된 Azure AD 테넌트가 있습니다.
  * 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](quickstart-create-new-tenant.md).
  * 테넌트에 등록된 앱이 없다면 [Azure AD에 응용 프로그램을 추가하는 방법을 알아봅니다](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>웹 API에 액세스하는 클라이언트 애플리케이션 구성

웹/기밀 클라이언트 응용 프로그램이 인증을 요구하고 액세스 토큰을 얻는 권한 부여 흐름에 참여하려면 보안 자격 증명을 설정해야 합니다. Azure Portal에서 지원하는 기본 인증 방법은 클라이언트 ID + 비밀 키입니다. 이 섹션에서는 비밀 키로 클라이언트의 자격 증명을 제공하는 데 필요한 구성 단계에 대해 설명합니다.

클라이언트에서 리소스 응용 프로그램(예: Microsoft Graph API)에서 공개한 웹 API에 액세스하기 전에 동의 프레임워크는 클라이언트에서 요청된 권한에 따라 필요한 권한 부여를 얻도록 합니다. 기본적으로 모든 응용 프로그램은 **Azure Active Directory**(Graph API) 및 Azure 클래식 배포 모델의 사용 권한을 선택할 수 있습니다. [Graph API "로그인 및 읽기 사용자 프로필" 사용 권한](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails)도 기본적으로 선택됩니다. Office 365를 구독한 계정이 있는 테넌트에 클라이언트를 등록하는 경우 SharePoint 및 Exchange Online에 대한 웹 API 및 권한도 선택할 수 있습니다. 원하는 웹 API 각각에서 두 가지 형식의 사용 권한 중에 선택할 수 있습니다.

- 애플리케이션 권한: 클라이언트 애플리케이션이 직접 웹 API에 액세스해야 합니다(사용자 컨텍스트 없음). 이 유형의 권한은 관리자의 동의가 필요하며 네이티브 클라이언트 애플리케이션에 대해 사용할 수 없습니다.
- 위임된 권한: 클라이언트 애플리케이션이 로그인된 사용자로 웹 API에 액세스해야 하지만 이 액세스는 선택한 권한에 따라 제한됩니다. 이 형식의 사용 권한은 관리자의 동의를 필요로 하지 않는 한 사용자가 부여할 수 있습니다.

  > [!NOTE]
  > 위임된 권한을 애플리케이션에 추가할 경우 테넌트 내의 사용자에게 자동으로 동의를 부여하지 않습니다. 관리자가 모든 사용자를 대신하여 동의를 허락하지 않는 한 사용자는 런타임에 추가로 위임된 사용 권한에 대해 수동으로 동의해야 합니다.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>웹 API에 액세스할 수 있는 응용 프로그램 자격 증명 또는 권한 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 계정이 둘 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
3. 왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록**을 차례로 클릭한 다음, 구성하려는 응용 프로그램을 찾아 선택합니다.

   ![애플리케이션의 등록 업데이트](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. 응용 프로그램의 **설정** 페이지가 포함된 응용 프로그램의 기본 등록 페이지로 이동합니다. 웹 애플리케이션에 대한 자격 증명을 추가하려면:
   1. **설정** 페이지에서 **키** 섹션을 선택합니다.
   1. 인증서를 추가하려면:
      - **공개 키 업로드**를 선택합니다.
      - 업로드할 파일을 선택합니다. cer, .pem, .crt 중 한 가지 파일 형식이어야 합니다.
   1. 암호를 추가하려면:
      - 키에 대한 설명을 추가합니다.
      - 기간을 선택합니다.
      - **저장**을 선택합니다. 구성 변경 사항을 저장하면 맨 오른쪽 열에 키 값이 포함됩니다. 이 페이지를 벗어나면 액세스할 수 없으므로 클라이언트 애플리케이션 코드에서 사용할 **키를 복사해야 합니다**.

5. 클라이언트에서 리소스 API에 액세스하는 사용 권한을 추가하려면
   1. **설정** 페이지에서 **필요한 사용 권한**을 선택한 다음, **추가**를 선택합니다.
   1. **API 선택**을 선택하고 선택할 리소스 유형을 선택합니다.
   1. 사용 가능한 API 목록을 탐색하거나 검색 상자를 사용하여 Web API를 공개하는 디렉터리에서 사용 가능한 리소스 애플리케이션 중에서 선택할 수 있습니다. 관심 있는 리소스를 선택한 다음, **선택**을 클릭합니다.
   1. **액세스 사용** 페이지에서 API에 액세스할 때 응용 프로그램에서 필요로 하는 응용 프로그램 사용 권한 및/또는 위임된 사용 권한을 선택합니다.
   
   ![애플리케이션의 등록 업데이트 - permissions api](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

   ![애플리케이션의 등록 업데이트 - permissions perms](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. 작업을 마치면 **액세스 사용** 페이지에서 **선택** 단추를 선택한 다음, **API 액세스 추가** 페이지에서 **완료** 단추를 클릭합니다. **필수 사용 권한** 페이지로 돌아가게 됩니다. 여기서 새 리소스를 API 목록에 추가합니다.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>웹 API를 공개하는 리소스 애플리케이션 구성

액세스 [범위](developer-glossary.md#scopes) 및 [역할](developer-glossary.md#roles)을 공개하면 웹 API를 개발하고 클라이언트 애플리케이션에서 사용할 수 있게 만들 수 있습니다. 올바르게 구성된 웹 API는 Graph API 및 Office 365 API와 같은 다른 Microsoft 웹 API와 마찬가지로 사용 가능합니다. 액세스 범위와 역할은 [애플리케이션의 매니페스트](developer-glossary.md#application-manifest)를 통해 공개되며, 이 매니페스트는 애플리케이션의 ID 구성을 나타내는 JSON 파일입니다.

다음 섹션에서는 리소스 애플리케이션의 매니페스트를 수정하여 액세스 범위를 공개하는 방법을 보여줍니다.

### <a name="add-access-scopes-to-your-resource-application"></a>리소스 응용 프로그램에 액세스 범위 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 계정이 둘 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
3. 왼쪽의 탐색 창에서 **Azure Active Directory > 앱 등록**을 선택한 다음, 구성하려는 응용 프로그램을 찾아 선택합니다.

   ![애플리케이션의 등록 업데이트](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. 애플리케이션의 기본 등록 페이지로 이동하면 애플리케이션의 **설정** 페이지를 엽니다. 응용 프로그램의 등록 페이지에서 **매니페스트**를 클릭하여 **매니페스트 편집** 페이지로 전환합니다. 웹 기반 매니페스트 편집기가 열리면 포털 내에서 매니페스트를 **편집**할 수 있습니다. 필요에 따라 **다운로드**를 클릭하고 로컬로 편집하고 **업로드**를 사용하여 애플리케이션에 다시 적용할 수 있습니다.
5. 이 예제에서는 `oauth2Permissions` 컬렉션에 다음 JSON 요소를 추가하여 리소스/API에서 `Employees.Read.All`이라는 새 범위를 노출합니다. 등록하는 동안 기본적으로 기존 `user_impersonation` 범위가 제공됩니다. `user_impersonation`을 사용하면 클라이언트 애플리케이션이 로그인한 사용자의 ID로 리소스에 액세스하는 사용 권한을 요청할 수 있습니다. 기존 `user_impersonation` 범위 요소 뒤에 쉼표를 추가하고 리소스의 요구 사항에 맞게 속성 값을 변경해야 합니다. 

   ```json
   {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
   }
   ```

   > [!NOTE]
   > `id` 값은 [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx)과 같은 GUID 생성 도구를 사용하거나 프로그래밍 방식으로 만들어야 합니다. `id`는 웹 API에 의해 노출되는 범위에서 고유한 식별자를 나타냅니다. 웹 API에 액세스할 수 있는 권한이 있는 클라이언트를 적절하게 구성하면 Azure AD에서 OAuth 2.0 액세스 토큰을 발급합니다. 클라이언트가 웹 API를 호출하면 해당 애플리케이션 등록에서 요청된 권한으로 설정된 범위(scp) 클레임을 갖는 액세스 토큰을 표시합니다.
   >
   > 추가 범위를 나중에 필요한 대로 노출할 수 있습니다. 웹 API에서 다양한 기능과 관련된 여러 범위를 공개할 수도 있음을 고려하세요. 리소스에서는 OAuth 2.0 액세스 토큰에서 수신된 범위(`scp`) 클레임을 평가하여 런타임 시 웹 API에 대한 액세스를 제어할 수 있습니다.

6. 완료되면 **저장**을 클릭합니다. 이제 Web API가 디렉토리에 있는 다른 애플리케이션에 의해 사용되도록 구성되었습니다.

   ![애플리케이션의 등록 업데이트](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>웹 API가 테넌트의 다른 애플리케이션에 노출되었는지 확인합니다.

1. Azure AD 테넌트로 다시 돌아가서 **앱 등록**을 다시 선택한 다음, 구성하려는 클라이언트 응용 프로그램을 찾아 선택합니다.

   ![애플리케이션의 등록 업데이트](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. [웹 API에 액세스하는 클라이언트 애플리케이션 구성](#configure-a-client-application-to-access-web-apis)에서 수행한 대로 5단계를 반복합니다. **API 선택** 단계에 도달하면 검색 필드에 해당 애플리케이션 이름을 입력하여 리소스를 검색하고 **선택**을 클릭합니다.

3. **액세스 사용** 페이지에서 클라이언트 권한 요청에 사용할 수 있는 새 범위가 표시되어야 합니다.

   ![새 사용 권한이 표시됨](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>애플리케이션 매니페스트에 대한 추가 정보

응용 프로그램 매니페스트는 설명한 API 액세스 범위를 포함하여, Azure AD 응용 프로그램의 ID 구성의 모든 특성을 정의하는 응용 프로그램 엔터티를 업데이트하기 위한 메커니즘으로 사용됩니다. 애플리케이션 엔터티 및 해당 스키마에 대한 자세한 내용은 [Graph API 애플리케이션 엔터티 설명서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)를 참조하세요. 이 문서에는 다음을 비롯하여 API에 대한 사용 권한을 지정하는 데 사용되는 애플리케이션 엔터티 멤버에 대한 전체 참조 정보가 포함됩니다.  

- 웹 API에 대한 [애플리케이션 권한](developer-glossary.md#permissions)을 정의하는 데 사용되는 [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) 엔터티의 컬렉션인 appRoles 멤버 
- 웹 API에 대한 [위임된 권한](developer-glossary.md#permissions)을 정의하는 데 사용되는 [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) 엔터티의 컬렉션인 oauth2Permissions 멤버

일반적인 응용 프로그램 매니페스트 개념에 대한 자세한 정보는 [Azure AD 앱 매니페스트](reference-app-manifest.md)를 참조하세요.

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Microsoft Graph API를 통해 Azure AD Graph 및 Office 365에 액세스  

앞서 설명한 대로 고유한 애플리케이션의 API를 노출/액세스하는 것 외에도 클라이언트 애플리케이션을 등록하여 Microsoft 리소스에 의해 노출된 API에 액세스할 수 있습니다. 포털의 리소스/API 목록에서 "Microsoft Graph"라고도 하는 Microsoft Graph API는 Azure AD에 등록된 모든 애플리케이션에 사용할 수 있습니다. Office 365 구독에 등록된 계정을 포함하는 테넌트에서 클라이언트 애플리케이션을 등록하는 경우 다양한 Office 365 리소스에 의해 노출되는 범위에 액세스할 수도 있습니다.

Microsoft Graph API에서 노출되는 범위에 대한 자세한 내용은 [Microsoft Graph 권한 참조](https://docs.microsoft.com/graph/permissions-reference) 문서를 참조하세요.

> [!NOTE]
> 현재 제한으로 인해 네이티브 클라이언트 애플리케이션이 “조직의 디렉터리 액세스” 권한을 사용하는 경우 Azure AD 그래프 API만 호출할 수 있습니다. 이 제한은 웹 애플리케이션에는 적용되지 않습니다.

## <a name="configuring-multi-tenant-applications"></a>다중 테넌트 애플리케이션 구성

Azure AD에서 애플리케이션을 등록하는 경우 조직 내의 사용자만 해당 애플리케이션에 액세스할 수 있도록 제한하는 것이 좋습니다. 또는 외부 조직의 사용자가 애플리케이션에 액세스할 수 있게 만들 수도 있습니다. 이러한 두 애플리케이션 종류를 단일 테넌트 및 다중 테넌트 애플리케이션이라고 합니다. 이 섹션에서는 단일 테넌트 애플리케이션의 구성을 다중 테넌트 애플리케이션으로 수정하는 방법을 설명합니다.

단일 테넌트와 다중 테넌트 애플리케이션 간의 차이점을 인식하는 것이 중요합니다.  

- 단일 테넌트 애플리케이션은 단일 조직에서 사용하기 위한 것입니다. 일반적으로 엔터프라이즈 개발자가 작성한 LoB(기간 업무) 애플리케이션입니다. 단일 테넌트 애플리케이션은 애플리케이션 등록과 동일한 테넌트에 있는 계정을 사용하는 사용자에 의해서만 액세스될 수 있습니다. 결과적으로 하나의 디렉터리에서만 프로비전되어야 합니다.
- 다중 테넌트 애플리케이션은 여러 조직에서 사용하기 위한 것입니다. 일반적으로 ISV(Independent Software Vendor)에서 작성한 SaaS(Software-as-a-Service) 웹 애플리케이션이라고 합니다. 사용자가 액세스해야 하는 각 테넌트에서 다중 테넌트 애플리케이션을 프로비전해야 합니다. 애플리케이션이 등록된 테넌트가 아닌 다른 테넌트에서 해당 프로그램을 등록하기 위해 사용자 또는 관리자 동의가 필요합니다. 네이티브 클라이언트 애플리케이션은 기본적으로 리소스 소유자의 장치에 설치된 다중 테넌트입니다. 동의 프레임워크에 대한 자세한 내용은 앞에서 본 동의 프레임워크 개요 섹션을 참조하세요.

다중 테넌트 애플리케이션을 만드는 작업에는 애플리케이션 등록뿐만 아니라 웹 애플리케이션 자체도 변경해야 합니다. 다음 섹션에서 모두 설명합니다.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>다중 테넌트를 지원하도록 애플리케이션 등록 변경

고객이나 조직 외부의 파트너가 사용할 수 있는 애플리케이션을 작성 중인 경우 Azure Portal에서 애플리케이션 정의를 업데이트해야 합니다.

> [!IMPORTANT]
> Azure AD에서는 다중 테넌트 애플리케이션의 앱 ID URI가 전역적으로 고유해야 합니다. 앱 ID URI는 프로토콜 메시지에서 애플리케이션을 식별하는 방법 중 하나입니다. 단일 테넌트 애플리케이션의 경우 앱 ID URI이 해당 테넌트 내에서 고유한 것으로 충분합니다. 다중 테넌트 애플리케이션의 경우, 앱 ID URI이 전역적으로 고유해야 Azure AD가 모든 테넌트에서 애플리케이션을 찾을 수 있습니다.
> 앱 ID URI이 Azure AD 테넌트의 확인된 도메인과 일치하는 호스트 이름을 갖게 함으로써 전역 고유성이 적용됩니다. 예를 들어, 테넌트의 이름이 contoso.onmicrosoft.com이라면 유효한 앱 ID URI은 https://contoso.onmicrosoft.com/myapp이 될 것입니다. 테넌트에 contoso.com이라는 확인된 도메인이 있으면 유효한 앱 ID URI도 https://contoso.com/myapp이 됩니다. 앱 ID URI가 이 패턴을 따르지 않으면 애플리케이션을 다중 테넌트로 설정하지 못합니다.

애플리케이션에 액세스하는 기능을 외부 사용자에게 부여하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 계정이 하나 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 클릭하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
3. 왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록**을 차례로 클릭하고 구성하려는 새 애플리케이션을 찾아 클릭합니다. 애플리케이션의 기본 등록 페이지로 이동하면 애플리케이션의 **설정** 페이지를 엽니다.
4. **설정** 페이지에서 **속성**을 클릭하고 **다중 테넌트** 스위치를 **예**로 변경합니다.

변경한 후에는 다른 조직의 사용자와 관리자가 해당 사용자에게 애플리케이션에 로그인하는 권한을 부여할 수 있고 애플리케이션이 해당 테넌트에 의해 보호된 리소스에 액세스할 수 있습니다.

### <a name="changing-the-application-to-support-multi-tenant"></a>다중 테넌트를 지원하도록 애플리케이션 변경

다중 테넌트 애플리케이션에 대한 지원은 Azure AD 동의 프레임워크에 크게 의존합니다. 동의는 다른 테넌트의 사용자가 사용자의 테넌트에서 보호되는 리소스에 대한 애플리케이션 액세스 권한을 부여할 수 있게 하는 메커니즘입니다. 이 환경은 "사용자 동의"라고 합니다.

웹 애플리케이션은 다음 기능을 제공할 수도 있습니다.

- 관리자가 "회사를 등록"하는 기능 "관리자 동의"라고 하는 이 환경은 조직의 *모든 사용자*를 대신하여 관리자에게 동의를 부여할 수 있는 기능을 제공합니다. 전역 관리자 역할에 속해 있는 계정으로 인증하는 사용자만이 관리자 동의를 제공할 수 있으며, 다른 사용자에게는 오류가 발생합니다.
- 사용자의 등록 환경입니다. 사용자에게 Azure AD OAuth2.0 `/authorize` 엔드포인트 또는 OpenID Connect `/userinfo` 엔드포인트에 브라우저를 리디렉션하는 "등록" 단추를 제공할 예정입니다. 이러한 엔드포인트는 id_token을 검사하여 애플리케이션이 새 사용자에 대한 정보를 얻을 수 있도록 지원합니다. 가입 단계에 따라 동의 프레임워크 개요 섹션에 표시된 프롬프트와 유사한 동의 프롬프트가 사용자에게 표시됩니다.

다중 테넌트 액세스 및 로그인/등록 환경을 지원하는 데 필요한 애플리케이션 변경 내용에 대한 자세한 내용은 다음을 참조하세요.

- [다중 테넌트 애플리케이션 패턴을 사용하여 모든 Azure AD(Active Directory) 사용자를 로그인하는 방법](howto-convert-app-to-be-multi-tenant.md)
- [다중 테넌트 코드 샘플](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) 목록
- [빠른 시작: Azure AD에서 로그인 페이지에 회사 브랜딩 추가](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>단일 페이지 애플리케이션에 OAuth 2.0 암시적 허용 사용

SPA(단일 페이지 응용 프로그램)는 일반적으로 브라우저에서 실행되는 JavaScript 기반 프런트 엔드로 구성됩니다. 이는 응용 프로그램의 웹 API 백 엔드를 호출하여 비즈니스 논리를 수행합니다. Azure AD에서 호스트되는 SPA의 경우, OAuth 2.0 암시적 권한 부여를 사용하여 Azure AD에서 사용자를 인증하고, 애플리케이션의 JavaScript 클라이언트에서 해당 백 엔드 웹 API로의 보안 호출을 사용할 수 있는 토큰을 가져옵니다.

사용자가 승인하면 이 동일한 인증 프로토콜을 사용하여 클라이언트와 애플리케이션에 대해 구성된 다른 웹 API 리소스 간의 보안 호출을 위해 토큰을 가져올 수 있습니다. 암시적 권한 부여에 관한 자세한 내용을 확인하고 자신의 애플리케이션 시나리오에 적절한지 판단하려면 [Azure Active Directory에서 OAuth2 암시적 권한 부여 흐름 이해](v1-oauth2-implicit-grant-flow.md)를 참조하세요.

기본적으로 애플리케이션에 대해 OAuth 2.0 암시적 허용이 사용되지 않도록 설정됩니다. 해당 [애플리케이션 매니페스트](reference-app-manifest.md)에서 `oauth2AllowImplicitFlow` 값을 설정하여 애플리케이션에 OAuth 2.0 암시적 권한 부여를 사용할 수 있습니다.

### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 암시적 허용을 사용하도록 설정하려면

> [!NOTE]
> 애플리케이션 매니페스트를 편집하는 방법에 대한 세부 정보는 먼저 이전 섹션인 [웹 API를 노출하도록 리소스 애플리케이션 구성](#configuring-a-resource-application-to-expose-web-apis)을 검토해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 계정이 하나 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 클릭하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
3. 왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록**을 차례로 클릭하고 구성하려는 새 애플리케이션을 찾아 클릭합니다. 애플리케이션의 기본 등록 페이지로 이동하면 애플리케이션의 **설정** 페이지를 엽니다.
4. 애플리케이션의 등록 페이지에서 **매니페스트**를 클릭하여 **매니페스트 편집** 페이지로 전환합니다. 웹 기반 매니페스트 편집기가 열리면 포털 내에서 매니페스트를 **편집**할 수 있습니다. "oauth2AllowImplicitFlow" 값을 찾아 "true"로 설정합니다. 기본적으로 "false"로 설정됩니다.
   
   ```json
   "oauth2AllowImplicitFlow": true,
   ```
5. 업데이트된 매니페스트를 저장합니다. 저장되면 웹 API는 이제 OAuth 2.0 암시적 허용을 사용하여 사용자를 인증하도록 구성된 것입니다.

## <a name="next-steps"></a>다음 단계

Azure AD v1.0 엔드포인트를 사용하는 앱에 대한 관련된 다른 앱 관리 빠른 시작에 대해 알아봅니다.
- [Azure AD에 응용 프로그램 추가](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Azure AD에서 응용 프로그램 제거](quickstart-v1-remove-azure-ad-app.md)

등록된 애플리케이션 및 이들 간의 관계를 나타내는 두 개의 Azure AD 개체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

Azure Active Directory를 사용해 애플리케이션을 개발할 때 사용해야 하는 브랜딩 지침에 대해 자세히 알아보려면 [애플리케이션에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조하세요.
