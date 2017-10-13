---
title: "Azure CLI를 사용하여 영역이 설정된 공용 IP 주소 만들기 | Microsoft Docs"
description: "Azure CLI를 사용하여 가용성 영역에서 공용 IP를 만듭니다."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Azure CLI를 사용하여 가용성 영역에서 공용 IP 주소 만들기

Azure 가용성 영역(미리 보기)에서 공용 IP 주소를 배포할 수 있습니다. 가용성 영역은 Azure 지역에서 물리적으로 별도 영역입니다. 방법 배우기:

> * 가용성 영역에서 공용 IP 주소 만들기
> * 가용성 영역에서 만든 관련 리소스 식별

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 2.0.17 이후 버전을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

> [!NOTE]
> 가용성 영역은 미리 보기 상태이며 개발 및 테스트 시나리오에 대한 준비를 마쳤습니다. Azure 리소스, 지역 및 VM 크기 제품군을 선택하는 데 지원을 사용할 수 있습니다. 시작하는 방법에 대한 자세한 내용 및 가용성 영역을 사용해 볼 수 있는 Azure 리소스, 지역 및 VM 크기 제품군은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요. 지원을 위해 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones)에 연결하거나 [Azure 지원 티켓을 열](../azure-supportability/how-to-create-azure-support-request.md) 수 있습니다.

## <a name="create-a-zonal-public-ip-address"></a>영역 공용 IP 주소 만들기

다음 명령을 사용하여 가용성 영역에서 공용 IP 주소를 만듭니다.


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> 가상 컴퓨터의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.

## <a name="get-zone-information-about-a-public-ip-address"></a>공용 IP 주소에 대한 영역 정보 가져오기

다음 명령을 사용하여 공용 IP 주소의 영역 정보를 가져옵니다.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>다음 단계

- [가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)에 대해 자세히 알아보기
- [공용 IP 주소](../virtual-network/virtual-network-public-ip-address.md)에 대해 자세히 알아보기 
