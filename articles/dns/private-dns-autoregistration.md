---
title: Azure DNS 개인 영역의 자동 등록 기능
description: Azure DNS 개인 영역의 자동 등록 기능 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961231"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS 개인 영역의 자동 등록 기능은 무엇입니까?

Azure DNS 개인 영역 자동 등록 기능은 가상 네트워크에 배포된 가상 시스템에 대한 DNS 레코드 관리에서 어려움을 겪습니다. 가상 [네트워크를](./private-dns-virtual-network-links.md) 개인 DNS 영역과 연결하고 모든 가상 시스템에 대한 자동 등록을 사용하도록 설정하면 가상 네트워크에 배포된 가상 시스템에 대한 DNS 레코드가 개인 DNS 영역에 자동으로 생성됩니다. 앞으로 보기 레코드(A 레코드) 외에도 가상 시스템에 대해 역방향 조회 레코드(PTR 레코드)도 자동으로 만들어집니다.
가상 네트워크에 가상 컴퓨터를 더 추가하면 이러한 가상 시스템에 대한 DNS 레코드도 연결된 개인 DNS 영역에서 자동으로 생성됩니다.

가상 컴퓨터를 삭제하면 가상 시스템에 대한 DNS 레코드가 개인 DNS 영역에서 자동으로 삭제됩니다.

가상 네트워크 링크를 만드는 동안 "자동 등록 사용" 옵션을 선택하여 자동 등록을 활성화할 수 있습니다.

![자동 등록 사용](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>제한

* 자동 등록은 가상 시스템에만 작동합니다. 내부 로드 밸로드등과 같은 다른 모든 리소스의 경우 가상 네트워크에 연결된 개인 DNS 영역에서 수동으로 DNS 레코드를 만들 수 있습니다.
* DNS 레코드는 기본 가상 컴퓨터 NIC에 대해서만 자동으로 만들어집니다. 가상 시스템에 NIC가 두 개 이상 있는 경우 다른 네트워크 인터페이스에 대한 DNS 레코드를 수동으로 만들 수 있습니다.
* IPv6(AAAA 레코드)에 대한 자동 등록은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.md)를 참조하세요.
