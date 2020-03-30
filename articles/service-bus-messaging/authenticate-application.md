---
title: Azure 서비스 버스 엔터티에 액세스하는 응용 프로그램 인증
description: 이 문서에서는 Azure Service Bus 엔터티(큐, 항목 등)에 액세스하기 위해 Azure Active Directory를 사용하여 응용 프로그램을 인증하는 방법을 제공합니다.
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259293"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Azure Active Directory를 사용하여 응용 프로그램을 인증하고 인증하여 Azure Service Bus 엔터티에 액세스
Azure Service Bus는 Azure Active Directory(Azure AD)를 사용하여 서비스 버스 엔터티(큐, 토픽, 구독 또는 필터)에 대한 요청을 승인합니다. Azure AD를 사용하면 역할 기반 액세스 제어(RBAC)를 사용하여 사용자, 그룹 또는 응용 프로그램 서비스 주체일 수 있는 보안 주체에 대한 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대해 자세히 알아보려면 [다양한 역할 이해를](../role-based-access-control/overview.md)참조하십시오.

## <a name="overview"></a>개요
보안 주체(사용자, 그룹 또는 응용 프로그램)가 Service Bus 엔터티에 액세스하려고 하면 요청이 승인되어야 합니다. Azure AD를 사용하면 리소스에 대한 액세스는 2단계 프로세스입니다. 

 1. 먼저 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://servicebus.azure.net`.
 1. 다음으로 토큰은 지정된 리소스에 대한 액세스를 승인하기 위해 Service Bus 서비스에 대한 요청의 일부로 전달됩니다.

인증 단계에서는 응용 프로그램 요청에 런타임시 OAuth 2.0 액세스 토큰이 포함되어 야 합니다. 응용 프로그램이 Azure VM, 가상 시스템 규모 집합 또는 Azure Function 앱과 같은 Azure 엔터티 내에서 실행 중인 경우 관리되는 ID를 사용하여 리소스에 액세스할 수 있습니다. 관리되는 ID에서 Service Bus 서비스에 대한 요청을 인증하는 방법을 알아보려면 [Azure Active Directory를 사용하여 Azure Service Bus 리소스에 대한 인증 액세스 및 Azure 리소스에 대한 관리되는 ID를](service-bus-managed-service-identity.md)참조하세요. 

권한 부여 단계에서는 하나 이상의 RBAC 역할을 보안 주체에 할당해야 합니다. Azure Service Bus는 서비스 버스 리소스에 대한 사용 권한 집합을 포함하는 RBAC 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가질 수 있는 권한이 결정됩니다. Azure Service Bus에 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Service Bus에 대한 기본 제공 RBAC 역할을](#built-in-rbac-roles-for-azure-service-bus)참조하십시오. 

Service Bus에 요청을 하는 네이티브 응용 프로그램 및 웹 응용 프로그램도 Azure AD로 권한을 부여할 수 있습니다. 이 문서에서는 액세스 토큰을 요청하고 이를 사용하여 Service Bus 리소스에 대한 요청을 승인하는 방법을 보여 주며 이 문서에서는 


## <a name="assigning-rbac-roles-for-access-rights"></a>액세스 권한에 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Service Bus는 Service Bus 엔터티에 액세스하는 데 사용되는 일반적인 사용 권한 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의하고 데이터에 액세스하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스범위는 구독 수준, 리소스 그룹 또는 Service Bus 네임스페이스로 범위를 지정할 수 있습니다. Azure AD 보안 주체는 Azure 리소스에 대한 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [관리되는 ID일](../active-directory/managed-identities-azure-resources/overview.md)수 있습니다.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure 서비스 버스에 대한 기본 제공 RBAC 역할
Azure Service Bus의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 RBAC(*역할 기반 액세스 제어*) 모델을 사용하여 보호되고 있습니다. Azure는 서비스 버스 네임스페이스에 대한 액세스를 승인하기 위한 아래기본 제공 RBAC 역할을 제공합니다.

- [Azure Service Bus 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): 서비스 버스 네임스페이스 및 해당 엔터티(큐, 토픽, 구독 및 필터)에 대한 데이터 액세스를 활성화합니다.
- [Azure Service 버스 데이터 보낸 자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 send 액세스 권한을 부여합니다.
- [Azure Service 버스 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 수신 액세스를 제공합니다. 

## <a name="resource-scope"></a>리소스 범위 
보안 주체에 RBAC 역할을 할당하기 전에 보안 주체가 가져야 할 액세스 범위를 결정합니다. 모범 사례는 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위부터 시작하여 Service Bus 리소스에 대한 액세스 범위를 좁힐 수 있는 수준에 대해 설명합니다.

- **큐,** **토픽**또는 **구독**: 역할 할당은 특정 서비스 버스 엔터티에 적용됩니다. 현재 Azure 포털은 구독 수준에서 Service Bus RBAC 역할에 사용자/그룹/관리ID 할당을 지원하지 않습니다. 
- **서비스 버스 네임스페이스**: 역할 할당은 네임스페이스 아래의 서비스 버스의 전체 토폴로지와 연결된 소비자 그룹에 걸쳐 있습니다.
- **리소스 그룹:** 역할 할당은 리소스 그룹 아래의 모든 Service Bus 리소스에 적용됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹의 모든 Service Bus 리소스에 적용됩니다.

> [!NOTE]
> RBAC 역할 할당이 전파되는 데 최대 5분이 걸릴 수 있습니다. 

기본 제공 역할이 정의되는 방법에 대한 자세한 내용은 [역할 정의 이해하기](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하십시오. 사용자 지정 RBAC 역할 만들기에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어에 대한 사용자 지정 역할 만들기를](../role-based-access-control/custom-roles.md)참조하십시오.


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure 포털을 사용하여 RBAC 역할 할당  
RBAC 및 Azure 포털을 사용하여 Azure 리소스에 대한 액세스 관리에 대한 자세한 내용은 [이 문서를](..//role-based-access-control/role-assignments-portal.md)참조하세요. 

역할 할당에 대한 적절한 범위를 결정한 후 Azure Portal에서 해당 리소스로 이동합니다. 리소스에 대한 IAM(액세스 제어) 설정을 표시하고 다음 지침을 따라 역할 할당을 관리합니다.

> [!NOTE]
> 아래에 설명된 단계는 서비스 버스 네임스페이스에 역할을 할당합니다. 동일한 단계를 수행하여 지원되는 다른 범위(리소스 그룹, 구독 등)에 역할을 할당할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com/)Service Bus 네임스페이스로 이동합니다. 네임스페이스에 대한 액세스 제어 설정을 표시하려면 왼쪽 메뉴에서 **IAM(액세스 제어)을** 선택합니다. 서비스 버스 네임스페이스를 만들어야 하는 경우 이 문서의 [지침: 서비스 버스 메시징 네임스페이스 만들기.](service-bus-create-namespace-portal.md)

    ![왼쪽 메뉴에서 액세스 제어 선택](./media/authenticate-application/select-access-control-menu.png)
1. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다. 도구 모음에서 **추가** 단추를 선택한 다음 **역할 할당 추가를**선택합니다. 

    ![도구 모음에 버튼 추가](./media/authenticate-application/role-assignments-add-button.png)
1. 역할 **할당 추가** 페이지에서 다음 단계를 수행합니다.
    1. 할당할 **서비스 버스 역할을** 선택합니다. 
    1. 역할을 할당할 **보안 주체(사용자,** 그룹, 서비스 주체)를 찾으려고 검색합니다.
    1. 역할 할당을 저장하려면 **저장을** 선택합니다. 

        ![사용자에게 역할 할당](./media/authenticate-application/assign-role-to-user.png)
    4. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 Azure 사용자가 Azure 서비스 버스 데이터 소유자 역할에 있음을 보여 주었습니다. 
        
        ![목록의 사용자](./media/authenticate-application/user-in-list.png)

유사한 단계를 수행하여 리소스 그룹 또는 구독에 범위가 지정된 역할을 할당할 수 있습니다. 역할 및 해당 범위를 정의한 후에는 [GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)의 샘플을 사용하여 이 동작을 테스트할 수 있습니다.


## <a name="authenticate-from-an-application"></a>애플리케이션에서 인증
Service Bus와 Azure AD를 사용하는 주요 이점은 자격 증명을 더 이상 코드에 저장할 필요가 없다는 것입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 응용 프로그램을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 액세스 토큰을 응용 프로그램에 반환하고 응용 프로그램은 액세스 토큰을 사용하여 Azure Service Bus에 대한 요청을 승인할 수 있습니다.

다음 섹션에서는 Microsoft ID 플랫폼 2.0을 사용하여 인증을 위해 기본 응용 프로그램 또는 웹 응용 프로그램을 구성하는 방법을 보여 줍니다. Microsoft ID 플랫폼 2.0에 대한 자세한 내용은 [Microsoft ID 플랫폼(v2.0) 개요를](../active-directory/develop/v2-overview.md)참조하십시오.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용하여 Service Bus 엔터티를 승인하는 첫 번째 단계는 [Azure 포털에서](https://portal.azure.com/)Azure AD 테넌트를 사용하여 클라이언트 응용 프로그램을 등록하는 것입니다. 클라이언트 응용 프로그램을 등록할 때 AD에 응용 프로그램에 대한 정보를 제공합니다. 그런 다음 Azure AD는 응용 프로그램을 Azure AD 런타임과 연결하는 데 사용할 수 있는 클라이언트 ID(응용 프로그램 ID라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

다음 이미지는 웹 응용 프로그램을 등록하는 단계를 보여 준다.

![애플리케이션 등록](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 응용 프로그램을 네이티브 응용 프로그램으로 등록하는 경우 리디렉션 URI에 대해 유효한 URI를 지정할 수 있습니다. 네이티브 응용 프로그램의 경우 이 값이 실제 URL일 필요는 없습니다. 웹 응용 프로그램의 경우 토큰이 제공되는 URL을 지정하므로 리디렉션 URI는 유효한 URI여야 합니다.

응용 프로그램을 등록하면 **설정**아래에 응용 **프로그램(클라이언트) ID가** 표시됩니다.

![등록된 신청서의 신청 ID](./media/authenticate-application/application-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/quickstart-v2-register-an-app.md)을 참조하세요.

> [!IMPORTANT]
> **테넌트 Id** 및 **ApplicationId를 기록해**둡자 확인합니다. 응용 프로그램을 실행하려면 이러한 값이 필요합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
응용 프로그램에는 토큰을 요청할 때 클라이언트 보안 이 필요합니다. 클라이언트 보안 을 추가하려면 다음 단계를 따르십시오.

1. 페이지에 아직 없는 경우 Azure 포털에서 앱 등록으로 이동합니다.
1. 왼쪽 메뉴에서 **인증서 & 비밀을** 선택합니다.
1. **클라이언트 암호에서** **새 클라이언트 비밀을** 선택하여 새 비밀을 만듭니다.

    ![새 클라이언트 비밀 - 버튼](./media/authenticate-application/new-client-secret-button.png)
1. 비밀에 대한 설명을 제공하고 원하는 만료 간격을 선택한 다음 **추가를**선택합니다.

    ![클라이언트 비밀 페이지 추가](./media/authenticate-application/add-client-secret-page.png)
1. 새 비밀의 값을 즉시 안전한 위치에 복사합니다. 채우기 값은 한 번만 표시됩니다.

    ![클라이언트 암호](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>서비스 버스 API에 대한 권한
응용 프로그램이 콘솔 응용 프로그램인 경우 기본 응용 프로그램을 등록하고 필요한 사용 권한 집합에 **Microsoft.ServiceBus에** 대한 API 권한을 추가해야 **합니다.** 네이티브 응용 프로그램에는 식별자 역할을 하는 Azure AD에서 **리디렉션 URI가** 필요합니다. URI가 네트워크 대상일 필요는 없습니다. 이 예제의 경우 샘플 코드가 이미 해당 URI를 사용하므로 `https://servicebus.microsoft.com`을 사용합니다.

### <a name="client-libraries-for-token-acquisition"></a>토큰 수집을 위한 클라이언트 라이브러리  
응용 프로그램을 등록하고 Azure Service Bus에서 데이터를 보내고 받을 수 있는 권한을 부여한 후에는 응용 프로그램에 코드를 추가하여 보안 주체를 인증하고 OAuth 2.0 토큰을 획득할 수 있습니다. 토큰을 인증하고 획득하려면 [Microsoft ID 플랫폼 인증 라이브러리](../active-directory/develop/reference-v2-libraries.md) 중 하나 또는 OpenID 또는 Connect 1.0을 지원하는 다른 오픈 소스 라이브러리를 사용할 수 있습니다. 그런 다음 응용 프로그램에서 액세스 토큰을 사용하여 Azure Service Bus에 대한 요청을 승인할 수 있습니다.

토큰 획득이 지원되는 시나리오 목록은 .NET GitHub 리포지토리에 [대한 MSAL(Microsoft 인증 라이브러리)의](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [시나리오](https://aka.ms/msal-net-scenarios) 섹션을 참조하십시오.

## <a name="sample-on-github"></a>GitHub의 샘플
GitHub: [서비스 버스에 대한 역할 기반 액세스 제어의](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)다음 샘플을 참조하십시오. 

대화형 사용자 로그인 옵션이 아닌 **클라이언트 비밀** **로그인** 옵션을 사용합니다. 클라이언트 보안 옵션을 사용하면 팝업 창이 표시되지 않습니다. 응용 프로그램은 테넌트 ID와 앱 ID를 인증에 사용합니다. 

### <a name="run-the-sample"></a>샘플 실행

샘플을 실행하기 전에 **app.config** 파일을 편집하고 시나리오에 따라 다음 값을 설정합니다.

- `tenantId`: **TenantId** 값으로 설정합니다.
- `clientId`: **ApplicationId** 값으로 설정합니다.
- `clientSecret`: 클라이언트 암호를 사용하여 로그인하려는 경우 Azure AD에서 만듭니다. 또한 네이티브 앱 대신 웹앱 또는 API를 사용합니다. 또한 앱을 이전에 만든 네임스페이스의 **액세스 제어(IAM)** 에 추가합니다.
- `serviceBusNamespaceFQDN`: 새로 만든 Service Bus 네임스페이스의 전체 DNS 이름으로 설정합니다(예: `example.servicebus.windows.net`).
- `queueName`: 만든 큐의 이름으로 설정합니다.
- 이전 단계에서 앱에 지정된 리디렉션 URI입니다.

콘솔 응용 프로그램을 실행하면 시나리오를 선택하라는 메시지가 표시됩니다. 숫자를 입력하고 ENTER를 눌러 **대화형 사용자 로그인을** 선택합니다. 애플리케이션에서는 로그인 창이 표시되고, Service Bus에 액세스할지 묻는 메시지가 표시된 다음, 서비스를 사용하여 로그인 ID를 통해 보내기/받기 시나리오를 실행합니다.


## <a name="next-steps"></a>다음 단계
- RBAC에 대해 자세히 알아보려면 [역할 기반 액세스 제어(RBAC)란 무엇입니까?](../role-based-access-control/overview.md)
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 RBAC 역할 할당을 할당하고 관리하는 방법을 알아보려면 다음과 같은 아티클을 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-rest.md)
    - [Azure 리소스 관리자 템플릿을 사용 하 고 역할 기반 액세스 제어 (RBAC) 관리](../role-based-access-control/role-assignments-template.md)

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [서비스 버스 RBAC 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
- [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
