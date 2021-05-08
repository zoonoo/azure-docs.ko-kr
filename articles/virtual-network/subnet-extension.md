---
title: Azure의 서브넷 확장 | Microsoft Docs
description: Azure의 서브넷 확장에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: 7effa870bc57bccc33652df343cdb0c187642ce8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108141130"
---
# <a name="subnet-extension"></a>서브넷 확장
퍼블릭 클라우드로 워크로드를 마이그레이션하려면 신중한 계획과 조율이 필요합니다. 그중에서도 특히 고려해야 하는 사항은 IP 주소를 보존할 수 있는지 여부입니다. 이는 애플리케이션에 IP 주소 종속성이 있거나 특정 IP 주소를 사용해야 하는 규정 준수 요구 사항이 있는 경우에 특히 중요할 수 있습니다. Azure Virtual Network는 원하는 IP 주소 범위를 사용하여 VNet 및 서브넷을 만들 수 있도록 지원함으로써 이 문제를 해결합니다.

마이그레이션은 위 요구 사항에 일부 애플리케이션을 온-프레미스에 유지해야 한다는 추가 요구 사항이 더해질 경우 까다로워질 수 있습니다. 이러한 상황에서는 Azure와 온-프레미스 양쪽에서 IP 주소의 번호를 다시 지정하지 않고 양쪽에 애플리케이션을 분할해야 합니다. 또한 애플리케이션이 모두 동일한 네트워크에 있는 것처럼 통신할 수 있도록 지원해야 합니다.

이러한 문제에 대한 한 가지 솔루션은 서브넷 확장입니다. 네트워크를 확장하면 애플리케이션이 서로 다른 물리적 위치에 있는 경우에도 동일한 브로드캐스트 도메인을 통해 통신할 수 있게 되어 네트워크 토폴로지를 다시 구성해야 할 필요가 없어집니다. 

네트워크를 확장하는 것은 일반적으로는 좋은 방법이 아니지만 다음과 같은 사용 사례에는 네트워크 확장이 필요할 수 있습니다.

- **단계적 마이그레이션**: 가장 일반적인 시나리오는 마이그레이션을 단계별로 수행하려는 경우입니다. 이 시나리오에서는 먼저 Azure로 몇 개의 애플리케이션만 마이그레이션한 후에 차차 나머지를 마이그레이션합니다.
- **대기 시간**: 낮은 대기 시간 요구 사항은 애플리케이션이 가급적 데이터 센터 가까이에 위치하도록 일부 애플리케이션을 온-프레미스에 유지하게 되는 또 다른 이유입니다.
- **규정 준수**: 또 다른 사용 사례는 일부 애플리케이션을 온-프레미스에 유지해야 한다는 규정 준수 요구 사항이 있는 경우입니다.
 
> [!NOTE] 
> 서브넷은 반드시 필요한 경우가 아닌 이상 확장하지 않아야 합니다. 서브넷을 확장하려는 경우에는 이를 일시적으로만 진행해야 합니다. 시간이 흐른 후에는 온-프레미스 네트워크에 있는 애플리케이션에 번호를 다시 지정하여 Azure로 마이그레이션해야 합니다.

다음 섹션에서는 Azure로 서브넷을 확장하는 방법을 설명합니다.


## <a name="extend-your-subnet-to-azure"></a>Azure로 서브넷 확장
 계층 3 오버레이 네트워크 기반 솔루션을 사용하여 온-프레미스 서브넷을 Azure로 확장할 수 있습니다. 대부분의 솔루션은 VXLAN과 같은 오버레이 기술을 사용하여 계층 3 네트워크를 사용해서 계층 2 네트워크를 확장합니다. 아래 다이어그램에서는 일반화된 솔루션을 보여 줍니다. 이 솔루션에서는 일부 서브넷이 Azure와 온-프레미스, 양쪽에 모두 있습니다. 

![서브넷 확장 예제](./media/subnet-extension/subnet-extension.png)

서브넷의 IP 주소가 Azure와 온-프레미스의 VM에 할당됩니다. Azure와 온-프레미스에서는 모두 네트워크에 NVA가 삽입되어 있습니다. Azure의 VM이 온-프레미스 네트워크의 VM과 통신하려고 하면 Azure NVA가 패킷을 캡처하여 캡슐화한 다음 VPN/Express Route를 통해 온-프레미스 네트워크로 전송합니다. 온-프레미스 NVA가 패킷을 수신하여 캡슐화를 해제하고 네트워크에 있는 의도된 수신자에게 전달합니다. 반환 트래픽도 비슷한 경로와 로직을 사용합니다.

위 예제에서 Azure NVA와 온-프레미스 NVA는 서로의 IP 주소를 알아내어 통신합니다. 더 복잡한 네트워크에는 NVA와 IP 주소 사이의 매핑을 유지하는 매핑 서비스가 있을 수도 있습니다. NVA가 패킷을 수신하면 매핑 서비스에 쿼리하여 해당 대상 IP 주소를 갖는 NVA의 주소를 알아냅니다.

다음 섹션에서는 Azure에서 테스트한 서브넷 확장 솔루션에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계 
[Azure 확장 네트워크를 사용하여 온-프레미스 서브넷을 Azure로 확장](/windows-server/manage/windows-admin-center/azure/azure-extended-network)