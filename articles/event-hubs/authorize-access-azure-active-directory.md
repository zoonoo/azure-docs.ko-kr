---
title: Azure Active Directory를 사용하여 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 액세스 권한을 부여하는 방법에 대한 정보를 제공합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064855"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여
Azure 이벤트 허브는 Azure Active Directory(Azure AD)를 사용하여 이벤트 허브 리소스에 대한 요청을 승인합니다. Azure AD를 사용하면 역할 기반 액세스 제어(RBAC)를 사용하여 사용자 또는 응용 프로그램 서비스 보안 주체일 수 있는 보안 주체에 대한 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대해 자세히 알아보려면 [다양한 역할 이해를](../role-based-access-control/overview.md)참조하십시오.

## <a name="overview"></a>개요
보안 주체(사용자 또는 응용 프로그램)가 Event Hubs 리소스에 액세스하려고 하면 요청이 승인되어야 합니다. Azure AD를 사용하면 리소스에 대한 액세스는 2단계 프로세스입니다. 

 1. 먼저 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://eventhubs.azure.net/`. Kafka 클라이언트의 경우 토큰을 요청하는 `https://<namespace>.servicebus.windows.net`리소스는 .
 1. 다음으로, 토큰은 지정된 리소스에 대한 액세스를 승인하기 위해 Event Hubs 서비스에 대한 요청의 일부로 전달됩니다.

인증 단계에서는 응용 프로그램 요청에 런타임시 OAuth 2.0 액세스 토큰이 포함되어 야 합니다. 응용 프로그램이 Azure VM, 가상 시스템 규모 집합 또는 Azure Function 앱과 같은 Azure 엔터티 내에서 실행 중인 경우 관리되는 ID를 사용하여 리소스에 액세스할 수 있습니다. 관리되는 ID에서 이벤트 허브 서비스에 대한 요청을 인증하는 방법을 알아보려면 [Azure Active Directory및 Azure 리소스에 대한 관리되는 ID를 사용하여 Azure Event Hubs 리소스에 대한 인증 액세스를](authenticate-managed-identity.md)참조하세요. 

권한 부여 단계에서는 하나 이상의 RBAC 역할을 보안 주체에 할당해야 합니다. Azure 이벤트 허브는 이벤트 허브 리소스에 대한 사용 권한 집합을 포함하는 RBAC 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가질 수 있는 권한이 결정됩니다. RBAC 역할에 대한 자세한 내용은 [Azure 이벤트 허브에 대한 기본 제공 RBAC 역할을](#built-in-rbac-roles-for-azure-event-hubs)참조하십시오. 

이벤트 허브에 요청을 하는 네이티브 응용 프로그램 및 웹 응용 프로그램도 Azure AD로 권한을 부여할 수 있습니다. 액세스 토큰을 요청하고 이를 사용하여 Event Hubs 리소스에 대한 요청을 승인하는 방법을 알아보려면 [응용 프로그램에서 Azure AD를 사용하여 Azure 이벤트 허브에](authenticate-application.md)대한 액세스 인증을 참조하세요. 

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Event Hubs는 이벤트 허브 데이터에 액세스하는 데 사용되는 일반적인 사용 권한 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의하고 데이터에 액세스하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스범위는 구독 수준, 리소스 그룹, 이벤트 허브 네임스페이스 또는 그 아래에 있는 리소스로 범위를 지정할 수 있습니다. Azure AD 보안 주체는 Azure 리소스에 대한 사용자 또는 응용 프로그램 서비스 보안 주체 또는 [관리되는 ID일](../active-directory/managed-identities-azure-resources/overview.md)수 있습니다.

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Azure 이벤트 허브에 대한 기본 제공 RBAC 역할
Azure는 Azure AD 및 OAuth를 사용하여 이벤트 허브 데이터에 대한 액세스를 승인하기 위한 다음 기본 제공 RBAC 역할을 제공합니다.

- [Azure 이벤트 허브 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): 이 역할을 사용하여 이벤트 허브 리소스에 대한 완전한 액세스 권한을 부여합니다.
- [Azure 이벤트 허브 데이터 보낸 자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): 이 역할을 사용하여 이벤트 허브 리소스에 대한 전송 액세스 권한을 부여합니다.
- [Azure 이벤트 허브 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): 이 역할을 사용하여 이벤트 허브 리소스에 대한 사용/수신 액세스를 제공합니다.

## <a name="resource-scope"></a>리소스 범위 
보안 주체에 RBAC 역할을 할당하기 전에 보안 주체가 가져야 할 액세스 범위를 결정합니다. 모범 사례는 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위부터 시작하여 Event Hubs 리소스에 대한 액세스 범위를 좁힐 수 있는 수준에 대해 설명합니다.

- **소비자 그룹**: 이 범위에서 역할 할당은 이 엔터티에만 적용됩니다. 현재 Azure 포털은 이 수준에서 보안 보안 주체에 RBAC 역할을 할당하는 것을 지원하지 않습니다. 
- **이벤트 허브**: 역할 할당은 이벤트 허브 엔터티 및 그 아래에 있는 소비자 그룹에 적용됩니다.
- **네임스페이스**: 역할 할당은 네임스페이스 아래의 이벤트 허브의 전체 토폴로지와 그와 연관된 소비자 그룹에 걸쳐 있습니다.
- **리소스 그룹:** 역할 할당은 리소스 그룹 아래의 모든 이벤트 허브 리소스에 적용됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹의 모든 이벤트 허브 리소스에 적용됩니다.

> [!NOTE]
> - RBAC 역할 할당이 전파되는 데 최대 5분이 걸릴 수 있습니다. 
> - 이 콘텐츠는 아파치 카프카의 이벤트 허브와 이벤트 허브 모두에 적용됩니다. 카프카 지원 이벤트 허브에 대한 자세한 내용은 [Kafka - 보안 및 인증을 위한 이벤트 허브를](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)참조하십시오.


기본 제공 역할이 정의되는 방법에 대한 자세한 내용은 [역할 정의 이해하기](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하십시오. 사용자 지정 RBAC 역할 만들기에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어에 대한 사용자 지정 역할 만들기를](../role-based-access-control/custom-roles.md)참조하십시오.



## <a name="samples"></a>샘플
- [Microsoft.Azure.EventHubs 샘플.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    이러한 샘플은 이전 **Microsoft.Azure.EventHubs 라이브러리를** 사용하지만 최신 **Azure.Messaging.EventHubs 라이브러리를** 사용하도록 쉽게 업데이트할 수 있습니다. 샘플을 이전 라이브러리를 새 라이브러리로 이동하려면 [Microsoft.Azure.EventHubs에서 Azure.Messaging.EventHubs로 마이그레이션하는 가이드를](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)참조하십시오.
- [Azure.메시징.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    이 샘플은 최신 **Azure.Messaging.EventHubs 라이브러리를** 사용하도록 업데이트되었습니다.
- [카프카에 대한 이벤트 허브 - OAuth 샘플](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>다음 단계
- 보안 주체에 기본 제공 RBAC 역할을 할당하는 방법을 알아보고 [Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 인증 액세스를](authenticate-application.md)참조하세요.
- [RBAC 를 사용하여 사용자 지정 역할을 만드는 방법에](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)대해 알아봅니다.
- [EH를 사용하여 Azure Active Directory를 사용하는 방법](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK) 알아보기

다음 관련 문서를 참조하십시오.

- [Azure Active Directory를 사용하여 응용 프로그램에서 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-application.md)
- [Azure Active Directory를 사용하여 관리되는 ID를 인증하여 이벤트 허브 리소스에 액세스](authenticate-managed-identity.md)
- [공유 액세스 서명을 사용하여 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-shared-access-signature.md)
- [공유 액세스 서명을 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)
