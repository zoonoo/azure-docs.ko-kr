---
title: 보안 가상 허브란?
description: 보안 가상 허브에 대해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948071"
---
# <a name="what-is-a-secured-virtual-hub"></a>보안 가상 허브란?

가상 허브는 다른 리소스에서 연결할 수 있도록 하는 Microsoft에서 관리 하는 가상 네트워크입니다. Azure Portal의 가상 WAN에서 가상 허브를 만들면 가상 허브 VNet 및 게이트웨이 (선택 사항)가 구성 요소로 만들어집니다.

보안 *가상 허브* 는 Azure 방화벽 관리자에서 구성 하는 연결 된 보안 및 라우팅 정책을 포함 하는 [Azure 가상 WAN 허브](../virtual-wan/virtual-wan-about.md#resources) 입니다. 보안 virtual hubs를 사용 하 여 트래픽 거 버 넌 스 및 보호를 위한 기본 보안 서비스를 통해 허브 및 스포크 및 전이적 아키텍처를 쉽게 만들 수 있습니다. 

보안 가상 허브를 사용 하 여 V2V (가상 네트워크), B2V (가상 네트워크) 및 인터넷에 대 한 트래픽 (B2I/V2I) 간의 트래픽을 필터링 할 수 있습니다. 보안 가상 허브는 자동화 된 라우팅을 제공 합니다. 방화벽을 통해 트래픽을 라우팅하도록 자체 UDRs (사용자 정의 경로)를 구성할 필요가 없습니다.

Azure 방화벽, 타사 SECaaS security as a service) 공급자 또는 둘 다를 포함 하 여 네트워크 트래픽을 보호 하 고 제어 하는 데 필요한 보안 공급자를 선택할 수 있습니다. 현재, 보안 허브는 여러 허브에 걸친 B2B (Branch to Branch) 필터링 및 필터링을 지원 하지 않습니다. 자세히 알아보려면 [Azure 방화벽 관리자 란?](overview.md#known-issues)을 참조 하세요. 

## <a name="create-a-secured-virtual-hub"></a>보안 가상 허브 만들기

Azure Portal에서 방화벽 관리자를 사용 하 여 새 보안 가상 허브를 만들거나 이전에 Azure 가상 WAN을 사용 하 여 만든 기존 가상 허브를 변환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

보안 가상 허브를 만들어 허브 및 스포크 네트워크를 보호 하 고 관리 하는 데 사용 하려면 [자습서: Azure Portal을 사용 하 여 Azure 방화벽 관리자로 클라우드 네트워크 보호](secure-cloud-network.md)를 참조 하세요.