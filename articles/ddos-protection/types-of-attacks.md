---
title: 표준 완화 Azure DDoS Protection 공격 유형
description: 표준에서 보호 하 Azure DDoS Protection 공격 유형에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 50dd5cf9a51ebcc6b2df188838ef2910afcd224d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905506"
---
# <a name="types-of-ddos-attacks-overview"></a>DDoS 공격 유형 개요

DDoS Protection 표준은 다음 유형의 공격을 완화할 수 있습니다.

- **대규모 공격** : 이러한 공격은 상당한 양의 합법적인 트래픽으로 네트워크 계층을 초과 합니다. 여기에는 UDP 초과, 증폭 및 기타 스푸핑된 패킷 홍수가 포함 됩니다. DDoS Protection Standard는 Azure의 글로벌 네트워크 규모를 자동으로 흡수 하 여 이러한 잠재적인 멀티 기가바이트 공격을 완화 합니다.
- **프로토콜 공격** : 이러한 공격은 계층 3 및 계층 4 프로토콜 스택의 취약점을 악용하여 대상을 액세스 불능 상태로 만듭니다. SYN 홍수 공격, 리플렉션 공격 및 기타 프로토콜 공격을 포함 합니다. DDoS Protection 표준은 클라이언트와의 상호 작용을 통해 악성 트래픽과 정상 트래픽을 구분하고 악성 트래픽을 차단하여 이러한 공격을 완화합니다. 
- **리소스(애플리케이션) 계층 공격** : 이러한 공격은 대상 웹 애플리케이션 패킷을 공격하여 호스트 간 데이터 전송을 방해합니다. 여기에는 HTTP 프로토콜 위반, SQL 주입, 교차 사이트 스크립팅 및 기타 계층 7 공격이 포함 됩니다. Azure [Application Gateway 웹 응용 프로그램 방화벽과](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)같은 웹 응용 프로그램 방화벽 및 DDoS Protection 표준을 사용 하 여 이러한 공격에 대 한 방어를 제공 합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)에서 사용할 수 있는 타사 웹 애플리케이션 방화벽 제품도 있습니다.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection 표준

DDoS Protection 표준은 가상 머신과 연결된 공용 IP 주소, 부하 분산 장치 및 애플리케이션 게이트웨이를 비롯한 가상 네트워크의 리소스를 보호합니다. Application Gateway 웹 응용 프로그램 방화벽이 나 공용 IP를 사용 하는 가상 네트워크에 배포 된 타사 웹 응용 프로그램 방화벽과 함께 사용 하는 경우 DDoS Protection Standard는 계층 7 완화 기능에 전체 계층 3을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 만드는](manage-ddos-protection.md)방법에 대해 알아봅니다.