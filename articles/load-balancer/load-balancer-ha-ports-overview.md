---
title: "Azure의 고가용성 포트 개요 | Microsoft Docs"
description: "내부 부하 분산 장치에서 고가용성 포트 부하 분산에 대해 알아보기"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 2219aeb725b207fd92ff3e7603d7ee9c78f2844c
ms.contentlocale: ko-kr
ms.lasthandoff: 09/28/2017

---

# <a name="high-availability-ports-overview-preview"></a>고가용성 포트 개요(미리 보기)

Azure Load Balancer의 표준 SKU에는 모든 포트에서와 지원되는 모든 프로토콜에 대해 트래픽을 분산하는 기능인 HA(고가용성) 포트가 도입되었습니다. 내부 부하 분산 장치를 구성하는 동안 사용자는 프런트 엔드 및 백 엔드 포트를 **0**으로 설정하고 프로토콜을 **all**로 설정할 수 있는 HA 포트 규칙을 구성할 수 있으므로 모든 트래픽이 내부 부하 분산 장치를 통과할 수 있습니다.

>[!NOTE]
> 고가용성 포트 기능은 현재 미리 보기 상태입니다. 미리 보기 중 이 기능은 일반 공급 릴리스에 있는 기능과 동일한 수준의 가용성 및 안정성을 제공하지 못할 수도 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

부하 분산 알고리즘은 여전히 동일하게 유지되며 대상은 <원본 IP 주소, 원본 포트, 대상 IP 주소, 대상 포트, 프로토콜>의 다섯 가지 입력 사항에 따라 선택됩니다. 그러나 이 구성을 사용하면 단일 LB 규칙에서 사용 가능한 모든 트래픽을 처리할 수 있으며 구성이 복잡해지지 않고 추가할 수 있는 부하 분산 규칙의 최대 수에 따른 제한도 줄어듭니다.

HA 포트를 사용할 수 있는 중요한 시나리오 중 하나는 Azure 가상 네트워크에서 네트워크 가상 어플라이언스의 고가용성 배포입니다. 또한 HA 포트에서 사용할 수 있는 또 다른 일반적인 시나리오는 포트 범위에 대한 부하 분산입니다. 

## <a name="why-use-high-ha-ports"></a>높은 HA 포트를 사용하는 이유

Azure 고객은 여러 유형의 보안 위협으로부터 작업 부하를 보호하기 위해 NVA(네트워크 가상 어플라이언스)에 크게 의존합니다. 또한 NVA는 안정적이고 가용성이 높아야 합니다.  

HA 포트는 Zookeeper와 같은 보다 복잡한 솔루션의 필요성을 없앰으로써 NVA HA 시나리오의 복잡성을 줄이고 신속한 장애 조치(failover) 및 확장 옵션을 통해 안정성을 높입니다. 이제 Azure 내부 부하 분산 장치의 백 엔드 풀에 NVA를 추가한 다음 HA 포트 부하 분산 장치 규칙을 구성하여 NVA HA를 구현할 수 있습니다.

다음 예제는 허브 및 스포크 가상 네트워크 배포를 제공하며 스포크가 신뢰할 수 있는 공간을 나가기 전에 허브 가상 네트워크 및 NVA를 통해 트래픽을 터널링합니다. NVA는 HA 포트 구성을 사용하는 내부 부하 분산 장치 뒤에 있으므로 모든 트래픽을 처리하고 이에 따라 전달할 수 있습니다. 

![ha 포트 예제](./media/load-balancer-ha-ports-overview/nvaha.png)

그림 1 - HA 모드로 배포된 NVA가 있는 허브 및 스포크 가상 네트워크


## <a name="region-availability"></a>지역 가용성

HA 포트는 현재 다음 지역에서 제공됩니다.
- 미국 동부 2
- 미국 중부
- 북유럽
- 미국 중서부
- 서유럽
- 동남아시아 

## <a name="preview-sign-up"></a>미리 보기 등록

Load Balancer 표준 SKU에서 HA 포트 기능의 미리 보기에 참여하려면 PowerShell 또는 Azure CLI 2.0을 사용하여 액세스 권한을 얻도록 구독을 등록합니다.

- PowerShell을 사용하여 등록

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Azure CLI 2.0을 사용하여 등록

    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network 
    ```
## <a name="caveats"></a>주의 사항

다음은 HA 포트에 대해 지원되는 구성 또는 예외 사항입니다.

- 단일 프런트 엔드 ipConfiguration에는 HA 포트(모든 포트)가 있는 단일 DSR 부하 분산 장치 규칙이나 HA 포트(모든 포트)가 있는 단일 비DSR 부하 분산 장치 규칙을 설정할 수 있습니다. 둘 다 사용할 수는 없습니다.
- 단일 네트워크 인터페이스 IP 구성에는 HA 포트가 있는 비DSR 부하 분산 장치 규칙 하나만 설정할 수 있습니다. 이 ipconfig에 대해 다른 규칙을 구성할 수는 없습니다.
- 단일 네트워크 인터페이스 IP 구성에 HA 포트가 있는 하나 이상의 DSR 부하 분산 장치 규칙을 설정할 수 있습니다. 단, 각각의 프런트 엔드 IP 구성은 고유해야 합니다.
- 모든 부하 분산 규칙이 HA 포트(DSR 전용)이거나 모든 규칙이 비HA 포트(DSR 및 비DSR)인 경우 동일한 백 엔드 풀을 가리키는 두 개(또는 그 이상)의 부하 분산 장치 규칙이 공존할 수 있습니다. HA 포트와 비HA 포트 규칙의 조합이 있는 경우 이러한 두 가지 LB 규칙이 공존할 수 없습니다.
- IPv6 사용 가능 테넌트의 HA 포트는 사용할 수 없습니다.


## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치에서 HA 포트 구성](load-balancer-configure-ha-ports.md)


