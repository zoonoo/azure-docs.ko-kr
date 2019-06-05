---
title: IP 주소 168.63.129.16 | Microsoft Docs
description: IP 주소 168.63.129.16을 소개하고 이 주소에서 리소스를 사용하는 방법에 대해 설명합니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: acfd5230d1bd572ea5179651558e3f736a8570af
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833130"
---
# <a name="what-is-ip-address-1686312916"></a>IP 주소 168.63.129.16

IP 주소 168.63.129.16은 Azure 플랫폼 리소스에 대한 통신 채널을 원활하게 설정하는 데 사용되는 가상 공용 IP 주소입니다. 고객은 Azure에서 프라이빗 가상 네트워크용 주소 공간을 정의할 수 있습니다. 따라서 Azure 플랫폼 리소스가 고유 공용 IP 주소로 포함되어 있어야 합니다. 이 가상 공용 IP 주소가 있으면 다음 작업을 원활하게 수행할 수 있습니다.

- VM 에이전트가 Azure 플랫폼과 통신하여 "준비" 상태임을 알릴 수 있습니다.
- 사용자 지정 DNS 서버가 없는 VM 등의 리소스에 필터링된 이름 확인 기능을 제공하기 위해 DNS 가상 서버와 통신할 수 있습니다. 이 필터링을 통해 고객은 리소스의 호스트 이름만 확인할 수 있습니다.
- 사용 하도록 설정 [Azure 부하 분산 장치에서 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) Vm의 상태를 확인 하려면.
- VM이 Azure의 DHCP 서비스에서 동적 IP 주소를 가져올 수 있도록 합니다.
- PaaS 역할에 대해 게스트 에이전트 하트비트 메시지를 사용할 수 있습니다.

## <a name="scope-of-ip-address-1686312916"></a>IP 주소 168.63.129.16의 범위

공용 IP 주소 168.63.129.16은 모든 지역 및 국가 클라우드의 모든에서 사용 됩니다. 이 특별 한 공용 IP 주소는 Microsoft가 소유 및 변경 되지 않습니다. 기본 네트워크 보안 그룹 규칙을 통해 허용됩니다. 모든 로컬 방화벽 정책에서 이 IP 주소를 허용하는 것이 좋습니다. 이 IP 주소에서는 내부 Azure 플랫폼만이 메시지 원본을 제공할 수 있으므로, 이 특수 IP 주소와 리소스 간의 통신은 안전합니다. 이 주소가 차단되면 여러 시나리오에서 예기치 않은 동작이 발생할 수 있습니다.

[Azure Load Balancer 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) 이 IP 주소에서 시작 합니다. 이 IP 주소를 차단 하는 경우에 프로브 실패 합니다.

가상 네트워크 (클래식) 시나리오에서는 상태 프로브는 개인 IP에서 소싱 된 및 168.63.129.16 사용 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [보안 그룹](security-overview.md)
- [네트워크 보안 그룹 만들기, 변경 또는 삭제](manage-network-security-group.md)
