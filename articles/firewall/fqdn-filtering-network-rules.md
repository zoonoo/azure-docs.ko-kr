---
title: 네트워크 규칙의 Azure 방화벽 FQDN 필터링
description: 네트워크 규칙에서 Azure 방화벽 FQDN 필터링을 사용 하는 방법
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: 2f2cf9639acfa1330c8347ff654649004d7c382e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380909"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>네트워크 규칙에서 FQDN 필터링 사용

FQDN (정규화 된 도메인 이름)은 호스트 또는 IP 주소의 도메인 이름을 나타냅니다. Azure 방화벽 및 방화벽 정책에서 DNS 확인을 기반으로 네트워크 규칙에서 Fqdn을 사용할 수 있습니다. 이 기능을 사용 하면 모든 TCP/UDP 프로토콜 (NTP, SSH, RDP 등 포함)을 사용 하 여 아웃 바운드 트래픽을 필터링 할 수 있습니다. 네트워크 규칙에서 Fqdn을 사용 하려면 DNS 프록시를 사용 하도록 설정 해야 합니다. 자세한 내용은 [Azure 방화벽 DNS 설정](dns-settings.md)을 참조 하세요.

> [!NOTE]
> 기본적으로 FQDN 필터링은 와일드 카드를 지원 하지 않습니다.

## <a name="how-it-works"></a>작동 방식

조직에 필요한 DNS 서버 (Azure DNS 또는 사용자 지정 DNS)를 정의 하 고 나면 Azure 방화벽은 선택한 DNS 서버를 기반으로 FQDN을 IP 주소로 변환 합니다. 이러한 변환은 응용 프로그램 및 네트워크 규칙 처리 모두에서 발생 합니다.

새 DNS 확인이 수행 되 면 새 IP 주소가 방화벽 규칙에 추가 됩니다. DNS 서버에서 더 이상 반환 되지 않는 기존 IP 주소는 15 분 후에 만료 됩니다. Azure 방화벽 규칙은 네트워크 규칙에서 Fqdn의 DNS 확인 으로부터 15 초 마다 업데이트 됩니다.

### <a name="differences-in-application-rules-vs-network-rules"></a>응용 프로그램 규칙 및 네트워크 규칙의 차이점

- HTTP/S 및 MSSQL의 응용 프로그램 규칙에서 FQDN 필터링은 응용 프로그램 수준 투명 프록시와 SNI 헤더를 기반으로 합니다. 따라서 동일한 IP 주소로 확인 되는 두 Fqdn을 구분할 수 있습니다. 네트워크 규칙의 FQDN 필터링은 그렇지 않습니다. 

   가능 하면 항상 응용 프로그램 규칙을 사용 합니다.
     - 프로토콜이 HTTP/S 또는 MSSQL 인 경우 FQDN 필터링에 응용 프로그램 규칙을 사용 합니다.
   - HTTP/S 또는 MSSQL 이외의 다른 프로토콜의 경우 FQDN 필터링을 위해 응용 프로그램 또는 네트워크 규칙을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 방화벽 DNS 설정](dns-settings.md)
