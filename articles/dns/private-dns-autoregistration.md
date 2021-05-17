---
title: Azure DNS 프라이빗 영역의 자동 등록 기능 정의
description: Azure DNS 프라이빗 영역의 자동 등록 기능 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 2e5f41c0e149c99b5524c439c59e72afe554c776
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783900"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS 프라이빗 영역의 자동 등록 기능 정의

Azure DNS 프라이빗 영역 자동 등록 기능을 사용하면 가상 네트워크에 배포된 가상 머신에 대한 DNS 레코드 관리가 간편해집니다. 프라이빗 DNS 영역에 [가상 네트워크를 연결](./private-dns-virtual-network-links.md)하고 모든 가상 머신에 대해 자동 등록을 사용하도록 설정하는 경우 가상 네트워크에 배포된 가상 머신의 DNS 레코드가 자동으로 프라이빗 DNS 영역에 만들어집니다. 정방향 조회 레코드(A 레코드) 외에도 가상 머신에 대해 역방향 조회 레코드(PTR 레코드)도 자동으로 만들어집니다.
가상 네트워크에 가상 머신을 더 추가하는 경우 해당 가상 머신에 대한 DNS 레코드도 연결된 프라이빗 DNS 영역에 자동으로 생성됩니다.

가상 머신을 삭제하면 가상 머신에 대한 DNS 레코드가 프라이빗 DNS 영역에서 자동으로 삭제됩니다.

가상 네트워크 링크를 만드는 동안 "자동 등록 사용" 옵션을 선택하여 자동 등록을 사용하도록 설정할 수 있습니다.

![자동 등록 사용](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>제한

* 자동 등록은 가상 머신에 대해서만 작동합니다. 내부 부하 분산 장치 등의 다른 모든 리소스의 경우 가상 네트워크에 연결된 프라이빗 DNS 영역에서 DNS 레코드를 수동으로 만들 수 있습니다.
* DNS 레코드는 주 가상 머신 NIC에 대해서만 자동으로 생성됩니다. 가상 머신에 NIC가 둘 이상 있는 경우 다른 네트워크 인터페이스에 대한 DNS 레코드를 수동으로 만들 수 있습니다.
* 주 가상 머신 NIC에서 DHCP를 사용하는 경우에만 DNS 레코드가 자동으로 생성됩니다. 고정 IP 주소가 구성된 경우(예: [Azure에서 여러 IP 주소](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)를 사용하려는 경우) 자동 등록이 해당 가상 머신에 대한 레코드를 만들지 않습니다.
* IPv6에 대한 자동 등록(AAAA 레코드)은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.md)를 참조하세요.
