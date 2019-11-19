---
title: Azure Firewall 규칙 처리 논리
description: Azure Firewall에는 NAT 규칙, 네트워크 규칙 및 애플리케이션 규칙이 있습니다. 규칙은 규칙 유형에 따라 처리됩니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166784"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure Firewall 규칙 처리 논리
Azure Firewall에는 NAT 규칙, 네트워크 규칙 및 애플리케이션 규칙이 있습니다. 규칙은 규칙 유형에 따라 처리됩니다.


## <a name="network-rules-and-applications-rules"></a>네트워크 규칙 및 애플리케이션 규칙 
네트워크 규칙이 가장 먼저 적용되고, 다음으로 애플리케이션 규칙이 적용됩니다. 규칙은 종료됩니다. 따라서 네트워크 규칙에서 일치 항목이 발견된 경우 애플리케이션 규칙이 처리되지 않습니다.  네트워크 규칙이 일치하지 않고 패킷 프로토콜이 HTTP/HTTPS이면 애플리케이션 규칙에 따라 패킷이 평가됩니다. 여전히 일치하는 항목이 없으면 인프라 규칙 컬렉션과 비교하여 패킷이 평가됩니다. 여전히 일치하는 항목이 없으면 기본적으로 패킷이 거부됩니다.

## <a name="nat-rules"></a>NAT 규칙
[자습서: Azure Portal을 사용하여 Azure Firewall DNAT를 통해 인바운드 트래픽 필터링](tutorial-firewall-dnat.md)에 설명된 대로 DNAT(Destination Network Address Translation)를 구성하여 인바운드 연결을 사용할 수 있습니다. DNAT 규칙이 가장 먼저 적용됩니다. 일치 항목이 발견되면 변환된 트래픽을 허용하도록 암시적인 해당 네트워크 규칙이 추가됩니다. 변환된 트래픽을 일치시키는 거부 규칙을 사용하여 네트워크 규칙 컬렉션을 명시적으로 추가함으로써 이 동작을 재정의할 수 있습니다. 이러한 연결에는 애플리케이션 규칙이 적용되지 않습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.