---
title: Private Link로 프라이빗 액세스 사용(미리 보기) - CLI
titleSuffix: Azure Digital Twins
description: Azure CLI를 사용하여 Private Link로 Azure Digital Twins 솔루션에 대해 프라이빗 액세스를 사용하도록 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0a9b92eb4d49274c23d89674e6f97f906dc28b43
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110616141"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Private Link로 프라이빗 액세스 사용(미리 보기): Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

이 문서에서는 [Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트로 Private Link를 사용 설정](concepts-security.md#private-network-access-with-azure-private-link-preview)하는 여러 방법을 설명합니다(현재 미리 보기 상태). Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트를 구성하면 [Azure Virtual Network(VNet)](../virtual-network/virtual-networks-overview.md)에서 데이터 반출을 방지할 뿐만 아니라 Azure Digital Twins 인스턴스를 보호하고 공개 노출을 없앨 수 있습니다.

이 문서에서는 [Azure CLI](/cli/azure/what-is-azure-cli)를 사용하여 프로세스를 안내합니다.

이 문서에서 설명하는 단계는 다음과 같습니다. 
1. Private Link를 켜고 Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트를 구성합니다.
1. Private Link 연결만으로 API 액세스를 제한하기 위해 공용 네트워크 액세스 플래그를 사용하지 않거나 사용하도록 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

프라이빗 엔드포인트를 설정하려면 먼저 엔드포인트를 배포할 수 있는 [Azure Virtual Network(VNet)](../virtual-network/virtual-networks-overview.md) 가 필요합니다. VNet이 아직 없으면 [Azure Virtual Network 빠른 시작](../virtual-network/quick-create-portal.md) 중 하나에 따라 이를 설정할 수 있습니다.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트 관리 

[Azure CLI](/cli/azure/what-is-azure-cli)를 사용하는 경우 이미 존재하는 Azure Digital Twins 인스턴스에 Private Link로 프라이빗 엔드포인트를 설정할 수 있습니다(인스턴스 생성의 일부로 추가할 수 없음). 그런 다음, 추가 CLI 명령을 통해 계속 확인하고 관리할 수 있습니다. 

>[!TIP]
> 또한 Azure Digital Twins 인스턴스 대신 Private Link 서비스를 통해 Private Link 엔드포인트를 설정할 수 있습니다. 이 방법도 동일한 구성 옵션 및 동일한 최종 결과를 제공합니다.
>
> Private Link 리소스 설정에 대한 자세한 내용은 [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM 템플릿](../private-link/create-private-endpoint-template.md) 또는 [PowerShell](../private-link/create-private-endpoint-powershell.md)에 대한 Private Link 설명서를 참조하세요.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>기존 인스턴스에 프라이빗 엔드포인트 추가

프라이빗 엔드포인트를 만들어 Azure Digital Twins 인스턴스에 연결하려면 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create) 명령을 사용합니다. `--private-connection-resource-id` 매개 변수에 정규화된 ID를 사용하여 Azure Digital Twins 인스턴스를 식별합니다.

다음은 명령을 사용하여 필수 매개 변수만 있는 프라이빗 엔드포인트를 만드는 예제입니다.

```azurecli-interactive
az network private-endpoint create --connection-name <private-link-service-connection> --name <name-for-private-endpoint> --resource-group <resource-group> --subnet <subnet-ID> --private-connection-resource-id "/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<Azure-Digital-Twins-instance-name>" 
```

필수 및 선택적 매개 변수의 전체 목록과 더 많은 프라이빗 엔드포인트 생성 예제는 [az network private-endpoint create 참조 설명서](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create)를 참조하세요.

### <a name="manage-private-endpoint-connections-on-the-instance"></a>인스턴스에서 프라이빗 엔드포인트 연결 관리

Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트를 만든 후에는 [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 인스턴스와 관련된 프라이빗 엔드포인트 **연결** 을 계속 관리할 수 있습니다. 작업에는 다음 항목이 포함됩니다.
* 프라이빗 엔드포인트 연결 표시
* 프라이빗 엔드포인트 연결의 상태 설정
* 프라이빗 엔드포인트 연결 삭제
* 인스턴스에 대한 모든 프라이빗 엔드포인트 연결 나열

자세한 내용 및 예제는 [az dt network private-endpoint 참조 설명서](/cli/azure/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true)를 참조하세요.

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스에서 다른 Private Link 정보 관리

[az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 인스턴스의 Private Link 상태에 대한 추가 정보를 가져올 수 있습니다. 작업에는 다음 항목이 포함됩니다.
* Azure Digital Twins 인스턴스와 연결된 Private Link 나열
* 인스턴스와 연결된 Private Link 표시

자세한 내용 및 예제는 [az dt network private-link 참조 설명서](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true)를 참조하세요.

## <a name="disable--enable-public-network-access-flags"></a>공용 네트워크 액세스 플래그 사용 안 함/사용

네트워크 보안 향상을 위해 모든 공용 연결을 거부하고 프라이빗 엔드포인트를 통한 연결만 허용하도록 Azure Digital Twins 인스턴스를 구성할 수 있습니다. 이 작업은 **공용 네트워크 액세스 플래그** 를 사용하여 수행됩니다. 

이 정책을 사용하면 API 액세스를 Private Link 연결만으로 제한할 수 있습니다. 공용 네트워크 액세스 플래그가 *사용 안 함* 으로 설정되었으면 퍼블릭 클라우드에서 Azure Digital Twins 인스턴스 데이터 영역으로의 모든 REST API 호출이 `403, Unauthorized`를 반환합니다. 또는 정책이 *사용 안 함* 으로 설정되고 프라이빗 엔드포인트를 통해 요청하면 API 호출이 성공합니다.

이 문서에서는 [Azure CLI](/cli/azure/) 또는 [ARMClient 명령 도구](https://github.com/projectkudu/ARMClient)를 사용하여 네트워크 플래그의 값을 업데이트하는 방법을 보여줍니다. Azure Portal을 사용하여 이 작업을 수행하는 방법에 대한 지침은 이 문서의 [포털 버전](how-to-enable-private-link-portal.md)을 참조하세요.

### <a name="use-the-azure-cli"></a>Azure CLI 사용

Azure CLI에서 `az dt create` 명령에 `--public-network-access` 매개 변수를 추가하여 공용 네트워크 액세스를 사용하거나 사용하지 않도록 설정할 수 있습니다. 이 명령을 사용하여 새 인스턴스를 만들 수도 있지만, 이 명령을 사용하여 이미 존재하는 인스턴스의 이름을 제공하여 기존 인스턴스의 속성을 편집할 수 있습니다. 이 명령에 대한 자세한 내용은 해당 [참조 설명서](/cli/azure/dt?view=azure-cli-latest&preserve-view=true#az_dt_create) 또는 [Azure Digital Twins 인스턴스를 설정하기 위한 일반 지침](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)을 참조하세요.

Azure Digital Twins 인스턴스에 대해 공용 네트워크 액세스를 **사용하지 않도록 설정** 하려면 다음과 같이 `--public-network-access` 매개 변수를 사용합니다.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Disabled
```

현재 사용하지 않도록 설정된 인스턴스에서 공용 네트워크 액세스를 **사용하도록 설정** 하려면 다음과 유사한 명령을 사용합니다.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>ARMClient  명령 도구 사용 

[ARMClient 명령 도구](https://github.com/projectkudu/ARMClient)를 사용하면 아래 명령을 사용하여 공용 네트워크 액세스를 사용하거나 사용하지 않도록 설정할 수 있습니다. 

공용 네트워크 액세스 **사용 안 함**:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

공용 네트워크 액세스 **사용**:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>다음 단계

Azure Private Link에 대해 자세히 알아보기: 
* [Azure Private Link 서비스는 무엇입니까?](../private-link/private-link-service-overview.md)