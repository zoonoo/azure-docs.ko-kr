---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: db8ae4bba6717cb1c0f27befd0897b60c2760ed2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059210"
---
## <a name="private-endpoints-and-vnet-service-endpoints"></a>프라이빗 엔드포인트 및 VNet 서비스 엔드포인트

Azure는 [프라이빗 Azure 백본 네트워크](https://azure.microsoft.com/global-infrastructure/global-network/)를 사용하여 트래픽 터널링에 대한 프라이빗 엔드포인트 및 VNet 서비스 엔드포인트를 제공합니다. 이러한 엔드포인트 유형은 목적과 기반이 되는 기술이 비슷합니다. 그러나 두 기술 간에는 차이가 있으므로 네트워크를 디자인하기 전에 각각의 장단점에 대해 자세히 알아보는 것이 좋습니다.

선택할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.
- 두 기술 모두 VNet과 음성 리소스 간의 트래픽이 공용 인터넷을 통해 이동 *하지* 않도록 합니다.
- 프라이빗 엔드포인트는 음성 리소스에 전용 개인 IP 주소를 제공합니다. 이 IP 주소는 특정 VNet 및 서브넷 내에서만 액세스할 수 있습니다. 네트워크 인프라 내에서 이 IP 주소에 대한 액세스를 완전히 제어할 수 있습니다.
- VNet 서비스 엔드포인트는 음성 리소스에 대한 전용 개인 IP 주소를 제공 *하지* 않지만 대신 음성 리소스로 전송되는 모든 패킷을 캡슐화하고 Azure 백본 네트워크를 통해 직접 전달합니다.
- 두 기술 모두 온-프레미스 시나리오를 지원합니다. 기본적으로 VNet 서비스 엔드포인트를 사용하는 경우 가상 네트워크에 보호되는 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없지만 이를 [설정할 수 있습니다](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises).
- VNet 서비스 지점은 트래픽이 시작된 VNet을 기준으로 음성 리소스에 대한 액세스를 제한하려는 경우에 자주 사용됩니다.
- Cognitive Services의 경우 VNet 서비스 엔드포인트를 사용하도록 설정하면 **모든** Cognitive Services 리소스에 대한 트래픽이 프라이빗 백본 네트워크를 통과하게 됩니다. 이렇게 하려면 명시적인 네트워크 액세스 구성이 필요합니다(자세한 내용은 [여기](../speech-service-vnet-service-endpoint.md#configure-vnets-and-the-speech-resource-networking-settings)참조). 프라이빗 엔드포인트에는 이러한 제한이 없으며 네트워크 구성에 더 많은 유연성을 제공합니다. 즉, 동일한 VNet의 동일한 서브넷을 사용하는 프라이빗 백본을 통해 하나의 리소스에 액세스하고 공용 인터넷을 통해 다른 리소스에 액세스할 수 있습니다.
- 프라이빗 엔드포인트에는 [추가 비용](https://azure.microsoft.com/pricing/details/private-link)이 발생합니다. VNet 서비스 엔드포인트는 무료입니다.
- 프라이빗 엔드포인트에는 [추가 DNS 구성](../speech-services-private-link.md#turn-on-private-endpoints)이 필요합니다.
- 하나의 음성 리소스는 프라이빗 엔드포인트와 VNet 서비스 엔드포인트 모두에서 동시에 작동할 수 있습니다.

프로덕션 디자인을 결정하기 전에 두 엔드포인트 유형을 모두 시도하는 것이 좋습니다. 

- [Private Link 및 프라이빗 엔드포인트 설명서](../../../private-link/private-link-overview.md)
- [VNet 서비스 엔드포인트 설명서](../../../virtual-network/virtual-network-service-endpoints-overview.md)
