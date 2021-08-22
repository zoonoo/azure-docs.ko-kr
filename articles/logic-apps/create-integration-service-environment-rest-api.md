---
title: Logic Apps REST API를 사용하여 ISE(통합 서비스 환경) 만들기
description: Azure Logic Apps에서 Azure VNET(가상 네트워크)에 액세스할 수 있도록 Logic Apps REST API를 사용하여 ISE(통합 서비스 환경) 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: f50fecfb2675e870025d9e188d5303aacf77f7a2
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112295726"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Logic Apps REST API를 사용하여 ISE(통합 서비스 환경) 만들기

논리 앱 및 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)에 액세스해야 하는 시나리오의 경우 Logic Apps REST API를 사용하여 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만들 수 있습니다. ISE에 대해 자세히 알아보려면 [Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](connect-virtual-network-vnet-isolated-environment-overview.md)를 참조하세요.

이 문서에서는 일반적으로 Logic Apps REST API를 사용하여 ISE를 만드는 방법을 보여줍니다. 필요에 따라 ISE에서 [시스템 할당 또는 사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 활성화할 수도 있지만 이 경우에는 Logic Apps REST API만 사용합니다. 이 ID를 사용하면 ISE에서는 Azure 가상 네트워크 안에 있거나 이 네트워크에 연결된 가상 머신, 기타 시스템 또는 서비스와 같은 보호된 리소스에 액세스하도록 인증할 수 있습니다. 이렇게 하면 자격 증명을 사용하여 로그인할 필요가 없습니다.

ISE를 만드는 다른 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Portal을 사용하여 ISE 만들기](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [샘플 Azure Resource Manager 빠른 시작 템플릿을 사용하여 ISE 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/integration-service-environment)
* [미사용 데이터 암호화를 위해 고객 관리형 키 사용을 지원하는 ISE 만들기](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure Portal에서 ISE를 만드는 경우와 동일한 [필수 구성 요소](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) 및 [액세스 요구 사항](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* ISE에서 함께 사용하려는 추가 리소스(ISE 정의에 해당 정보를 포함할 수 있음), 예를 들어: 

  * 자체 서명된 인증서 지원이 가능하도록 설정하려면 ISE 정의에 해당 인증서에 대한 정보를 포함해야 합니다.

  * 사용자가 할당한 관리 ID를 사용하도록 설정하려면 해당 ID를 미리 만들고 ISE 정의에 `objectId`, `principalId`, `clientId` 속성 및 해당 값을 포함해야 합니다. 자세한 내용은 [Azure Portal에서 사용자가 할당한 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 참조하세요.

* HTTPS PUT 요청을 사용하여 Logic Apps REST API를 호출하여 ISE를 만드는 데 사용할 수 있는 도구. 예를 들어 [Postman](https://www.getpostman.com/downloads/)을 사용하거나 이 작업을 수행하는 논리 앱을 빌드할 수 있습니다.

## <a name="create-the-ise"></a>ISE 만들기

Logic Apps REST API를 호출하여 ISE를 만들려면 다음 HTTPS PUT 요청을 수행합니다.

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 버전을 사용하려면 ISE 커넥터에 대한 HTTP PUT 요청을 직접 수행해야 합니다.

배포는 완료하는 데 일반적으로 2시간 정도 소요됩니다. 배포에 최대 4시간이 걸리는 경우가 간혹 있습니다. 배포 상태를 확인하려면 [Azure Portal](https://portal.azure.com)의 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

> [!NOTE]
> 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이렇게 지연되면 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 대기해야 할 수도 있습니다.
>
> 가상 네트워크를 삭제하면 Azure에서 서브넷이 해제되기까지 일반적으로 최대 2시간이 걸리지만 더 오래 걸릴 수도 있습니다. 
> 가상 네트워크를 삭제할 때는 그 때까지 연결된 리소스가 없는지 확인해야 합니다. 
> [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조하세요.

## <a name="request-header"></a>요청 헤더

요청 헤더에 다음 속성을 포함합니다.

* `Content-type`: 이 속성 값을 `application/json`으로 설정합니다.

* `Authorization`: 이 속성 값을 사용하려는 Azure 구독 또는 리소스 그룹에 대한 액세스 권한이 있는 고객에 대한 전달자 토큰으로 설정합니다.

<a name="request-body"></a>

## <a name="request-body"></a>요청 본문

요청 본문에서 ISE에서 사용하도록 설정하려는 추가 기능에 대한 정보를 포함하여 ISE를 만드는 데 사용할 리소스 정의를 제공합니다. 예를 들면 다음과 같습니다.

* 자체 서명된 인증서 및 `TrustedRoot` 위치에 설치된 Enterprise Certificate Authority에서 발급한 인증서 사용을 허용하는 ISE를 생성하려면, 이 문서의 뒷부분에서 설명하는 것처럼 ISE 정의의 `properties` 섹션에 `certificates` 개체를 포함합니다.

* 시스템 할당 또는 사용자가 할당한 관리 ID를 사용하는 ISE를 만들려면 이 문서의 뒷부분에서 설명하는 것처럼 관리 ID 유형 및 기타 필수 정보가 있는 `identity` 개체를 ISE 정의에 포함합니다.

* 고객 관리형 키 및 Azure Key Vault를 사용하여 미사용 데이터를 암호화하는 ISE를 만들려면 [고객 관리형 키 지원을 가능하게 하는 정보](customer-managed-keys-integration-service-environment.md)를 포함합니다. 고객 관리형 키는 *만들 때만* 설정할 수 있으며 나중에는 설정할 수 없습니다.

### <a name="request-body-syntax"></a>요청 본문 구문

다음은 ISE를 만들 때 사용할 속성을 설명하는 요청 본문 구문입니다.

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>요청 본문 예제

이 예제 요청 본문은 샘플 값을 보여 줍니다.

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>사용자 지정 루트 인증서

ISE를 사용하여 가상 네트워크 또는 온-프레미스의 사용자 지정 서비스에 연결하는 경우가 있습니다. 이러한 사용자 지정 서비스는 사용자 지정 루트 인증 기관(예: 엔터프라이즈 인증 기관)에서 발급한 인증서 또는 자체 서명된 인증서로 보호되는 경우가 많습니다. 자체 서명된 인증서 사용에 대한 자세한 내용은 [보안 액세스 및 데이터 - 다른 서비스 및 시스템에 대한 아웃바운드 호출 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)를 참조하세요. ISE가 TLS(전송 계층 보안)를 통해 이러한 서비스에 성공적으로 연결하려면 ISE가 이러한 루트 인증서에 액세스해야 합니다.

#### <a name="considerations-for-adding-custom-root-certificates"></a>사용자 지정 루트 인증서 추가 시 고려 사항

사용자 지정 신뢰할 수 있는 루트 인증서로 ISE를 업데이트하기 전에 다음 고려 사항을 검토하세요.

* 루트 인증서와 모든 중간 인증서를 업로드해야 합니다. 최대 인증서 수는 20개입니다.

* 인증서의 주체 이름은 Azure Logic Apps에서 호출하려는 대상 엔드포인트의 호스트 이름과 일치해야 합니다. 

* 루트 인증서 업로드는 최신 업로드가 이전 업로드를 덮어쓰는 대체 작업입니다. 예를 들어 한 인증서를 업로드하는 요청을 보낸 다음, 다른 인증서를 업로드하는 또 다른 요청을 보내면 ISE는 두 번째 인증서만 사용합니다. 두 인증서를 모두 사용해야 하는 경우에는 동일한 요청에 함께 추가합니다.  

* 루트 인증서를 업로드하는 작업은 다소 시간이 걸릴 수 있는 비동기 작업입니다. 상태 또는 결과를 확인하기 위해 동일한 URI를 사용하여 `GET` 요청을 보낼 수 있습니다. 업로드 작업이 계속 작동하는 경우 응답 메시지에는 `InProgress` 값을 반환하는 `provisioningState` 필드가 있습니다. `provisioningState` 값이 `Succeeded`이면 업로드 작업이 완료된 것입니다.

#### <a name="request-syntax"></a>요청 구문

사용자 지정 신뢰할 수 있는 루트 인증서로 ISE를 업데이트하려면 다음 HTTPS PATCH 요청을 [Azure Resource Manager URL(Azure 환경에 따라 다름)](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane)에 보냅니다. 예를 들면 다음과 같습니다.

| Environment | Azure Resource Manager URL |
|-------------|----------------------------|
| Azure 글로벌(다중 테넌트) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure 중국 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>사용자 지정 루트 인증서를 추가하기 위한 요청 본문 구문

다음은 루트 인증서를 추가할 때 사용할 속성을 설명하는 요청 본문 구문입니다.

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경에 리소스 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
