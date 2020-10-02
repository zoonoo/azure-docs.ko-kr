---
title: Azure Load Balancer 부동 IP 구성
description: Azure Load Balancer 부동 IP 개요
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: e15aab3ebfe82fa97f5716769b5ff9675b4b3dc4
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637443"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer 부동 IP 구성

부하 분산 장치는 몇 가지 기능을 UDP 및 TCP 애플리케이션 모두에 제공합니다.

## <a name="floating-ip"></a>부동 IP

일부 애플리케이션 시나리오는 백 엔드 풀의 단일 VM에서 여러 애플리케이션 인스턴스가 동일한 포트를 사용하는 것을 선호하거나 필요로 합니다. 포트 재사용의 일반적인 예에는 고가용성을 위한 클러스터링, 네트워크 가상 어플라이언스 및 재암호화 없이 다중 TLS 엔드포인트 노출이 포함됩니다. 여러 규칙 전반에 백 엔드 포트를 재사용하려면 규칙 정의에 부동 IP를 사용하도록 설정해야 합니다.

**부동 IP**는 DSR(Direct Server Return)로 알려진 부분에 대한 Azure의 용어입니다. DSR은 다음과 같은 두 부분으로 구성됩니다.

- 흐름 토폴로지
- IP 주소 매핑 구성표

플랫폼 수준에서 Azure Load Balancer는 부동 IP 사용 여부에 관계 없이 DSR 흐름 토폴로지에서 항상 작동합니다. 이는 아웃바운드 부분의 흐름이 다시 원점으로 직접 흘러가도록 언제나 올바르게 다시 작성되는 것을 의미합니다.
부동 IP가 없으면 Azure는 사용 편의성을 위해 기존의 부하 분산 IP 주소 매핑 체계(VM 인스턴스의 IP)를 제공합니다. 부동 IP를 사용하도록 설정하면 Load Balancer의 프런트 엔드 IP에 매핑되는 IP 주소가 변경되어 추가적인 유연성이 제공됩니다. [여기](load-balancer-multivip-overview.md)를 참조하세요.

## <a name="limitations"></a><a name = "limitations"></a>제한 사항

- 부동 IP는 부하 분산 시나리오에 대 한 보조 IP 구성에서 현재 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요.
- [Azure Load Balancer 아웃바운드 연결](load-balancer-outbound-connections.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/security-overview.md)에 대해 자세히 알아보세요.
