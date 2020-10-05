---
title: Azure Load Balancer 개념
description: Azure Load Balancer 개념에 대한 개요입니다.
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
ms.openlocfilehash: 61620a8497765c4d8f90a3d616bd2f4b932f8dcc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91629032"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer 알고리즘

부하 분산 장치는 몇 가지 기능을 UDP 및 TCP 애플리케이션 모두에 제공합니다.

## <a name="load-balancing-algorithm"></a>부하 분산 알고리즘

트래픽을 프런트 엔드에서 백 엔드 풀로 분산시키는 부하 분산 규칙을 만들 수 있습니다. Azure Load Balancer는 인바운드 흐름(바이트 아님)을 배포하는 데 해싱 알고리즘을 사용합니다. 부하 분산 장치는 흐름의 헤더를 백 엔드 풀 인스턴스에 다시 작성합니다. 상태 프로브가 정상 백 엔드 엔드포인트을 나타내면 서버에서 새 흐름을 받을 수 있습니다.

기본적으로 부하 분산 장치는 5 튜플 해시를 사용합니다.

해시에는 다음이 포함됩니다.

- **원본 IP 주소**
- **원본 포트**
- **대상 IP 주소**
- **대상 포트**
- **사용 가능한 서버에 흐름을 매핑하기 위한 IP 프로토콜 번호**

원본 IP 주소에 대한 선호도는 2 튜플 또는 3 튜플 해시를 사용하여 만들어집니다. 동일한 흐름의 패킷은 부하 분산된 프런트 엔드 내의 동일한 인스턴스에 도착합니다.

클라이언트에서 동일한 원본 IP의 새 흐름을 시작하면 원본 포트가 변경됩니다. 결과적으로 5 튜플 해시로 인해 트래픽이 다른 백 엔드 엔드포인트로 이동할 수 있습니다.
자세한 내용은 [Azure Load Balancer의 배포 모드 구성](./load-balancer-distribution-mode.md)을 참조하세요.

다음 이미지는 해시 기반 배포를 표시합니다.

![해시 기반 배포](./media/load-balancer-overview/load-balancer-distribution.png)

*그림: 해시 기반 배포*

## <a name="application-independence-and-transparency"></a>애플리케이션 독립성 및 투명성

부하 분산 장치는 TCP, UDP 또는 애플리케이션 계층과 직접 상호 작용하지 않습니다. 모든 TCP 또는 UDP 애플리케이션 시나리오를 지원할 수 있습니다. 부하 분산 장치는 흐름을 닫거나 시작하거나 흐름의 페이로드와 상호 작용하지 않습니다. 부하 분산 장치는 애플리케이션 계층 게이트웨이 기능을 제공하지 않습니다. 프로토콜 핸드셰이크는 항상 클라이언트와 백 엔드 풀 인스턴스 사이에서 직접 발생합니다. 인바운드 흐름에 대한 응답은 항상 가상 머신의 응답입니다. 흐름이 가상 머신에 도착하면 원래 원본 IP 주소도 유지됩니다.

- 모든 엔드포인트는 VM을 통해 응답합니다. 예를 들어 TCP 핸드셰이크는 클라이언트와 선택한 백 엔드 VM 간에 발생합니다. 프런트 엔드에 요청에 대한 응답은 백 엔드 VM에서 생성한 응답입니다. 프런트 엔드에 대한 연결의 유효성을 성공적으로 검사하는 경우 하나 이상의 백 엔드 가상 머신에 대한 전체 연결의 유효성을 검사하게 됩니다.
- 애플리케이션 페이로드는 부하 분산 장치에 투명합니다. 모든 UDP 또는 TCP 애플리케이션을 지원할 수 있습니다.
- 부하 분산 장치는 TCP 페이로드와 상호 작용하지 않고 TLS 오프로드를 제공하므로 포괄적인 암호화 시나리오를 구축할 수 있습니다. 부하 분산 장치를 사용하면 VM 자체에서 TLS 연결을 종료하여 TLS 애플리케이션에 대한 대규모 확장이 구현됩니다. 예를 들어 TLS 세션 키 용량은 백 엔드 풀에 추가하는 VM의 수와 유형으로만 제한됩니다.

## <a name="limitations"></a><a name = "limitations"></a>제한 사항

- 부하 분산 장치 규칙은 두 개의 가상 네트워크에 걸쳐 있을 수 없습니다.  프런트 엔드 및 해당 백 엔드 인스턴스는 동일한 가상 네트워크에 있어야 합니다.  

- 부하 분산 장치는 특정 TCP 또는 UDP 프로토콜에 대한 부하 분산 및 포트 전달을 제공합니다. 부하 분산 규칙 및 인바운드 NAT 규칙은 TCP 및 UDP를 지원하지만 ICMP를 비롯한 다른 IP 프로토콜은 지원하지 않습니다.

- 백 엔드 VM에서 내부 Load Balancer의 프런트 엔드로의 아웃바운드 흐름이 실패합니다.

- 부하 분산 규칙에서는 IP 조각 전달이 지원되지 않습니다. UDP 및 TCP 패킷의 IP 조각화는 부하 분산 규칙에서 지원되지 않습니다. HA 포트 부하 분산 규칙을 사용하여 기존 IP 조각을 전달할 수 있습니다. 자세한 내용은 [고가용성 포트 개요](load-balancer-ha-ports-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요. 하나의 부하 분산 장치를 만들고, 사용자 지정 IIS 확장이 설치된 VM을 만들고, VM 간에 웹앱의 부하를 분산시킵니다.
- [Azure Load Balancer 아웃바운드 연결](load-balancer-outbound-connections.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/security-overview.md)에 대해 자세히 알아보세요.
