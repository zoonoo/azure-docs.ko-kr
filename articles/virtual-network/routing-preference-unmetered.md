---
title: Azure에서 라우팅 기본 설정의 동일 하지 않음
description: CDN 공급자에 게 데이터를 제공 하는 리소스에 대 한 라우팅 기본 설정을 구성 하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 1fcc918e5b4b54a415fed0f38d210aeeaa62c008
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679792"
---
# <a name="what-is-routing-preference-unmetered"></a>라우팅 기본 설정의 우선 순위는 무엇입니까?

라우팅 기본 설정 상호 연결은 Azure에서 원본 콘텐츠를 호스트 하는 고객이 있는 CDN (Content Delivery Network) 공급자에 게 제공 됩니다. 이 서비스를 통해 CDN 공급자는 다양 한 위치에서 Microsoft 글로벌 네트워크에 지 라우터와의 직접 피어 링 연결을 설정할 수 있습니다.

![라우팅 기본 설정의 우선 순위](media/routing-preference-unmetered/unmetered.png)

Azure의 원본에서 CDN 공급자를 대상으로 하는 네트워크 트래픽이 직접 연결의 이점을 활용 합니다.
* 이러한 직접 링크를 통해 라우팅되는 Azure 리소스에서 전송 되는 트래픽에 대 한 데이터 전송 청구서는 무료입니다.
* Azure에서 CDN 공급자와 원본 간의 직접 연결은 사이에 홉이 없기 때문에 최적의 성능을 제공 합니다. 이는 원본에서 데이터를 자주 인출 하는 CDN 워크 로드에 유용 합니다.

## <a name="configuring-routing-preference-unmetered"></a>라우팅 기본 설정의 우선 순위 구성

라우팅 기본 설정의 우선 순위를 활용 하려면 CDN 공급자를이 프로그램에 포함 해야 합니다. CDN 공급자가 프로그램의 일부가 아닌 경우 CDN 공급자에 게 문의 하세요.

다음으로 리소스에 대 한 라우팅 기본 설정을 구성 하 고 라우팅 기본 설정 유형을 **인터넷** 으로 설정 합니다. 공용 IP 주소를 만드는 동안 라우팅 기본 설정을 구성한 다음 공용 IP를 가상 컴퓨터, 인터넷 연결 부하 분산 장치와 같은 리소스에 연결할 수 있습니다. [Azure Portal를 사용 하 여 공용 IP 주소에 대 한 라우팅 기본 설정을 구성 하는 방법을 알아봅니다.](routing-preference-portal.md)

저장소 계정에 대 한 라우팅 기본 설정을 사용 하도록 설정 하 고 CDN 공급자가 저장소 원본에서 데이터를 인출 하는 데 사용 해야 하는 두 번째 끝점을 게시할 수도 있습니다. 예를 들어 저장소 계정 *StorageAccountA* 에 대 한 인터넷 경로 특정 끝점을 게시 하면 아래와 같이 저장소 서비스에 대 한 두 번째 끝점을 게시 합니다.

![저장소 계정에 대 한 라우팅 기본 설정](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>다음 단계

* [Azure PowerShell을 사용하여 VM에 대한 라우팅 기본 설정 구성](configure-routing-preference-virtual-machine-powershell.md)
* [Azure CLI를 사용하여 VM에 대한 라우팅 기본 설정 구성](configure-routing-preference-virtual-machine-cli.md)
* [저장소 계정에 대 한 라우팅 기본 설정 구성](/azure/storage/common/network-routing-preference)