---
title: Service Bus를 통해 Azure 리소스에 관리 ID 사용
description: 이 문서에서는 관리되는 ID를 사용하여 Azure Service Bus 엔터티(큐, 토픽 및 구독)에 액세스하는 방법을 설명합니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 89de6bf80d14ec77fe6b1f98b6e1d15c6e573fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756286"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Azure Active Directory를 사용하여 관리되는 ID를 인증하여 Azure Service Bus 리소스에 액세스
[Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 애플리케이션 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음 애플리케이션에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 애플리케이션 내에서 실행되거나 Azure 리소스용 관리 ID를 지원하도록 설정된 가상 머신에서 실행되는 Service Bus 클라이언트 앱은 SAS 규칙과 키 또는 기타 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Service Bus 메시지 네임스페이스의 엔드포인트 주소만 있으면 됩니다. 앱이 연결되면 Service Bus는 이 문서 뒷부분의 예제에 나와 있는 작업에서 관리 ID의 컨텍스트를 클라이언트에 바인딩합니다. 관리 ID와 연결된 Service Bus 클라이언트는 권한이 부여된 모든 작업을 수행할 수 있습니다. 관리 ID를 Service Bus 역할에 연결하는 방식을 통해 권한이 부여됩니다. 

## <a name="overview"></a>개요
보안 주체(사용자, 그룹 또는 응용 프로그램)가 Service Bus 엔터티에 액세스하려고 하면 요청이 승인되어야 합니다. Azure AD를 사용하면 리소스에 대한 액세스는 2단계 프로세스입니다. 

 1. 먼저 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://servicebus.azure.net`.
 1. 다음으로 토큰은 지정된 리소스에 대한 액세스를 승인하기 위해 Service Bus 서비스에 대한 요청의 일부로 전달됩니다.

인증 단계에서는 응용 프로그램 요청에 런타임시 OAuth 2.0 액세스 토큰이 포함되어 야 합니다. 응용 프로그램이 Azure VM, 가상 시스템 규모 집합 또는 Azure Function 앱과 같은 Azure 엔터티 내에서 실행 중인 경우 관리되는 ID를 사용하여 리소스에 액세스할 수 있습니다. 

권한 부여 단계에서는 하나 이상의 RBAC 역할을 보안 주체에 할당해야 합니다. Azure Service Bus는 서비스 버스 리소스에 대한 사용 권한 집합을 포함하는 RBAC 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가질 수 있는 권한이 결정됩니다. Azure Service Bus에 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Service Bus에 대한 기본 제공 RBAC 역할을](#built-in-rbac-roles-for-azure-service-bus)참조하십시오. 

Service Bus에 요청을 하는 네이티브 응용 프로그램 및 웹 응용 프로그램도 Azure AD로 권한을 부여할 수 있습니다. 이 문서에서는 액세스 토큰을 요청하고 이를 사용하여 Service Bus 리소스에 대한 요청을 승인하는 방법을 보여 주며 이 문서에서는 


## <a name="assigning-rbac-roles-for-access-rights"></a>액세스 권한에 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Service Bus는 Service Bus 엔터티에 액세스하는 데 사용되는 일반적인 사용 권한 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의하고 데이터에 액세스하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스범위는 구독 수준, 리소스 그룹 또는 Service Bus 네임스페이스로 범위를 지정할 수 있습니다. Azure AD 보안 주체는 Azure 리소스에 대한 사용자, 그룹, 응용 프로그램 서비스 주체 또는 관리되는 ID일 수 있습니다.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure 서비스 버스에 대한 기본 제공 RBAC 역할
Azure Service Bus의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 RBAC(*역할 기반 액세스 제어*) 모델을 사용하여 보호되고 있습니다. Azure는 서비스 버스 네임스페이스에 대한 액세스를 승인하기 위한 아래기본 제공 RBAC 역할을 제공합니다.

- [Azure Service Bus 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): 서비스 버스 네임스페이스 및 해당 엔터티(큐, 토픽, 구독 및 필터)에 대한 데이터 액세스를 활성화합니다.
- [Azure Service 버스 데이터 보낸 자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 send 액세스 권한을 부여합니다.
- [Azure Service 버스 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 수신 액세스를 제공합니다. 

## <a name="resource-scope"></a>리소스 범위 
보안 주체에 RBAC 역할을 할당하기 전에 보안 주체가 가져야 할 액세스 범위를 결정합니다. 모범 사례는 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위부터 시작하여 Service Bus 리소스에 대한 액세스 범위를 좁힐 수 있는 수준에 대해 설명합니다.

- **큐,** **토픽**또는 **구독**: 역할 할당은 특정 서비스 버스 엔터티에 적용됩니다. 현재 Azure 포털은 구독 수준에서 Service Bus RBAC 역할에 사용자/그룹/관리ID 할당을 지원하지 않습니다. 다음은 Azure CLI 명령을 사용하는 예: [az-역할 할당-만들기를](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) 사용하여 서비스 버스 RBAC 역할에 ID를 할당합니다. 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **서비스 버스 네임스페이스**: 역할 할당은 네임스페이스 아래의 서비스 버스의 전체 토폴로지와 연결된 소비자 그룹에 걸쳐 있습니다.
- **리소스 그룹:** 역할 할당은 리소스 그룹 아래의 모든 Service Bus 리소스에 적용됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹의 모든 Service Bus 리소스에 적용됩니다.

> [!NOTE]
> RBAC 역할 할당이 전파되는 데 최대 5분이 걸릴 수 있습니다. 

기본 제공 역할이 정의되는 방법에 대한 자세한 내용은 [역할 정의 이해하기](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하십시오. 사용자 지정 RBAC 역할 만들기에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어에 대한 사용자 지정 역할 만들기를](../role-based-access-control/custom-roles.md)참조하십시오.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용
Azure Resources에 대해 관리되는 ID를 사용하여 VM에서 서비스 버스 리소스에 권한을 부여하려면 먼저 VM에서 Azure 리소스에 대해 관리되는 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure 포털](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure 파워쉘](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 리소스 관리자 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리되는 ID에 대한 사용 권한 부여
응용 프로그램에서 관리되는 ID에서 Service Bus 서비스에 대한 요청을 승인하려면 먼저 해당 관리되는 ID에 대한 RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure Service Bus는 Service Bus에서 보내고 읽을 수 있는 권한을 포함하는 RBAC 역할을 정의합니다. RBAC 역할이 관리되는 ID에 할당되면 관리되는 ID는 적절한 범위에서 Service Bus 엔터티에 대한 액세스 권한을 부여합니다.

RBAC 역할 할당에 대한 자세한 내용은 [Azure Active Directory에서 서비스 버스 리소스에 대한 액세스 권한을 인증하고 승인합니다.](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus를 통해 Azure 리소스에 관리 ID 사용
관리 되는 ID와 서비스 버스를 사용 하려면 ID 역할 및 적절 한 범위를 할당 해야 합니다. 이 섹션의 프로시저는 관리되는 ID로 실행되는 간단한 응용 프로그램을 사용하고 Service Bus 리소스에 액세스합니다.

여기서Azure [앱 서비스에서](https://azure.microsoft.com/services/app-service/)호스팅되는 샘플 웹 응용 프로그램을 사용하고 있습니다. 웹 응용 프로그램을 만들기 위한 단계별 지침은 [Azure에서 ASP.NET Core 웹 앱 만들기를](../app-service/app-service-web-get-started-dotnet.md) 참조하십시오.

응용 프로그램이 만들어지면 다음 단계를 따르십시오. 

1. **설정으로** 이동하여 **ID**를 선택합니다. 
1. **을 사용할** **상태를** 선택합니다. 
1. **저장**을 선택하여 설정을 저장합니다. 

    ![웹 앱에 대한 관리되는 ID](./media/service-bus-managed-service-identity/identity-web-app.png)

이 설정을 활성화하면 Azure Active Directory(Azure AD)에서 새 서비스 ID가 만들어지고 앱 서비스 호스트에 구성됩니다.

이제 Service Bus 리소스에서 필요한 범위의 역할에 이 서비스 ID를 할당합니다.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Azure 포털을 사용하여 RBAC 역할을 할당하려면
서비스 버스 네임스페이스에 역할을 할당하려면 Azure 포털의 네임스페이스로 이동합니다. 리소스에 대한 IAM(액세스 제어) 설정을 표시하고 다음 지침을 따라 역할 할당을 관리합니다.

> [!NOTE]
> 다음 단계에서는 서비스 버스 네임스페이스에 서비스 ID 역할을 할당합니다. 동일한 단계를 수행하여 지원되는 다른 범위(리소스 그룹 및 구독)에서 역할을 할당할 수 있습니다. 
> 
> [서비스 버스 메시징 네임스페이스가](service-bus-create-namespace-portal.md) 없는 경우 만듭니다. 

1. Azure 포털에서 서비스 버스 네임스페이스로 이동하여 네임스페이스에 대한 **개요를** 표시합니다. 
1. 서비스 버스 네임스페이스에 대한 액세스 제어 설정을 표시하려면 왼쪽 메뉴에서 **IAM(액세스 제어)을** 선택합니다.
1.  **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
3.  새 역할을 추가하려면 **추가를** 선택합니다.
4.  역할 **할당 추가** 페이지에서 할당할 Azure 서비스 버스 역할을 선택합니다. 그런 다음 역할을 할당하기 위해 등록한 서비스 ID를 찾기 위해 검색합니다.
    
    ![역할 할당 페이지 추가](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  **저장**을 선택합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 서비스 ID에 Azure Service Bus Data 소유자가 있음을 보여 주었습니다.
    
    ![역할에 할당된 ID](./media/service-bus-managed-service-identity/role-assigned.png)

역할을 할당하면 웹 응용 프로그램은 정의된 범위 내에서 Service Bus 엔터티에 액세스할 수 있습니다. 

### <a name="run-the-app"></a>앱 실행

이제 만든 ASP.NET 애플리케이션의 기본 페이지를 수정합니다. [이 GitHub 리포지토리](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)의 웹 애플리케이션 코드를 사용할 수 있습니다.  

Default.aspx 페이지가 방문 페이지입니다. 코드는 Default.aspx.cs 파일에서 찾을 수 있습니다. 그 결과 몇 가지 입력 필드와 메시지를 보내거나 받기 위해 Service Bus에 연결되는 **전송** 및 **수신** 단추가 있는 최소 웹 애플리케이션이 생성됩니다.

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 개체가 어떻게 초기화되는지에 유의합니다. 이 코드는 SAS(공유 액세스 토큰) 토큰 공급자를 사용하는 대신 `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` 호출을 사용하여 관리 ID용 토큰 공급자를 만듭니다. 이와 같이 유지하고 사용하는 데 특별한 비결은 없습니다. Service Bus 및 권한 부여 핸드셰이크에 대한 관리 ID 컨텍스트의 흐름은 토큰 공급자가 자동으로 처리합니다. 이 모델은 SAS를 사용하는 방식보다 단순합니다.

이와 같이 변경한 후에는 애플리케이션을 게시하고 실행합니다. Visual Studio에서 게시 프로필을 다운로드한 다음 가져오면 올바른 게시 데이터를 쉽게 가져올 수 있습니다.

![게시 프로필 받기](./media/service-bus-managed-service-identity/msi3.png)
 
메시지를 보내거나 받으려면 만든 엔터티의 이름과 네임스페이스의 이름을 입력합니다. 그런 후에 **보내기**나 **받기**를 클릭합니다.


> [!NOTE]
> - 관리 ID는 Azure 환경, App 서비스, Azure VM 및 확장 집합 내에서만 작동합니다. .NET 애플리케이션의 경우 Service Bus NuGet 패키지에서 사용되는 Microsoft.Azure.Services.AppAuthentication 라이브러리는 이 프로토콜에 대한 추상화를 제공하고 로컬 개발 환경을 지원합니다. 또한 이 라이브러리를 통해 개발 머신에서 Visual Studio, Azure CLI 2.0 또는 Active Directory 통합 인증의 사용자 계정을 사용하여 코드를 로컬로 테스트할 수 있습니다. 이 라이브러리를 통한 로컬 개발 옵션에 대한 자세한 내용은 [.NET을 사용하여 Azure Key Vault에 대한 서비스 간 인증](../key-vault/service-to-service-authentication.md)을 참조하세요.  
> 
> - 현재 관리 ID는 App Service 배포 슬롯에서 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
