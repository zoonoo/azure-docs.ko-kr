---
title: 네트워크 규칙의 Azure Firewall Manager 필터링(미리 보기)
description: 네트워크 규칙의 FQDN 필터링 사용 방법을 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460153"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>네트워크 규칙의 FQDN 필터링(미리 보기)

> [!IMPORTANT]
> 네트워크 규칙의 FQDN 필터링은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

FQDN(정규화된 도메인 이름)은 호스트 또는 IP 주소의 도메인 이름을 나타냅니다. Azure Firewall 및 방화벽 정책에서 DNS 확인을 기반으로 네트워크 규칙의 FQDN을 사용할 수 있습니다. 이 기능을 사용하면 임의의 TCP/UDP 프로토콜(NTP, SSH, RDP 등)을 사용하여 아웃바운드 트래픽을 필터링할 수 있습니다. DNS 프록시에서 네트워크 규칙의 FQDN을 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Firewall 정책 DNS 설정(미리 보기)](dns-settings.md)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

조직에 필요한 DNS 서버(Azure DNS 또는 고유한 사용자 지정 DNS)를 정의하면 Azure Firewall에서 선택한 DNS 서버를 기반으로 FQDN을 IP 주소로 변환합니다. 변환은 애플리케이션 규칙 처리 및 네트워크 규칙 처리 둘 다에 발생합니다.

애플리케이션 규칙의 도메인 이름을 사용하는 것과 네트워크 규칙의 도메인 이름을 사용하는 것의 차이점은 무엇인가요? 

- HTTP/S 및 MSSQL에 대한 애플리케이션 규칙의 FQDN 필터링은 애플리케이션 수준 투명 프록시와 SNI 헤더를 기반으로 합니다. 따라서 동일한 IP 주소로 확인되는 두 FQDN을 구별할 수 있습니다. 네트워크 규칙의 FQDN 필터링을 사용하는 경우에는 구별할 수 없습니다. 가능한 경우 항상 애플리케이션 규칙을 사용하세요.
- 애플리케이션 규칙에서는 HTTP/S 및 MSSQL을 선택한 프로토콜로 사용할 수 있습니다. 네트워크 규칙에서는 임의의 TCP/UDP 프로토콜을 대상 FQDN과 함께 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Firewall DNS 설정](dns-settings.md)
