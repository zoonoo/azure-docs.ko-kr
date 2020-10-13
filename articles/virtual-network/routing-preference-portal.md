---
title: 공용 IP 주소에 대한 라우팅 기본 설정 구성 - Azure Portal
description: 인터넷 트래픽 라우팅 기본 설정을 사용하여 공용 IP를 만드는 방법 알아보기
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 7d41893d734037e466ad786641fed98c461adcfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86231814"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Azure Portal를 사용하여 공용 IP 주소에 대한 라우팅 기본 설정 구성

이 문서에서는 공용 IP 주소에 대 한 ISP 네트워크 (**인터넷** 옵션)를 통해 [라우팅 기본 설정을](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview) 구성 하는 방법을 보여 줍니다. 공용 IP 주소를 만든 후 인터넷에 대한 인바운드 및 아웃바운드 트래픽을 위해 다음 Azure 리소스와 연결할 수 있습니다.

* 가상 머신
* 가상 머신 크기 집합
* AKS(Azure Kubernetes Service)
* 인터넷 연결 부하 분산 장치
* Application Gateway
* Azure Firewall

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 모든 Azure 서비스에 대해 Microsoft 글로벌 네트워크로 설정되며 모든 Azure 서비스에 연결할 수 있습니다.

> [!IMPORTANT]
> 라우팅 기본 설정은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

## <a name="register-the-feature-for-your-subscription"></a>구독에 대한 기능 등록
라우팅 기본 설정 기능은 현재 미리 보기로 제공됩니다. 다음과 같이 Azure PowerShell을 사용하여 구독에 대한 기능을 등록합니다.
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>라우팅 기본 설정을 사용하여 공용 IP 주소 만들기
1. [Azure Portal](https://preview.portal.azure.com/)에 로그인합니다.
2. **리소스 만들기**를 선택합니다. 
3. 검색 상자에 *공용 IP 주소*를 입력합니다.
3. 검색 결과에서 **공용 IP 주소**를 선택합니다. 그런 다음, **공용 IP 주소** 페이지에서 **만들기**를 선택합니다.
3. **라우팅 기본 설정** 옵션에서 **인터넷**을 선택합니다.

      ![공용 IP 주소 만들기](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > 공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 그러나 라우팅 기본 설정은 현재 IPV4만 지원합니다.

위에서 만든 공용 IP 주소를 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 자습서 페이지의 CLI 섹션을 사용합니다. [공용 IP 주소를 가상 머신에 연결](associate-public-ip-address-vm.md#azure-cli)하여 공용 IP를 VM에 연결합니다. 위에서 만든 공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)와 연결할 수도 있습니다. 공용 IP 주소는 부하가 분산된 VIP(가상 IP 주소)로 사용됩니다.

## <a name="next-steps"></a>다음 단계
- [라우팅 기본 설정을 사용하는 공용 IP](routing-preference-overview.md)에 대해 자세히 알아봅니다.
- [VM에 대한 라우팅 기본 설정을 구성합니다](tutorial-routing-preference-virtual-machine-portal.md).
- [PowerShell을 사용하여 공용 IP 주소에 대한 라우팅 기본 설정을 구성합니다](routing-preference-powershell.md).
- Azure에서 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
