---
title: Service Bus를 통해 Azure 리소스에 관리 ID 사용
description: 이 문서에서는 관리 되는 id를 사용 하 여 Azure Service Bus 엔터티 (큐, 토픽 및 구독)를 통해 액세스 하는 방법을 설명 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 01fe6c59ad878276619a96666e2da82b6a1b1fe9
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371229"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Azure Service Bus 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증
[Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 애플리케이션 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음 애플리케이션에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 애플리케이션 내에서 실행되거나 Azure 리소스용 관리 ID를 지원하도록 설정된 가상 머신에서 실행되는 Service Bus 클라이언트 앱은 SAS 규칙과 키 또는 기타 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Service Bus 메시지 네임스페이스의 엔드포인트 주소만 있으면 됩니다. 앱이 연결되면 Service Bus는 이 문서 뒷부분의 예제에 나와 있는 작업에서 관리 ID의 컨텍스트를 클라이언트에 바인딩합니다. 관리 ID와 연결된 Service Bus 클라이언트는 권한이 부여된 모든 작업을 수행할 수 있습니다. 관리 ID를 Service Bus 역할에 연결하는 방식을 통해 권한이 부여됩니다. 

## <a name="overview"></a>개요
보안 주체 (사용자, 그룹 또는 응용 프로그램)가 Service Bus 엔터티에 대 한 액세스를 시도 하는 경우 요청에 권한이 부여 되어야 합니다. Azure AD를 사용 하 여 리소스에 대 한 액세스는 2 단계 프로세스입니다. 

 1. 먼저, 보안 주체의 id가 인증 되 고 OAuth 2.0 토큰이 반환 됩니다. 토큰을 요청 하는 리소스 이름은 `https://servicebus.azure.net` 입니다.
 1. 그런 다음 토큰은 지정 된 리소스에 대 한 액세스 권한을 부여 하기 위해 Service Bus 서비스에 대 한 요청의 일부로 전달 됩니다.

인증 단계를 수행 하려면 응용 프로그램 요청이 런타임에 OAuth 2.0 액세스 토큰을 포함 해야 합니다. 응용 프로그램이 azure VM, 가상 머신 확장 집합 또는 azure 함수 앱과 같은 Azure 엔터티 내에서 실행 되는 경우 관리 id를 사용 하 여 리소스에 액세스할 수 있습니다. 

권한 부여 단계를 수행 하려면 하나 이상의 RBAC 역할을 보안 주체에 할당 해야 합니다. Azure Service Bus는 Service Bus 리소스에 대 한 권한 집합을 포함 하는 RBAC 역할을 제공 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 부여 되는 사용 권한이 결정 됩니다. Azure Service Bus에 RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure Service Bus에 대 한 Azure 기본 제공 역할](#azure-built-in-roles-for-azure-service-bus)을 참조 하세요. 

Service Bus에 대 한 요청을 하는 네이티브 응용 프로그램 및 웹 응용 프로그램은 Azure AD를 사용 하 여 권한을 부여할 수도 있습니다. 이 문서에서는 액세스 토큰을 요청 하 고이를 사용 하 여 Service Bus 리소스에 대 한 요청에 권한을 부여 하는 방법을 보여 줍니다. 


## <a name="assigning-rbac-roles-for-access-rights"></a>액세스 권한에 대 한 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Service Bus는 Service Bus 엔터티에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 Azure 기본 제공 역할 집합을 정의 하 고 데이터에 액세스 하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 주체에 할당 되 면 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스 범위는 구독, 리소스 그룹 또는 Service Bus 네임 스페이스의 수준으로 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 응용 프로그램 서비스 주체 또는 Azure 리소스에 대 한 관리 되는 id 일 수 있습니다.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure Service Bus에 대 한 Azure 기본 제공 역할
Azure Service Bus의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 RBAC(*역할 기반 액세스 제어*) 모델을 사용하여 보호되고 있습니다. Azure는 Service Bus 네임 스페이스에 대 한 액세스 권한을 부여 하기 위한 다음과 같은 Azure 기본 제공 역할을 제공 합니다.

- [Azure Service Bus 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Service Bus 네임 스페이스와 해당 엔터티 (큐, 토픽, 구독 및 필터)에 대 한 데이터 액세스를 가능 하 게 합니다.
- [Azure Service Bus 데이터 발신자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender):이 역할을 사용 하 여 Service Bus 네임 스페이스 및 해당 엔터티에 대 한 액세스를 보낼 수 있습니다.
- [Azure Service Bus 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver):이 역할을 사용 하 여 Service Bus 네임 스페이스 및 해당 엔터티에 대 한 액세스 권한을 부여할 수 있습니다. 

## <a name="resource-scope"></a>리소스 범위 
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체에 게 부여 해야 하는 액세스 범위를 결정 합니다. 모범 사례는 항상 가장 좁은 범위를 부여 하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위에서 시작 하 여 Service Bus 리소스에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.

- **큐**, **토픽**또는 **구독**: 역할 할당은 특정 Service Bus 엔터티에 적용 됩니다. 현재 Azure Portal는 구독 수준에서 RBAC 역할을 Service Bus 하는 사용자/그룹/관리 id 할당을 지원 하지 않습니다. Azure CLI 명령을 사용 하는 예제는 다음과 같습니다. [az-role-create-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) 를 사용 하 여 id를 Service Bus RBAC 역할에 할당 합니다. 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus 네임 스페이스**: 역할 할당은 네임 스페이스 아래 Service Bus의 전체 토폴로지 및 이와 연결 된 소비자 그룹을 확장 합니다.
- **리소스 그룹**: 역할 할당은 리소스 그룹 아래의 모든 Service Bus 리소스에 적용 됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 Service Bus 리소스에 적용 됩니다.

> [!NOTE]
> RBAC 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있다는 점에 유의 하세요. 

기본 제공 역할을 정의 하는 방법에 대 한 자세한 내용은 [역할 정의 이해](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조 하세요. Azure 사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 [azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조 하세요.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용
Azure 리소스에 관리 되는 id를 사용 하 여 VM에서 Service Bus 리소스에 권한을 부여 하려면 먼저 VM에서 Azure 리소스에 대 한 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리 되는 id에 대 한 사용 권한 부여
응용 프로그램의 관리 되는 id에서 Service Bus 서비스에 대 한 요청에 권한을 부여 하려면 먼저 관리 되는 id에 대 한 RBAC (역할 기반 액세스 제어) 설정을 구성 합니다. Azure Service Bus Service Bus에서 전송 및 읽기 권한을 포함 하는 RBAC 역할을 정의 합니다. RBAC 역할이 관리 id에 할당 되 면 관리 되는 id에 적절 한 범위의 Service Bus 엔터티에 대 한 액세스 권한이 부여 됩니다.

RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [Service Bus 리소스에 대 한 액세스를 위해 Azure Active Directory 인증 및 권한 부여](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)를 참조 하세요.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus를 통해 Azure 리소스에 관리 ID 사용
관리 id에 Service Bus를 사용 하려면 id를 역할과 적절 한 범위에 할당 해야 합니다. 이 섹션의 절차에서는 관리 되는 id로 실행 되 고 Service Bus 리소스에 액세스 하는 간단한 응용 프로그램을 사용 합니다.

여기서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에서 호스트 되는 샘플 웹 응용 프로그램을 사용 하 고 있습니다. 웹 응용 프로그램을 만드는 방법에 대 한 단계별 지침은 [Azure에서 ASP.NET Core 웹 앱 만들기](../app-service/app-service-web-get-started-dotnet.md) 를 참조 하세요.

응용 프로그램을 만든 후에는 다음 단계를 수행 합니다. 

1. **설정** 으로 이동 하 여 **id**를 선택 합니다. 
1. **상태** **를 선택 합니다.** 
1. **저장**을 선택하여 설정을 저장합니다. 

    ![웹 앱에 대 한 관리 id](./media/service-bus-managed-service-identity/identity-web-app.png)

이 설정을 사용 하도록 설정 하면 Azure Active Directory (Azure AD)에서 새 서비스 id가 만들어지고 App Service 호스트로 구성 됩니다.

이제 Service Bus 리소스에서 필요한 범위의 역할에이 서비스 id를 할당 합니다.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Azure Portal를 사용 하 여 RBAC 역할을 할당 하려면
Service Bus 네임 스페이스에 역할을 할당 하려면 Azure Portal에서 네임 스페이스로 이동 합니다. 리소스의 IAM (Access Control) 설정을 표시 하 고 다음 지침에 따라 역할 할당을 관리 합니다.

> [!NOTE]
> 다음 단계에서는 Service Bus 네임 스페이스에 서비스 id 역할을 할당 합니다. 동일한 단계에 따라 지원 되는 다른 범위 (리소스 그룹 및 구독)에서 역할을 할당할 수 있습니다. 
> 
> 없는 경우 [Service Bus Messaging 네임 스페이스를 만듭니다](service-bus-create-namespace-portal.md) . 

1. Azure Portal에서 Service Bus 네임 스페이스로 이동 하 여 네임 스페이스에 대 한 **개요** 를 표시 합니다. 
1. 왼쪽 메뉴에서 **Access Control (IAM)** 을 선택 하 여 Service Bus 네임 스페이스에 대 한 액세스 제어 설정을 표시 합니다.
1.  **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
3.  **추가** 를 선택 하 여 새 역할을 추가 합니다.
4.  **역할 할당 추가** 페이지에서 할당 하려는 Azure Service Bus 역할을 선택 합니다. 그런 다음를 검색 하 여 역할을 할당 하기 위해 등록 한 서비스 id를 찾습니다.
    
    ![역할 할당 추가 페이지](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  **저장**을 선택합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 서비스 id가 데이터 소유자 Azure Service Bus 있음을 보여 줍니다.
    
    ![역할에 할당 된 id](./media/service-bus-managed-service-identity/role-assigned.png)

역할을 할당 하면 웹 응용 프로그램은 정의 된 범위에서 Service Bus 엔터티에 대 한 액세스 권한을 갖게 됩니다. 

### <a name="run-the-app"></a>앱 실행

이제 만든 ASP.NET 애플리케이션의 기본 페이지를 수정합니다. [이 GitHub 리포지토리](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)의 웹 애플리케이션 코드를 사용할 수 있습니다.  

Default.aspx 페이지가 방문 페이지입니다. 코드는 Default.aspx.cs 파일에서 찾을 수 있습니다. 그 결과 몇 가지 입력 필드와 메시지를 보내거나 받기 위해 Service Bus에 연결되는 **전송** 및 **수신** 단추가 있는 최소 웹 애플리케이션이 생성됩니다.

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 개체가 어떻게 초기화되는지에 유의합니다. 이 코드는 SAS(공유 액세스 토큰) 토큰 공급자를 사용하는 대신 `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` 호출을 사용하여 관리 ID용 토큰 공급자를 만듭니다. 이와 같이 유지하고 사용하는 데 특별한 비결은 없습니다. Service Bus 및 권한 부여 핸드셰이크에 대한 관리 ID 컨텍스트의 흐름은 토큰 공급자가 자동으로 처리합니다. 이 모델은 SAS를 사용하는 방식보다 단순합니다.

이와 같이 변경한 후에는 애플리케이션을 게시하고 실행합니다. Visual Studio에서 게시 프로필을 다운로드한 다음 가져오면 올바른 게시 데이터를 쉽게 가져올 수 있습니다.

![게시 프로필 가져오기](./media/service-bus-managed-service-identity/msi3.png)
 
메시지를 보내거나 받으려면 만든 엔터티의 이름과 네임스페이스의 이름을 입력합니다. 그런 후에 **보내기**나 **받기**를 클릭합니다.


> [!NOTE]
> - 관리 ID는 Azure 환경, App 서비스, Azure VM 및 확장 집합 내에서만 작동합니다. .NET 애플리케이션의 경우 Service Bus NuGet 패키지에서 사용되는 Microsoft.Azure.Services.AppAuthentication 라이브러리는 이 프로토콜에 대한 추상화를 제공하고 로컬 개발 환경을 지원합니다. 또한 이 라이브러리를 통해 개발 머신에서 Visual Studio, Azure CLI 2.0 또는 Active Directory 통합 인증의 사용자 계정을 사용하여 코드를 로컬로 테스트할 수 있습니다. 이 라이브러리를 통한 로컬 개발 옵션에 대한 자세한 내용은 [.NET을 사용하여 Azure Key Vault에 대한 서비스 간 인증](../key-vault/general/service-to-service-authentication.md)을 참조하세요.  
> 
> - 현재 관리 ID는 App Service 배포 슬롯에서 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
