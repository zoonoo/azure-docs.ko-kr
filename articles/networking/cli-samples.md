---
title: Azure CLI 샘플-네트워킹
description: Azure 리소스와 부하 분산 및 트래픽 방향 샘플 간의 연결에 대 한 샘플을 포함 하 여 네트워킹에 대 한 Azure CLI 샘플에 대해 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: c038f0d238646f43b93ba2a2c6a1120ab5feccee
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497293"
---
# <a name="azure-cli-samples-for-networking"></a>네트워킹을 위한 Azure CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| 스크립트 | Description |
|-|-|
|**Azure 리소스 간 연결**||
| [다중 계층 애플리케이션을 위한 가상 네트워크 만들기](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 트래픽은 HTTP 및 SSH로 제한되며, 백 엔드 서브넷에 대한 트래픽은 MySQL(3306 포트)로 제한됩니다. |
| [2개 가상 네트워크 피어링](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 동일한 지역에 2개 가상 네트워크를 만들고 연결합니다. |
| [네트워크 가상 어플라이언스를 통한 트래픽 라우팅](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | 프런트 엔드 및 백 엔드 서브넷과 두 서브넷 간에 트래픽을 라우팅할 수 있는 VM이 있는 가상 네트워크를 만듭니다. |
| [인바운드 및 아웃바운드 VM 네트워크 트래픽 필터링](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 인바운드 네트워크 트래픽은 HTTP, HTTPS 및 SSH로 제한됩니다. 백 엔드 서브넷에서 인터넷으로의 아웃바운드 트래픽은 허용되지 않습니다. |
|**부하 분산 및 트래픽 방향**||
| [VM에서 여러 웹 사이트에 부하 분산](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Azure 가용성 집합에 연결되고 Azure 부하 분산 장치를 통해 액세스할 수 있는 2개의 VM을 여러 IP 구성을 사용해서 만듭니다. |
| [높은 애플리케이션 가용성을 위해 여러 지역 간에 트래픽 전송](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  두 개의 App Service 계획, 두 개의 웹앱, Traffic Manager 프로필 및 두 개의 Traffic Manager 엔드포인트를 만듭니다. |
| | |
