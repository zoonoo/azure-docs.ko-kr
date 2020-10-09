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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73587512"
---
# <a name="subnet-extension"></a>서브넷 확장
공용 클라우드로의 워크 로드 마이그레이션은 신중한 계획과 조정이 필요 합니다. 중요 한 고려 사항 중 하나는 사용자의 IP 주소를 유지 하는 기능 일 수 있습니다. 특히 응용 프로그램에 IP 주소 종속성이 있거나 특정 IP 주소를 사용 하기 위한 규정 준수 요구 사항이 있는 경우 중요할 수 있습니다. Azure Virtual Network는 사용자가 선택한 IP 주소 범위를 사용 하 여 VNet 및 서브넷을 만들 수 있도록 하 여이 문제를 해결 합니다.

위의 요구 사항이 일부 응용 프로그램을 온-프레미스에 유지 하기 위해 추가 요구 사항과 결합 된 경우 마이그레이션을 약간 어려울 수 있습니다. 이러한 상황에서 한 쪽에 IP 주소를 다시 설정 하지 않고 Azure와 온-프레미스 간에 응용 프로그램을 분할 해야 합니다. 또한 응용 프로그램이 동일한 네트워크에 있는 것 처럼 통신할 수 있도록 해야 합니다.

위의 문제에 대 한 한 가지 해결 방법은 서브넷 확장입니다. 네트워크를 확장 하면 응용 프로그램이 서로 다른 물리적 위치에 있을 때 동일한 브로드캐스트 도메인을 통해 통신할 수 있으므로 네트워크 토폴로지를 다시 설계할 필요가 없습니다. 

일반적으로 네트워크를 확장 하는 것은 좋은 방법이 아니지만 아래 사용 사례에서 필요할 수 있습니다.

- **단계적 마이그레이션**: 가장 일반적인 시나리오는 마이그레이션 단계를 수행 하는 것입니다. 몇 가지 응용 프로그램을 먼저 가져와서 시간이 지남에 따라 나머지 응용 프로그램을 Azure로 마이그레이션합니다.
- **대기 시간**: 짧은 대기 시간 요구 사항은 데이터 센터에 최대한 가깝게 유지 되도록 일부 응용 프로그램을 온-프레미스에 유지 하는 또 다른 이유가 될 수 있습니다.
- **규정 준수**: 다른 사용 사례는 일부 응용 프로그램을 온-프레미스로 유지 하기 위한 규정 준수 요구 사항이 있을 수 있다는 것입니다.
 
> [!NOTE] 
> 필요 하지 않은 경우 서브넷을 확장 해서는 안 됩니다. 서브넷을 확장 하는 경우 중간 단계로 만들어야 합니다. 시간이 있으면 온-프레미스 네트워크에서 응용 프로그램 번호를 다시 지정 하 고 Azure로 마이그레이션해야 합니다.

다음 섹션에서는 서브넷을 Azure로 확장할 수 있는 방법을 설명 합니다.


## <a name="extend-your-subnet-to-azure"></a>Azure로 서브넷 확장
 계층 3 오버레이 네트워크 기반 솔루션을 사용 하 여 온-프레미스 서브넷을 Azure로 확장할 수 있습니다. 대부분의 솔루션은 계층 3 오버레이 네트워크를 사용 하 여 계층 2 네트워크를 확장 하는 VXLAN와 같은 오버레이 기술을 사용 합니다. 아래 다이어그램은 일반화 된 솔루션을 보여 줍니다. 이 솔루션에서는 Azure 및 온-프레미스 모두에 동일한 서브넷이 있습니다. 

![서브넷 확장 예제](./media/subnet-extension/subnet-extension.png)

서브넷의 IP 주소는 Azure 및 온-프레미스의 Vm에 할당 됩니다. Azure와 온-프레미스 모두에는 네트워크에 삽입 된 NVA가 있습니다. Azure의 VM이 온-프레미스 네트워크의 VM과 통신 하려고 하는 경우 Azure NVA는 패킷을 캡처하고, 캡슐화 하 고, VPN/Express 경로를 통해 온-프레미스 네트워크로 전송 합니다. 온-프레미스 NVA는 패킷을 수신 하 고, decapsulates 하 고, 네트워크의 원하는 받는 사람에 게 전달 합니다. 반환 트래픽은 비슷한 경로와 논리를 사용 합니다.

위의 예에서 Azure NVA와 온-프레미스 NVA는 서로 통신 하 고 서로 관련 된 IP 주소에 대해 알아봅니다. 더 복잡 한 네트워크에는 Nva와 그 뒤에 있는 IP 주소 간의 매핑을 유지 관리 하는 매핑 서비스가 있을 수 있습니다. NVA는 패킷을 수신 하면 매핑 서비스를 쿼리하여 그 뒤에 대상 IP 주소가 있는 NVA의 주소를 확인 합니다.

다음 섹션에서는 Azure에서 테스트 한 서브넷 확장 솔루션에 대 한 세부 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계 
[공급 업체 솔루션을 사용 하 여 서브넷을 Azure로 확장 합니다.](https://github.com/microsoft/Azure-LISP)