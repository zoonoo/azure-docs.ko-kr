---
title: Azure 이벤트 허브 리소스에 액세스하는 응용 프로그램 인증
description: 이 문서에서는 Azure Active Directory를 사용하여 응용 프로그램을 인증하여 Azure Event Hubs 리소스에 액세스하는 방법을 제공합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: a242da8cc98a21248c48a1b3981fa713706028ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064951"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Azure Active Directory를 사용하여 응용 프로그램을 인증하여 이벤트 허브 리소스에 액세스
Microsoft Azure는 Azure AD(Active Directory)를 기반으로 하는 리소스 및 애플리케이션에 대해 통합된 액세스 제어 관리 기능을 제공합니다. Azure 이벤트 허브와 Azure AD를 사용하는 주요 이점은 더 이상 코드에 자격 증명을 저장할 필요가 없다는 것입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청하는 리소스 이름은 `https://eventhubs.azure.net/` (Kafka 클라이언트의 경우 토큰을 `https://<namespace>.servicebus.windows.net`요청하는 리소스는)입니다. Azure AD는 응용 프로그램을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 응용 프로그램에 대한 액세스 토큰을 반환하고 응용 프로그램은 액세스 토큰을 사용하여 Azure Event Hubs 리소스에 대한 요청을 승인할 수 있습니다.

역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스범위는 구독 수준, 리소스 그룹, 이벤트 허브 네임스페이스 또는 그 아래에 있는 리소스로 범위를 지정할 수 있습니다. Azure AD 보안은 Azure 리소스에 대한 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [관리되는 ID에](../active-directory/managed-identities-azure-resources/overview.md)역할을 할당할 수 있습니다. 

> [!NOTE]
> 역할 정의는 사용 권한 컬렉션입니다. RBAC(역할 기반 액세스 제어)는 역할 할당을 통해 이러한 사용 권한을 적용하는 방법을 제어합니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다. 자세한 내용은 [다른 역할 이해를](../role-based-access-control/overview.md)참조하십시오.

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure 이벤트 허브에 대한 기본 제공 역할
Azure는 Azure AD 및 OAuth를 사용하여 이벤트 허브 데이터에 대한 액세스를 승인하기 위한 다음 기본 제공 RBAC 역할을 제공합니다.

- [Azure 이벤트 허브 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): 이 역할을 사용하여 이벤트 허브 리소스에 대한 완전한 액세스 권한을 부여합니다.
- [Azure 이벤트 허브 데이터 보낸 자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): 이 역할을 사용하여 이벤트 허브 리소스에 대한 전송 액세스 권한을 부여합니다.
- [Azure 이벤트 허브 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): 이 역할을 사용하여 이벤트 허브 리소스에 대한 수신 액세스 권한을 부여합니다.   

> [!IMPORTANT]
> 미리 보기 릴리스는 소유자 또는 기여자 역할에 이벤트 허브 데이터 액세스 권한을 추가하는 것을 지원했습니다. 그러나 소유자 및 기여자 역할에 대한 데이터 액세스 권한은 더 이상 적용되지 않습니다. 소유자 또는 기여자 역할을 사용하는 경우 Azure 이벤트 허브 데이터 소유자 역할을 사용하여 전환합니다.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure 포털을 사용하여 RBAC 역할 할당  
RBAC 및 Azure 포털을 사용하여 Azure 리소스에 대한 액세스 관리에 대한 자세한 내용은 [이 문서를](..//role-based-access-control/role-assignments-portal.md)참조하세요. 

역할 할당에 대한 적절한 범위를 결정한 후 Azure Portal에서 해당 리소스로 이동합니다. 리소스에 대한 IAM(액세스 제어) 설정을 표시하고 다음 지침을 따라 역할 할당을 관리합니다.

> [!NOTE]
> 아래에 설명된 단계는 이벤트 허브 네임스페이스 에서 이벤트 허브에 역할을 할당하지만 동일한 단계를 수행하여 이벤트 허브 리소스에 범위가 지정된 역할을 할당할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com/)이벤트 허브 네임스페이스로 이동합니다.
2. **개요** 페이지에서 역할을 할당할 이벤트 허브를 선택합니다.

    ![이벤트 허브 선택](./media/authenticate-application/select-event-hub.png)
1. 이벤트 허브에 대한 액세스 제어 설정을 표시하려면 **IAM(액세스 제어)을** 선택합니다. 
1. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다. 도구 모음에서 **추가** 단추를 선택한 다음 **역할 할당 추가를**선택합니다. 

    ![도구 모음에 버튼 추가](./media/authenticate-application/role-assignments-add-button.png)
1. 역할 **할당 추가** 페이지에서 다음 단계를 수행합니다.
    1. 할당할 **이벤트 허브 역할을** 선택합니다. 
    1. 역할을 할당할 **보안 주체(사용자,** 그룹, 서비스 주체)를 찾으려고 검색합니다.
    1. 역할 할당을 저장하려면 **저장을** 선택합니다. 

        ![사용자에게 역할 할당](./media/authenticate-application/assign-role-to-user.png)
    4. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 Azure 사용자가 Azure 이벤트 허브 데이터 소유자 역할에 있음을 보여 주었습니다. 
        
        ![목록의 사용자](./media/authenticate-application/user-in-list.png)

비슷한 단계를 수행하여 Event Hubs 네임스페이스, 리소스 그룹 또는 구독에 범위가 지정된 역할을 할당할 수 있습니다. 역할 및 해당 범위를 정의하면 [이 GitHub 위치의](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)샘플을 사용하여 이 동작을 테스트할 수 있습니다.


## <a name="authenticate-from-an-application"></a>애플리케이션에서 인증
이벤트 허브와 Azure AD를 사용하는 주요 이점은 자격 증명을 더 이상 코드에 저장할 필요가 없다는 것입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 응용 프로그램을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 액세스 토큰을 응용 프로그램에 반환하고 응용 프로그램은 액세스 토큰을 사용하여 Azure Event Hubs에 대한 요청을 승인할 수 있습니다.

다음 섹션에서는 Microsoft ID 플랫폼 2.0을 사용하여 인증을 위해 기본 응용 프로그램 또는 웹 응용 프로그램을 구성하는 방법을 보여 줍니다. Microsoft ID 플랫폼 2.0에 대한 자세한 내용은 [Microsoft ID 플랫폼(v2.0) 개요를](../active-directory/develop/v2-overview.md)참조하십시오.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용하여 이벤트 허브 리소스를 승인하는 첫 번째 단계는 [Azure 포털에서](https://portal.azure.com/)Azure AD 테넌트에 클라이언트 응용 프로그램을 등록하는 것입니다. 클라이언트 응용 프로그램을 등록할 때 AD에 응용 프로그램에 대한 정보를 제공합니다. 그런 다음 Azure AD는 응용 프로그램을 Azure AD 런타임과 연결하는 데 사용할 수 있는 클라이언트 ID(응용 프로그램 ID라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

다음 이미지는 웹 응용 프로그램을 등록하는 단계를 보여 준다.

![애플리케이션 등록](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 응용 프로그램을 네이티브 응용 프로그램으로 등록하는 경우 리디렉션 URI에 대해 유효한 URI를 지정할 수 있습니다. 네이티브 응용 프로그램의 경우 이 값이 실제 URL일 필요는 없습니다. 웹 응용 프로그램의 경우 토큰이 제공되는 URL을 지정하므로 리디렉션 URI는 유효한 URI여야 합니다.

응용 프로그램을 등록하면 **설정**아래에 응용 **프로그램(클라이언트) ID가** 표시됩니다.

![등록된 신청서의 신청 ID](./media/authenticate-application/application-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/quickstart-v2-register-an-app.md)을 참조하세요.


### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
응용 프로그램에는 토큰을 요청할 때 클라이언트 보안 이 필요합니다. 클라이언트 보안 을 추가하려면 다음 단계를 따르십시오.

1. Azure 포털에서 앱 등록으로 이동합니다.
1. 인증서 & 비밀 설정을 **선택합니다.**
1. **클라이언트 암호에서** **새 클라이언트 비밀을** 선택하여 새 비밀을 만듭니다.
1. 비밀에 대한 설명을 제공하고 원하는 만료 간격을 선택합니다.
1. 새 비밀의 값을 즉시 안전한 위치에 복사합니다. 채우기 값은 한 번만 표시됩니다.

    ![클라이언트 암호](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>토큰 수집을 위한 클라이언트 라이브러리  
응용 프로그램을 등록하고 Azure Event Hubs에서 데이터를 보내고 받을 수 있는 권한을 부여한 후에는 응용 프로그램에 코드를 추가하여 보안 주체를 인증하고 OAuth 2.0 토큰을 획득할 수 있습니다. 토큰을 인증하고 획득하려면 [Microsoft ID 플랫폼 인증 라이브러리](../active-directory/develop/reference-v2-libraries.md) 중 하나 또는 OpenID 또는 Connect 1.0을 지원하는 다른 오픈 소스 라이브러리를 사용할 수 있습니다. 그런 다음 응용 프로그램에서 액세스 토큰을 사용하여 Azure Event Hubs에 대한 요청을 승인할 수 있습니다.

토큰 획득이 지원되는 시나리오 목록은 .NET GitHub 리포지토리에 [대한 MSAL(Microsoft 인증 라이브러리)의](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [시나리오](https://aka.ms/msal-net-scenarios) 섹션을 참조하십시오.

## <a name="samples"></a>샘플
- [Microsoft.Azure.EventHubs 샘플.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    이러한 샘플은 이전 **Microsoft.Azure.EventHubs 라이브러리를** 사용하지만 최신 **Azure.Messaging.EventHubs 라이브러리를** 사용하도록 쉽게 업데이트할 수 있습니다. 샘플을 이전 라이브러리를 새 라이브러리로 이동하려면 [Microsoft.Azure.EventHubs에서 Azure.Messaging.EventHubs로 마이그레이션하는 가이드를](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)참조하십시오.
- [Azure.메시징.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    이 샘플은 최신 **Azure.Messaging.EventHubs 라이브러리를** 사용하도록 업데이트되었습니다.

## <a name="next-steps"></a>다음 단계
- RBAC에 대해 자세히 알아보려면 [역할 기반 액세스 제어(RBAC)란 무엇입니까?](../role-based-access-control/overview.md)
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 RBAC 역할 할당을 할당하고 관리하는 방법을 알아보려면 다음과 같은 아티클을 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-rest.md)
    - [Azure 리소스 관리자 템플릿을 사용 하 고 역할 기반 액세스 제어 (RBAC) 관리](../role-based-access-control/role-assignments-template.md)

다음 관련 문서를 참조하십시오.
- [Azure Active Directory를 사용하여 관리되는 ID를 인증하여 이벤트 허브 리소스에 액세스](authenticate-managed-identity.md)
- [공유 액세스 서명을 사용하여 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-shared-access-signature.md)
- [Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)
- [공유 액세스 서명을 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)

