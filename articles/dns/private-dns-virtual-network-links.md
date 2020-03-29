---
title: Azure DNS 개인 영역의 가상 네트워크 링크 하위 리소스란 무엇입니까?
description: Azure DNS 개인 영역 가상 네트워크 링크 하위 리소스 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646764"
---
# <a name="what-is-a-virtual-network-link"></a>가상 네트워크 링크란?

Azure에서 개인 DNS 영역을 만든 후에는 가상 네트워크에서 즉시 액세스할 수 없습니다. 해당 네트워크에서 호스팅되는 VM이 개인 DNS 영역에 액세스하려면 먼저 가상 네트워크에 연결해야 합니다.
개인 DNS 영역을 가상 네트워크와 연결하려면 개인 DNS 영역 아래에 가상 네트워크 링크를 만들어야 합니다. 모든 개인 DNS 영역에는 가상 네트워크 링크 자식 리소스의 컬렉션이 있습니다. 이러한 각 리소스는 가상 네트워크에 대한 연결을 나타냅니다.

가상 네트워크를 등록 가상 네트워크 또는 해상도 가상 네트워크로 개인 DNS 영역에 연결할 수 있습니다.

## <a name="registration-virtual-network"></a>가상 네트워크 등록

개인 DNS 영역과 가상 네트워크 간의 [링크를 만들](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) 때 가상 시스템에 대한 DNS 레코드자동 [등록을](./private-dns-autoregistration.md) 설정하는 옵션이 있습니다. 이 옵션을 선택하면 가상 네트워크는 개인 DNS 영역에 대한 등록 가상 네트워크가 됩니다. 네트워크에 배포하는 가상 시스템에 대한 DNS 레코드가 자동으로 만들어집니다. DNS 레코드는 가상 네트워크에 이미 배포한 가상 시스템에 대해 만들어집니다. 가상 네트워크 관점에서 개인 DNS 영역은 해당 가상 네트워크의 등록 영역이 됩니다.
하나의 개인 DNS 영역에는 여러 개의 등록 가상 네트워크가 있을 수 있지만 모든 가상 네트워크는 정확히 하나의 등록 영역을 연결할 수 있습니다.

## <a name="resolution-virtual-network"></a>해결 가상 네트워크

개인 DNS 영역 아래에 가상 네트워크 링크를 만들고 DNS 레코드 자동 등록을 사용하도록 설정하지 않도록 선택하면 가상 네트워크는 해상도전용 가상 네트워크로 처리됩니다. 이러한 네트워크에 배포된 가상 시스템에 대한 DNS 레코드는 연결된 개인 DNS 영역에서 자동으로 생성되지 않습니다. 그러나 이러한 네트워크에 배포된 가상 시스템은 개인 DNS 영역에서 DNS 레코드를 성공적으로 쿼리할 수 있습니다. 이러한 레코드는 귀하가 수동으로 만들거나 개인 DNS 영역과 등록 네트워크로 연결된 다른 가상 네트워크에서 채워질 수 있습니다.
하나의 개인 DNS 영역에는 여러 해상도의 가상 네트워크가 있을 수 있으며 가상 네트워크에는 여러 해상도 영역이 연결되어 있을 수 있습니다.

## <a name="limits"></a>제한

등록 및 해결 네트워크 수를 이해하려면 Azure [DNS 제한을](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) 참조하는 개인 DNS 영역에 연결할 수 있습니다.

## <a name="other-considerations"></a>기타 고려 사항

* 클래식 배포 모델을 사용하여 배포된 가상 네트워크는 지원되지 않습니다.

* 개인 DNS 영역과 가상 네트워크 간에 하나의 링크만 만들 수 있습니다.

* 개인 DNS 영역 아래의 각 가상 네트워크 링크는 개인 DNS 영역의 컨텍스트 내에서 고유한 이름을 가져야 합니다. 다른 개인 DNS 영역에서 이름이 같은 링크를 가질 수 있습니다.

* 가상 네트워크 링크를 만든 후 가상 네트워크 링크 리소스의 "링크 상태" 필드를 확인합니다. 가상 네트워크의 크기에 따라 링크가 작동하고 링크 상태가 *완료됨으로*변경되기까지 몇 분 정도 걸릴 수 있습니다.

* 가상 네트워크를 삭제하면 다른 개인 DNS 영역에 연결된 모든 가상 네트워크 링크와 자동 등록된 DNS 레코드가 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure 포털을](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) 사용하여 가상 네트워크를 개인 DNS 영역에 연결하는 방법 알아보기

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.md)를 참조하세요.
