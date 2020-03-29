---
title: Azure DNS 개인 영역이란 무엇입니까?
description: 개인 DNS 영역 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646798"
---
# <a name="what-is-a-private-azure-dns-zone"></a>개인 Azure DNS 영역이란 무엇입니까?

Azure 프라이빗 DNS는 사용자 지정 DNS 솔루션을 추가하지 않고도 가상 네트워크의 도메인 이름을 관리하고 확인할 수 있는 안정적이고 신뢰할 수 있는 DNS 서비스를 제공합니다. 프라이빗 DNS 영역을 사용하면 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 사용할 수 있습니다. 

개인 DNS 영역에 포함된 레코드는 인터넷에서 확인할 수 없습니다. 개인 DNS 영역에 대한 DNS 해결은 연결된 가상 네트워크에서만 작동합니다.

[가상 네트워크 링크를](./private-dns-virtual-network-links.md)만들어 개인 DNS 영역을 하나 이상의 가상 네트워크에 연결할 수 있습니다.
또한 자동 [등록](./private-dns-autoregistration.md) 기능을 사용하여 가상 네트워크에 배포된 가상 시스템에 대한 DNS 레코드의 수명 주기를 자동으로 관리할 수 있습니다.

## <a name="limits"></a>제한

구독에서 만들 수 있는 개인 DNS 영역 수와 개인 DNS 영역에서 지원되는 레코드 집합 수를 이해하려면 [Azure DNS 제한을](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) 참조합니다.

## <a name="restrictions"></a>제한

* 단일 레이블이 지정된 개인 DNS 영역은 지원되지 않습니다. 개인 DNS 영역에는 둘 이상의 레이블이 있어야 합니다. 예를 들어 contoso.com 점으로 구분된 두 개의 레이블이 있습니다. 개인 DNS 영역에는 최대 34개의 레이블이 있을 수 있습니다.
* 개인 DNS 영역에서 는 영역 위임(NS 레코드)을 만들 수 없습니다. 자식 도메인을 사용하려는 경우 상위 영역에서 네임 서버 위임을 설정하지 않고 도메인을 개인 DNS 영역으로 직접 만들고 가상 네트워크에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.md)를 참조하세요.
