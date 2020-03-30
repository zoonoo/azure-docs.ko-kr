---
title: 클라우드별 Azure VMware 솔루션 단순 - 서비스
description: CloudSimple 서비스 및 개념에 대한 개요를 제공합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024958"
---
# <a name="cloudsimple-service-overview"></a>클라우드 단순 서비스 개요

CloudSimple 서비스를 사용하면 CloudSimple별로 Azure VMware 솔루션을 사용할 수 있습니다.  서비스를 만들면 노드를 구입하고 노드를 예약하고 사설 클라우드를 만들 수 있습니다.  CloudSimple 서비스를 사용할 수 있는 각 Azure 리전에서 CloudSimple 서비스를 만듭니다. 서비스는 CloudSimple에 의해 Azure VMware 솔루션의 에지 네트워크를 정의합니다. 에지 네트워크는 VPN, ExpressRoute 및 프라이빗 클라우드에 대한 인터넷 연결을 포함하는 서비스를 지원합니다.

## <a name="gateway-subnet"></a>게이트웨이 서브넷

CloudSimple 서비스에 따라 게이트웨이 서브넷이 필요하며 게이트웨이 서브넷이 생성된 리전에 고유합니다. 게이트웨이 서브넷은 에지 네트워크를 만들 때 사용되며 /28 CIDR 블록이 필요합니다.  게이트웨이 서브넷 주소 공간은 고유해야 합니다. CloudSimple 환경과 통신하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신하는 네트워크에는 온-프레미스 네트워크 및 Azure 가상 네트워크가 포함됩니다.  게이트웨이 서브넷은 생성된 후에는 삭제할 수 없습니다.  서비스가 삭제되면 게이트웨이 서브넷이 제거됩니다.

## <a name="next-steps"></a>다음 단계

* Azure 에서 [CloudSimple 서비스를 만드는](quickstart-create-cloudsimple-service.md)방법에 대해 알아봅니다.
