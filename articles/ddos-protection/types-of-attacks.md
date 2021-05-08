---
title: Azure DDoS Protection 표준으로 완화되는 공격 유형
description: Azure DDoS Protection 표준이 보호하는 공격 유형을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8eac365fc5146f315db8f09a67960aa892fa1c85
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106057"
---
# <a name="types-of-ddos-attacks-overview"></a>DDoS 공격 유형 개요

DDoS Protection 표준은 다음 유형의 공격을 완화할 수 있습니다.

- **대규모 공격**: 이 공격은 정상적으로 보이는 대량의 트래픽으로 네트워크 계층을 플러드하는 것입니다. 여기에는 UDP 플러드, 증폭 플러드 및 기타 스푸핑된 패킷 플러드가 포함됩니다. DDoS Protection 표준은 자동으로 Azure의 글로벌 네트워크 규모를 사용하여 이러한 잠재적인 멀티 기가바이트 공격을 흡수하고 스크럽하여 완화시킵니다.
- **프로토콜 공격**: 이러한 공격은 계층 3 및 계층 4 프로토콜 스택의 취약점을 악용하여 대상을 액세스 불능 상태로 만듭니다. 여기에는 SYN 플러드 공격, 리플렉션 공격 및 기타 프로토콜 공격이 포함됩니다. DDoS Protection 표준은 클라이언트와의 상호 작용을 통해 악성 트래픽과 정상 트래픽을 구분하고 악성 트래픽을 차단하여 이러한 공격을 완화합니다. 
- **리소스(애플리케이션) 계층 공격**: 이러한 공격은 대상 웹 애플리케이션 패킷을 공격하여 호스트 간 데이터 전송을 방해합니다. 여기에는 HTTP 프로토콜 위반, SQL 주입, 교차 사이트 스크립팅 및 기타 계층 7 공격이 포함됩니다. Azure [Application Gateway 웹 애플리케이션 방화벽](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)과 같은 웹 애플리케이션 방화벽과 DDoS Protection 표준을 사용하여 이러한 공격에 대한 방어를 제공합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)에서 사용할 수 있는 타사 웹 애플리케이션 방화벽 제품도 있습니다.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection 표준

DDoS Protection 표준은 가상 머신과 연결된 공용 IP 주소, 부하 분산 장치 및 애플리케이션 게이트웨이를 비롯한 가상 네트워크의 리소스를 보호합니다. Application Gateway 웹 애플리케이션 방화벽 또는 공용 IP를 사용하여 가상 네트워크에 배포된 타사 웹 애플리케이션 방화벽과 결합하면 DDoS Protection 표준은 전체 계층 3에서 계층 7까지 완화 기능을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 생성](manage-ddos-protection.md)하는 방법을 알아봅니다.