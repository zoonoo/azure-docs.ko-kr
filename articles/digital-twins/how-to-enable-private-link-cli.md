---
title: 개인 링크를 사용 하 여 개인 액세스 사용 (미리 보기)-CLI
titleSuffix: Azure Digital Twins
description: Azure CLI를 사용 하 여 개인 링크를 통해 Azure Digital Twins 솔루션에 대 한 개인 액세스를 사용 하도록 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5bd7ffda508980a9a56d86037887fc53a0fed640
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202946"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>개인 링크를 사용 하 여 개인 액세스 사용 (미리 보기): Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

이 문서에서는 [Azure Digital Twins 인스턴스의 개인 끝점과 개인 링크를 사용 하도록 설정](concepts-security.md#private-network-access-with-azure-private-link-preview) 하는 다양 한 방법에 대해 설명 합니다 (현재 미리 보기 상태). Azure digital twins 인스턴스에 대 한 개인 끝점을 구성 하면 azure digital twins 인스턴스를 보호 하 고, 공개 노출을 제거 하 고, [azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)에서 데이터 반출을 방지할 수 있습니다.

이 문서에서는 [**Azure CLI**](/cli/azure/what-is-azure-cli)를 사용 하는 프로세스를 안내 합니다.

이 문서에서 설명 하는 단계는 다음과 같습니다. 
1. 개인 링크를 켜고 Azure Digital Twins 인스턴스에 대 한 개인 끝점을 구성 합니다.
1. 공용 네트워크 액세스 플래그를 사용 하지 않거나 사용 하도록 설정 하 여 전용 링크 연결에 대 한 API 액세스만 제한 합니다.

## <a name="prerequisites"></a>필수 조건

개인 끝점을 설정 하려면 먼저 끝점을 배포할 수 있는 [**Azure Virtual Network (VNet)**](../virtual-network/virtual-networks-overview.md) 가 필요 합니다. VNet이 아직 없는 경우 Azure Virtual Network 빠른 시작 중 하나를 수행 하 여이 [를 설정할](../virtual-network/quick-create-portal.md) 수 있습니다.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스의 개인 끝점 관리 

[Azure CLI](/cli/azure/what-is-azure-cli)사용 하는 경우 이미 존재 하는 Azure 디지털 twins 인스턴스에서 개인 링크를 사용 하 여 개인 끝점을 설정할 수 있습니다 (인스턴스를 만들 때이를 추가할 수 없음). 그런 다음 추가 CLI 명령을 통해 계속 확인 하 고 관리할 수 있습니다. 

>[!TIP]
> Azure Digital Twins 인스턴스 대신 개인 링크 서비스를 통해 개인 링크 끝점을 설정할 수도 있습니다. 또한 동일한 구성 옵션 및 동일한 최종 결과를 제공 합니다.
>
> 개인 링크 리소스를 설정 하는 방법에 대 한 자세한 내용은 [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM 템플릿](../private-link/create-private-endpoint-template.md)또는 [PowerShell](../private-link/create-private-endpoint-powershell.md)에 대 한 개인 링크 설명서를 참조 하세요.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>기존 인스턴스에 개인 끝점 추가

개인 끝점을 만들어 Azure Digital Twins 인스턴스에 연결 하려면 [**az network private-endpoint create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 명령을 사용 합니다. 매개 변수에 정규화 된 ID를 사용 하 여 Azure Digital Twins 인스턴스를 식별 `--private-connection-resource-id` 합니다.

다음은 명령을 사용 하 여 필수 매개 변수만 있는 개인 끝점을 만드는 예제입니다.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

필수 및 선택적 매개 변수의 전체 목록과 더 많은 개인 끝점 생성 예제는 [ **az network private 엔드포인트 create** reference 설명서](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)를 참조 하세요.

### <a name="manage-private-endpoint-connections-on-the-instance"></a>인스턴스의 개인 끝점 연결 관리

Azure Digital Twins 인스턴스에 대해 개인 끝점을 만든 후에는 [**az dt network 개인 끝점 연결**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection) 명령을 사용 하 여 인스턴스와 관련 된 개인 끝점 **연결** 을 계속 관리할 수 있습니다. 작업에는 다음 항목이 포함됩니다.
* 개인 끝점 연결 표시
* 개인 끝점 연결의 상태를 설정 합니다.
* 개인 끝점 연결을 삭제 합니다.
* 인스턴스에 대 한 모든 개인 끝점 연결 나열

자세한 내용 및 예제는 [ **az dt network private-endpoint** reference 설명서](/cli/azure/ext/azure-iot/dt/network/private-endpoint)를 참조 하세요.

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스에서 다른 개인 링크 정보 관리

[**Az dt network Private Link**](/cli/azure/ext/azure-iot/dt/network/private-link) 명령을 사용 하 여 인스턴스의 개인 링크 상태에 대 한 추가 정보를 가져올 수 있습니다. 작업에는 다음 항목이 포함됩니다.
* Azure Digital Twins 인스턴스와 연결 된 개인 링크 나열
* 인스턴스와 연결 된 개인 링크를 표시 합니다.

자세한 내용 및 예제는 [ **az dt network private link** reference 설명서](/cli/azure/ext/azure-iot/dt/network/private-link)를 참조 하세요.

## <a name="disable--enable-public-network-access-flags"></a>공용 네트워크 액세스 플래그 사용/사용 안 함

모든 공용 연결을 거부 하 고 개인 끝점을 통한 연결만 허용 하 여 네트워크 보안을 향상 하도록 Azure Digital Twins 인스턴스를 구성할 수 있습니다. 이 작업은 **공용 네트워크 액세스 플래그** 를 사용 하 여 수행 됩니다. 

이 정책을 사용 하면 전용 링크 연결에 대 한 API 액세스만 제한할 수 있습니다. 공용 네트워크 액세스 플래그가 *사용 안 함으로* 설정 되 면 공용 클라우드의 Azure Digital twins 인스턴스 데이터 평면에 대 한 모든 REST API 호출이 반환 됩니다 `403, Unauthorized` . 또는 정책을 *사용 안 함으로* 설정 하 고 개인 끝점을 통해 요청을 수행 하면 API 호출이 성공 합니다.

이 문서에서는 [Azure CLI](/cli/azure/) 또는 [ARMClient 명령 도구](https://github.com/projectkudu/ARMClient)를 사용 하 여 네트워크 플래그의 값을 업데이트 하는 방법을 보여 줍니다. Azure Portal를 사용 하 여이 작업을 수행 하는 방법에 대 한 지침은이 문서의 [포털 버전](how-to-enable-private-link-portal.md) 을 참조 하세요.

### <a name="use-the-azure-cli"></a>Azure CLI 사용

Azure CLI에서 `--public-network-access` 명령에 매개 변수를 추가 하 여 공용 네트워크 액세스를 사용 하지 않도록 설정 하거나 사용 하도록 설정할 수 있습니다 `az dt create` . 이 명령을 사용 하 여 새 인스턴스를 만들 수도 있지만이 명령을 사용 하 여 이미 존재 하는 인스턴스의 이름을 제공 하 여 기존 인스턴스의 속성을 편집할 수 있습니다. 이 명령에 대 한 자세한 내용은 해당 [참조 설명서](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) 또는 [Azure Digital twins 인스턴스를 설정 하기 위한 일반 지침](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)을 참조 하세요.

Azure Digital Twins 인스턴스에 대해 공용 네트워크 액세스를 **사용 하지 않도록 설정** 하려면 `--public-network-access` 다음과 같이 매개 변수를 사용 합니다.

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

현재 사용 하지 않도록 설정 된 인스턴스에서 공용 네트워크 액세스를 **사용 하도록 설정** 하려면 다음과 유사한 명령을 사용 합니다.

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>ARMClient 명령 도구 사용 

[ARMClient 명령 도구](https://github.com/projectkudu/ARMClient)를 사용 하면 아래 명령을 사용 하 여 공용 네트워크 액세스를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 

공용 네트워크 액세스를 **사용 하지 않도록 설정** 하려면:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

공용 네트워크 액세스를 **사용 하도록 설정** 하려면:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>다음 단계

Azure의 개인 링크에 대해 자세히 알아보세요. 
* [*Azure 개인 링크 서비스 란?*](../private-link/private-link-service-overview.md)