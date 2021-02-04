---
title: Logic Apps REST API를 사용 하 여 통합 서비스 환경 (ISEs) 만들기
description: 에서 Vnet (Azure virtual network)에 액세스할 수 있도록 Logic Apps REST API를 사용 하 여 ISE (통합 서비스 환경)를 만듭니다 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550660"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Logic Apps REST API를 사용하여 ISE(통합 서비스 환경) 만들기

논리 앱 및 통합 계정에 [Azure virtual network](../virtual-network/virtual-networks-overview.md)에 대 한 액세스 권한이 필요한 시나리오의 경우 Logic Apps REST API를 사용 하 여 [ISE ( *통합 서비스 환경* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 를 만들 수 있습니다. ISE에 대해 자세히 알아보려면 [Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](connect-virtual-network-vnet-isolated-environment-overview.md)를 참조하세요.

이 문서에서는 Logic Apps REST API를 사용 하 여 일반적으로 ISE를 만드는 방법을 보여 줍니다. 필요에 따라 ISE에서 [시스템 할당 또는 사용자 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 를 사용 하도록 설정할 수도 있습니다. 단,이 경우에는 Logic Apps REST API 사용 해야 합니다. ISE는이 id를 사용 하 여 Azure 가상 네트워크에 있거나 연결 되어 있는 가상 머신, 기타 시스템 또는 서비스와 같은 보안 리소스에 대 한 액세스를 인증할 수 있습니다. 이렇게 하면 자격 증명을 사용 하 여 로그인 할 필요가 없습니다.

ISE를 만드는 다른 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Azure Portal를 사용 하 여 ISE 만들기](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [샘플 Azure Resource Manager 빠른 시작 템플릿을 사용 하 여 ISE 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [미사용 데이터를 암호화 하기 위해 고객이 관리 하는 키를 사용할 수 있도록 지 원하는 ISE 만들기](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure Portal에서 ISE를 만들 때와 동일한 [필수 구성 요소](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) 및 [액세스 요구 사항](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* Ise 정의에 정보를 포함할 수 있도록 ISE와 함께 사용 하려는 추가 리소스입니다. 예를 들면 다음과 같습니다. 

  * 자체 서명 된 인증서 지원을 사용 하려면 ISE 정의에 해당 인증서에 대 한 정보를 포함 해야 합니다.

  * 사용자 할당 관리 id를 사용 하도록 설정 하려면 해당 id를 미리 만들고 `objectId` `principalId` `clientId` ISE 정의에, 및 속성 및 해당 값을 포함 해야 합니다. 자세한 내용은 [Azure Portal에서 사용자 할당 관리 Id 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 참조 하세요.

* HTTPS PUT 요청을 사용 하 여 Logic Apps REST API를 호출 하 여 ISE를 만드는 데 사용할 수 있는 도구입니다. 예를 들어 [Postman](https://www.getpostman.com/downloads/)을 사용 하거나이 작업을 수행 하는 논리 앱을 빌드할 수 있습니다.

## <a name="create-the-ise"></a>ISE 만들기

Logic Apps REST API 호출 하 여 ISE를 만들려면 HTTPS PUT 요청을 만듭니다.

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 버전을 사용 하려면 ISE 커넥터에 대 한 HTTP PUT 요청을 직접 수행 해야 합니다.

배포는 일반적으로 완료 하는 데 2 시간 이내에 수행 됩니다. 배포에 최대 4시간이 걸리는 경우가 간혹 있습니다. 배포 상태를 확인 하려면 [Azure Portal](https://portal.azure.com)의 Azure 도구 모음에서 알림 아이콘을 선택 합니다. 그러면 알림 창이 열립니다.

> [!NOTE]
> 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이렇게 지연되면 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 대기해야 할 수도 있습니다.
>
> 가상 네트워크를 삭제하면 Azure에서 서브넷이 해제되기까지 일반적으로 최대 2시간이 걸리지만 더 오래 걸릴 수도 있습니다. 
> 가상 네트워크를 삭제할 때는 그 때까지 연결된 리소스가 없는지 확인해야 합니다. 
> [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조하세요.

## <a name="request-header"></a>요청 헤더

요청 헤더에 다음 속성을 포함 합니다.

* `Content-type`:이 속성 값을로 설정 `application/json` 합니다.

* `Authorization`: 사용 하려는 Azure 구독 또는 리소스 그룹에 대 한 액세스 권한이 있는 고객에 대 한 전달자 토큰으로이 속성 값을 설정 합니다.

<a name="request-body"></a>

## <a name="request-body"></a>요청 본문

요청 본문에서 ise에 대해 사용 하도록 설정 하려는 추가 기능에 대 한 정보를 포함 하 여 ISE를 만드는 데 사용할 리소스 정의를 제공 합니다. 예를 들면 다음과 같습니다.

* 해당 위치에 설치 된 엔터프라이즈 인증 기관에서 발급 한 자체 서명 된 인증서 및 인증서를 사용 하도록 허용 하는 ISE를 만들려면 `TrustedRoot` 나중에 `certificates` 이 문서에서 설명 하는 것 처럼 ise 정의의 섹션 내에 개체를 포함 합니다 `properties` .

* 시스템 할당 또는 사용자 할당 관리 id를 사용 하는 ISE를 만들려면 `identity` 나중에이 문서에서 설명 하는 것 처럼 관리 되는 id 형식 및 기타 필수 정보를 포함 하는 개체를 ise 정의에 포함 합니다.

* 고객이 관리 하는 키와 Azure Key Vault 사용 하 여 미사용 데이터를 암호화 하는 ISE를 만들려면 [고객이 관리 하는 키 지원을 사용 하도록 설정 하는 정보](customer-managed-keys-integration-service-environment.md)를 포함 합니다. 고객이 관리 하는 키는 나중에 *만들 때만* 설정할 수 있습니다.

### <a name="request-body-syntax"></a>요청 본문 구문

다음은 ISE를 만들 때 사용할 속성을 설명 하는 요청 본문 구문입니다.

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

## <a name="add-custom-root-certificates"></a>사용자 지정 루트 인증서 추가

종종 ISE를 사용 하 여 가상 네트워크 또는 온-프레미스의 사용자 지정 서비스에 연결 합니다. 이러한 사용자 지정 서비스는 엔터프라이즈 인증 기관 또는 자체 서명 된 인증서와 같은 사용자 지정 루트 인증 기관에서 발급 한 인증서로 보호 되는 경우가 많습니다. 자체 서명 된 인증서를 사용 하는 방법에 대 한 자세한 내용은 [다른 서비스와 시스템에 대 한 아웃 바운드 호출을 위한 보안 액세스 및 데이터 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)를 참조 하세요. ISE가 TLS (Transport Layer Security)를 통해 이러한 서비스에 성공적으로 연결 하려면 ISE에서 이러한 루트 인증서에 액세스 해야 합니다.

#### <a name="considerations-for-adding-custom-root-certificates"></a>사용자 지정 루트 인증서 추가 시 고려 사항

사용자 지정 신뢰할 수 있는 루트 인증서를 사용 하 여 ISE를 업데이트 하기 전에 다음 고려 사항을 검토 하세요.

* 루트 인증서 *와* 모든 중간 인증서를 업로드 해야 합니다. 최대 인증서 수는 20 개입니다.

* 루트 인증서 업로드는 최근 업로드가 이전 업로드를 덮어쓰는 대체 작업입니다. 예를 들어 한 인증서를 업로드 하는 요청을 보낸 다음 다른 인증서를 업로드 하는 다른 요청을 보내면 ISE는 두 번째 인증서만 사용 합니다. 두 인증서를 모두 사용 해야 하는 경우 동일한 요청에 함께 추가 합니다.  

* 루트 인증서를 업로드 하는 작업은 다소 시간이 걸릴 수 있는 비동기 작업입니다. 상태 또는 결과를 확인 하려면 `GET` 동일한 URI를 사용 하 여 요청을 보낼 수 있습니다. 응답 메시지에는 `provisioningState` `InProgress` 업로드 작업이 계속 작동 하는 경우 값을 반환 하는 필드가 있습니다. `provisioningState`값이 이면 `Succeeded` 업로드 작업이 완료 된 것입니다.

#### <a name="request-syntax"></a>요청 구문

사용자 지정 신뢰할 수 있는 루트 인증서를 사용 하 여 ISE를 업데이트 하려면 다음 HTTPS 패치 요청을 [Azure 환경에 따라 달라 지는 AZURE RESOURCE MANAGER URL](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane)에 보냅니다. 예를 들면 다음과 같습니다.

| 환경 | Azure Resource Manager URL |
|-------------|----------------------------|
| Azure global (다중 테 넌 트) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure 중국 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>사용자 지정 루트 인증서를 추가 하기 위한 요청 본문 구문

다음은 루트 인증서를 추가할 때 사용할 속성을 설명 하는 요청 본문 구문입니다.

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
