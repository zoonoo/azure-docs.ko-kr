---
title: 가상 네트워크에 대한 Azure CLI 샘플
description: 가상 네트워크에 대한 Azure CLI 샘플입니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 03041f7c0172312828487d934ab540972dc5e2c9
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232103"
---
# <a name="azure-cli-samples-for-virtual-network"></a>가상 네트워크에 대한 Azure CLI 샘플

다음 표에는 Azure CLI 명령을 사용하는 bash 스크립트에 대한 링크가 포함됩니다.

| 스크립트 | Description |
|----|----|
| [다중 계층 애플리케이션을 위한 가상 네트워크 만들기](./scripts/virtual-network-cli-sample-multi-tier-application.md) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 트래픽은 HTTP 및 SSH로 제한되며, 백 엔드 서브넷에 대한 트래픽은 MySQL(3306 포트)로 제한됩니다. |
| [2개 가상 네트워크 피어링](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | 동일한 지역에 2개 가상 네트워크를 만들고 연결합니다. |
| [네트워크 가상 어플라이언스를 통한 트래픽 라우팅](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | 프런트 엔드 및 백 엔드 서브넷과 두 서브넷 간에 트래픽을 라우팅할 수 있는 VM이 있는 가상 네트워크를 만듭니다. |
| [인바운드 및 아웃바운드 VM 네트워크 트래픽 필터링](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 인바운드 네트워크 트래픽은 HTTP, HTTPS 및 SSH로 제한됩니다. 백 엔드 서브넷에서 인터넷으로의 아웃바운드 트래픽은 허용되지 않습니다. |
|[기본 Load Balancer를 사용하여 IPv4 + IPv6 이중 스택 가상 네트워크 구성](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|IPv4 및 IPv6 공용 IP 주소가 있는 Azure Basic Load Balancer 및 두 개의 VM으로 이중 스택(IPv4+IPv6) 가상 네트워크를 배포합니다. |
|[표준 Load Balancer를 사용하여 IPv4 + IPv6 이중 스택 가상 네트워크 구성](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|IPv4 및 IPv6 공용 IP 주소가 있는 Azure Standard Load Balancer 및 두 개의 VM으로 이중 스택(IPv4+IPv6) 가상 네트워크를 배포합니다. |
|[자습서: NAT 게이트웨이 만들기 및 테스트 - Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|원본 및 대상 가상 머신을 사용하여 NAT 게이트웨이를 만들고 유효성을 검사합니다. |