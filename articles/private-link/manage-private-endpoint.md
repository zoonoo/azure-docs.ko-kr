---
title: Azure에서 개인 끝점 연결 관리
description: Azure에서 개인 엔드포인트 연결을 관리하는 방법 알아보기
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452974"
---
# <a name="manage-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 관리
Azure Private Link는 개인 링크 서비스 소비자가 서비스를 소비하기 위해 서비스 공급자에 대한 연결을 요청할 수 있는 승인 호출 흐름 모델에서 작동합니다. 그런 다음 서비스 공급자는 소비자가 연결할 수 있도록 허용할지 여부를 결정할 수 있습니다. Azure 개인 링크를 사용하면 서비스 공급자가 리소스에서 개인 끝점 연결을 관리할 수 있습니다. 이 문서에서는 개인 끝점 연결을 관리하는 방법에 대한 지침을 제공합니다.

![비공개 엔드포인트 관리](media/manage-private-endpoint/manage-private-endpoint.png)

개인 링크 서비스 소비자가 선택할 수 있는 두 가지 연결 승인 방법이 있습니다.
- **자동**: 서비스 소비자가 서비스 공급자 리소스에 대한 RBAC 권한이 있는 경우 소비자는 자동 승인 방법을 선택할 수 있습니다. 이 경우 요청이 서비스 공급자 리소스에 도달하면 서비스 공급자로부터 아무작업도 필요하지 않으며 연결이 자동으로 승인됩니다. 
- **수동**: 반대로 서비스 소비자가 서비스 공급자 리소스에 대한 RBAC 권한이 없는 경우 소비자는 수동 승인 방법을 선택할 수 있습니다. 이 경우 연결 요청은 서비스 리소스에 **보류 중으로**나타납니다. 서비스 공급자는 연결을 설정하기 전에 요청을 수동으로 승인해야 합니다. 수동 경우 서비스 소비자는 서비스 공급자에게 더 많은 컨텍스트를 제공하기 위해 요청이 있는 메시지를 지정할 수도 있습니다. 서비스 공급자는 모든 개인 엔드포인트 연결에 대해 선택할 수 있는 다음 옵션이 있습니다: **승인됨,** **거부,** **제거**.

아래 표는 다양한 서비스 공급자 작업과 개인 끝점에 대한 결과 연결 상태를 보여 주며 있습니다.  또한 서비스 공급자는 소비자의 개입 없이 나중에 개인 끝점 연결의 연결 상태를 변경할 수도 있습니다. 이 작업은 소비자 측의 끝점 상태를 업데이트합니다. 


|서비스 공급자 작업   |서비스 소비자 개인 엔드포인트 상태   |설명   |
|---------|---------|---------|
|None    |    Pending     |    연결은 수동으로 만들어지며 개인 링크 리소스 소유자의 승인을 위해 보류 중입니다.       |
|승인    |  승인됨       |  연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다.     |
|거부     | 거부됨        | Private Link 리소스 소유자가 연결을 거부했습니다.        |
|제거    |  연결 끊김       | 개인 링크 리소스 소유자에 의해 연결이 제거되었으며 개인 끝점은 정보를 제공하므로 정리를 위해 삭제해야 합니다.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Azure PaaS 리소스에서 프라이빗 엔드포인트 연결 관리
포털은 Azure PaaS 리소스에서 개인 끝점 연결을 관리하는 데 선호되는 방법입니다. 현재 Azure PaaS 리소스에서 연결을 관리하기 위한 PowerShell/CLI 지원이 없습니다.
1. [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.
2. 개인 링크 센터로 이동합니다.
3. **리소스에서**개인 끝점 연결을 관리할 리소스 유형을 선택합니다.
4. 각 리소스 유형에 대해 연결된 개인 끝점 연결 수를 볼 수 있습니다. 필요에 따라 리소스를 필터링할 수 있습니다.
5. 전용 끝점 연결을 선택합니다.  나열된 연결에서 관리할 연결을 선택합니다. 
6. 상단의 옵션에서 선택하여 연결 상태를 변경할 수 있습니다.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>고객/파트너 소유의 프라이빗 링크 서비스에서 개인 엔드포인트 연결 관리

Azure PowerShell 및 Azure CLI는 Microsoft 파트너 서비스 또는 고객 소유 서비스에서 개인 엔드포인트 연결을 관리하는 데 선호되는 방법입니다. 현재 개인 링크 서비스에서 연결을 관리하기 위한 포털 지원이 없습니다.  
 
### <a name="powershell"></a>PowerShell 
  
다음 PowerShell 명령을 사용하여 개인 끝점 연결을 관리합니다.  
#### <a name="get-private-link-connection-states"></a>비공개 링크 연결 상태 얻기 
cmdlet을 `Get-AzPrivateLinkService` 사용하여 개인 끝점 연결 및 해당 상태를 가져옵니다.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>개인 엔드포인트 연결 승인 
 
cmdlet을 `Approve-AzPrivateEndpointConnection` 사용하여 개인 끝점 연결을 승인합니다. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부 
 
cmdlet을 `Deny-AzPrivateEndpointConnection` 사용하여 개인 끝점 연결을 거부합니다. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>개인 끝점 연결 제거 
 
cmdlet을 `Remove-AzPrivateEndpointConnection` 사용하여 개인 끝점 연결을 제거합니다. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
개인 `az network private-link-service update` 엔드포인트 연결을 관리하는 데 사용합니다. 연결 상태는 매개 변수에 ```azurecli connection-status``` 지정됩니다. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>다음 단계
- [프라이빗 엔드포인트에 대해 알아보기](private-endpoint-overview.md)
 
